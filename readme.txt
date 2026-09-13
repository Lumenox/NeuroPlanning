1.21
change of schema in supabase for better organization: commun and neuroplanning.
MAIN SQL code was: -- Création des schémas
CREATE SCHEMA commun;
CREATE SCHEMA neuroplanning;

-- Déplacement des tables partagées
ALTER TABLE public.profiles     SET SCHEMA commun;
ALTER TABLE public.neurologues  SET SCHEMA commun;
ALTER TABLE public.annonces     SET SCHEMA commun;
ALTER FUNCTION public.get_email_from_hospital_id(text) SET SCHEMA commun;

-- Déplacement des tables spécifiques à Neuroplanning
ALTER TABLE public.planning                   SET SCHEMA neuroplanning;
ALTER TABLE public.absences                   SET SCHEMA neuroplanning;
ALTER TABLE public.conflict_acceptances       SET SCHEMA neuroplanning;
ALTER TABLE public.planning_history           SET SCHEMA neuroplanning;
ALTER TABLE public.planning_sandbox           SET SCHEMA neuroplanning;
ALTER TABLE public.planning_sandbox_baseline  SET SCHEMA neuroplanning;
ALTER TABLE public.sandbox_meta               SET SCHEMA neuroplanning;

-- Droits d'accès (indispensable, sinon le client JS ne pourra rien lire)
GRANT USAGE ON SCHEMA commun TO anon, authenticated;
GRANT USAGE ON SCHEMA neuroplanning TO anon, authenticated;

GRANT ALL ON ALL TABLES IN SCHEMA commun TO anon, authenticated;
GRANT ALL ON ALL TABLES IN SCHEMA neuroplanning TO anon, authenticated;

GRANT EXECUTE ON FUNCTION commun.get_email_from_hospital_id(text) TO anon, authenticated;

then weird but needed to correct here:
CREATE OR REPLACE FUNCTION commun.get_email_from_hospital_id(h_id text)
 RETURNS text
 LANGUAGE sql
 SECURITY DEFINER
AS $function$
  SELECT email FROM commun.profiles WHERE hospital_id = h_id LIMIT 1;
$function$;

AND MOST IMPORTANTLY: dont forget to go in settings > data api > exposed schemas and expose all new schema

1.20
-ajouts cosmétiques (lignes verticales et horizontales)
-même les viewers peuvent double cliquer pour vérifier quel médecin est dispo sur un poste (dans le planning)
-amélioration du mode clair
-mode impression propre
-pour les admin, toute valeur entrée est injectée au pinceau. 
-le pinceau peut être activé par alt+clic
-corrections cosmétiques



Récapitulatif du système Sandbox

Création : copie d'une période du planning réel vers planning_sandbox + photo figée dans planning_sandbox_baseline
Édition : dropdown filtré (exclut les conflits cliniques, signale les absences), mode pinceau, sauvegarde
Visualisation continue : rouge/violet pour les conflits cliniques internes à la sandbox, bleu pour les modifications faites en sandbox
Comparaison à la demande : bouton "🔍 Comparer" qui détecte deux types d'écarts avec le planning réel — les vrais conflits (double édition divergente) et les drifts (le réel a bougé, la sandbox ne le sait pas) — tous deux signalés par un encadré orange avec tooltip explicite
Push sélectif : sélection période + colonnes, analyse fraîche, preview cellule par cellule avec checkbox individuelle (cochée par défaut seulement pour les changements propres), affichage clair de la valeur qui sera effectivement écrite
Suppression : reset complet des 3 tables sandbox

C'est un système avec une vraie gestion de concurrence multi-admin, ce qui n'est pas trivial à faire proprement — le découpage clean/drift/conflict avec sélection manuelle est probablement la partie la plus solide de l'ensemble.
Quelques pistes pour plus tard si tu en ressens le besoin à l'usage :

Extension de la période d'une sandbox existante (tu l'avais mentionné en tout début de conversation — "quitte à ce que l'on puisse étendre les dates si besoin") : pas encore implémenté, on pourrait l'ajouter si tu sens que recréer une sandbox à chaque fois devient pénible
Optimisation des updates de baseline en boucle si tu pushs un jour de très gros volumes (200+ cellules) et que ça devient lent
Indicateur visuel dans la barre d'onglets (badge sur "🧪 Sandbox") si une sandbox active existe, pour que tu ne l'oublies pas en navigant entre les onglets

Système de gestion de la consultation avec système de "prévision des plages de cs à 5sem"


1.10

update majeure 1.10 : Messagerie et implémentation de la gestion individuelle de conflits : "rouge" à résoudre, "jaune" accepté, "violet" non clinique

Fonctionnalités :
Mode clair/sombre améliorée

pour vous connecter :
votre APH et par défaut 123 > vous pourrez changer le mdp une fois connecté
Si cela ne marche pas : je n’ai pas encore crée votre session, prévenez moi

Mode Viewer : 
-3 onglets : planning, absences, semainier
-planning assez proche de l’excel, avec une visualisation des « conflits »
-En rouge (et ligne légèrement surlignée en rouge) figureront des « conflits » non résolus.
-si la ligne est légèrement surligné en jaune : c’est un conflit « accepté » par un admin (par exemple bip + HDJ) ; si un conflit intéresse uniquement un viewer, celui-ci peut alors « l’accepter » lui-même.
-si la ligne est légèrement violette : il y a un conflit avec les absences 
-les internes peuvent être affichés (petit filtre sur lequel cliquer)
-planning absence : désormais par colonne de médecin. Figurent CA (congé annuel et rtt), CF (formation), Ens (enseignement), Rech (Recherche)
-semainier : résumé de ce qui figure dans les précédentes tables à l’échelle d’un médecin. Vous pouvez aussi afficher un deuxième à droite pour anticiper des échanges par exemple.
-filtres : dates : par défaut à partir du jour même jusqu’à +100 ; peut etre changé. nom médecin : permet de surligner sur le tableau le nom recherché. boutons : interne et groupes de salles : pour afficher ou non des colonnes (valider aussi pour le semainier pour n’afficher que les gardes pour un médecin par exemple)
-double cliquer pour voir médecins dispo

Mode Admin :
-chaque admin a des droits de modification qui différent (ex la consultation n’a aucun droit sur les absences) : revenir vers moi si besoin d’upgrader les droits
-des compteurs permettent de compter ce qui est affiché par les filtres
-bouton « modifier » :
-sur les tables : double cliquez sur une cellule pour entrer un nom, une liste déroulante de proposition s’affiche aussi
                -« pinceau » : vous permet de rentrer un nom et vous n’avez plus qu’à cliquer sur toutes les cellules que vous souhaitez appliquer la modif
                -« ajouter en masse » : + : ajouter de grosses plages d’absence.
-« Sauvegarder » pour valider les modif qui seront répercutées sur la base de données (et sur l’historique de modif). Ou « Annuler ».
-sur la page planning : en glissant la souris sur le jour « rouge » ou « jaune », on peut passer de l’état « conflit à traiter » à « conflit accepté » (vice versa)


Sécurité :
code écrit et hébergé sur mon github perso + sécurisation des data sur supabase Supabase Auth,
mdp hashing bcrypt

Fonctionnalités prévues à venir :
-ajout d’un onglet « bac à sable » pour admin faire des modifications tests, et si approuvées => le pousser sur le vrai planning immédiatement.
-faire disparaitre la colonne 2e medecin du semainier si aucun n’est sélectionné

