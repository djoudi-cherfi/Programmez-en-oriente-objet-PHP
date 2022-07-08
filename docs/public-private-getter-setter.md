# Concevez l'API publique de vos objets

## Définissez le domaine public de vos classes

Toutes les méthodes et les propriétés que vous allez vouloir garder accessibles au niveau de l’objet, que vous souhaitez utiliser en dehors des méthodes, de la classe elle même, doivent être préfixées du mot clé public.

C'est ce que nous avons fait jusqu'ici. Si maintenant nous avons besoin d’ajouter une propriété ou méthode qui ne servira qu’au sein de la classe (et pas à l’extérieur), nous allons la préfixer du mot-clé private.

Pour illustrer cela, imaginons que nous souhaitions plutôt renvoyer la surface
au format suivant uniquement : 5m². Nous allons pouvoir avoir une propriété
privéeunite :

```php
<?php

declare(strict_types=1);

class Pont
{
  // $unite ne sert que dans la classe, on met cette propriété en privé.
   private string $unite = 'm²';
  
   public float $longueur;
   public float $largeur;
  
   public function getSurface(): string
   {
       return ($this->longueur * $this->largeur) . $this->unite; // on renvoie l’unité en plus de la surface
   }}

$towerBridge = new Pont;
$towerBridge->longueur = 286.0;
$towerBridge->largeur = 15.0;

echo $towerBridge->getSurface();
```

La propriété $unite n’est alors plus accessible depuis l’extérieur de la classe.
Cela peut paraître peu utile sur cet exemple, mais si vous avez beaucoup de
méthodes ou de propriétés dans votre classe, le développeur saura facilement
quelles méthodes lui seront utiles.

## Masquez la complexité de vos objets

Si on part du principe que vous souhaitez toujours avoir un maximum de contrôle
sur ce que contiennent les propriétés. Avec le typage strict, vous êtes garanti
d'avoir un flottant en longueur et en largeur. Mais rien ne vous empêche d'y
insérer des valeurs négatives, par exemple. Pour se protéger, **il faut privatiser**
`$longueur` et `$largeur` :

```php
<?php

declare(strict_types=1);

class Pont
{
   private string $unite = 'm²';
  
   private float $longueur;
   private float $largeur;
}

$towerBridge = new Pont;
$towerBridge->longueur = 286.0;
```

A présent ce n’est plus possible de modifier les valeurs depuis l'extérieur de l'objet ; les modifications doivent absolument être faites dans la classe.

> D’ailleurs, si vous avez testé le code vous avez dû remarquer que PHP vous renverra une erreur. Une erreur particulièrement problématique puisque que c’est une erreur “fatale”. Elle stoppe le script.

Dès que l’on verrouille des accès, on rentre dans le principe de
l'encapsulation. On encapsule, on enferme les données dans l’objet et on va
devoir créer une méthode publique, qu’on appellera  setLongueur, pour les
modifier :

```php
<?php

declare(strict_types=1);

class Pont
{
   private string $unite = 'm²';
  
   private float $longueur;
   private float $largeur;
  
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
}

$towerBridge = new Pont;
$towerBridge->setLongueur(286.0);
$towerBridge->setLongueur(-286.0);
```

`setLongueur` attend un argument `$longueur` de type `float` qui sera assigné à la propriété ligne 21. Nous retrouvons la flèche de sélection et le signe égal d'assignation.

Cette méthode s'appelle un **setter** ou **mutateur** en français. Aux lignes 26
et 27, nous assignons des valeurs à notre mutateur.

Elle est souvent accompagnée d'un **getter** ou **accesseur** en français. Cette
seconde méthode permet d'obtenir la valeur contenue dans la propriété. Dans
notre exemple, ce serait une méthode nommée `getLongueur` :

```php
<?php
class Pont
{
…
    public function getLongueur(): float {
        return $this->longueur;
    }
}
```

```php
<?php

declare(strict_types=1);

class Pont
{
   private string $unite = 'm²';
  
   private float $longueur;
   private float $largeur;
  
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
}

$towerBridge = new Pont;
$towerBridge->setLongueur(286.0);

echo $towerBridge->getLongueur();
```

## En résumé

- Vous réduisez la complexité d’utilisation des classes en privatisant les méthodes et les propriétés avecprivate.

- Nous pouvons appliquer **le principe d’encapsulation**, en utilisant des getters et des setters, permettant d’accéder aux propriétés et de les modifier en s’assurant de garder des valeurs cohérentes.

- En réduisant la complexité, vous réduisez le risque d’erreur.
