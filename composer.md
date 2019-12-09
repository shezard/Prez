# Composer

### Pourquoi ?

 - Séparer le code tiers du code principal de l'application.
 - Savoir quelle version de quelle bibliothèque est utilisée.
 - Mettre à jour facilement ses dépendances.

### Comment ?

 - [Installer composer](https://getcomposer.org/download/)
 - Lancer `composer init`
 - Ajouter ses dépendances `composer require vendor/super-lib`
 - Lancer `composer install`

Exemple de fichier `composer.json`

```json
{
    "name": "Mon Projet",
    "require": {
        "symfony/http-foundation": "^5.0",
        "twig/twig": "^3.0"
    }
}
```

### Développer une bibliothéque utilisable avec composer

 - Installer composer
 - Lancer `composer init`
 - Coder ...
 - Commmit son code `git add . && git commit -m "Yeah !"`
 - Créer un tag `git tag v1.0.0`
 - Pusher son code et son tag `git push && git push --tags`

Dans le projet qui va utiliser cette bibliothéque, l'ajouter dans le `composer.json`

```json
{
    "name": "Mon Projet",
    "require": {
        "symfony/http-foundation": "^5.0",
        "twig/twig": "^3.0",
        "mon-vendor/ma-lib" : "^1.0"
    }
}
```

### L'autoload dans les bibliothèques.

##### Pourquoi ?
 - Charger automatiquement le code tiers, classes ou fonctions


##### Comment ?

###### Coté bibliothèque :

Exemple de hiérarchie:

```
 - src
   - ma
     - fonction.php
   - mes
     - classes
       - MaClasse.php
       - MonAutreClasse.php  
```

Exemple de `composer.json` utilisant l'autoload

```json
{
    "name": "Ma Lib",
    "autoload": {
        "files": [
            "/src/ma/fonction.php"
        ],
        "classmap": [
            "/src/mes/classes/"
        ]
    }
}
```

###### Coté application

`index.php`

```php
<?php

require_once('vendor/autoload.php'); // Composer crée un autoloader

$hello = hello("world"); // Défini dans vendor/mon-vendor/ma-lib/src/ma/fonction.php

$world = new World(); // Défini dans vendor/mon-vendor/ma-lib/src/mes/classes/MaClasse.php
```

### Le problème des collisions

Si deux bibliothéques définissent une même fonction ou classe, des problèmes de collision vont se poser.

##### La solution : Les namespaces et le PSR-4

##### Comment ?

###### Coté bibliothèque :

Exemple de hiérarchie:

```
 - src
   - mes
     - classes
       - MaClasse.php
       - MonAutreClasse.php  
```

Le fichier `MaClass.php`
```php
<?php
namespace MaLib;

class MaClasse
{

}
```

Exemple de `composer.json` utilisant le PSR-4

```json
{
    "name": "Ma Lib",
    "autoload": {
        "psr-4": {
            "MaLib\\": "src/",
        }
    }
}
```

###### Coté application :

`index.php`

```php
<?php

require_once('vendor/autoload.php'); // Composer crée un autoloader

use MaLib\MaClasse;

$maClass = new MaClasse(); // Défini dans vendor/mon-vendor/ma-lib/src/mes/classes/MaClasse.php
```

Plus de problème de collision !

###### Docs

- 