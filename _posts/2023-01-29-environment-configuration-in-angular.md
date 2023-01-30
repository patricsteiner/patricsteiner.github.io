---
layout: post 
title: Environment Configuration in Angular 
image: environment.jpg 
categories: [angular, software-architecture]
---

There are different ways of configuring Angular applications. By default, new Angular projects are set up to store configuration in the code. But there are other approaches worth considering, especially once you start building larger projects.

# The Angular way: storing config in the code
New Angular projects contain a `environment.ts` and a `environment.prod.ts` file. These files can be used to
set environment-specific config parameters, such as for example the firebase config:

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

When building the app with `ng build`, Angular will replace the `environment.ts` file with the `environment.prod.ts` file. This happens because the default build configis `production`. And if we go ahead and check `Angular.json`, we can see that in the `production` config, the environment file is replaced:

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

To use a different build config, we can pass the `--configuration` flag with the desired config, for example `--configuration=development`. And we could easily define additional environments, such as for example a `preprod` config with a `environment.preprod.ts` file.

## Problems when storing config in code
This standard Angular way of configuring the application is very simple - everything you need is already setup by Angular and all config is stored in the project itself, so it's easy to find everything. However, there are some downsides too:

- Config is set at build time --> need separate builds for different environments
- Config for all environments must be checked in to source control
- CI pipeline takes longer since you cannot clone a tested artifact from dev to prod but instead need to rebuild it
- --> Violates the [third principle of The Twelve-Factor App](https://12factor.net/config)


# A different approach: Separate config from code
Instead of storing the config in the project itself, a better way might be to make the build independent of the environment. We can achieve this by injecting the config at release-time or reading it at run-time from a server.

## Inject config at release-time
In this approach, we create our own json config files, store them in the `assets` folder and stay away from `environment.ts`: 

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

### Advantages
- Separation of config and code --> only build once --> shorter CI/CD pipeline.
- `config.json` file can be replaced in the release-pipeline by the CI server.
- Not necessary to check in configuration for all environments to source control.

### Disadvantages
- More complicated to set up.
- Artifacts can still not be cloned from dev to prod, since they contain different configurations, we need to set up a separate release pipeline instead.

> _**NOTE:**  Instead of using the CI server to replace the config asset, we could also include all configs as assets and then fetch the correct one according to the current URL. However, this hard-couples everything to the URL, which might not be desirable._


## Reading config at run-time from a server
We could go one step further and avoid the config file altogether and instead rely on a dedicated endpoint to serve the configuration.

### Advantages
- Simple CI pipeline, no fancy replacement logic needs to happen.
- Artifact can be 1:1 cloned from dev to prod in an instant without any adjustments needed.

### Disadvantages
- We need a dedicated endpoint to serve the config.
- An extra network request is needed to fetch that config.
- Hosting needs to be set up to redirect a relative URL (e.g. /api/config) to an absolute one pointing to the correct URL of the current environment

> _**NOTE:**  As an alternative to redirect the relative URL, we could also have one single endpoint that serves the correct config based on the request URL. But once again, this couples the config to the URL, which does not feel very clean._

# Conclusion
It's hard to say something is clearly better or worse - it's all tradeoffs. However, in my opinion, separating code from config seems like a very reasonable idea for everything that is more than a toy project with only one environment. There is some extra setup effort involved, but it will likely pay off in the long run.
