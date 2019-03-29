---
sidebar: auto
---

# Options Reference

## Vssue Options

Vssue Options (type `VssueOptions`) is set when calling `Vue.use()`:

```js
import Vue from 'vue'
import Vssue from 'vssue'

Vue.use(Vssue, {
  // Vssue Options
})
```

### api

- __Type__: `VssueAPI.Constructor`
- __Details__:

  The constructor of the VssueAPI class that implements `VssueAPI.Instance` interface.

  Vssue will use it to create an instance of VssueAPI, and use the VssueAPI instance to send requests to platform.

  The name of VssueAPI packages format as `@vssue/api-${platform}-${version}`, for different API version of different platforms.

  Check [Supported Platforms](../guide/supported-platforms.md) for available VssueAPI pacakges.

- __Example__:

  ```js
  import Vue from 'vue'
  import Vssue from 'vssue'
  import GithubV3 from '@vssue/api-github-v3'

  Vue.use(Vssue, {
    api: GithubV3,
    // Other Vssue Options
  })
  ```

  ::: tip
  In fact, the platform itself is "opaque" to Vssue. Vssue just loads comments via the API instance, and does not care about what the platform is and what the version of API is. This is how Vssue supports different platforms.

  So it is easy to support new platforms / new version of API by creating new API packages.
  :::

### owner

- __Type__: `string`
- __Details__:

  The owner's name of repository to store the issues and comments. Could be the name of a __user__ or an organization (__Github Organization__ / __Gitlab Group__ / __Bitbucket Team__).

  Together with `repo`, Vssue could locate the repository on the platform.

- __Reference__: [repo](#repo)

### repo

- __Type__: `string`
- __Details__:

  The name of repository to store the issues and comments.

  Together with `owner`, Vssue could locate the repository on the platform.

- __Reference__: [owner](#owner)

::: tip
The common pattern of repository's URL is `` `${baseURL}/${owner}/${repo}`  ``:

- Github: `https://github.com/${owner}/${repo}`
- Gitlab: `https://gitlab.com/${owner}/${repo}`
- Bitbucket: `https://bitbucket.org/${owner}/${repo}`
:::

### clientId

- __Type__: `string`
- __Details__:

  The `client_id` introduced in [OAuth2 spec](https://tools.ietf.org/html/rfc6749#section-2.3.1).

  It is the client identifier that issued by the platform. You will get it after you set up OAuth App.

  Vssue will use it to get user's access token.

- __Reference__:
  - [Set up OAuth App](../guide/supported-platforms.md)

### clientSecret

- __Type__: `string`
- __Default__: `undefined`
- __Details__:

  The `client_secret` introduced in [OAuth2 spec](https://tools.ietf.org/html/rfc6749#section-2.3.1).

  It is the client secret that generated by the platform. You will get it after you set up OAuth App.

  When work with some of the platforms, Vssue will use it together with `clientId` to get user's access token.

  ::: tip
  Some platforms (e.g. Bitbucket and GitLab) support [Implicit Grant](https://tools.ietf.org/html/rfc6749#section-4.2), so we don't need `clientSecret` for those platforms.

  However, some platforms (e.g. GitHub) do not support it now, so `clientSecret` is required for those platforms.
  :::

- __Reference__:
  - [clientId](#clientid)
  - [proxy](#proxy)
  - [Set up OAuth App](../guide/supported-platforms.md)
  - [Security](../guide/security.md)

### baseURL

- __Type__: `string`
- __Default__: `undefined` (according to the platform)
- __Details__:

  This is the base URL of your platform.

  Default values for supported platforms are:

  - `'https://github.com'` for Github
  - `'https://gitlab.com'` for Gitlab
  - `'https://bitbucket.org'` for Bitbucket

  ::: warning ATTENSION
  Only when you choose to use __self-hosted__ platform should you set this option. (e.g. __GitLab Community / Enterprise Edition__ or __GitHub Enterprise Server__)
  :::

- __Reference__:
  - [GitHub OAuth App](../guide/github.md)
  - [GitLab Application](../guide/gitlab.md)

### state

- __Type__: `string`
- __Default__: `'Vssue'`
- __Details__:

  The `state` element introduced in [OAuth2 spec](https://tools.ietf.org/html/rfc6749#section-4.1.1).
  
  Vssue will send it with OAuth redirection and check if it is matched in callback.

  It's designed for preventing [CSRF](https://tools.ietf.org/html/rfc6749#section-10.12), but it's not so useful here as we put everything in a static page. So just ignore it or set it to anything you like.

### labels

- __Type__: `string`
- __Default__: `['Vssue']`
- __Details__:

  To set the labels of issues that Vssue uses.

  Vssue will only request those issues with the labels and ignore others. Together with `title`, `labels` will help to identify the corresponding issue of Vssue. If you use multiple labels by setting more strings, only issues with all those labels will be requested by Vssue.

  ::: tip
  Bitbucket does not support issue labels for now, so this option will be ignored if you are using Bitbucket.

  Github supports emoji in labels' name, e.g. `[':heart:Vssue', ':mailbox:Comments']`.
  :::

- __Reference__: [title](#title)

### prefix

- __Type__: `string`
- __Default__: `'[Vssue]'`
- __Details__:

  The title prefix for issues. Used for generating the actual title of the corresponding issue.

  For example, if the `prefix` is `'[Vssue]'` and the `title` is `'Vssue Demo'`, the actual title of the corresponding issue is `'[Vssue]Vssue Demo'`.

  It will be ignored if the type of `title` is `Function`.

- __Reference__: [title](#title)

### admins

- __Type__: `Array<string>`
- __Default__: `[]`
- __Details__:

  Array of username that has admin access to Vssue. The `owner` always has admin access.

  Users with admin access can delete all comments, while other users can only delete their own comments.

  Only `admins` can auto create corresponding issue if it does not exist.

  ::: tip
  If you want to auto create the issue when the `owner` is a organization rather than a user, you can add your username into `admins`.
  :::

- __Reference__: [owner](#owner)

### perPage <Badge text="v0.2+"/>

- __Type__: `number`
- __Default__: `10`
- __Details__:

  The default value of how many comments to show per page.

### locale <Badge text="v0.5+"/>

- __Type__: `string`
- __Default__: `undefined`
- __Details__:

  The locale language.

  If not set, Vssue will use one of `window.navigator.languages`, or fallback to `'en'`.

  ::: tip
  Vssue uses [vue-i18n](https://kazupon.github.io/vue-i18n/) for i18n, but it will not affect other parts of your Vue App.
  And if you already have vue-i18n in your project, it will not affect Vssue.

  Language packages locate in [src/i18n/langs](https://github.com/meteorlxy/vssue/tree/master/packages/vssue/src/i18n/langs) directory. Currently we have supported:

  - `'en'` (`'en-US'`)
  - `'zh'` (`'zh-CN'`)
  - `'pt'` (`'pt-BR'`)
  - `'ja'` (`'ja-JP'`)

  Contributions welcome for more languages support.
  :::

### proxy <Badge text="v0.6+"/>

- __Type__: `string | ((url: string) => string)`
- __Default__: `` url => `https://cors-anywhere.herokuapp.com/${url}` ``
- __Details__:

  Some platforms do not support Implicity Grant, so we have to request the API of the platform to get the access token.
  
  However, the access token API of the platforms do not support CORS (see [related issue of GitHub](https://github.com/isaacs/github/issues/330)). As Vssue is a pure front-end plugin, we have to use a proxy to request access token.

  By default, we use an open source CORS proxy service [cors-anywhere](https://github.com/Rob--W/cors-anywhere) for that.
  
  If you want to use your own proxy, you need to set this option.

  If the platform you use does not require `clientSecret`, this option will be ignored.

- __Example__:

  ```js
  proxy: url => `https://your.cors.porxy?target=${url}`
  ```

- __Reference__:
  - [clientSecret](#clientsecret)
  - [Security](../guide/security.md)

### issueContent <Badge text="v0.7+"/>

- __Type__: `((param: { options: Vssue.Options, url: string }) => string | Promise<string>)`
- __Default__: `({ url }) => url`
- __Details__:

  The content of issue that auto created by Vssue.

  Vssue will use the return value of the function as the content.
  
  The parameter includes two properties:

  - `options` is the options of Vssue.
  - `url` is the URL of current page, which is the default content.

- __Example__:

  ```js
  issueContent: ({ url }) => `This issue is auto created by Vssue to store comments of this page: ${url}`
  ```

  ::: tip
  The `issueContent` option is only used to auto create the corresonpding issue when it does not exist.

  If the issue already exists, Vssue will not try to update the content.
  :::

### autoCreateIssue <Badge text="v0.8.1+"/>

- __Type__: `boolean`
- __Default__: `true`
- __Details__:

  If `autoCreateIssue` is set to `false`, Vssue won't create issues for you, and you have to __create issues manually__.

## Component Props

### title

- __Type__: `string | ((options: VssueOptions) => string)`
- __Required__: `false`
- __Default__: `` options => `${options.prefix}${document.title}` ``
- __Details__:

  The title of issue that used by this Vssue component.

  - If the type is `string`, the actual title of issue is `` `${prefix}${title}` ``.
  - If the type is `Function`, the actual title of issue is the return value of the function. Notice that the first parameter of the function is the options of Vssue, and you can use them to generate the actual title.

  ::: warning ATTENSION
  When trying to load comments, Vssue will request the corresponding issue according to `labels` and `title`. If the issue does not exist, Vssue will try to create a new issue with `title`, `issueContent` and `labels`.

  In other words, `labels` and `title` is the identifier of the corresponding issue.
  
  So make sure that Vssue on different pages have different `title`s. Vssue with same `title` will correspond to the same issue, and share the same comments.
  :::

- __Reference__:
  - [prefix](#prefix)
  - [labels](#labels)
  - [issueContent](#issuecontent)

### issueId <Badge text="v0.2+"/>

- __Type__: `string | number`
- __Required__: `false`
- __Default__: `null`
- __Details__:

  The id of issue that used by this Vssue component.
  
  If `issueId` is set, these parameters will be ignored:

  - Options: `labels`, `prefix`, `issueContent` and `autoCreateIssue`
  - Props: `title`

  ::: danger ATTENSION
  If `issueId` is set, Vssue will use it to determine which issue to use directly, instead of requesting issues according to `labels` and `title`. This will make the initialization process of Vssue faster.

  In this case, however, you have to __create issues manually__. If the corresponding issue is not found, Vssue will not try to create a new issue for you. 
  :::

### options

- __Type__: `Object` (`Partial<VssueOptions>`)
- __Required__: `false`
- __Default__: `{}`
- __Details__:

  The properties in prop `options` will override those set by `Vue.use()`. Accepts all the properties in `VssueOptions`.

  You can take the options set by `Vue.use()` as __global__ / __default__ options, and take the prop `options` as __local__ options.

- __Reference__: [Vssue Options](#vssue-options)