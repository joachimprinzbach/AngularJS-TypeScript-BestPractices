# AngularJS-TypeScript-BestPractices
 Best practices for AngularJS Applications using Typescript.

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
