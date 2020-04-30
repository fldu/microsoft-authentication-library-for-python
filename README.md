# Microsoft Authentication Library (MSAL) for Python


| `dev` branch | Reference Docs | # of Downloads
|---------------|---------------|----------------|
 [![Build status](https://api.travis-ci.org/AzureAD/microsoft-authentication-library-for-python.svg?branch=dev)](https://travis-ci.org/AzureAD/microsoft-authentication-library-for-python) | [![Documentation Status](https://readthedocs.org/projects/msal-python/badge/?version=latest)](https://msal-python.readthedocs.io/en/latest/?badge=latest) | [![Download monthly](https://pepy.tech/badge/msal/month)](https://pypistats.org/packages/msal)

The Microsoft Authentication Library for Python enables applications to integrate with the [Microsoft identity platform](https://aka.ms/aaddevv2). It allows you to sign in users or apps with Microsoft identities ([Azure AD](https://azure.microsoft.com/services/active-directory/), [Microsoft Accounts](https://account.microsoft.com) and [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) accounts) and obtain tokens to call Microsoft APIs such as [Microsoft Graph](https://graph.microsoft.io/) or your own APIs registered with the Microsoft identity platform. It is built using industry standard OAuth2 and OpenID Connect protocols

Not sure whether this is the SDK you are looking for your app? There are other Microsoft Identity SDKs
[here](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Microsoft-Authentication-Client-Libraries).

Quick links:

| [Getting Started](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-python-webapp) | [Docs](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [Samples](https://aka.ms/aaddevsamplesv2) | [Support](README.md#community-help-and-support)
| --- | --- | --- | --- |

## Installation

You can find MSAL Python on [Pypi](https://pypi.org/project/msal/).
1. If you haven't already, [install and/or upgrade the pip](https://pip.pypa.io/en/stable/installing/)
   of your Python environment to a recent version. We tested with pip 18.1.
2. As usual, just run `pip install msal`.

## Versions

This library follows [Semantic Versioning](http://semver.org/).

You can find the changes for each version under
[Releases](https://github.com/AzureAD/microsoft-authentication-library-for-python/releases).

## Usage

Before using MSAL Python (or any MSAL SDKs, for that matter), you will have to
[register your application with the Microsoft identity platform](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-register-an-app).

Acquiring tokens with MSAL Python follows this 3-step pattern.
(Note: That is the high level conceptual pattern.
There will be some variations for different flows. They are demonstrated in
[runnable samples hosted right in this repo](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample).
)


1. MSAL proposes a clean separation between
   [public client applications, and confidential client applications](https://tools.ietf.org/html/rfc6749#section-2.1).
   So you will first create either a `PublicClientApplication` or a `ConfidentialClientApplication` instance,
   and ideally reuse it during the lifecycle of your app. The following example shows a `PublicClientApplication`:

   ```python
   from msal import PublicClientApplication
   app = PublicClientApplication(
       "your_client_id",
       "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here")
   ```

   Later, each time you would want an access token, you start by:
   ```python
   result = None  # It is just an initial value. Please follow instructions below.
   ```

2. The API model in MSAL provides you explicit control on how to utilize token cache.
   This cache part is technically optional, but we highly recommend you to harness the power of MSAL cache.
   It will automatically handle the token refresh for you.

   ```python
   # We now check the cache to see
   # whether we already have some accounts that the end user already used to sign in before.
   accounts = app.get_accounts()
   if accounts:
       # If so, you could then somehow display these accounts and let end user choose
       print("Pick the account you want to use to proceed:")
       for a in accounts:
           print(a["username"])
       # Assuming the end user chose this one
       chosen = accounts[0]
       # Now let's try to find a token in cache for this account
       result = app.acquire_token_silent(["your_scope"], account=chosen)
   ```

3. Either there is no suitable token in the cache, or you chose to skip the previous step,
   now it is time to actually send a request to AAD to obtain a token.
   There are different methods based on your client type and scenario. Here we demonstrate a placeholder flow.

   ```python
   if not result:
       # So no suitable token exists in cache. Let's get a new one from AAD.
       result = app.acquire_token_by_one_of_the_actual_method(..., scopes=["User.Read"])
   if "access_token" in result:
       print(result["access_token"])  # Yay!
   else:
       print(result.get("error"))
       print(result.get("error_description"))
       print(result.get("correlation_id"))  # You may need this when reporting a bug
   ```

Refer the [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) pages for more details on the MSAL Python functionality and usage.

## Migrating from ADAL

If your application is using ADAL Python, we recommend you to update to use MSAL Python. No new feature work will be done in ADAL Python.

See the [ADAL to MSAL migration](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Migrate-to-MSAL-Python) guide.

## Roadmap

You can follow the latest updates and plans for MSAL Python in the [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap) published on our Wiki.

## Samples and Documentation

MSAL Python supports multiple [application types and authentication scenarios](https://docs.microsoft.com/azure/active-directory/develop/authentication-flows-app-scenarios).
The generic documents on
[Auth Scenarios](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)
and
[Auth protocols](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols)
are recommended reading.

We provide a [full suite of sample applications](https://aka.ms/aaddevsamplesv2) and [documentation](https://aka.ms/aaddevv2) to help you get started with learning the Microsoft identity platform.

## Community Help and Support

We leverage Stack Overflow to work with the community on supporting Azure Active Directory and its SDKs, including this one!
We highly recommend you ask your questions on Stack Overflow (we're all on there!)
Also browser existing issues to see if someone has had your question before.

We recommend you use the "msal" tag so we can see it!
Here is the latest Q&A on Stack Overflow for MSAL:
[http://stackoverflow.com/questions/tagged/msal](http://stackoverflow.com/questions/tagged/msal)

## Security Reporting

If you find a security issue with our libraries or services please report it to [secure@microsoft.com](mailto:secure@microsoft.com) with as much detail as possible. Your submission may be eligible for a bounty through the [Microsoft Bounty](http://aka.ms/bugbounty) program. Please do not post security issues to GitHub Issues or any other public site. We will contact you shortly upon receiving the information. We encourage you to get notifications of when security incidents occur by visiting [this page](https://technet.microsoft.com/security/dd252948) and subscribing to Security Advisory Alerts.

## Contributing

All code is licensed under the MIT license and we triage actively on GitHub. We enthusiastically welcome contributions and feedback. Please read the [contributing guide](./contributing.md) before starting.

## We Value and Adhere to the Microsoft Open Source Code of Conduct

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.
