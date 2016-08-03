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
