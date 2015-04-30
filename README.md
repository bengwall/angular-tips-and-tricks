Angular Tips and Tricks
=======================

Routing
-------

* Use the Angular UI Router over Angular's build in router.  It provides child views, routing by state, and much more.
* Use the `resolve` property to resolve dependencies before control is handed to a controller and the page displays.  This will reduce flickering during page transitions when retrieving data via REST calls needed for the view.  Here is a very good [tutorial and example](http://blog.brunoscopelliti.com/show-route-only-after-all-promises-are-resolved)
* The current state is `$state.current.name`.
    
REST calls
----------

* Use Restangular.  [Restangular](https://github.com/mgonto/restangular) gives you a higher abstraction layer for your REST calls.  With Restangular, you can add request and response interceptors, define a base api URL, handle errors, and much more.
* If you do not use Restangular, use `$resource` instead of `$http` when possible. The higher level of abstraction will save you from redundancy.

Promises
--------

* Use promises (`$q`) instead of callbacks. It will make your code look more elegant and clean, and save you from callback hell.
* Get familiar with [`$q.when()`](http://stackoverflow.com/questions/16770821/how-does-angular-q-when-work) and $q.reject().  It can save a lot of code.  
* Here's a great [video from Dave Smith](https://www.youtube.com/watch?v=33kl0iQByME&list=PLhc_bKwZngxW_ZlY0NkaGkvKpiA_pzcZ-&index=12) on promises.


Controllers and $scope
----------------------

* Use the [Controller as](http://www.thinkster.io/pick/GmI3KetKo6/angularjs-experimental-controller-as-syntax) syntax and as the first line of your controller add the line `var model = this`.  This will place the controller object into $scope which you can use as your View Model.
* On the occasions you need to add properties and functions to $scope, as a general rule of thumb, you should avoid direct bindings to the $scope's properties, but instead bind to an object bound the $scope.  Per Google, this also reduces the risk around prototypal inheritance masking primitives.  Here is an excellent [video](https://egghead.io/lessons/angularjs-experimental-controller-as-syntax) which explains this concept.
* Rather than `ngInit` to initialize values on a scope, initialize the values in the controller.  The only appropriate use of `ngInit` is for aliasing special properties of `ngRepeat`.

Events
------

* Broadcast and Emit are expensive so use them judiciously.


Templates
---------

  - **`ng-if` vs `ng-show`**: When you are conditionaly hiding/showing form field use `ng-if` instead of `ng-show`. Because `ng-if` actually adds/removed the element from the DOM, the any validation on hidden fields will not fire.  `ng-show`, on the other hand, only hide DOM with css and therefore validations within `ng-show` will be fired.  The primary validation which will be affected is the `required` validation.  If you use a `required` validation inside a `ng-show`, use the `ng-required` attribute with the same condition as being used by the `ng-show` and that will solve the issue also.

  - **One-time binding syntax**: In Angular 1.3, use the one-time binding syntax `{{ ::value }}` where it makes sense

    ```html
    // avoid
    <h1>{{ vm.title }}</h1>

    // recommended
    <h1>{{ ::vm.title }}</h1>
    ```
    
    *Why?* : Binding once removes the watcher from the scope's `$$watchers` array after the `undefined` variable becomes resolved, thus improving performance in each dirty-check
    
  - **Consider $scope.$digest**: Use `$scope.$digest` over `$scope.$apply` where it makes sense. Only child scopes will update

    ```javascript
    $scope.$digest();
    ```
    
    *Why?* : `$scope.$apply` will call `$rootScope.$digest`, which causes the entire application `$$watchers` to dirty-check again. Using `$scope.$digest` will dirty check current and child scopes from the initiated `$scope`


Minification
------------

* If you are using a JavaScript minifier (and you should), you must use `gulp-ng-annotate` or `grunt-ng-anotate` to add annotation dependencies.  If this is not done, your app will not run after minification because Angular cannot tell what to inject.


When to use $rootScope
----------------------

Using Angular has it's gray areas in design decisions.  One of these areas is when to use \$rootscope.  \$rootScope is global, so it should be used carefully.  (For web developers, think of how you use Session.)

Use \$rootScope when you need to use an object globally throughout your whole app.  Many folks believe that a Service should be used to store global objects and properties.  I've tried this and it becomes too cumbersome to constantly inject the service into each controller which needs access to a global object or value.  I generally create an object under $rootScope called SESSION.  I then store all global values there.

> Do not use \$rootScope lazily.  You do not want to litter $rootScope unnecessarily.  Your first option should be to place the logic in a domain model if it is really needed globally.

Forms
-----

* Make sure that all your buttons have `type='submit'` or `type='button'`.  Some browsers will assume type is submit is no type is found.
* Use `ng-submit` on your forms instead of `ng-click` on the submit button

```
<form id="view_form" name="view_form" novalidate ng-submit="model.handleSubmit();">
	...
	<button type="submit">Next &rarr;</button>
</form>
```

Skip jQuery
-----------

* Angular apps do not depend on jQuery.  Directives and other Angular features should solve all of your needs. 
* The only real valid reason to include jQuery to use a jQuery plug-in which does not have an equivilent Angular directive.  In these cases, you will need to include jQuery and optionally wrap the plug-in inside a directive with the help of something like the [Angular UI jQuery Passthough directive](http://angular-ui.github.io/ui-utils/#/jq).  I know developers whom have had great success using this method.

Helpful Libraries and Directives
--------------------------------

* Use restful-ng-mock for mocking.  It is a wraps Angulars ngMock and it a lot easier to use.
* Look at the AngularUI Bootstrap directives.  Make sure you install using `bower install angular-bootstrap` instead of `bower install angular-ui-bootstrap`
* Consider using Restangular which wraps and abstracts $http.

Misc Tips
---------

* Learn EM6 and TypeScript as soon as possible.  You can down convert to EM5.  This will prepare you for Angular 2.0.

