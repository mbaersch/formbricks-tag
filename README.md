# Formbricks (unofficial)
**Custom Tag Template for Google Tag Manager**

This template is not provided or maintained by Formbricks.

Loads the [Formbricks](https://formbricks.com) JS SDK and initializes it with your workspace ID. Optionally identifies users, sets attributes, language and hidden fields and tracks actions.

![Template Status](https://img.shields.io/badge/Community%20Template%20Gallery%20Status-submitted-orange) ![Repo Size](https://img.shields.io/github/repo-size/mbaersch/formbricks-tag) ![License](https://img.shields.io/github/license/mbaersch/formbricks-tag)

---

This template replaces the Custom HTML snippet from the Formbricks setup instructions and can be used in containers without access to Custom HTML tags ([restricted tag types](https://developers.google.com/tag-platform/tag-manager/restrict)).

## Usage
Add the template to your container, create a new *Formbricks* tag, enter your workspace ID and trigger it when there is consent to load external resources. The tag loads the following script:

```
https://app.formbricks.com/js/formbricks.umd.cjs
```

After the script has loaded, `formbricks.setup()` is called with the workspace ID and the app URL. Setup runs only once per page, even if the tag fires multiple times. Every Formbricks tag needs the workspace ID, as any of them may be the first one to fire.

## Optional methods
All options are executed after Formbricks has finished its setup (`formbricks_setup_successful`). A tag firing before that point waits for the event; a tag firing later runs them right away. This way, one tag can load Formbricks and send data at the same time, and additional tags on other triggers (e.g. a login or checkout event) can send more data later.

The methods are called in this order, so a survey triggered by the tracked action already has all data:

1. **Identify user** (Enterprise Edition): `setUserId()`, `setEmail()` and `setAttributes()`. If the user ID is empty at runtime, no user data is sent, as Formbricks drops email and attributes without a user ID. Empty email or attribute values are skipped. Identified users count towards your monthly tracked users.
2. **Set language**: `setLanguage()` with an ISO code or an alias defined in Formbricks. Only relevant for multi-language surveys.
3. **Set hidden fields**: `setEmbeddedData()`, see below.
4. **Track action**: `track()` with the key of a code action defined in Formbricks (*Actions* > *Add action* > *Code*). Unknown keys are rejected with "Action with identifier '…' is unknown". New actions may take up to a minute to become available.

## Hidden fields
Check *Set hidden fields* to pass values to [hidden fields](https://formbricks.com/docs/surveys/general-features/hidden-fields) via `formbricks.setEmbeddedData()`. Each call adds to or overwrites existing keys; an empty or undefined value removes the key. Use *Clear existing hidden fields first* to start with an empty set (`formbricks.clearEmbeddedData()`).

Keys must match `^[a-z][a-z0-9_]*$`, and a value only ends up in a response if the survey declares the key as hidden field. Values are kept in memory only, so they have to be set again after each page load.

## Self-hosted Formbricks
The *App URL* field defaults to `https://app.formbricks.com`. When using a self-hosted instance, enter its base URL (e.g. `https://surveys.example.com`) and add `https://surveys.example.com/js/formbricks.umd.cjs` to the *Injects scripts* permission in the template editor. Otherwise the script will be blocked.
