# Créez vos propres classes

## Découvrez la syntaxe des classes en PHP

```php
<?php

class NomDeLaClasse
{
}
```

Le mot clé class suivi d'un nom en PascalCase, un retour à la ligne, suivi d'une
paire d'accolades. Le code de notre classe se trouvera entre les accolades.

> Prenez soin de nommer votre classe de manière pertinente. Cela vous paraît anodin, mais il est important, pour s’y retrouver, de comprendre le rôle de la classe en lisant son nom. C’est d’autant plus vrai lorsque le code de votre application devient complexe..

### Pour instancier une classe, rien ne change

```php
<?php

class Pont
{
}

$pont = new Pont;
```

---

## Ajoutez des propriétés et y accéder

```php
<?php

declare(strict_types=1);

class Pont
{
   public float $longueur = 0;
}

$pont = new Pont;
$pont->longueur = 263.0;

var_dump($pont);
```

En ligne 3 se trouve une instruction demandant à PHP d'être exigeant avec le
typage. Le mot clédeclare permet d'indiquer à PHP un comportement spécifique
pour le fichier dans lequel nous nous trouvons. Si vous êtes curieux, d'autres instructions existent dans [la documentation
PHP](https://www.php.net/manual/fr/control-structures.declare.php).

### En ligne 7 se trouve la déclaration de la propriété. Elle se compose

- du mot clépublic, afin de définir l'accessibilité de la propriété, nous y reviendrons;

- du type de la propriété, ici float (ce n’est pas obligatoire mais c’est une très bonne pratique);

- et enfin, du nom de la propriété préfixé du symbole$. Ce symbole est présent pour la même raison que la déclaration de vos variables : pour distinguer la propriété de la constante.

---

## Ajoutez des méthodes et les utiliser

Se contenter de propriétés, ce serait dommage. Une classe peut **définir des fonctions** qu'elle **seule** sera en capacité d'exécuter. Sans les classes, vous vous retrouverez très probablement avec des fichiers très imposants dans lesquels vous devez fouiller pour retrouver les fonctions nécessaires.

Même en vous organisant en plusieurs fichiers, il y a une limite pour laquelle
il devient difficile pour nos cerveaux de suivre. Un bon moyen c'est de
regrouper les fonctionnalités de manière logique, et **les classes sont
parfaites pour ce rôle** !

Reprenons l’exemple du pont de notre précédente section. Nous souhaitons pouvoir
calculer la surface du pont. Pour cela nous avons besoin de connaître, en plus
de la longueur, la largeur du pont. Ajoutons une nouvelle propriété `largeur` à notre pont et **calculons la surface** :

```php
<?php

declare(strict_types=1);

class Pont
{
   public float $longueur;
   public float $largeur;
}

$pont = new Pont;
$pont->longueur = 286.0;
$pont->largeur = 15.0;

$surface = $pont->longueur * $pont->largeur;

var_dump($surface);
```

Pour éviter d’avoir à faire le calcul de la surface plusieurs fois, il peut être
intéressant de créer une fonction dédiée à cette tâche.  Cette fonction est
rattachée à notre classe Pont, on appelle donc cela une méthode :

```php
<?php

declare(strict_types=1);

class Pont
{
    public float $longueur;
    public float $largeur;
    
    public function getSurface(): float
    {
        return $this->longueur * $this->largeur;
    }
}

$pont = new Pont;
$pont->longueur = 286.0;
$pont->largeur = 15.0;

$surface = $pont->getSurface();

var_dump($surface);
```

### Que constate-t-on ?

- La méthodegetSurface() est déclarée en commençant par sa visibilité "public". Nous y reviendrons au prochain chapitre.

- Puis le mot cléfunction suivi d'un nom. Ce nom doit suivre cette fois-ci la nomenclature camelCase. C'est-à-dire que le premier mot est en minuscule, mais chaque nouveau mot commence par une majuscule.

### L'écriture du code PHP est assez bien normée.

Vous pouvez retrouver l'ensemble des règles en suivant les PHP Standard Recommendations. Entre autres les [PSR-1](https://www.php-fig.org/psr/psr-1/) et [PSR-12](https://www.php-fig.org/psr/psr-12/).

- Ensuite une paire de parenthèses qui nous permettront par la suite d'y
  réclamer des arguments, de la même manière qu'une fonction.

- Juste après les parenthèses se trouve la déclaration du typefloat du résultat de la méthode. C’est optionnel mais c’est une excellente pratique.

- Enfin entre accolades, les instructions de la méthode.

### Appelons maintenant cette méthode pour obtenir la surface

```php
// …

$pont = new Pont;
$pont->longueur = 286.0;
$pont->largeur = 15.0;

$surface = $pont->getSurface();

var_dump($surface);
```

### Cela commence de la même manière que l'accès à une propriété

- le nom de la variable contenant l'instance,

- suivi d'une flèche-> ainsi que le nom de la méthode,

- suffixé d'une paire de parenthèses vide.

C'est cette paire de parenthèses qui indique à PHP qu'il ne doit pas tenter de
renvoyer la valeur d'une propriété, mais le résultat de l'exécution d'une
méthode. Vous pouvez tout à fait avoir une méthode et une propriété du même nom,
même si ce n’est pas recommandé.

---

## Manipulez vos propriétés et vos méthodes depuis votre objet

Il est maintenant temps de vous expliquer les secrets de $this, que vous avez
remarqué dans le code précédent. Regardons tout d’abord de plus près cette
formule :

```php
$surface = $pont->longueur * $pont->largeur;
```

$pont fait référence à l'instance de notre classe. Lorsque vous déclarez une
méthode dans votre classe, vous ne pouvez pas avoir connaissance des instances
futures.

Dans notre exemple, peu importe le pont créé, getSurface() doit renvoyer la
surface du pont en question, à partir de sa propre largeur et de sa propre
longueur. Nous n’aurons pas les mêmes tailles pour tous les ponts. Pour cela, on
utilise le mot clé$this:

```php
<?php

declare(strict_types=1);

class Pont
{
   public float $longueur;
   public float $largeur;
  
   public function getSurface(): float
   {
       return $this->longueur * $this->largeur;
   }
}

$towerBridge = new Pont;
$towerBridge->longueur = 286.0;
$towerBridge->largeur = 15.0;

$manhattanBridge = new Pont;
$manhattanBridge->longueur = 2089.0;
$manhattanBridge->largeur = 36.6;

$towerBridgeSurface = $towerBridge->getSurface();
$manhattanBridgeSurface = $manhattanBridge->getSurface();

var_dump($towerBridgeSurface);
var_dump($manhattanBridgeSurface);
```

Dans cet exemple, nous avons 2 instances de pont, chacune avec ses dimensions. L'usage de $this nous permet de faire références aux valeurs portées par chacune des instances.

> Dans les 2 cas le code passera par $this->longueur. Mais ces $this n’auront pas la même signification en fonction de l’objet à l’origine de l’appel. Dans le premier cas, il signifiera “donne-moi la longueur associée au manhattanBridge”, et dans le seconde, “donne-moi la longueur associée au  towerBridge”.

---

## ‌En résumé

- Les objets possèdent des propriétés et des méthodes

- Pour désigner une instance, nous utilisons le mot clé$this, permettant de faire appel aux propriétés ou méthodes de notre objet au sein d’une autre méthode.
