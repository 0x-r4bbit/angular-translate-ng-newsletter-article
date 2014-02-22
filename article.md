# Easy global i18n angularJS language translations for your Angular app

As worldwide access to the web increases, we as developers are constantly pressed to make our apps internationally and locally accessible.
When a user visits our apps, he or she should be able to switch languages on the fly at runtime.

Given that we are building AngularJS client-side apps, we don’t particularly want the user to have to refresh the page or visit an entirely different URL.
Of course, AngularJS could easily accommodate your international audience natively, perhaps by generating different templates for different languages and serving those within the app.

This process can become cumbersome, and what happens when you want to change the layout of the app?
Every single template needs to be rebuilt and redeployed.
This process should just be easy.

Instead of creating new templates, we’ll use angular-translate, an AngularJS module that brings i18n (internationalization) to your Angular app.
angularjs-translate requires us to create a JSON file that represents translation data per language.
It lazy-loads the language-specific translation data from the server only when necessary.

The library angular-translate comes with built-in directives and filters that make the process of internationalizing apps simple.
Let’s get started.

## Installation
To use angular-translate, we need to load the angular-translate library.
We can install it in several different ways, but we prefer using Bower.

Bower is a front-end package manager.
It handles not only JavaScript libraries, but also HTML, CSS, and image packages.
A package is simply encapsulated, third-party code that is typically publicly accessible in a repository.

### Using Bower
We install angular-translate using the normal Bower process:

```sh
$ bower install angular-translate
```

Alternatively, we can download the minified version of angular-translate from github.

Once we’ve installed the latest stable version of angular-translate, we can simply embed it in our HTML document.
Just make sure it’s embedded after Angular itself, as it depends on the core angular library.

```html
<script src="path/to/angular.js"></script>
<script src="path/to/angular-translate.js"></script>
```

Last but not least, our app has to declare angular-translate as a load dependency:

```js
var app = angular.module('myApp', ['pascalprecht.translate']);
```

Great, we’re now ready to use angular-translate’s components to translate our app!

## Teaching your app a new language
Now our app depends upon angular-translate as installed, and our app declares it as a dependency, so we can use it to translate our app’s contents.

First, we need to provide translation material for our app to actually speak a new language.
This step actually entails configuring the $translate service through our fresh $translateProvider service.

Training our app to use a new language is simple.
Using the config function on our app, we provide the different language translations for our app, i.e. English, German, Hebrew, etc..
First, we inject our $translateProvider in the config function, like so:

```js
angular.module('angularTranslateApp', ['pascalprecht.translate'])
  .config(['$translateProvider', function($translateProvider) {
    // Our translations will go in here
}]);
```

To add a language, we have to make $translateProvider aware of a translation table, which is a JSON object containing our messages (keys) that are to be translated into (values).
Using a translation table enables us to write our translations as simple JSON for loading remotely or setting at compile-time, such as:

```json
{
  'MESSAGE': 'Hello world',
}
```

In a translation table, the key represents a translation id, whereas the value represents the concrete translation for a certain language.
Now add a translation table to your app.
`$translateProvider` provides a method called translations(), which takes care of that.

```js
app.config(function ['$translateProvider', ($translateProvider) {
  $translateProvider.translations({
    HEADLINE: 'Hello there, This is my awesome app!',
    INTRO_TEXT: 'And it has i18n support!'
  });
}]);
```

With this translation table in place, our app is set to use angular-translate.
Since we’re adding the translation table at configuration time, angular-translate’s components are able to access it as soon as they are instantiated.

Let’s switch over to our app template.
Adding translations in the view layer is as simple as binding our key to the view.
Using the translate filter, we don’t even have to engage our controller or services or worry about the view layer: We’re able to decouple the translate logic from any controller or service and make our view replaceable without touching business logic code.

Basically, the translate filter works like so:

```html
<h2>{{ 'TRANSLATION_ID' | translate }}</h2>
```

To update our example app, we make use of the translate filter:

```html
<h2>{{ 'HEADLINE' | translate }}</h2>
<p>{{ 'INTRO_TEXT' | translate }}</p>
```

Great! We’re now able to translate our content within the view layer without polluting your controllers logic with translation logic; however, we could achieve the same result without using angular-translate at all, since our app only knows about one language.

Let’s see angular-translate’s real power and learn how to teach our app more than one language.

## Multi-language support
You’ve already learned how to add a translation table to your app using `$translateProvider.translations()`.

The $translateProvider knows one language, as we set it with the `$translateProvider.translations()` method.
Now, we can add an additional language in the same way by providing a second translation table.

When we set our first translation table, we can provide it a key (a language key) that specifies the language we’re translating.
We can simply add another translation key with another language key.

Let’s update our app to include a second language:

```js
app.config(function ['$translateProvider', ($translateProvider) {
  $translateProvider.translations('en_US', {
    HEADLINE: 'Hello there, This is my awesome app!',
    INTRO_TEXT: 'And it has i18n support!'
  });
}]);
```

To add a second translation table for another language, let’s say German, just do the same with a different language key:

```js
app.config(function ['$translateProvider', ($translateProvider) {
  $translateProvider.translations('en', {
    HEADLINE: 'Hello there, This is my awesome app!',
    INTRO_TEXT: 'And it has i18n support!'
  })
  .translations('de', {
    HEADLINE: 'Hey, das ist meine großartige App!',
    INTRO_TEXT: 'Und sie untersützt mehrere Sprachen!'
  });
}]);
```

Now our app knows about two different languages.
We can add as many languages as needed, there’s no limit; however, since there are now two languages available, how does our app know which language should to use?
angular-translate doesn’t prefer any language until you tell it to do so.

To set a preferred language, we can use the method `$translateProvider.preferredLanguage()`.
This method tells angular-translate which of the registered languages is the one that our app should use, by default.
It expects an argument with the value of the language key, which points to a certain translation table.

Now, let’s tell our app that it should use English as its default language:

```js
app.config(function ['$translateProvider', ($translateProvider) {
  $translateProvider.translations('en', {
    HEADLINE: 'Hello there, This is my awesome app!',
    INTRO_TEXT: 'And it has i18n support!'
  })
  .translations('de', {
    HEADLINE: 'Hey, das ist meine großartige App!',
    INTRO_TEXT: 'Und sie untersützt mehrere Sprachen!'
  });
  $translateProvider.preferredLanguage('en');
}]);
```

### Switching the language at runtime

To switch to a new language at runtime, we have to use angular-translate’s `$translate` service.
It has a method `uses()` that either returns the language key of the currently used language, or, when passing a language key as argument, tells angular-translate to use the corresponding language.

To get a feeling for how this capability works in a real app, add two new translation id’s that represent translations for buttons you’ll add later in your HTML template:

```js
app.config(function ['$translateProvider', ($translateProvider) {
  $translateProvider.translations('en', {
    HEADLINE: 'Hello there, This is my awesome app!',
    INTRO_TEXT: 'And it has i18n support!',
    BUTTON_TEXT_EN: 'english',
    BUTTON_TEXT_DE: 'german'
  })
  .translations('de', {
    HEADLINE: 'Hey, das ist meine großartige App!',
    INTRO_TEXT: 'Und sie untersützt mehrere Sprachen!'
    BUTTON_TEXT_EN: 'englisch',
    BUTTON_TEXT_DE: 'deutsch'
  });
  $translateProvider.preferredLanguage('en');
}]);
```

Next, implement a function on a controller that uses the `$translate` service and its `uses()` method to change the language at runtime.
To do that, we’ll inject the `$translate` service in our app’s controller and add a function on its $scope:

```js
app.controller('TranslateCtrl', ['$translate', '$scope', function ($translate, $scope) {
  $scope.changeLanguage = function (langKey) {
    $translate.uses(langKey);
  };
}]);
```

Now, let’s reflect this change in the HTML template by adding a button for each language.
We’ll also have to set up an ng-click directive on each button, which calls the function that changes the language at runtime:

```html
<div ng-controller="Ctrl">
  <button ng-click="changeLanguage('de')" translate="BUTTON_TEXT_DE"></button>
  <button ng-click="changeLanguage('en')" translate="BUTTON_TEXT_EN"></button>
</div>
```

Et voilà! We now have an app with multi-language support!

## Loading languages

What fun would it be if we were going to set the languages statically?
We can dynamically load languages thanks to Angular’s `$http` service, through the `$translateProvider`’s `registerLoader` function.

First, we need to install the loader-url extension by setting the loader-url service, which expects that there is a back-end server to send back JSON by handling the lang parameter.
If you do have a back end that handles the route with the lang parameter, install the loader-url service with Bower like so:

```sh
$ bower install angular-translate-loader-url
```

If you prefer to have a service that loads static files, we can use the static-files loader that loads JSON files from a path with language files.
Since this router is simpler, we’ll go ahead and install this service through Bower:

```sh
$ bower install angular-translate-loader-static-files
```

Now, let’s make sure this file is loaded in our view through a script tag:

```html
<script src="/js/angular-translate-loader-url.min.js"></script>
```

To configure our service to use the static-files loader, we need to tell our `$translateProvider` to use the loader with a configuration object.
The configuration object takes two parameters:

prefix - which specifies the file prefix (including file paths)
suffix - which specifies the file suffix (usually the extension)
The file loader attempts to fetch files at the following URL path: [prefix]/[langKey]/[suffix]. For instance, if we set our config object as:

```js
$translateProvider.useStaticFilesLoader({
  prefix: '/languages/',
  suffix: '.json'
});
```

Then angular-translate attempts to load the en_US language from /languages/en_US.json.
Using the StaticFilesLoader like so gives us the side benefit of lazy-loading.
`$translate` will only pull down the language files it needs at runtime.

Of course, using asynchronous loading will cause a flash of untranslated content as the app loads.
We can circumvent this side effect by setting a default language that is packaged with the app.

One last cool feature: We can use local storage to store our language files.
angular-translate provides the ability to use local storage; this capability can be enabled with one function:

```js
$translateProvider.useLocalStorage();
```

## Conclusion
We’ve covered how to use angular-translate to bring i18n support to your Angular app using `$translateProvider.translations()` and the translate filter.
We’ve also shown how to change the language at runtime using `$translate` service and its `uses()` method.

Try out angular-translate; it comes with a lot of really nice built-in features, such as handling pluralization, using custom loaders, and setting translations through a service.
The docs are fantastic; we suggest you check them out here.

There are a lot of examples with which you can play directly on the site!
There’s also an API Reference that shows all available components and the interfaces you can use to build awesome apps with internationalization support!
