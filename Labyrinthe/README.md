# Algorithme génétique : trouver la sortie d'un labyrinthe

## Présentation

Ce projet utilise un **algorithme génétique** pour trouver le chemin d'un robot qui va de l'**entrée** (case (1,1)) à la **sortie** (case (10,10)) d'un labyrinthe de 10 x 10 cases.

## Le labyrinthe

`1` = mur (case tramée), `0` = case libre (case blanche).

```
0 0 0 1 0 0 0 0 0 0
1 1 0 1 0 1 1 1 1 0
0 0 0 0 0 0 0 0 1 0
0 1 1 1 1 1 1 0 1 0
0 0 0 0 0 0 1 0 0 0
1 1 1 1 1 0 1 1 1 0
0 0 0 0 0 0 0 0 1 0
0 1 1 1 1 1 1 0 1 0
0 0 0 0 0 0 1 0 0 0
1 1 1 1 1 0 0 0 1 0
```

## Représentation d'une solution

- Un **individu** (chromosome) est une liste de 40 déplacements : `0` = haut, `1` = bas, `2` = gauche, `3` = droite.
- Une **génération** est un ensemble de 70 individus tous différents.
- Le robot s'arrête dans trois cas : il heurte un mur (ou le bord), il revient sur une case déjà visitée, ou il atteint la sortie.

## Paramètres

| Paramètre | Valeur |
|---|---|
| `nombre_deplacements` | 40 |
| `nombre_individu` | 70 |
| `max_generation` | 300 |
| `probabilite_croisement` | 0.9 |
| `probabilite_mutation` | 0.3 |

## Description des fonctions

- `parcours(individu)` : fait avancer le robot et renvoie le chemin parcouru.
- `calculer_distances()` : parcours en largeur à partir de la sortie ; il donne, pour chaque case libre, le nombre de déplacements restants (la distance de Manhattan piégerait le robot dans les impasses).
- `distance_sortie(individu)` : fitness = distance restante entre la dernière case atteinte et la sortie (`0` = sortie atteinte). On cherche le **minimum**.
- `creation_individu()` / `createGeneration()` : construisent la population initiale (individus tous différents).
- `evaluation(generation)` : renvoie le meilleur score et la position du meilleur individu.
- `afficheGeneration(generation, nombreGeneration)` : affiche le meilleur individu de la génération.
- `selection(generation)` : choisit les parents.
- `croisement(init, individu1, individu2)` : produit 2 enfants.
- `mutation(individu)` : modifie un déplacement au hasard.
- `developpement_generation(parents)` : applique le croisement puis la mutation pour former la nouvelle génération.

## Méthodes disponibles

Une seule méthode est active pour chaque étape ; les autres restent **en commentaire** dans le code.

### Sélection

- **Roulette** (méthode utilisée) : la probabilité est proportionnelle à `1 / (distance + 1)` (plus la distance est petite, plus l'individu a de chances d'être choisi).
- Tournoi : on tire 3 individus et on garde celui qui a la plus petite distance.
- Rang : la probabilité est proportionnelle au rang (du pire au meilleur).
- Élitiste : seule la meilleure moitié de la population peut devenir parent.

### Croisement

- **Un point** (méthode utilisée) : début du parent 1 + fin du parent 2 (et inversement).
- Deux points : le morceau situé entre les 2 points est échangé.
- Uniforme : un masque de 0 et de 1 décide quel parent fournit chaque déplacement.

### Mutation

Un déplacement tiré au hasard est remplacé par un autre, avec une probabilité de 0.3.

## Déroulement de l'algorithme

1. Création de la génération initiale.
2. Sélection des parents (roulette).
3. Croisement puis mutation pour créer la nouvelle génération.
4. Élitisme : le meilleur individu trouvé depuis le début est remis dans la génération.
5. Arrêt dès que la sortie est atteinte (distance = 0) ou lorsque `max_generation` est dépassé.

## Lancer le programme

```bash
python genetique.py
```

## Exemple de résultat

```
le meilleur score est : 0
le chemin (ligne, colonne) est : [(1, 1), (1, 2), (1, 3), (2, 3), (3, 3), (3, 4), (3, 5), (3, 6), (3, 7), (3, 8), (4, 8), (5, 8), (5, 9), (5, 10), (6, 10), (7, 10), (8, 10), (9, 10), (10, 10)]
la sortie est atteinte en 18 deplacements
```

Le résultat change d'une exécution à l'autre (nombre de générations, chemin trouvé).
