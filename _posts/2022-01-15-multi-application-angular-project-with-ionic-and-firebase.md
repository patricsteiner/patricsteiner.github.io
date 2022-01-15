---
layout: post
title: Multi-Application Angular Project with Ionic and Firebase
image: ananas.jpg
categories: [angular, ionic, firebase, software-architecture]
---

Imagine you have multiple frontends and libraries that access the same firebase project and the same firestore database.
The general approach to this would be to either create one repository per frontend/library or create a big monorepo with all the code (eg. with npm workspaces). Having seperate repositories means more fragmentation, more dependency management and having no central place for your firebase configuration (unless you _also_ make a seperate repository for that, but that feels quite overkill IMO...). 

So for non-enterprise projects, there is another great solution: if all your code is angular-based, you can setup an angular workspace with multiple projects in it. This way you have a single place for all your configuration files (e.g. firebase, tsconfig, prettier, ...), easily put shared code in a library and have faster build times by sharing dependencies in the same node_modules folder.

Here's how that would look like:

```
myAngularWorkspace
├── projects
│   ├── b2bAngularApp
│   ├── b2cIonicApp
│   └── myLib
├── package.json
├── tsconfig.json
├── firebase.json
├── angular.json
├── node_modules
└── ...
```

## Setup
Sounds good? Here's how to set it all up. First, create a new angular workspace:

```
ng new myAngularWorkspace --no-create-application --prefix myApp --strict
```

_The prefix and strict parametes are optional, but I like adding them. The prefix tells angular to name your components with your own prefix instead of `app-<componentName>` and the strict flag makes sure you properly make use of the typescript and thus avoids bugs in the long term._

Now you can setup your applications in the workspace:

```
cd myAngularWorkspace
ng generate app b2bAngularApp --prefix myApp
ng generate app b2cIonicApp --prefix myApp
ng generate library myLib --prefix myApp
```

_Instead of using `ionic start` for the ionic app, I use `ng generate app` and then manually add the @ionic dependencies to package.json. This way I make sure the ionic cli does not mess up my angular configuration. Additional styles and assets like the ionicons can still be manually added to angular.json._

## Development
Since there are now multiple projects in the same angular workspace, we need to explicitly state which project we want to run the `ng` commands on, for example:

```
ng generate component myComponent --project myLib

ng serve b2bAngularApp --port 4200
ng serve b2cIonicApp --port 4201
ng build myLib --watch
```

For commands that we will use the time, we can write scripts in package.json, such as:

```json
 "scripts": {
    "start:b2b": "ng serve b2bAngularApp --port 4200",
    "start:b2c": "ng serve b2cIonicApp --port 4200",
    "watch:lib": "ng build myLib --watch",
    ...
 }
```

However, it can be even simpler if we are using firebase. Instead of starting each app individually, we can emulate firebase hosting to automatically serve each app locally by typing `firebase emulators:start` in the terminal. This will launch and output local URLs for every defined site in our project.

## Deployment
Firebase allows us to host multiple apps in the same project, and this is exactly what we are going to do. After creating your firebase project with `firebase init`, you can define multiple hosting sites in the [firebase console](https://console.firebase.google.com/), each with a seperate domain.

To be ready to easily deploy every project from our workspace, we need to configure two more things. First in `.firebaserc` :

```json
{
  "projects": {
    "default": "myFirebaseProject"
  },
  "targets": {
    "myFirebaseProject": {
      "hosting": {
        "b2bAngularApp": [
          "b2bAngularApp"
        ],
        "b2cIonicApp": [
          "b2cIonicApp"
        ]
      }
    }
  }
}
```

And then in `firebase.json`:
```json
{
  "hosting": [
    {
      "target": "b2bAngularApp",
      "public": "dist/b2bAngularApp",
      "ignore": ["firebase.json", "**/.*", "**/node_modules/**"],
      "rewrites": [
        {
          "source": "**",
          "destination": "/index.html"
        }
      ]
    },
    ...
}
```

And that's it! we can now deploy all our apps together by simply running:
```
ng build b2bAngularApp
ng build b2cIonicApp
ng build myLib

firebase deploy
```

Done 😃🎉