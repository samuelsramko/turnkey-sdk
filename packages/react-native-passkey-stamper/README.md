# @turnkey/react-native-passkey-stamper

[![npm](https://img.shields.io/npm/v/@turnkey/react-native-passkey-stamper?color=%234C48FF)](https://www.npmjs.com/package/@turnkey/react-native-passkey-stamper)

This package contains a React Native passkey stamper. It uses [`react-native-passkey`](https://github.com/f-23/react-native-passkey) to do the heavy lifting. This stamper is meant to be used with [`@turnkey/http`](https://www.npmjs.com/package/@turnkey/http)

## Installation

- Install both [`react-native-passkey`](https://www.npmjs.com/package/react-native-passkey) and `@turnkey/react-native-passkey-stamper` (this package) in your React Native project.
- Set up provisioning correctly to make sure your app is signed (needed for passkey functionality)
- Serve an `apple-app-site-association` file from your domain to reference your application. See [more details here](https://github.com/f-23/react-native-passkey?tab=readme-ov-file#configuration)
- Add a new "Associated Domains" capability for web credentials (`webcredentials:your.site.com`)

## Usage

### Create a new passkey

```ts
import { createPasskey } from "@turnkey/react-native-passkey-stamper";
import { v4 as uuidv4 } from "uuid";

// Returns authenticator params that can be used with sub-org creation, user creation, etc.
const authenticatorParams = await createPasskey({
  // Won't be visible to users, this is the name of the Turnkey resource
  authenticatorName: "End-User Passkey",
  rp: {
    id: "your.site.com",
    name: "Your App",
  },
  user: {
    // This ID isn't visible to users
    // NOTE: For Android, this must be a valid base64-encoded string
    id: uuidv4(),
    // ...but name and display names are. This is what's shown in the passkey prompt
    name: "Some Name",
    // displayName should be the same as "name"
    displayName: "Some Name",
  },
});
```

### Use an existing passkey

```ts
import { PasskeyStamper } from "@turnkey/react-native-passkey-stamper";
import { TurnkeyClient } from "@turnkey/http";

const stamper = new PasskeyStamper({
  rpId: "your.site.com",
});

// New HTTP client able to sign with passkeys!
const httpClient = new TurnkeyClient(
  { baseUrl: "https://api.turnkey.com" },
  stamper
);

// Now, send authenticated requests
httpClient.signTransaction(...)
```

## Demo app

Head over to [this repository](https://github.com/r-n-o/passkeyapp/tree/main) for a fully functional React Native app built with Expo.
