---
title: Custom OAuth provider
---

# Building your custom OAuth provider

You can use an OAuth provider that isn't built-in by using a custom object.

As an example of what this looks like, this is the provider object returned for the Google provider:

```js
{
  id: "google",
  name: "Google",
  type: "oauth",
  wellKnown: "https://accounts.google.com/.well-known/openid-configuration",
  authorization: { params: { scope: "openid email profile" } },
  idToken: true,
  checks: ["pkce", "state"],
  profile(profile) {
    return {
      id: profile.sub,
      name: profile.name,
      email: profile.email,
      image: profile.picture,
    }
  },
}
```

As you can see, if your provider supports OpenID Connect and the `/.well-known/openid-configuration` endpoint contains support for the `grant_type`: `authorization_code`, you only need to pass the URL to that configuration file and define some basic fields like `name` and `type`.

Otherwise, you can pass a more full set of URLs for each OAuth2.0 flow step, for example:

```js
{
  id: "kakao",
  name: "Kakao",
  type: "oauth",
  authorization: "https://kauth.kakao.com/oauth/authorize",
  token: "https://kauth.kakao.com/oauth/token",
  userinfo: "https://kapi.kakao.com/v2/user/me",
  profile(profile) {
    return {
      id: profile.id,
      name: profile.kakao_account?.profile.nickname,
      email: profile.kakao_account?.email,
      image: profile.kakao_account?.profile.profile_image_url,
    }
  },
}
```

Replace all the options in this JSON object with the ones from your custom provider - be sure to give it a unique ID and specify the required URLs, and finally add it to the providers array when initializing the library:

```js title="pages/api/auth/[...nextauth].js"
import TwitterProvider from "next-auth/providers/twitter"
...
providers: [
  TwitterProvider({
    clientId: process.env.TWITTER_ID,
    clientSecret: process.env.TWITTER_SECRET,
  }),
  {
    id: 'customProvider',
    name: 'CustomProvider',
    type: 'oauth',
    scope: ''  // Make sure to request the users email address
    ...
  }
]
...
```
