
# AIKEJI.Xero.OAuth2 Release Notes

## 1.5.2
* Support: Update Licence Agreement

## 1.5.1

* Security: Update CefSharp dependency with security update. [CefSharp release v85.3.130](https://github.com/cefsharp/CefSharp/releases/tag/v85.3.130)

## 1.5.0

* Feature: Added support for Enterprise licence.
* Feature: Licence file naming convention changed to '*.aikeji.lic'. ('aikeji.lic' still supported).

## 1.4.1

* Bugfix: Fix deadlock when calling GetToken from non-async method.

## 1.4.0

* Feature: Internal improvements for library redirect performance.
* Bugfix: Fix possible error from race condition.
* Docs: Add section for RedirectUri.
* Obsolete: ListenPort and WaitMessage in TokenHelperOptions marked Obsolete to be removed in a future version.

## 1.3.0

* Feature: Add support for PKCE authorisation flow. [info](https://developer.xero.com/documentation/oauth2/pkce-flow) [docs](README.md#pkce-flow-details)
* Bugfix: Handle invalid token during refresh if client credentials have changed.
* Docs: Add section for using Xero API and options

## 1.2.0

* Improve error messages when licence support has ended for the current version.

## 1.1.0

* Added support for Trial licences.

## 1.0.1

* Initial Version
