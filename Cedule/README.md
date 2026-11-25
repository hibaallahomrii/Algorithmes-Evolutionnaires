# Planification de la maintenance des centrales

Il faut planifier la maintenance de **7 centrales électriques** sur une année
découpée en **4 intervalles**. Chaque centrale doit rester en maintenance pendant
un certain nombre d'intervalles **consécutifs**, et sa puissance n'est plus
disponible pendant ce temps. L'objectif est de **maximiser la réserve de puissance**
sur l'ensemble de l'année.

```
Réserve (intervalle) = Capacité totale - Puissance en maintenance - Demande
```

La demande doit toujours être satisfaite : si la réserve d'un intervalle est
négative, la cédule est invalide.

## Données

| Unité | Capacité (MW) | Nombre d'intervalles de maintenance |
|---|---|---|
| 1 | 30 | 2 |
| 2 | 20 | 2 |
| 3 | 25 | 1 |
| 4 | 40 | 1 |
| 5 | 15 | 1 |
| 6 | 10 | 1 |
| 7 | 20 | 1 |

Capacité totale : **160 MW**.

| Intervalle | 1 | 2 | 3 | 4 |
|---|---|---|---|---|
| **Demande (MW)** | 85 | 90 | 70 | 75 |

## Codage d'une solution

Une solution est une liste de 7 gènes de 4 bits, soit un gène par unité.
Un bit à `1` signifie que l'unité est en maintenance pendant cet intervalle.
Les gènes autorisés sont :

| Unité | Maintenances possibles |
|---|---|
| 1 et 2 | `1100`, `0110`, `0011` |
| 3 à 7 | `1000`, `0100`, `0010`, `0001` |

Exemple : `["0110", "0011", "0001", "1000", "0100", "0010", "1000"]`
donne les réserves `[15, 25, 30, 40]`.

## Fonction objectif

La **performance** d'une solution est la **plus petite réserve** des 4 intervalles
(elle vaut 0 si une réserve est négative). On cherche à la maximiser.
L'exemple ci-dessus a donc une performance de 15.

## Fichiers

| Fichier | Algorithme | Principe |
|---|---|---|
| [`genetique.py`](./genetique.py) | Algorithme génétique | **Sélection par roulette** (probabilité proportionnelle à la performance), croisement en un point sur les blocs de 4 bits, mutation d'un gène vers un autre gène autorisé, la meilleure cédule est conservée à chaque génération |
| [`recuit_simule.py`](./recuit_simule.py) | Recuit simulé | Un voisin est obtenu en changeant la période de maintenance d'une unité, une solution moins bonne est acceptée avec la probabilité `exp(Δ/T)`, refroidissement géométrique |
| [`recherche_tabou.py`](./recherche_tabou.py) | Recherche tabou | Un voisin est obtenu en changeant la période d'une unité, les voisins tabous ou invalides sont écartés, on choisit le meilleur voisin, la liste tabou a une taille fixe (`deque`) |

## Lancer les programmes

```bash
python genetique.py
python recuit_simule.py
python recherche_tabou.py
```

## Résultats obtenus

| Algorithme | Solution | Réserves | Performance |
|---|---|---|---|
| Génétique | `['0011', '0110', '0001', '1000', '0010', '1000', '0100']` | [25, 30, 25, 30] | 25 |
| Recuit simulé | `['0011', '0011', '1000', '0100', '0010', '0001', '1000']` | [30, 30, 25, 25] | 25 |
| Recherche tabou | `['0011', '0011', '0100', '1000', '0010', '0001', '0100']` | [35, 25, 25, 25] | 25 |

La performance **25** est l'optimum : on l'a vérifié en testant les 9216 cédules
possibles, dont 3383 sont valides. 14 cédules différentes atteignent cette valeur,
ce qui explique que les trois solutions ne soient pas identiques.

## Paramètres utilisés

| Algorithme | Paramètres |
|---|---|
| Génétique | population = 100, générations = 100, taux de mutation = 0.3, point de croisement tiré au hasard à chaque génération |
| Recuit simulé | T initiale = 20, T finale = 0.01, alpha = 0.95, 50 voisins par palier |
| Recherche tabou | 200 itérations, liste tabou = 10, 7 voisins par itération |

Le recuit simulé et la recherche tabou démarrent de la cédule de l'exemple
(performance 15), alors que le génétique démarre d'une population aléatoire.
`random.seed(42)` fixe le hasard afin que les résultats soient reproductibles.
