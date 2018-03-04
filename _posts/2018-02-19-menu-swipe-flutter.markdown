---
layout: post
title:  "Flutter: menu swipe"
date:   2018-02-19 12:23:28 +0100
categories: flutter dart
comments: true
---

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

Le déroulé présenté ici utilise tous les helpers du plugin mais vous pouvez à tout moment utiliser des classes de base (voir ThirdPage dans [exemple](https://github.com/fidelisa/flutter_plugins/tree/master/examples/menu_swipe)).

### Créer une page

Créer un Widget `Stateful`, la partie state doit implémenter le mixin `DrawerStateMixin`. 

Le mixin va gérer pour vous la mise en page, vous pouvez surcharger tous les éléments (buildBody, buildAppBar, buildActions, buildPersistentFooterButtons, buildFloatingButton) afin de personnaliser les affichages.

```dart
class FirstPage extends StatefulWidget {
  @override
  _FirstPage createState() => new _FirstPage();
}

class _FirstPage extends State<FirstPage> with DrawerStateMixin {
  @override
  String get title => "First Page";
}
```
### Définir les menus

Créer une classe qui implemente l'interface `DrawerDefinition`. A noter que le builder doit permettre de construire la page à afficher.

```dart
class FirstPageDefinition implements DrawerDefinition {
  @override
  Icon get icon => const Icon(Icons.home);

  @override
  String get subtitle => null;

  @override
  String get title => "First page";

  @override
  Widget builder(BuildContext context) {
    return new FirstPage();
  }
}
```

### Créer un menu 

Créer instancier la classe `DrawerHelper` avec comme paramètre une liste de `DrawerDefinition`.

```dart
var _drawerBuilder =  new DrawerHelper(
  drawerContents: [
    new FirstPageDefinition()
  ]
);
```

### Mettre en place le store pour gérer le menu

Le mécanisme de mise à jour du menu et de la page est basé sur le package [flutter_redux](https://pub.dartlang.org/packages/flutter_redux).
Il faut créer quelques classes qui seront utilisées par ce package.

Commençons par le `Store`, il doit étendre l'interface `DrawerStore`.
```dart
class AppState implements DrawerStore {
  final Widget activeDrawer;
  final DrawerDefinition activePage;
  
  AppState({this.activeDrawer, this.activePage});
}
```

Créer maintenant un 'reducer', c'est lui qui sera chargé de propager les actions.
```dart
AppState appReducer(AppState state, action) {
  return new AppState(
      activeDrawer: drawerReducer(state.activeDrawer, action),
      activePage: pageReducer(state.activePage, action));
}
````

Créer l'instance du `Store`.
```dart
final store = new Store<AppState>(
  appReducer,
  initialState: new AppState(
      activeDrawer: _drawerBuilder,
      activePage: _drawerBuilder.drawerContents.first
  )
);
```

Il ne reste plus qu'a créer le composant racine de notre application. Il faut encadrer le composant `MaterialApp` par un `StoreProvider` qui embarquera le `Store` créé précédemment.
```dart
  @override
  Widget build(BuildContext context) {
    return new StoreProvider(
      store: store,
      child: new MaterialApp(
          title: 'Flutter Demo',
          theme: new ThemeData(
            primarySwatch: Colors.blue,
          ),
          home: new ActivePage()),
    );
  }
```

Et voilà ...

## Sources 
Plugins et exemples : [https://github.com/fidelisa/flutter_plugins](https://github.com/fidelisa/flutter_plugins)
