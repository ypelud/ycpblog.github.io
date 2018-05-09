---
layout: post
title:  "Flutter: menu swipe (revisité)"
date:   2018-05-09 15:23:28 +0100
categories: flutter dart
comments: true
---

*Ce post est une version revisité du précédent code suite à la suppression de redux du plugin [menu_swipe_helpers](https://github.com/fidelisa/flutter_plugins/tree/master/packages/menu_swipe_helpers).*

Comment réaliser un menu swipe (burger) dans une application Flutter. 

![My helpful screenshot]({{ "/assets/img/swipe.gif" | absolute_url }})

## Installation du plugin : menu_swipe_helpers

1. ajout de la dépendance dans le fichier pubspec.yaml du projet

```yaml
menu_swipe_helpers:
    git:
      url: https://github.com/fidelisa/flutter_plugins.git
      path: packages/menu_swipe_helpers
```
```shell
flutter packages get
```

2. import du packet (ex: dans main.dart)

```dart
import 'package:menu_swipe_helpers/menu_swipe_helpers.dart';
```

## Utilisation du plugin

Le déroulé présenté ici utilise tous les helpers du plugin mais vous pouvez à tout moment utiliser des classes de base (voir ThirdPage dans [exemple](https://github.com/fidelisa/flutter_plugins/tree/master/packages/menu_swipe_helpers/example)).


### Définir les menus

Créer un object de type `DrawerDefinition`. A noter que le builder doit permettre de construire la page à afficher.

```dart
var _firstPage = new DrawerDefinition(
    title: "First page",
    iconData: Icons.home,
    widgetBuilder: (BuildContext context) =>
        new FirstPage(title: "First Page"));
```

### Créer un menu 

Créer un objet de type `DrawerHelper` avec comme paramètre une liste de `DrawerDefinition`.

```dart
var _drawerBuilder =  new DrawerHelper(
  drawerContents: [
    _firstPage
  ]
);
```

### Mettre en place le fournisseur de menu 

Il suffit d'envelopper la première page de votre application par `DrawerProvider` et de lui indiquer quel menu mettre en place.

```dart
return new MaterialApp(
  title: 'Flutter Demo',
  theme: new ThemeData(
    primarySwatch: Colors.blue,
  ),
  home: new DrawerProvider(
    drawer: _drawerBuilder,
    child: new FirstPage(
      title: "First Page",
    ),
  ),
);
```

Et voilà ...

## Sources 
Plugins et exemples : [menu_swipe_helpers](https://github.com/fidelisa/flutter_plugins/tree/master/packages/menu_swipe_helpers)
