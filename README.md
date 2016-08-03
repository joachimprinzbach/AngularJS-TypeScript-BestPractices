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
