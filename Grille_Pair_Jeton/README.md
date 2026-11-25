# Algorithme génétique : placement de jetons sur une grille

## Présentation

Ce projet met en œuvre un **algorithme génétique** qui place **10 jetons** sur une grille de **5 x 5** cases, de sorte que **chaque ligne et chaque colonne contienne un nombre pair de jetons**.

## Énoncé du problème

- La grille a `n x n` cases avec `n = 5`.
- Il faut placer `k = 10` jetons (au plus un jeton par case).
- Contrainte : le nombre de jetons de chaque ligne et de chaque colonne doit être **pair** (0, 2 ou 4).
- Taille de l'espace de recherche : C(25, 10) = 3 268 760 possibilités.

## Représentation d'une solution

- Un **individu** (chromosome) est une liste de 25 bits : `1` = jeton, `0` = case vide. La case (ligne, colonne) se trouve à la position `ligne * n + colonne`.
- Chaque individu contient exactement `k` jetons.
- Une **génération** est un ensemble de 70 individus tous différents.

## Paramètres

| Paramètre | Valeur |
|---|---|
| `n` | 5 |
| `k` | 10 |
| `nombre_cases` | 25 |
| `nombre_individu` | 70 |
| `max_generation` | 300 |
| `probabilite_croisement` | 0.9 |
| `probabilite_mutation` | 0.3 |

## Description des fonctions

- `jetons_ligne(individu, i)` / `jetons_colonne(individu, j)` : calculent le nombre de jetons d'une ligne / d'une colonne.
- `lignes_colonnes_impaires(individu)` : fitness = nombre de lignes et de colonnes qui contiennent un nombre **impair** de jetons (`0` = solution faisable). On cherche le **minimum**.
- `reparer(individu)` : après un croisement, retire des jetons au hasard (s'il y en a trop) ou en ajoute au hasard (s'il en manque) afin de revenir à `k` jetons.
- `creation_individu()` / `createGeneration()` : construisent la population initiale (individus tous différents).
- `evaluation(generation)` : renvoie le meilleur score et la position du meilleur individu.
- `afficheGeneration(generation, nombreGeneration)` : affiche le meilleur individu de la génération.
- `afficheGrille(individu)` : affiche la grille (`X` = jeton, `.` = case vide) avec le nombre de jetons par ligne et par colonne.
- `selection(generation)` : choisit les parents.
- `croisement(init, individu1, individu2)` : produit 2 enfants (réparés).
- `mutation(individu)` : déplace un jeton.
- `developpement_generation(parents)` : applique le croisement puis la mutation pour former la nouvelle génération.

## Méthodes disponibles

Une seule méthode est active pour chaque étape ; les autres restent **en commentaire** dans le code.

### Sélection

- **Roulette** (méthode utilisée) : la probabilité est proportionnelle à `1 / (score + 1)` (plus le score est petit, plus l'individu a de chances d'être choisi).
- Tournoi : on tire 3 individus et on garde celui qui a le plus petit score.
- Rang : la probabilité est proportionnelle au rang (du pire au meilleur).
- Élitiste : seule la meilleure moitié de la population peut devenir parent.

### Croisement

- **Un point** (méthode utilisée) : début du parent 1 + fin du parent 2 (et inversement).
- Deux points : le morceau situé entre les 2 points est échangé.
- Uniforme : un masque de 0 et de 1 décide quel parent fournit chaque case.

### Mutation

Un jeton est déplacé : une case avec jeton devient vide et une case vide reçoit le jeton (le nombre de jetons reste donc `k`). La mutation a lieu avec une probabilité de 0.3.

## Déroulement de l'algorithme

1. Création de la génération initiale.
2. Sélection des parents (roulette).
3. Croisement (avec réparation) puis mutation pour créer la nouvelle génération.
4. Élitisme : le meilleur individu trouvé depuis le début est remis dans la génération.
5. Arrêt dès qu'une solution faisable est trouvée (score = 0) ou lorsque `max_generation` est dépassé.

## Lancer le programme

```bash
python genetique.py
```

## Exemple de résultat

```
taille de l'espace de recherche : C(25, 10 ) = 3268760
le meilleur score est : 0
nombre de generations : 0
X . . . X   jetons: 2
X X . . .   jetons: 2
. X . . X   jetons: 2
. . X . X   jetons: 2
. . X . X   jetons: 2
2 2 2 0 4   <- jetons par colonne
solution faisable : chaque ligne et chaque colonne a un nombre pair de jetons
```

Le résultat change d'une exécution à l'autre (nombre de générations, grille trouvée).
