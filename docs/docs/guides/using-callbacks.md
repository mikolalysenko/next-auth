# Using Callbacks

NextAuth.js allows you to hook into various parts of the authentication flow via our [built-in callbacks](/configuration/callbacks).

For example, to pass a value from the sign-in to the frontend, client-side, you can use a combination of the [`session`](/configuration/callbacks#session-callback) and [`jwt`](/configuration/callbacks#jwt-callback) callback like so:

```javascript
...
callbacks: {
  async jwt({ token, account }) {
    // Persist the OAuth access_token to the token right after signin
    if (account) {
      token.accessToken = account.access_token
    }
    return token
  },
  async session({ session, token, user }) {
    // Send properties to the client, like an access_token from a provider.
    session.accessToken = token.accessToken
    return session
  }
}
...
```

Now whenever you call `getSession` or `useSession`, the data object which is returned will include the `accessToken` value.

```js
import { useSession, signIn, signOut } from "next-auth/react"

export default function Component() {
  const { data } = useSession()
  const { accessToken } = data

  return <div>Access Token: {accessToken}</div>
}
```
