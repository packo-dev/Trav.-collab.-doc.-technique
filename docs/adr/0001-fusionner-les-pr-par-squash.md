# 1. Fusionner les pull requests par squash

- **Date** : 2026-10-06
- **Statut** : Proposée

## Contexte

Tout changement arrive sur `main` par une pull request relue, une par issue. Pendant une PR, les commits s'accumulent : le test qui reproduit le bug, la correction, puis les corrections demandées en relecture (`fix typo`, `réponse à la relecture`). La PR des statistiques (#10) en compte déjà deux avant la relecture.

GitHub propose trois façons de fusionner, et le bouton vert choisit par défaut le merge commit. Sans règle écrite, chaque membre fusionnerait à sa façon et l'historique de `main` mélangerait les styles. L'équipe compte trois à quatre personnes, avec des PR courtes, de moins de 400 lignes.

## Options envisagées

1. **Merge commit.** Pour : tous les commits de la branche sont conservés, avec un commit de fusion qui marque l'arrivée de la PR. Contre : les commits de travail (`wip`, `fix typo`) arrivent sur `main`, et `git log` devient difficile à lire.
2. **Squash and merge.** Pour : un commit par PR sur `main`, dont le message est le titre de la PR, déjà relu ; `main` se lit comme la liste des changements validés, et un `git revert` annule une PR entière d'un coup. Contre : le détail des commits de la branche disparaît de `main`, par exemple le commit du test écrit avant la correction.
3. **Rebase and merge.** Pour : historique linéaire, sans commit de fusion, qui garde chaque commit. Contre : exige des commits déjà propres avant la fusion, donc de réécrire l'historique de la branche, ce qui se combine mal avec la règle « pas de `push --force` pendant la relecture ».

## Décision

Nous fusionnons toutes les PR par **Squash and merge**, puis nous supprimons la branche. Le titre de la PR devient le message du commit sur `main` : il décrit le changement et se relit avec la PR.

## Conséquences

- `main` reçoit un commit par PR relue : l'historique se lit comme un journal des changements, et une PR se défait avec un seul `git revert`.
- Les auteurs peuvent faire des petits commits pendant la relecture, sans réécrire l'historique de la branche.
- Le détail des commits disparaît de `main` : l'ordre « test, puis correction » ne se voit plus que dans l'onglet Commits de la PR, tant qu'elle reste consultable sur GitHub.
- Il faut penser à choisir **Squash and merge** dans le menu du bouton vert : par défaut, GitHub propose le merge commit. Le réglage Settings → General → Pull Requests peut ne laisser que le squash.
- À revoir si l'équipe a besoin de garder le détail des commits sur `main` (par exemple pour retrouver avec `git bisect` le commit précis d'une régression), ou si les PR deviennent trop grosses pour tenir en un seul commit lisible.
