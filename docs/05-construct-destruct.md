# Exploitez les méthodes communes aux objets

---

## Exploitez les méthodes communes à tous les objets

L'usage du mot clé  `class`  vous donne accès, dès le départ, à un jeu de méthodes sans que vous n'ayez rien à écrire. Ces méthodes sont particulières, accrochez-vous pour la phrase suivante ! **Vous n'avez pas besoin de les écrire pour les utiliser, et si vous les écrivez vous ne les appellerez probablement jamais.** Autrement dit, ces méthodes sont prédéfinies par PHP, et sont appelées automatiquement dans la plupart des cas. 

Ce jeu de méthodes est un moyen d'intervenir dans le processus de création, modification et suppression des objets de PHP, afin d'**y ajouter des comportements personnalisés**.

Elles sont reconnaissables parce qu'elles sont préfixées par deux
**underscores**. Il en existe plusieurs, que vous pouvez regarder dans [la
documentation PHP](https://www.php.net/manual/fr/language.oop5.magic.php). Ici, nous nous intéressons tout d'abord à deux d’entre elles
:  `__construct`  et `__destruct`.

La première est une méthode appelée automatiquement par PHP lorsque vous créez
une instance à l'aide du mot clé `new`. On appelle la méthode  `__construct`  un
constructeur. C'est une mécanique que l'on retrouve dans tous les langages
objets. Dans certains, la méthode porte le nom de la classe. La majeure partie
du temps, le constructeur nous sert à **initialiser des données de départ pour
notre objet**.

La seconde est un destructeur. La méthode est appelée automatiquement lorsque l’objet est supprimé de la mémoire, ce qui est fait à chaque fois que le script se termine. Il existe deux autres moyens de le déclencher manuellement : en supprimant l’objet avec `unset`  ou en remplaçant le contenu de la variable qui y fait référence. Pour faire simple, dès que PHP détecte que plus rien ne fait référence à un objet en mémoire, il le détruit, et donc déclenche  `__destruct`.

Reprenons notre pont avec deux mutateurs pour définir les valeurs des longueur
et largeur :

```php
<?php
 
declare(strict_types=1);
 
class Pont
{
    private float $longueur;
    private float $largeur;
 
    public function setLongueur(float $longueur): void
    {
        $this->longueur = $longueur;
    }
 
    public function setLargeur(float $largeur): void
    {
        $this->largeur = $largeur;
    }
}
 
$towerBridge = new Pont;
$towerBridge->setLongueur(286.0);
$towerBridge->setLargeur(62.2);
```

Et remplaçons à présent les mutateurs par l'usage du constructeur.

> Attention, le constructeur ne rend pas obsolètes les mutateurs pour autant. Ils se complètent.

```php
<?php
 
declare(strict_types=1);
 
class Pont
{
    private float $longueur;
    private float $largeur;
 
    public function __construct(float $longueur, float $largeur)
    {
        $this->longueur = $longueur;
        $this->largeur = $largeur;
    }
}
 
$towerBridge = new Pont(286.0, 62.0);
```

Nous avons assigné les valeurs de longueur et de largeur **dès la création de l'instance** de la classe. Pour donner les arguments au constructeur, nous avons ajouté une paire de parenthèses derrière le nom de la classe, comme s'il s'agissait d'une méthode, à la ligne 17. Ce qui est le cas en quelque sorte, puisque nous faisons appel à la méthode  `__construct`  !

Je vous ai montré ce que vous êtes susceptible de rencontrer le plus souvent.
Mais depuis PHP8, vous pouvez aussi le rencontrer sous sa forme courte. En
précisant la visibilité directement au niveau des arguments du constructeur,
vous pouvez **ne pas déclarer les propriétés et leur assignation**. Ce sera fait
automatiquement par PHP :

```php
<?php
 
declare(strict_types=1);
 
class Pont
{
    public function __construct(private float $longueur, private float $largeur)
    {
    }
}
 
$towerBridge = new Pont(286.0, 62.0);
var_dump($towerBridge);
```

Pour ne pas rendre ce cours trop long, je n’aborderai pas les autres méthodes avec vous. Je vous invite donc à aller les découvrir sur [la documentation PHP](https://www.php.net/manual/fr/language.oop5.magic.php). Ne faites pas l'erreur de juste lire la documentation ; à la fin de ce chapitre, **mettez ce cours en pause, testez-les, expérimentez, trouvez les limites, essayez de faire des choses bizarres** pour vous imprégner de leur mécanisme. Beaucoup de choses peuvent vous paraître magiques si vous ne les connaissez pas :).

---

## Tirez profit des appels automatisés avec PHP

Au risque de me répéter, ce jeu de méthodes est un moyen d'**intervenir dans le processus de création, modification et suppression (entre autres) des objets** de PHP, afin d'y ajouter des **comportements personnalisés**.

L'existence de ces méthodes est surveillée par PHP, et dès qu'il les détecte
dans votre classe, il les utilise aux différents moments de leur vie. PHP y fait
appel et les rend accessibles dès que vous les écrivez. Si vous essayez de les
appeler sans les déclarer, vous obtenez une erreur :

```php
<?php
 
class Pont
{
}
 
$towerBridge = new Pont;
$towerBridge->__construct(12.2, 43.1);
```

C’est parce que PHP les appelle dès que possible sans vous le demander que vous
rencontrerez souvent le terme méthode magique. Ne pas les connaître donne une
impression de magie, de boîte noire. Démystifions ces méthodes ensemble dans les
screencast ci-dessous. Commençons par  `__clone`  et  `__toString`  :

Depuis PHP 8.0, vous pouvez utiliser la `name: $valueSyntaxe` pour passer des arguments nommés dans n'importe quel ordre, mais **vous devez suivre certaines règles** :

- Les noms des arguments doivent correspondre à la définition de la fonction
- Si vous mélangez des arguments nommés avec des arguments positionnels *(arguments normaux sans aucun name:devant eux)*, **les arguments positionnels doivent venir en premier**.

### Et si vous rajoutiez un peu de magie à votre code ?

```php
<?php

declare(strict_types=1);

class Tablier
{
    public function __construct(public float $longueur, public float $largeur)
    {
    }
}

class Pont
{
    public function __construct(protected Tablier $tablier, protected string $name)
    {
    }
    
    public function __clone()
    {
        $this->tablier = clone $this->tablier;
    }
}

$pont1 = new Pont(new Tablier(longueur: 600.0, largeur: 40.0), name:'Manhattan');
$pont2 = clone $pont1;

var_dump($pont1, $pont2);
```

```php
<?php

declare(strict_types=1);

class Tablier
{
    public function __construct(public float $longueur, public float $largeur)
    {
    }
}

class Pont
{
    public function __construct(protected Tablier $tablier, protected string $name)
    {
    }
    
    public function __toString()
    {
        return sprintf('Ce pont mesure %d de long pour %d de large', $this->tablier->longueur, $this->tablier->largeur);
    }
}

$pont = new Pont(new Tablier(longueur: 600.0, largeur: 40.0), name:'Manhattan');

echo $pont;
```

---

Puis, découvrons  `__invoke`,  `__sleep`  et  `__wakeup`  :

```php
<?php

declare(strict_types=1);

class Majuscule
{
    public function __invoke(string $chaine)
    {
        return strToUpper($chaine);
    }
}

$enMajuscule = new Majuscule;

echo $enMajuscule(chaine: "Chaine en majuscule");
```

```php
<?php

declare(strict_types=1);

class StartWith
{
    public function __construct(private string $startWith)
    {
    }
    
    public function __invoke(string $chaine)
    {
        return str_starts_with($chaine, $this->startWith);
    }
}

var_dump(
    array_filter(["Globule", "Plante", "Garage", "Livre", "Good Omens"], new StartWith(startWith: "G"))
);
```

```php
<?php

declare(strict_types=1);

class Tablier
{
    public function __construct(public float $longueur, public float $largeur)
    {
    }
}

class Pont
{
    public function __construct(protected Tablier $tablier, protected string $name)
    {
    }
}

$pont = new Pont(new Tablier(longueur: 600.0, largeur: 40.0), name:'Manhattan');
$chaine = serialize($pont);

echo $chaine;
```

```php
<?php

declare(strict_types=1);

class Tablier
{
    public function __construct(public float $longueur, public float $largeur)
    {
    }
}

class Pont
{
    public function __construct(protected Tablier $tablier, protected string $name)
    {
    }
    
    public function __sleep()
    {
        return ["name"];
    }
}

$pont = new Pont(new Tablier(longueur: 600.0, largeur: 40.0), name:'Manhattan');
$chaine = serialize($pont);

echo $chaine;
```

```php
<?php

declare(strict_types=1);

class Tablier
{
    public function __construct(public float $longueur, public float $largeur)
    {
    }
}

class Pont
{
    public $size = null;

    public function __construct(protected Tablier $tablier, protected string $name)
    {
        $this->size = fn() => $this->tablier->largeur * $this->tablier->longueur;
    }
    
    public function __sleep()
    {
        return ["name", "tablier"];
    }
    
    public function __wakeup()
    {
        $this->size = fn() => $this->tablier->largeur * $this->tablier->longueur;
    }
}

$pont = new Pont(new Tablier(longueur: 600.0, largeur: 40.0), name:'Manhattan');
$chaine = serialize($pont);
$pont2 = unserialize($chaine, ["allowed_classes" => [Pont::class, Tablier::class]]);

var_dump($pont2);
```

---

Nous finirons notre tour des méthodes magiques avec  `__serialize`,  `__unserialize`,  `__get`,  `__set`,  `__isset`,  `__unset`  et  `__call`  :

```php
<?php

declare(strict_types=1);

class Tablier
{
    public function __construct(public float $longueur, public float $largeur)
    {
    }
}

class Pont
{
    public function __construct(protected Tablier $tablier, protected string $name)
    {
    }
    
    public function __serialize()
    {
        return [
            "name" => $this->name,
            "longueur" => $this->tablier->longueur,
            "largeur" => $this->tablier->largeur,
        ];
    }
    
    public function __unserialize(array $data)
    {
        $this->name = $data["name"];
        $this->tablier = new Tablier($data["longueur"], $data["largeur"]);
    }
}

$pont = new Pont(new Tablier(longueur: 600.0, largeur: 40.0), name:'Manhattan');
$chaine = serialize($pont);
echo $chaine . PHP_EOL;
$pont2 = unserialize($chaine, ["allowed_classes" => [Pont::class, Tablier::class]]);
var_dump($pont2);
```

```php
<?php

declare(strict_types=1);

class Pont
{
    private string $hiddenProperty = "Au bal !";

    public function __get(string $name)
    {
        return $this->{$name};
    }
    
    public function __set(string $name, $value): void
    {
        $this->{$name} = $value;
    }
    
    public function __isset(string $name): bool
    {
        return isset($this->{$name});
    }
    
    public function __unset(string $name): void
    {
        unset($this->{$name});
    }
}

$pont = new Pont;
echo $pont->hiddenProperty.PHP_EOL;


$pont->hiddenProperty = "Masqué ohé ohé !";
echo $pont->hiddenProperty.PHP_EOL;
```

```php
<?php

declare(strict_types=1);

class Pont
{
    private string $hiddenProperty = "Au bal !";

    public function hiddenMethod()
    {
        return $this->hiddenProperty;
    }
    
    public function __call(string $name, array $arguments)
    {
        return $this->{$name}(...$arguments);
    }
}

echo (new Pont)->hiddenMethod();
```

## En résumé

- Il existe des méthodes dites magiques, qui sont appelées automatiquement par PHP. Elles commencent par `__`.

- `__construct`  est la méthode appelée lors de la création d’un objet ;  `__destruct`  lors de sa suppression de la mémoire.

- Il existe de nombreuses autres méthodes magiques disponibles sur [la documentation PHP](https://www.php.net/manual/fr/language.oop5.magic.php).
