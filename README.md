# AngularJS-TypeScript-BestPractices
 Best practices for AngularJS Applications using Typescript.
 
## Introduction 
 Most examples found throughout the web that use AngularJS with TypeScript are not following common Software Engineering practices. 
 Code is hard to understand and very complex, often is another layer built around the framework.
 These small snippets should show how an AngularJS 1.5 application can use TypeScript very easily and still stick to the ES6 standard. Typescript is only used for typings, no inheritance, interfaces and other boilerplate code is needed.
 
## Code Snippets 
 
### App Definition Example

```TypeScript
import {typescriptControllerModule} from "./typescript-controller";
import {typescriptServiceModule} from "./typescript-service";
import {typescriptComponentModule} from "./typescript-component";

const modules = [
    typescriptServiceModule,
    typescriptControllerModule,
    typescriptComponentModule
];

angular
    .module('typescript-app', modules.map(module => module.name));
```
* In order to stick to angulars module approach, it is considered a best practice to use on module per file and therefore also per angular service, controller or component.

### Component definition example

```TypeScript
import {Person} from "../person";

const typescriptComponent = {
    bindings: {
        people: '<',
        addPerson: '&'
    },
    template: `
        <ul ng-repeat="person in vm.people">
           <li>{{ person.firstName }} {{ person.lastName }}</li>
        </ul>
        <button ng-click="vm.addPerson()">Add new person</button>
        `,
    controllerAs: 'vm',
    controller: TypescriptComponentController
}

class TypescriptComponentController {
    people: Person[];
}

export const typescriptComponentModule = angular.module('typescriptComponentModule', [])
    .component('typescriptComponent', typescriptComponent);
```
* The component is considered a "dumb" or presentation component. It has only Input properties and no internal logic.

### Data Service example - fetch data asynchronously via Rest Service

```TypeScript
import {Person} from "../person";
import IHttpService = angular.IHttpService;
import IQService = angular.IQService;
import IPromise = angular.IPromise;
import IHttpPromiseCallbackArg = angular.IHttpPromiseCallbackArg;

export class TypescriptService  {

	constructor(private $http: IHttpService, private $q: IQService) {
	}

	getPeople(): IPromise<Person[]> {
		var deferred = this.$q.defer();
		this.$http.get('./personresturl').then(response => {
			deferred.resolve(response.data);
		}, err => {
			deferred.reject(err);
		});
		return deferred.promise;
	}
}

export const typescriptServiceModule = angular.module('typeScriptService_module', [])
	.service('typescriptService', TypescriptService);
```

* The data service returns a promise of data.
* Extracting data from the response and error handling can be easily done with interceptors.
* Injected angular dependencies get Injected via constructor and are typed. 
* Besides typings - this service is plain ES6.

### Controller example
```TypeScript
import {Person} from "../person";
import {TypescriptService} from "./typescript-service";

class TypescriptController {

    people:Person[] = [];

    constructor(private typescriptService:TypescriptService) {
    }

    $onInit() {
        this.typescriptService.getPeople().then(people => {
            this.people = people;
            }
        )
    }

    addPerson() {
        this.people.push({
            firstName: 'First Name',
            lastName: 'Last Name'
        })
    }
}

export const typescriptControllerModule = angular.module('typescriptControllerModule', [])
    .controller('typescriptController', TypescriptController);
```
* Controller is a smart component - can mutate state and fetch data via services. 
* Initial logic is done in the new angular 1.5 onInit lifecycle hook method.

### Example partial snippet
```HTML
<script src="pathToAppBundle"></script>

<div ng-app="typescript-app">
	<h2>TypeScript Example</h2>
	<div ng-controller="typescriptController as vm">
		<h2>People Component:</h2>
		<typescript-component people="vm.people" add-person="vm.addPerson()"></typescript-component>
	</div>
</div>
```

### Using an Interceptor: 
```TypeScript
// Interceptor
import IQService = angular.IQService;

export class ResponseInterceptor {

    private static instance: ResponseInterceptor;

    public static Factory($q: IQService) {
        ResponseInterceptor.instance =  new ResponseInterceptor($q);
        return ResponseInterceptor.instance;
    }

    constructor(private $q: IQService) {
    }

    public response(response) {
        return ResponseInterceptor.instance.$q.resolve(response.data);
    }

    public responseError(error) {
        return ResponseInterceptor.instance.$q.reject(error);
    }
}

// Service
import {Person} from "../person";
import IHttpService = angular.IHttpService;
import IQService = angular.IQService;
import IPromise = angular.IPromise;
import IHttpPromiseCallbackArg = angular.IHttpPromiseCallbackArg;

export class TypescriptService  {

	constructor(private $http: IHttpService, private $q: IQService) {
	}

	getPeople(): IPromise<Person[]> {
		return this.$http.get('./rest/styleguide');
	}
}

export const typescriptServiceModule = angular.module('typeScriptService_module', [])
	.service('typescriptService', TypescriptService);
	
// Intercepter Registration in app module
import {typescriptControllerModule} from "./typescript-controller";
import {typescriptServiceModule} from "./typescript-service";
import {typescriptComponentModule} from "./typescript-component";
import {ResponseInterceptor} from "./response-interceptor";
import IHttpProvider = angular.IHttpProvider;

const modules = [
    typescriptServiceModule,
    typescriptControllerModule,
    typescriptComponentModule
];

angular
    .module('typescript-app', modules.map(module => module.name))
    .config(['$httpProvider', ($httpProvider: IHttpProvider) => {
        $httpProvider.interceptors.push(ResponseInterceptor.Factory);
    }]);

```
* Interceptor reduces the amount of code needed in services and also reduces duplication
* It is, however, kind of magic - as it is neither explicit nor clear why the get request does return a Promise<T> instead of a Promise<IHttpPromiseCallbackArg<T>>
* There is also a bit of dirty static instance code needed in order to access this correctly. (this itself is undefined)
