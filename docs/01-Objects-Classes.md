# Les objets et les classes

## En programmation, objet est le nom que l’on donne à l'élément

- qui se trouve dans une variable ;
- dans lequel on peut stocker des valeurs ;
- et auquel on peut demander de faire des actions.

Pour obtenir un objet, il faut demander au langage de le créer et de nous le donner pour qu’on puisse le manipuler. Pour faire ça, on précise au langage le nom de l’élément que l’on veut manipuler, c’est-à-dire la classe.

---

## Qu’est-ce qu’une classe ?

Une classe est une définition qui contient le nom des propriétés qu’on pourra manipuler ainsi que des méthodes. Les propriétés, ce sont des variables internes à cette définition dans lesquelles on stocke des valeurs. Une classe a aussi des méthodes : il s’agit de fonctions internes à la classe. La classe détermine ce qu'il sera possible de faire avec l'objet.

Le fait de créer un objet à partir d’une classe s’appelle instancier une classe.

---

## Instanciez votre première classe

Le langage PHP propose déjà plusieurs classes par défaut, comme DateTime. Pour
utiliser cette classe, vous devez l'appeler par son nom, précédé du mot clé new.

```php
<?php

$date = new DateTime;
```

Assigner l'instance   DateTime  à une variable, ici $date, pour pouvoir la
manipuler. Si je ne le fais pas, alors j’aurai juste demandé au langage de
générer l’objet et de le détruire immédiatement.

### PHP Standards Recommendations

<https://www.php-fig.org/psr/>

## Découvrez ce qu'est une instance

Bon, nous avons créé une instance, mais qu’est-ce que ça veut dire, exactement ?
Créer une instance, c'est créer un objet à partir d'une classe, ça vous le savez
déjà. Et en PHP, cette création est le résultat de trois opérations :

- l’allocation et l'initialisation de données ;
- le stockage en mémoire du nouvel objet pour suivre son état ;
- la construction d’une référence de l’objet pour le manipuler.

Revenons sur la notion de référence. Pour rappel, lorsque vous passez une
variable en paramètre d’une fonction, PHP en fait une copie par défaut. Par
exemple, à l’exécution de ce code, la variable $a n’a pas changé :

```php
<?php

// déclaration par référence avec le symbole &
function foo($var) {
    $var = 2;
}


$a=1;
foo ($a);
// $a vaut toujours 1
```

Si vous souhaitez pouvoir modifier la variable au sein de la fonction, vous
pouvez ajouter le symbole &  pour indiquer au langage de ne pas copier la valeur
dans votre fonction, mais plutôt de manipuler directement la variable d’origine.

```php
<?php

// déclaration par référence avec le symbole &
function foo(&$var) {
    $var = 2;
}


$a=1;
foo ($a);
// $a vaut toujours 1
```

Il n'y a pas de signe de référence dans l'appel de la fonction foo, uniquement
sur sa définition. La définition de la fonction en elle-même est suffisante pour
passer correctement des arguments par référence.

Si vous je parle de référence, c’est parce qu’en PHP, c’est un peu différent
pour les objets. Ils sont toujours passés par référence aux différentes
fonctions. Je manipule toujours le même objet en mémoire. Je n’ai pas besoin
du&.

```php
<?php
 
// déclaration de référence à l’objet
function foo(DateTime $date) {
    $date->modify('+1 day'); // permet d'ajouter 1 jour à la date
}
 
$date = new DateTime;
foo ($date);
 
// $date est maintenant au lendemain
```

Les objets se comparent de manière un peu différente des autres types de
variables. Si vous créez 2 objets issus de la même classe, que vous leur
assignez les mêmes valeurs et les comparez simplement (==), vous vérifiez que
les 2 éléments possèdent les mêmes propriétés et les mêmes valeurs. Si vous
testez strictement (===), alors vous vérifiez que vous êtes en train de
manipuler la même instance.

---

## Utilisez les fonctionnalités d'un objet

Pour rappel, les objets possèdent des propriétés (variables) et des méthodes
(fonctions). Pour y accéder, il faut utiliser le symbole ->   à la suite de la
variable, comme dans l’exemple suivant :

```php
<?php
 
$date = new DateTime;

echo $date->format('d/m/Y');
```

Une méthode d'un objet peut ne rien vous retourner, comme vous retourner
n'importe lequel des types primaires de PHP. Mais elle peut tout à fait vous
retourner un objet.

Dans ces cas-là, ça peut être une nouvelle instance, ou l'instance que vous êtes
en train de manipuler directement.

Retourner une instance offre la possibilité d'effectuer du chaînage.
C'est-à-dire enchaîner les appels à différentes méthodes les unes à la suite des
autres, sans avoir à faire référence à la variable. Comme ça :

```php
<?php
 
// D’abord, l’exemple sans chaînage :
$date = new DateTime;
$newDate = $date->modify('+1 day');
 
echo $date->format('d/m/Y').PHP_EOL;
echo $newDate->format('d/m/Y').PHP_EOL;
 
// Maintenant avec le chaînage. Nous exploitons directement
// l'objet qui nous est retourné sans le stocker dans une variable :
$formatedDate = $date->modify('+1 day')->format('d/m/Y');
echo $formatedDate.PHP_EOL;
```

On a vu ensemble que quand vous assignez un objet à 2 variables, vous manipulez
toujours le même objet. C’est pareil ici. Vous n’obtenez pas une copie de $date.
Cette variable pointe (fait référence à) vers le même objet, la même allocation
mémoire, que$date.

Supprimer la variable $date  n'est donc plus suffisant pour que PHP libère la
mémoire. Toutes les références doivent être supprimées.

D'autres objets vous renverront de nouvelles instances, qu'il s'agisse d'une
instance de leur propre classe, ou d'une toute autre classe. Comme par exemple
avec la classe DateTimeImmutable, dont le but est de laisser l'objet original
intact.

```php
<?php
 
$date = new DateTimeImmutable;
$newDate = $date->modify('+1 day');
 
echo $date->format('d/m/Y').PHP_EOL;
echo $newDate->format('d/m/Y').PHP_EOL;
```

Nous observons ici que $date  n’est pas changé lorsque nous appelons la méthode
modify , mais que la date du lendemain est retournée dans  $newDate) uniquement.
Il s’agit de l’unique différence entre les classes   DateTime  et
DateTimeImmutable.

Il n’y a pas de secret, pour savoir comment fonctionnent ces classes natives de
PHP, il faut aller lire [la documentation qui se trouve sur PHP.net](https://www.php.net/) (je vous
conseille la version anglaise : la version française peut parfois manquer de
précision ou d’exemples).

---

## Créez un objet à partir de (presque) rien

Lorsque vous manipulez des chaînes au format JSON et que celui-ci comprend la
définition d'un objet, par exemple :

```json
{
    "date":"2021-03-23 07:35:44.011207",
    "timezone_type":3,
    "timezone":"Europe/Paris"
}
```

.. PHP n'a aucune indication. Il ne connaît pas la classe dont ces valeurs sont
issues. Pourtant, lorsque vous allez lui demander de transformer cette chaîne en
objet, il va y arriver :

```php
<?php

$s = <<<JSON
{
    "date":"2021-03-23 07:35:44.011207",
    "timezone_type":3,
    "timezone":"Europe/Paris"
}
JSON;

var_dump(json_decode($s));
```

Vous allez obtenir un objet issu de la classe `stdClass`.

PHP est un langage extrêmement permissif. Il autorise d'ajouter des propriétés à
la volée. Tout comme il permet aux variables de changer de type au cours de leur
vie dans un script.

Pour éviter d’arrêter l’exécution du script par manque de connaissance, et
d’afficher une erreur, lorsqu'aucune classe n'est spécifiée, PHP utilisera la
classe `stdClass`. Par ailleurs, vous pouvez tout à fait instancier cette classe
manuellement.

> Ceci dit, je conseille toujours d’utiliser une autre classe que celle-ci, tout comme il sera préférable de ne pas abuser des capacités d'assignation dynamique de PHP.

---

## En résumé

- En programmation orienté objet, une classe est un modèle ou un mode d’emploi tandis que les objets sont les objets créés à partir de ce modèle
- Une classe possède un ensemble de variables, appelées propriétés ainsi qu’un ensemble de fonctions appelées méthodes.
- Il est possible de créer une classe à partir d’une classe grâce au mot-clé `new`. On appelle cela une instance.
- Pour accéder aux propriétés ou aux méthodes d’une classe, on utilise le symbole  ->.
