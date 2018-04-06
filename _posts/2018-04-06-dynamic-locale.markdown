---
layout: post
title:  "Flutter: Changer de langage sans redémarrer"
date:   2018-04-05 07:23:28 +0100
categories: flutter dart
comments: true
---

Comment changer dynamiquement de langue de l'application.

![My helpful screenshot]({{ "/assets/img/dynlocale.gif" | absolute_url }})

## Mise en place de la traduction

Suivre la procédure du post [globalization simplifié]({% post_url 2018-03-22-intl-helpers-flutter %}).


## Mise en place de LocaleListener

### Initialisation de l'instance

Tout d'abord il faut initialiser le langage de départ de l'application et permettre à l'écouteur d'accéder à la méthode d'initialisation des messages que nous avons généré dans l'étape "mise en place de la traduction". 

Plusieurs possibilités s'offrent à nous, dans le `main`, dans la surcharge de `initState` de votre `widget` qui contient `MaterialApp`, ...

Pour être sûr d'être au plus haut dans la hierarchie nous l'installerons lors du `main` comme dans le code suivant.

```dart
void main() {
  LocaleNotifier.init(
      locale: new Locale('fr', 'FR'), initializeMessages: initializeMessages);
  ...
}
```

### Ajout d'un écouteur pour rafraichir l'application 

Nous devons maintenant être alerté que l'application a changé de langue. C'est là que l'écouteur devient utile. 
Si ce n'est pas le cas vous devrez transformer votre `Widget` de départ (celui avec `MaterialApp`) en `StateFullWidget`.

Dans la méthode `initState`, nous allons ajouter le code suivant. 

```dart
@override
void initState() {
  super.initState();
  LocaleNotifier.instance.addListener(() {
    setState(() {});
  });
}
```


### Ajout d'un bouton pour changer de langue

Nous allons maintenant ajouter un bouton pour changer de langue. Nous allons par exemple ajouter un `FloatingActionButton` à notre page. L'exemple est assez basique, il passe de français à anglais et vice versa...

```dart
@override
Widget build(BuildContext context) {
  return new Scaffold(
...
    floatingActionButton: new FloatingActionButton(
      child: new Text(localeName),
      onPressed: _toggleLocale,
    )
  )
}
```

Il ne nous reste plus qu'a gérer le changement de langue.
```dart
 void _toggleLocale() {
  if (LocaleNotifier.instance.locale.languageCode == 'fr') {
    LocaleNotifier.instance.update(new Locale('en', 'US'));
  } else {
    LocaleNotifier.instance.update(new Locale('fr', 'FR'));
  }
}
```

Et voilà ...

## Sources 
Plugins et exemples : [intl_helpers](https://github.com/fidelisa/flutter_plugins/tree/master/packages/intl_helpers)


