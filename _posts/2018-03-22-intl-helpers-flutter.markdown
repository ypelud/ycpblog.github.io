---
layout: post
title:  "Flutter: Globalization simplifiée"
date:   2018-03-22 15:23:28 +0100
categories: flutter dart
comments: true
---

Comment simplifier la gestion des traductions dans un projet flutter.

## Installation du plugin : intl_helpers

1. ajout de la dépendance dans le fichier pubspec.yaml du projet

```yaml
intl_helpers:
    git:
      url: https://github.com/fidelisa/flutter_plugins.git
      path: packages/intl_helpers
```
```shell
flutter packages get
```

2. import du packet (ex: dans main.dart)

```dart
import 'package:intl_helpers/intl_helpers.dart';
```

## Utilisation du plugin

### Ajout des delegates de globalization dans `MaterialApp`

Commençons par ajouter le langage de base pour l'application (1) puis les delegates de localisation (2) pour finir nous ajoutons les langages supportés par l'application (3).

Nous avons utilisé un premier helper `createBasicLocalizationsDelegates`, il permet de récupérer la liste des delegates de base (`GlobalWidgetsLocalizations`, `GlobalMaterialLocalizations` et `GenericHelperLocalizations`).

```dart
@override
Widget build(BuildContext context) {
  return new MaterialApp(
    title: 'Intl Helper Example',
    locale: new Locale("fr", "FR"), //(1)
    localizationsDelegates: createBasicLocalizationsDelegates( // (2)
        supportedLanguages: ['fr', 'en']),
    supportedLocales: [ // (3)
      const Locale('en', 'US'), // English
      const Locale('fr', 'FR'), // French
    ],(3)

    theme: new ThemeData(
      primarySwatch: Colors.blue,
    ),
    home: new MyHomePage(title: 'Intl Helper Example Page'),
  );
}
```

### Utilisation d'une traduction fournie par Flutter

Nous pouvons utiliser les traductions qui sont fournis par le plugin directement en utilisant `GlobalMaterialLocalizations.of(context).<traduction_name>`. 

Par exemple pour afficher le texte "Connecté":

```dart
new Text(
  MaterialLocalizations.of(context).signedInLabel,
),
```

### Utilisation d'une traduction fournie par le plugin

Nous pouvons utiliser les traductions qui sont fournis par le plugin direcement en utilisant `GenericHelperLocalizations.of(context).<traduction_name>`. 

Par exemple pour afficher le texte "Bienvenue !":

```dart
new Text(
  GenericHelperLocalizations.of(context).welcome,
  style: Theme.of(context).textTheme.display2,
),
```

Le texte apparaît dans la langue du téléphone (français j'imagine), si vous passez en anglais le texte s'adaptera automatiquement.

## Créer ses propres traductions

### Mise en place d'un première traduction (unitaire)

Nous pouvons ajouter un texte à traduire directement dans le source avec `Intl.message`.

Le premier paramètre est la chaîne à traduire, qui peut être interpolé basé sur une ou plusieurs variables. Le [name] du message doit correspondre au nom de la fonction englobante. Pour les méthodes, il peut aussi être className_methodName (ex: _HomePageState_welcome). 
Le nom doit également être globalement unique dans le programme.
Le tableau [args] correspond aux arguments à interpoler, [desc] fournit une description de l'utilisation.

```dart
String welcome(name) => Intl.message('Bienvenue, $name',
    name: "_HomePageState_welcome",
    args: [name],
  desc: 'message de bienvenue avec nom');
```

Exemple d'utilisation de `welcome(name)`:
```dart
new Text(
  welcome('Alice'),
  style: Theme.of(context).textTheme.display1,
),
```

### Mise en place d'une traduction réutilisable (globale)

Nous allons créer un singleton `GlobalMessage` qui permettra de réutiliser les traductions n'importe où dans l'application.

```dart
class GlobalMessage {

  /// constructor not allowed, only singleton
  GlobalMessage._();

  /// singleton instance
  static final GlobalMessage instance = new GlobalMessage._();

  /// welcome global sample
  String get welcomeGlobal => Intl.message("Bienvenue à tous !",
      name: "welcomeGlobal", args: [], desc: "welcome message");
}
```
Exemple d'utilisation de `welcomeGlobal`:
```dart
new Text(
  GlobalMessage.instance.welcomeGlobal,
  style: Theme.of(context).textTheme.display1,
),
```

## Traduire

### Installer intl_translation

Cet outil permet d'extraire les chaines à traduire et fabrique un fichier au format 'arb' à traduire (par ex. avec les outils de Google).

```shell
flutter pub pub global activate intl_translation
```

### Extraire les traductions

Nous pouvons utiliser `intl_translation` pour extraire les traductions dans un fichier 'lib/l10n/intl_messages.arb'. Ce fichier pourra être traduit par des outils tel que [Translator toolkit](https://translate.google.com/toolkit/).

```shell
mkdir lib/l10n
flutter pub pub global run intl_translation:extract_to_arb \
  --output-dir=lib/l10n lib/**/*.dart
```

### Traduire et préparer les fichiers de traductions

Copier/renommer les fichiers de traductions comme suit : `app_message_<locale>.arb`, cela permet de simplifier la gestion des fichiers générés (ex: app_message_en.arb). 

Une fois les fichiers des différentes traductions copiés et traduits, il faut ajouter la propriété `"@@locale": "<locale>",`, comme dans l'exemple suivant:

```json
{
  "@@locale": "en",
  "@@last_modified": "2018-03-22T17:00:19.297438",
  "_MyHomePageState_welcome": "Welcome, {name}",
  ...
}
```

### Générer le code issu de la génération

Nous pouvons maintenant lancer la génération automatique des fichiers.

```shell
flutter pub pub global run intl_translation:generate_from_arb \
  --output-dir=lib/l10n --no-use-deferred-loading \
  lib/main.dart lib/l10n/app_*.arb
```

### Modifier la liste des délégates 

Nous ajoutons l'import des sources précédement générés dans `main.dart`

```dart
import 'l10n/messages_all.dart';
```

Puis nous allons modifier la création des delegates pour ajouter la traduction.

```dart
localizationsDelegates: createBasicLocalizationsDelegates(
    supportedLanguages: ['fr', 'en'],
    initializeMessages: initializeMessages),
```

Et voilà ...

## Sources 
Plugins et exemples : [intl_helpers](https://github.com/fidelisa/flutter_plugins/tree/master/packages/intl_helpers)


