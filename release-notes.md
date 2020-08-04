
# AIKEJI.Xero.OAuth2 Release Notes

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
