## Angular Routing Lab

| Objectives |
| :--- |
| Explore Routing in Single Page Apps |
| Create route-specific view templates and controllers. |
| Create RESTful Index and Show routes for a Wine resource. |

In this lab we will be working with templates and routing in [Angular JS](https://angularjs.org/).

When a user goes to `/` they should see a list of wines (`wines#index`).
When a user goes to `/wines/:id` they should see a single wine (`wines#show`).

Our data (a list of wines) lives at the bottom of `app.js`. Eventually we will use AJAX to retrieve this so we can perform all CRUD operations, but for now it's hardcoded.

### Hash Parameters
<a href="#setup">Setup</a><br/>
<a href="#ui-router">ui-router</a><br/>
<a href="#wine-list-challenge">Wine List Challenge</a><br/>
<a href="#wine-show-challenge">Wine Show Challenge</a>

### Setup
* Clone this repo.
* **Make sure to run `bower install`.**
* Note: We will need to run a local server once we start playing with routing.
    - In the application directory run `python -m SimpleHTTPServer 8000`.
    - Then open your browser to "localhost:8000" (or similar).

## [ui-router](http://angular-ui.github.io/ui-router/)
A Single Page App needs a way of responding to user navigation. In order to perform "front-end routing", we need a way to capture URL changes and respond to them. For example, if the user clicks on a link to "/wines/1414", we need our Angular application to know how to respond (what templates, controllers, and resources to use). What we *don't* want to happen is for the request to reach the server.

1. Include `ui-router`:
    * Run `bower install -s angular-ui-router` in your terminal.
    * Go to `index.html` and uncomment the ui-router script.
    * Add an `ui-view` element inside the `div` on `index.html#23`.
2. Configure your routes:
    * In `app.js`, we need to add the `ui.router` module:

        ``` javascript
            var app = angular.module('wineApp', ['ui.router']);
        ```

    * Next, we need to add our first route:

        ``` javascript
            app.config(function($locationProvider, $stateProvider, $urlRouterProvider) {

            $urlRouterProvider.otherwise("/");

            $stateProvider
              .state('home', {
                url: '/',
                template: "Home!"
              })
            })
        ```
3. Fire up your server:
    * From your application directory, run `python -m SimpleHTTPServer 8000`.
    * Then open your browser to "localhost:8000" (or similar).
    * You should see "Home!"

4. Use a template file instead of a string:
    * Change `template: 'Home!'` to `templateUrl: '/templates/wines-index.html'`
    * Refresh, you should see the content of `/templates/wines-index.html`.

5. Set up a controller:
    * It's time to attach a template to a specific controller, all we have to do is modify our route so that it looks like this:

        `app.js`
        ``` javascript
            app.config(function($locationProvider, $stateProvider, $urlRouterProvider){
              $stateProvider
              .state('wines-index', {
                url: "/wines",
                templateUrl: "templates/wines-index.html",
                controller: "WinesIndexCtrl"
              })
            })
        ```

    * Now let's add a single value to `WineIndexCtrl` (in `app.js`):

        ``` javascript
            app.controller('WinesIndexCtrl', function($scope){
              console.log("Wine Index");
              $scope.hello = "wine index controller is working!";
            })
        ```
    * And update our `wines-index.html` template to include:
        - `{{hello}}` in a `div`, `p`, or `h1` tag *Note: Angular uses double curly brackets for injecting variables, like this: `{{ variable.property }}`. *
    * When you refresh you should see: "wine index controller is working!" *Note: This is because the WineIndexCtrl contains the $scope.hello value. $scope is a strange, global-esque variable.*

    `wines-index.html`
    ```html
    <div class="container" ng-controller="WinesIndexCtrl">
      <p>{{ hello }}</p>
      <ul>
        /* Wines will go here */
      </ul>
    </div>
    ```

### Wine List Challenge
Can you display a list of all the wines on the wines index page? (Start by using the mock data object called `ALL_WINES` at the bottom of `app.js`).

<!-- Sneaky review -->

What directive would you use to loop through a list of wines?

Can you get it working using the `WineService`, without using `ALL_WINES` directly?
- How would you [inject](https://docs.angularjs.org/guide/di) the `WineService` into `WineIndexCtrl`?
- How would you **query** *all* of the wines?

### HTML5 Mode
Add, or uncomment, the following in your route configuration so that we don't have to use the query hash for navigation:
``` javascript
    $locationProvider.html5Mode({
      enabled: true,
      requireBase: false
    });
```

Now instead of linking to `#/wines/1424` we can link to "/wines/1424".

### Wine Show Challege
To setup a `wines#show` route, we need to first figure out how to capture the id parameter in the URL.

For each of your wines on the `wines#index` page, let's add a link:
``` html
    /* Normal string interpolation */
    <h5><a href="/wines/{{wine.id}}">{{wine.name}}</a></h5>
    /* state-based routing with ui-router */
    <a ui-sref="wines-show({id: wine.id})">See more</a>
```

When a user navigates to `/wines/:id` we want to display the wine with the matching id!

First, update the route:

`app.js`
``` javascript
$stateProvider
  .state('wines-index', {
    url: '/wines',
    templateUrl: 'templates/index.html',
    controller: 'WinesIndexCtrl'
  })
  .state('wines-show', {
    url: '/wines/:id', // the "id" parameter
    templateUrl: 'templates/show.html',
    controller: 'WinesShowCtrl'
  })
```

Next, we need to inject a new module into `WinesShowCtrl` called `$routeParams`:

`app.js`
``` javascript
app.controller('WinesShowCtrl', function ($scope, WineService, $routeParams) {
    console.log($routeParams.id);
});
```

`templates/wines-show.html`
```html
<div class="container" ng-controller="WinesShowCtrl">
  <h2>{{ wine.name }}</h2>
</div>
```

Can you get it working now that you know how to grab the correct `id`? What method (*hint: HTTP verb*) could **get** you that specific wine? How would you display only that individual wine? *hint: how did you display the greeting on your wines#index page?*

### Stretch: Prettify || More Routing
Go crazy. Use Bootstrap to make a fancy index and show page, listing out all the wine info, and showing an image for each of them.

Alternatively, try writing some functions in your controllers (attached as properties of $scope) that modify the WineService to simulate CRUD operations. What would you need for this? A button? An [event handler](https://docs.angularjs.org/api/ng/directive/ngClick)?

Here are some of the wine fields we have to work with:

``` json
{
    "id": 1429,
    "created_at": "2015-10-13T01:30:28.631Z",
    "updated_at": "2015-10-13T01:30:28.631Z",
    "name": "CHATEAU LE DOYENNE",
    "year": "2005",
    "grapes": "Merlot",
    "country": "France",
    "region": "Bordeaux",
    "price": 12,
    "description": "Though dense and chewy, this wine does not overpower with its finely balanced depth and structure. It is a truly luxurious experience for the senses.",
    "picture": "http://s3-us-west-2.amazonaws.com/sandboxapi/le_doyenne.jpg"
}
```
