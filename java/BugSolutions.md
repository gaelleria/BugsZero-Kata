# Bugs et remarques avec solutions

Je me suis inspirée des bugs mentionnés dans le lien fourni dans le readme section **Instructions and hints** 
pour m'orienter sur certains bugs.

## 1 - Au moins 2 jouers
> A Game could have less than two players - How could we make sure it always has at least two. Is this a runtime check or 
can this be enforced "statically"? ** What can be done in a language like javascript?

Solution 1: _appeler la méthode isPlayable() dans la methode roll(int roll) comme première instruction.
Si on a moins de 2 joueurs, alors on lève une exception_

```java
// file Game.java
public class Game {
    // ...
    public void roll(int roll) {
        if (!isPlayable()) {
            throw new RuntimeException("We need at least 2 players");
        }
        // ...
    }
}
```

Optionel: Actuellement la classe GameRunner lance toujours le jeu avec 3 joueurs. On pourrait récupérer les noms des jouers
depuis la ligne de commandes a l'execution et vérifier qu'on a bien au moins 2 paramètres fournie.

```java
// file GameRunner.java
public class GameRunner {
    // ...
    public static void main(String... args) {
        if (args.length < 2){
            System.out.println("We need at least 2 player's names");
            return;
        }
        Random rand = new Random();
        playGame(rand);
    }
}
```

## 2 - Jeu avec 7 joueurs ou plus

> Someone could try to create a game with 7 players, make sure that's impossible. ** or slightly 
> easier, allow for 7 players or more

**Solution 1** : Autoriser 6 joueurs au maximum en mettant à jour la méthode isPlayable() comme suit. 
Il faudra bien évidement avoir implémenté en plus la solution du bug 1.

```java
// file Game.java
public class Game {
    // ...
    public boolean isPlayable() {
        int numberOfPlayers = howManyPlayers();
        return numberOfPlayers >= 2 && numberOfPlayers < 7;
    }
}
```

**Solution 2**: Pour autoriser plus de 6 joueurs, une solution plus complexe serait d'utiliser la classe java.util.List 
au lieu des tableaux comme type pour les 3 attributs places, purses and inPenaltyBox.


## 3 - penalty box

> A player that gets into prison always stays there.

Si un joueur rate une question, il va en prison en positionnant son top inPenaltyBox à true. 
A aucun endroit dans le code il n'est evoqué la remise à false de ce top. Donc il faut implémenter le faire idéalement 
juste après la ligne 114 de la classe Game avant d'incrémenter currentPlayer.

``inPenaltyBox[currentPlayer] = true;``

## 4 - La logique générale du jeu

### la gestion des joueurs

Dans ma solution, il faudrait créer une classe java GamePlayer pour stocker toutes les infos du joueur et le faire évoluer : 
- son ordre
- son top s'il est en prison
- sa bourse
- sa place

Cette classe fournirait des methodes utiles pour le faire avancer et alimenter sa bourse, voir le mettre en prison, etc...

### la classe Game et GameRunner

Si l'idée est de toujours passer par le GameRunner pour lancer le jeu, alors il faudrait pouvoir recuperer le nom des 
jours de manière dynamique et faire des précontrôles nécessaires avant de lancer le jeu.  

Sinon l'ideale serait de déplacer la méthode ``playGame()`` de la classe GameRunner dans la classe Game car elle contient
une partie de la logique du jeu.


## 5 - Ajout des coins au mauvais joueur

> coins are added to the wrong player. Try to understand what made this bug likely and fix the design so that it becomes very unlikely.
 
Solution 1: Ce bug pourrait être évité facilement en mettant en place la solution mentionnée plus haut, point 4

Solution 2: incrementer la bourse ``purses[currentPlayer]++; (ligne 118)`` avant d'incrémenter le currentPlayer 
``currentPlayer++; (ligne 116)``
