# Utilisez les propriétés et méthodes statiques

Lors des précédents chapitres, nous avons étudiés les propriétés et méthodes qui
se rattachaient à un objet, comme la longueur ou la largeur d’un pont. Vous
allez maintenant découvrir **les méthodes et propriétés qui se rapportent à une
classe** (donc communes à tous nos objets). On les appelle **propriétés et méthodes
statiques**.

---

## Découvrez les méthodes statiques

Souvent, les classes ne se contentent pas seulement de manipuler leurs propriétés. Elles permettent aussi d'effectuer des calculs, des actions ou de retourner des informations. Et parfois, créer une instance n’est pas nécessaire.

Pour utiliser une méthode sans instance, elle doit être déclarée **statique** et nous ferons attention de **manipuler uniquement des propriétés statiques**.

Lorsqu’une propriété est déclarée statique, la valeur qu’elle contient sera partagée pour toutes les instances. En réalité, PHP possède une mémoire liée à la classe au lieu de l’instance. Ce que l’on va faire, c’est manipuler la valeur liée à la classe.

Déclarons une première méthode statique avec le mot clé `static` :

```php
<?php

declare(strict_types=1);

class Pont
{
   public static function validerTaille(float $taille): bool
   {
       if ($taille < 50.0) {
           trigger_error(
               'La longueur est trop courte. (min 50m)',
               E_USER_ERROR
           );
       }
      
       return true;
   }
}

var_dump(Pont::validerTaille(150.0));
var_dump(Pont::validerTaille(20.0));
```

Pourquoi on n’a pas utilisé une flèche pour accéder à `validerTaille()` ?

**Pont** fait référence à la classe or, rappelez-vous, `->` permet d'accéder aux
éléments d'un objet, c'est-à-dire d'une instance.

Pour dire à PHP que nous souhaitons faire référence à un élément de la classe,
il faut utiliser `::`  à la place, comme pour les constantes. Sans ceci, il va
chercher à utiliser la méthode d'un objet contenu dans une constante nommée
**Pont**. Ce qui est faux dans notre cas.

Comme vous pouvez le voir sur cet exemple, les méthodes statiques sont
particulièrement utiles pour réaliser des vérifications ou des calculs sans
effet sur l’objet. Et tout comme une méthode classique, vous pouvez aussi y
faire appel depuis votre objet. Mais il y a une subtilité, vous ne pourrez pas
l'appeler avec le mot clé `$this`  pour les mêmes raisons que pour la flèche.

### Utilisez le mot clé self

Vous devez utiliser le mot clé `self` pour cibler une méthode statique de classe,
lorsque vous l'appelez depuis une instance de cette même classe. Remplaçons
notre vérification de tout à l'heure :

```php
<?php

declare(strict_types=1);

class Pont
{
   public static function validerTaille(float $taille): bool
   {
       if ($taille < 50.0) {
           trigger_error(
               'La taille est trop courte. (min 50m)',
               E_USER_ERROR
           );
       }
      
       return true;
   }
  
   public function setLongueur(float $longueur): void
   {
       self::validerTaille($longueur);
      
       $this->longueur = $longueur;
   }
}

$towerBridge = new Pont;
$towerBridge->setLongueur(20.0);

// echo $towerBridge->longueur;
```

Avez-vous remarqué l’usage de la méthode `validerTaille` ? C’est exact, l’appel de
la méthode est  `self::validerTaille($longueur);`. Dès que vous voyez ce préfixe,
c’est un excellent moyen de savoir qu’il s’agit d’une méthode statique.

### Découvrez un des usages communs

Voyons un autre cas d'usage très courant avec le mot clé `static`.

Imaginons que l’on souhaite compter le nombre de piétons ayant franchi un pont, peu importe le pont, **sur toutes les instances confondues**. On pourrait s'amuser à additionner la valeur d'une propriété de chacune des instances, mais il y a plus malin !

```php
<?php

declare(strict_types=1);

class Pont
{
   // Définition de la propriété statique. Elle sera partagée
   public static int $nombrePietons = 0;
  
   // Je laisse volontairement la méthode non statique.
   // Seule la référence à la propriété est importante.
   public function nouveauPieton()
   {
       // Mise à jour de la propriété statique.
       self::$nombrePietons++;
   }
}

$pontLondres = new Pont;
$pontLondres->nouveauPieton();

$pontManhattan = new Pont;
$pontManhattan->nouveauPieton();

echo Pont::$nombrePietons;
```

En définissant une propriété statique, vous allez pouvoir manipuler une même
donnée à travers toutes les instances. Avez-vous remarqué les valeurs du nombre
de piétons ?

---

## Utilisez des valeurs immuables

Parfois, vous pouvez avoir besoin d’une propriété statique immuable, c’est-à-dire qui ne changera jamais. Vous vous souvenez de notre propriété `$unite` ? Nous pouvons remarquer deux choses :

- La propriété devrait être statique, car l’unité de surface s'exprime toujours en m² quelque soit le pont.

- Cette propriété ne sera jamais modifiée au cours de l’exécution du programme.

Nous pouvons alors passer cette propriété en constante avec le mot-clé `const` :

```php
<?php

declare(strict_types=1);

class Pont
{
   private const UNITE = "m²";
   private float $longueur;
   private float $largeur;

   public const MESSAGE = "La static c'est cool !";
  
   public function getSurface(): string
   {
       return ($this->longueur * $this->largeur) . self::UNITE;
   }
  // setLongueur et setLargeur ne changent pas
  public function setLongueur(float $longueur): void
   {
       if ($longueur < 0) {
           trigger_error(
               'La longueur est trop courte. (min 1)',
               E_USER_ERROR
           );
       }
      
       $this->longueur = $longueur;
   }

   public function getLongueur(): float {
        return $this->longueur;
    }
   
   public function setLargeur(float $largeur): void
   {
       if ($largeur < 0) {
           trigger_error(
               'La largeur est trop courte. (min 1)',
               E_USER_ERROR
           );
       }
      
       $this->largeur = $largeur;
   }

   public function getLargeur(): float {
        return $this->largeur;
    }
}

$towerBridge = new Pont;
$towerBridge->setLongueur(286.0);
$towerBridge->setLargeur(15.0);

echo $towerBridge->getLongueur().PHP_EOL;
echo $towerBridge->getLargeur().PHP_EOL;
echo $towerBridge->getSurface().PHP_EOL;
echo Pont::MESSAGE;
```

Que peut-on constater ?

- Une constante de classe s’écrit avec le mot-clé `const` est une valeur lui est donnée directement

- Nous avons renommé `$unite` en `UNITE` : les constantes ne sont pas préfixées par `$` et par convention sont écrites en **UPPER_SNAKE_CASE** (tout en majuscule, avec les mots séparés par des underscores)

- Une constante est statique, on utilise le mot-clé `self` pour l’utiliser dans  `getSurface`.

Avec l'expérience, on finit par considérer **toute valeur écrite en dur** (comme l’unité ici), comme de la configuration générale de la classe, et donc comme constante. Cela permet de concentrer **toutes les informations à un même endroit**, au début de la classe. Nous savons où retrouver ces informations et nous évitons alors de les dupliquer.

Par exemple, le coefficient multiplicateur de l'accélération d’un véhicule dans un jeu ? Hop, une constante. La vitesse de la lumière pour calculer des distances ? Hop, là aussi une constante !

> Seules les expressions de type scalaire (bool, int, float et string) et des tableaux contenant uniquement des expressions scalaires sont acceptées pour les constantes.

---

## En résumé

- En utilisant la staticité vous pouvez partager des valeurs, ou appeler des méthodes “utilitaires”, n’ayant pas d’impact sur l’état de l’objet.

- Il est possible et recommandé de créer des constantes pour les informations qui ne changent pas, grâce au mot-clé `const`.

- Pour appeler une propriété ou méthode statique au sein d’une classe, il faut
  utiliser le mot clé `self`.
