---
layout: post 
title: Environment Configuration in Angular 
image: environment.jpg 
categories: [angular, software-architecture]
---

There are different ways of configuring Angular applications. By default, new Angular projects are set up to store configuration in the code. But there are other approaches worth considering, especially once you start building larger projects.

# The Angular way: storing configuration in the code
By default, new Angular projects contain a `environment.ts` and a `environment.prod.ts` file. These files can be used to
set environment-specific configuration parameters, such as for example the firebase configuration:

```typescript
// environment.ts
export const environment = {
    production: false,
    firebase: {
        appId: '11111',
        projectId: 'foobar-dev',
        // ...
    },
};

// environment.prod.ts
export const environment = {
    production: true,
    firebase: {
        appId: '22222',
        projectId: 'foobar-prod',
        // ...
    },
};
```

When building the app with `ng build`, Angular will replace the `environment.ts` file with the `environment.prod.ts` file. This happens because the default build configuration is `production`. And if we go ahead and check `Angular.json`, we can see that in the `production` configuration, the environment file is replaced:

```typescript
// Angular.json
{
  "configurations": {
    "development": {
      "buildOptimizer": false,
      // ...
    },
    "production": {
      "fileReplacements": [
        {
          "replace": "src/environments/environment.ts",
          "with": "src/environments/environment.prod.ts"
        }
      ]
    }
  }
}
```

To use a different build configuration, we can pass the `--configuration` flag with the desired configuration, for example `--configuration=development`. And we could easily define additional environments, such as for example a `preprod` configuration with a `environment.preprod.ts` file.

## Advantages of this approach
- Simple - everything you need is already setup by Angular
- All configuration is stored in the Angular project itself, so it's easy to find

## Problems with this approach
- Configuration is set at build time --> need separate builds for different environments
- Configuration for all environments must be checked in to source control
- CI pipeline takes longer since you cannot clone a tested artifact from dev to prod but instead need to rebuild it
- --> Violates the [third principle of The Twelve-Factor App](https://12factor.net/config)

# A different approach: Separate config from code
Instead of storing the configuration in the project itself, a better approach might be to make the build independent of the environment. We can achieve this by injecting the configuration at release-time or reading it at run-time from a server.

## Injecting configuration at release-time
In this approach, we create our own json configuration files, store them in the `assets` folder and stay away from `environment.ts`: 

```typescript
// config.json
{
    "firebase": {
        "appId": "11111", 
        "projectId": "foobar-dev"
        // ...
    }
}
```

Then during run-time, we can use a basic `fetch()` request or Angular's `HttpClient` to read that file and use the values in our app.

### Advantages of this approach
- Separation of config and code --> only build once --> shorter CI/CD pipeline
- `config.json` file can be replaced in the release-pipeline by the CI server
- Not necessary to check in configuration for all environments to source control

### Disadvantages
- more complicated to set up
- artifacts can still not be cloned from dev to prod, since they contain different configurations, we need to se tup a separate release pipeline instead

### Remark
Instead of using the CI server to replace the config asset, we could also include all configs as assets and then fetch the correct one according to the current URL. However, this hard-couples everything to the URL, which might not be desirable.

## Reading config at run-time from a server
We could go one step further and avoid the config file altogether and instead rely on a dedicated endpoint to serve the configuration.

### Advantages of this approach
- Simple CI pipeline, no fancy replacement logic needs to happen
- Artifact can be 1:1 cloned from dev to prod in an instant without any adjustments needed

### Disadvantages
- We need a dedicated endpoint to serve the config
- An extra network request is needed to fetch that config
- Hosting needs to be set up to forward a relative URL (e.g. /api/config) to an absolute one pointing to the correct URL of the current environment
  - Alternatively we could also have one single endpoint that serves the correct config based on the request URL. But once again, this couples the config to the URL, which does not feel very clean.

# Conclusion
It's hard to say something is clearly better or worse - it's all tradeoffs. However, in my opinion, separating code from config seems like a very reasonable idea for everything that is more than a toy project with only one environment. There is some extra setup effort involved, but it will likely pay off in the long run.
