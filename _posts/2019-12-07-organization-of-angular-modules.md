---
layout: post
title: Organization of Angular Modules
image: building-blocks.jpg
---

As a backend engineer, my angular journey began very gently. I started working on small frontend issues when no dedicated frontend engineer was available, to get things done more quick instead of waiting for someone else to do it.

Using the given codebase that I could take as an example, doing this was not hard at all, even though I didn't really know what I was doing. Things just continued like this. I got more and more familiar with angular and started writing my own apps. But at some point I realized that I never actually got a proper introduction or read any tutorial about the basics of angular, such as for example how to organize my angular modules.

Time to change that! I read up on the [official documentation](https://angular.io/guide/ngmodules) and [some](https://medium.com/@cyrilletuzi/understanding-angular-modules-ngmodule-and-their-scopes-81e4ed6f7407) [blogposts](https://medium.com/@cyrilletuzi/architecture-in-angular-projects-242606567e40) and wrote a very concise summary of the most important aspects I learned about modules and providers in angular. Here you are:

## Types of Modules
Modules are the building blocks of an angular application. These modules can be roughly categorized in the following categories.

### Domain Module
- Example: Edit customer, place order
- Mostly declarations, only top component is exportet
- Usually no providers (only if they share the lifetime of the module)
- Usually only imported once by a bigger module

### Routed Module
- Domain Module with a route (-> implies that all lazy modules are also routed modules)
- no exports
- never imported by any other module (would not be lazy anymore)
- Usually no providers (only if they share the lifetime of the module)

### Routing Module
- Companion module for any other module
- Defines routes, guards, router config
- Only imported by companion module

### Service Module
- Only providers
- Only imported by root module
- No exports

### Widget Module
- Example: UI component library, SharedModule
- Mostly declarations, most of them exported
- Usually no providers
- Imported in every module that needs the widgets

## Service Providers
Starting with Angular version 6 it's almost always best to just provide services in the root injector using `providedIn: 'root'`, instead of providing them in modules. This also works for lazy loaded modules, since their injector is a child from the root injector.

