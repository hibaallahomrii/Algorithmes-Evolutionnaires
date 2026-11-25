# Problème du sac à dos (Knapsack 0/1)

Il y a **9 objets**, chacun avec un poids et une valeur. Le sac peut porter au
maximum **18 kg**. Un objet est pris en entier ou laissé. L'objectif est de choisir
les objets qui donnent la **valeur totale la plus grande** sans dépasser la capacité.

Le problème est résolu avec trois métaheuristiques, sur les mêmes données.

## Données du problème

| Objet | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
|---|---|---|---|---|---|---|---|---|---|
| **Poids** | 4 | 3 | 7 | 2 | 5 | 6 | 3 | 5 | 1 |
| **Valeur** | 14 | 9 | 20 | 6 | 11 | 16 | 8 | 13 | 5 |

Capacité du sac : **18**.

## Représentation d'une solution

Une solution est une liste de 0 et de 1, avec un élément par objet, par exemple
`[1, 1, 1, 0, 0, 0, 1, 0, 1]` (1 = objet dans le sac, 0 = objet laissé).
Une solution qui dépasse la capacité est invalide.

## Contenu du projet

| Fichier | Algorithme | Principe |
|---|---|---|
| [`genetique.py`](./genetique.py) | Algorithme génétique | Population de listes 0/1, sélection par tournoi, croisement à un point, mutation par inversion de bits, élitisme (fitness = 0 si le sac est trop lourd) |
| [`recuit_simule.py`](./recuit_simule.py) | Recuit simulé | Voisin obtenu en inversant un objet, une moins bonne solution est acceptée avec la probabilité `exp(Δ/T)`, refroidissement géométrique |
| [`recherche_tabou.py`](./recherche_tabou.py) | Recherche tabou | Voisins obtenus en inversant un objet, les voisins tabous et trop lourds sont écartés, le meilleur voisin est choisi, liste tabou de taille fixe (`deque`) |

## Lancer le projet

```bash
python genetique.py
python recuit_simule.py
python recherche_tabou.py
```

## Résultats obtenus

| Algorithme | Solution | Poids | Valeur |
|---|---|---|---|
| Génétique | `[1, 1, 1, 0, 0, 0, 1, 0, 1]` | 18 | 56 |
| Recuit simulé | `[1, 1, 1, 0, 0, 0, 1, 0, 1]` | 18 | 56 |
| Recherche tabou | `[1, 1, 1, 0, 0, 0, 1, 0, 1]` | 18 | 56 |

Les objets choisis sont 0, 1, 2, 6 et 8. La valeur **56** est l'optimum : elle a été
vérifiée en testant les 512 combinaisons possibles.

## Réglages des algorithmes

| Algorithme | Paramètres |
|---|---|
| Génétique | population = 50, générations = 100, taux de mutation = 0.1 |
| Recuit simulé | T initiale = 50, T finale = 0.01, alpha = 0.95, 50 voisins par palier, solution de départ `[1, 0, 0, 1, 0, 0, 1, 0, 0]` |
| Recherche tabou | 100 itérations, liste tabou = 10, 9 voisins par itération, solution de départ `[1, 0, 0, 1, 0, 0, 1, 0, 0]` |

L'instruction `random.seed(42)` fixe le hasard afin que les résultats soient
reproductibles.
