# Table of Contents

- [[#Resources|Resources]]
- [[#Introduction |Introduction]]
- [[#Single-SPA Setup|Single-SPA Setup]]
- [[#Splitting up applications|Splitting up applications]]
- [[#Shared Dependencies|Shared Dependencies]]
- [[#State Management|State Management]]
  - [[#UI State|UI State]]
  - [[#Global state|Global state]]
  - [[#Inter-app communication|Inter-app communication]]
- [[#Style Management|Style Management]]
- [[#Remarks|Remarks]]

# Resources

- [**Single-SPA Brief - By Samuel**](https://www.canva.com/design/DAF9StVLMd4/ixPbuuAWhrELQHzIo6btNA/edit?utm_content=DAF9StVLMd4&utm_campaign=designshare&utm_medium=link2&utm_source=sharebutton)
- https://martinfowler.com/articles/micro-frontends.html

# Introduction

Single-SPA Creator on what is a [MFE](https://youtu.be/3EUfbnHi6Wg?si=pLOOe8wWSifV0fn3)

Single-SPA is a top level router. When a route is active, it downloads and executes the code for that route.

The code for a route is called an "application", and each can (optionally) be in its own git repository, have its own CI process, and be separately deployed. The applications can all be written in the same framework, or they can be implemented in different frameworks.

# Single-SPA Setup

Recommended Approach (tentative)

- For the purposes of decoupling as much as possible we should take the approach of: if unsure, keep as 1 MFE unless very decoupled with large business model distinction
- [**Recommended Setup**](https://single-spa.js.org/docs/recommended-setup/)
- other recommended setups to consider
  - https://nx.dev/concepts/module-federation/micro-frontend-architecture

# Splitting up applications

[Different types of MFEs for Single-SPA JS](https://single-spa.js.org/docs/module-types)

need to decide what to split out from app (tentative)
https://single-spa.js.org/docs/separating-applications

**Options**

1. One code repo, one build
2. NPM packages
3. Monorepos
4. Dynamic Module Loading

#### Comparison[​](https://single-spa.js.org/docs/separating-applications#comparison "Direct link to Comparison")

|                | Separate code repositories possible | Independent CI builds                                                                       | Separate deployments                                                                        | Examples                                                                                                                                   |
| -------------- | ----------------------------------- | ------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| NPM Packages   | ✅                                  | ✅                                                                                          | ❌                                                                                          | [1](https://github.com/jualoppaz/single-spa-login-example-with-npm-packages)                                                               |
| Monorepo       | ❌                                  | ✅ [1](https://medium.com/labs42/monorepo-with-circleci-conditional-workflows-69e65d3f1bd0) | ✅ [1](https://medium.com/labs42/monorepo-with-circleci-conditional-workflows-69e65d3f1bd0) | [1](https://github.com/petermikitsh/learn-single-spa)                                                                                      |
| Module loading | ✅                                  | ✅                                                                                          | ✅                                                                                          | [1](https://github.com/react-microfrontends/) [2](https://github.com/vue-microfrontends/) [3](https://github.com/polyglot-microfrontends/) |

# Shared Dependencies

For performance, it is crucial that your web app loads large JavaScript libraries only once. Your framework of choice (React, Vue, Angular, etc) should only be loaded on the page a single time.

It is not advisable to make everything a shared dependency, because shared dependencies must be upgraded at once for every microfrontend that uses them. For small libraries, it is likely acceptable to duplicate them in each microfrontend that uses them. For example, react-router is likely small enough to duplicate, which is nice when you want to upgrade your routing one microfrontend at a time. However, for large libraries like react, momentjs, rxjs, etc, you may consider making them shared dependencies.

There are two approaches to sharing dependencies:

1. [In-browser modules with import maps](https://single-spa.js.org/docs/recommended-setup/#import-maps)
2. [Build-time modules with module federation](https://single-spa.js.org/docs/recommended-setup/#module-federation)

You may use either one, or both. We currently recommend only using import maps, although we have no objection to module federation.

# State Management

Video tutorial: https://www.youtube.com/watch?v=432thqUKs-Y

## UI State

https://single-spa.js.org/docs/recommended-setup/#ui-state

_If two microfrontends are frequently passing state between each other, consider merging them. The disadvantages of microfrontends are enhanced when your microfrontends are not isolated modules._

UI State, such as "is the modal open," "what's the current value of that input," etc. largely does not need to be shared between microfrontends. If you find yourself needing constant sharing of UI state, your microfrontends are likely more coupled than they should be. Consider merging them into a single microfrontend.

## Global state

https://single-spa.js.org/docs/recommended-setup/#state-management

The single-spa core team cautions against using redux, mobx, and other global state management libraries.

However, if you'd like to use a state management library, we recommend keeping the state management tool specific to a single repository / microfrontend instead of a single store for all of your microfrontends. The reason is that microfrontends are not truly decoupled or framework agnostic if they all must use a global store. You cannot independently deploy a microfrontend if it relies on the global store's state to be a specific shape or have specific actions fired by other microfrontends - to do so you'd have to think really hard about whether your changes to the global store are backwards and forwards compatible with all other microfrontends. Additionally, managing global state during route transitions is hard enough without the complexity of multiple microfrontends contributing to and consuming the global state.

Instead of a global store, the single-spa core team recommends using local component state for your components, or a store for each of your microfrontends. See the above section "[Inter-app communication](https://single-spa.js.org/docs/recommended-setup/#inter-app-communication)" for more related information

## Inter-app communication

_A good architecture is one in which microfrontends are decoupled and do not need to frequently communicate. Route-based single-spa applications inherently require less inter-app communication._

There are three things that microfrontends might need to share / communicate:

1. Functions, components, logic, and environment variables.
2. API data
3. UI state

Functions, components, logic, and environment variables:

We recommend using [cross microfrontend imports](https://single-spa.js.org/docs/recommended-setup/#cross-microfrontend-imports) to share functions, components, logic, and environment variables.

# Style Management

- https://github.com/single-spa/single-spa/issues/556

#### Styleguide

Reference: https://single-spa.js.org/docs/ecosystem-css/

example: https://github.com/react-microfrontends/styleguide

Shared CSS can be seen in the above example

#### Utility Modules

https://single-spa.js.org/docs/recommended-setup/#utility-modules-styleguide-api-etc

A "utility module" is an in-browser JavaScript module that is not a single-spa application or parcel. In other words, it's only purpose is to export functionality for other microfrontends to import.

Common examples of utility modules include styleguides, authentication helpers, and API helpers. These modules do not need to be registered with single-spa, but are important for maintaining consistency between several single-spa applications and parcels.

# Remarks

### TODO

[Official Examples]

1. Find suitable architecture (basically every MFE needs to interact with the map)
2. Find suitable layout (basically every MFE needs to interact with the map)
3. Look at security concerns specifically content security policy(in the works by Wei Hong's team)

#### Comments

_Microfrontends come with a lot of baggage and they make simple things terribly hard. Deployment becomes more complicated - you have to think about if you need a microfrontend registry containing multiple versions of a microfrontend, such that teams would not be forced to immediately react on other teams changes. You also have to setup a reverse proxy, just to load assets without getting a CORS error. Loading an image or a static translation file just does not work anymore without such reverse proxy. Routing can be tricky when you add web components into the mix to increase technological flexibility... The list goes on and on, but the only benefit they would deliver is the independent deployment and lets be honest, who the hell has that many frontend teams working on one product that something like that makes sense. and even then there might be easier organizational approaches than microfrontends. Big no from my side._

- https://www.reddit.com/r/Angular2/comments/18grzjl/are_micro_frontends_a_good_fit/

_This is a pretty dated article but the core concepts are still valid. Micro-frontends IMO are an excellent way to organize a large web app, especially if shared by multiple teams._

_HOWEVER you need to be very careful. Micro-frontends tend to introduce a tradeoff between independence and bundle size. The more independent your MFEs are, the more code duplication (mainly dependencies) there is, blowing up your overall application bundle size. The more you share code (mainly dependencies) between your MFEs, the more you create tighter coupling between them and thus make the architecture more brittle._

_This is why I would only recommend micro-frontends when using module federation, a feature introduced in webpack 5 and now supported by most bundlers. It is an exceptional tool that, among many other things, allows for code sharing between MFEs but in such a way that it is highly resilient to changes._

- https://www.reddit.com/r/reactjs/comments/12f6ecr/microfrontends/

**Architecture**
[Utility Modules for Shared State & Style Guide](https://single-spa.js.org/docs/recommended-setup/#applications-versus-parcels-versus-utility-modules)

With MFE architecture, a large application is split into:

1. A single **Host** application that references external...
2. **Remote** applications, which handle a single domain or feature.

- main skeletal microFE - main app with top navbar, sidebar, map
- each module can be a microFE (e.g. incident, opslog, VAP, OVH)
- state management
  - identify states in a microFE that may possibly affect another state in another microFE)
  - ideally UI state should be independent in each microFE (e.g. toggle some settings in settings page which affects UI in another microFE)
  - many modules interact with map, making it coupled together
    - map is the single thing common across all microFEs
    - NOTE: sharing context between microFEs is an anti-pattern since it eliminates the benefits of independent deployments
    - will need an API/contract to this handle this sync between map and microFEs

**Code structure**

- identify parts in each microFE that is required throughout the app
  - authentication & authorisation (hooks used can be brought out into a utility module)
  - styles (import from self-created styleguide utility module)
- decide on code arrangement (see part on Splitting up Applications)
  1.  One code repo, one build (webclient is huge, would stay away from this due to long build times)
  2.  NPM packages
  3.  Monorepos
  4.  Dynamic Module Loading

**Local Development & Routing**

- Routing for local development
  - https://www.youtube.com/watch?v=vjjcuIxqIzY&list=PLLUD8RtHvsAOhtHnyGx57EYXoaNsxGrTU&index=5&ab_channel=JoelDenning
- Rollup
  - https://www.youtube.com/watch?v=I6COIg-2lyM&ab_channel=JoelDenning
