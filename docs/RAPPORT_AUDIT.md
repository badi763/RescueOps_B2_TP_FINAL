# Rapport d'audit — RescueOps

Auteur : Rayane Badi
Pseudo GitHub : Badi763
Date : 09/11/2026
Commit de base : 5e99359aa7cb8ad6a79a67a02373300e8b5a6873

| ID | Règle RO | Fichier / fonction | Entrée, obtenu S0, attendu | Risque concret | Priorité P0/P1/P2 | Preuve du correctif (test / commit) |
|---|---|---|---|---|---|---|
| A-01 | RO-02 | `src/energy.js` / `estimateEnergy` | Distance 0 et charge 2 : S0 renvoie 11. Distance infinie : renvoie `Infinity`. Charge 5.1 kg et distance 5 km : renvoie 41. Attendu : `RangeError` pour ces valeurs. | Le calcul accepte des valeurs qui ne respectent pas les règles. | P1 | `tests/energy.test.js` vérifie les entrées interdites et les limites. `npm test` : 19 tests réussis. Commit énergie à faire. |
| A-02 | RO-04 | `src/dispatch.js` / `deployDrone` | Avec 35 points, S0 accepte le départ et laisse 4 points. Attendu : refus. Avec 51 points, il reste 20 points : le départ doit être accepté. | Le drone peut partir avec une réserve trop basse. | P0 | `tests/dispatch.test.js` vérifie le refus à 50 points et l'acceptation à 51. Commit affectation à faire. |
| A-03 | RO-03 | `src/dispatch.js` / `deployDrone` | S0 accepte un drone `busy` et une mission déjà `en_route`. Attendu : une erreur, sans modifier les objets. | Un drone ou une mission peut être affecté une deuxième fois. | P0 | `tests/dispatch.test.js` vérifie les deux refus et l'absence de modification. Commit affectation à faire. |
| A-04 | RO-06 | `src/mission.js` / `advanceMission` | S0 accepte `planned` vers `closed` et `en_route` vers `closed`. Attendu : avancer une étape à la fois. | Une mission peut être déclarée terminée trop tôt. | P1 | `tests/mission.test.js` vérifie les transitions interdites et le parcours normal. Commit cycle à faire. |
| A-05 | RO-07 | `src/mission.js` / `cancelMission` | S0 permet d'annuler une mission `en_route`. Attendu : seules les missions `planned` peuvent être annulées. | Le statut ne reflète plus le départ de la mission. | P1 | `tests/mission.test.js` vérifie le refus après le départ, la clôture et une première annulation. Commit cycle à faire. |
| A-06 | RO-08 | `src/public.js` / `getPublicStatus` | S0 renvoie `id`, `status`, `history`, `contactEmail`, `gps` et `priority`. `history` est le même tableau que dans la mission. Attendu : seulement `id`, `status` et une copie de `history`. | La sortie publique révèle des données internes et partage un tableau modifiable. | P0 | `tests/public.test.js` vérifie les clés exactes et la copie de l'historique. Commit confidentialité à faire. |
| A-07 | RO-09 | `src/php/MissionCard.php` / `publicCard` | S0 renvoie `id`, `statusLabel`, `contactEmail` et `gps`. Attendu : seulement `id` et `statusLabel`. | La carte publique révèle le contact et le GPS. | P0 | `tests/MissionCard.test.php` vérifie les clés exactes. Test PHP non exécuté : PHP indisponible dans l'environnement de contrôle. Commit confidentialité à faire. |

## D-01 — Dette technique reportable

Risque / raison du report / mesure compensatoire / responsable ou déclencheur de réévaluation :

Le dépôt ne dispose pas d'une exécution automatique des tests sur GitHub. Sa mise en place est reportée, car elle ne figure pas dans le périmètre du sujet. La mesure compensatoire consiste à exécuter `npm test`, `php tests/MissionCard.test.php`, `php -l src/php/MissionCard.php` et `git diff --check` avant chaque push. Responsable : Rayane Badi. Déclencheur de réévaluation : demande du formateur concernant l'automatisation de ces vérifications.

## Contrôles AVANT correction

Commandes, résultats réellement observés et limites :

npm test : 8 tests réussis sur la version S0. npm run demo : le départ avec 35 points a été accepté et il restait 4 points. La vue affichait l'email, le GPS et la priorité. Les cas A-01 à A-06 ont été rejoués. Pour A-07, les champs ont été vérifiés par lecture du code PHP. Les commandes PHP n'ont pas été exécutées, car PHP n'était pas installé dans l'environnement de contrôle.

## Contrôles APRÈS correction

Commandes, résultats réellement observés et limites :

npm test : 19 tests réussis. npm run demo : le départ avec 70 points est accepté et il reste 39 points. Le départ avec 35 points est refusé. La vue publique affiche seulement id, status et history. L'exécution de `php tests/MissionCard.test.php`, de `php -l src/php/MissionCard.php` et de `git diff --check` reste à effectuer dans le clone local du dépôt sur macOS.

## Conclusion d'audit

Les corrections JavaScript sont vérifiées. La validation PHP sur macOS, la publication des commits et l'ouverture de la pull request restent à effectuer. La fusion de la pull request est exclue du périmètre de cette livraison.
