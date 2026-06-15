
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

