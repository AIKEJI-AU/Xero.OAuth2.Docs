
# AIKEJI.Xero.OAuth2.Docs

## Description

This library is a .NET library that presents a simplified API for retrieving access tokens using Xero's OAuth2 API with an integrated login experience using an embedded browser.

## Features

* Pop up elegant login form with embedded browser for Xero login.
* Automatically get, save, and refresh token with a single method call.
* Automatically save token to encrypted file.
* Automatically load token from file.
* Automatically refresh token when expired or close to expiry.

## Dependencies

The library is dependant on the `.NET Framework v4.6`.\
\
It also has a dependency on the nuget package `CefSharp.Winforms`.\
This is bundled inside the nuget package to aid in ease of use.

Because of how the `CefSharp` redistributables are packaged, we have decided to package these dependencies directly into out nuget package, rather than let nuget try to work out how to reference files, which can differ depending on whether your project uses `packages.config` or `PackageReference` style references.\
This means that you will get both x86 and x64 dependencies for a project targeting `Any CPU`, but only the relevant architecture when targeting `x86` or `x64`.\
However due to keeping the package flexible, the package size is increased.

## Installation & Setup

The library is provided on [nuget.org](http://nuget.org/).\
For help on installing nuget packages, please refer to Microsoft's [documentation](https://docs.microsoft.com/en-us/nuget/quickstart/install-and-use-a-package-in-visual-studio).

All configuration is done in code, refer to the [configuration](#configuration) section and intellisense documentation for details.\
The license file `aikeji.lic` must be placed in the same folder as your application, otherwise the library will not function.

## How It Works

This library provides a simple `ITokenHelper` interface to use.\
This simplifies authentication with Xero's OAuth2 API, and provides a consolidated and user friendly experience.

When your application requires a token, it just has to call the `GetToken` method. If this is the first time the application has called this, a window will pop up allowing the user to log in to Xero and grant the application access.\
The library automatically saves this token to disk, and will load the file if present.

The library will also automatically refresh the token when it detects that the token has expired, as the tokens only last for 30 minutes (controlled by Xero).

The library will also cache the user's login session, if desired, to simplify the process of getting a new token if the application has not been used for a while.

### Technical Details

Xero's OAuth2 requires interactive login by a user.\
To achieve this, the library displays a WinForms Form with an embedded browser that is not dependant on the system's installed browser.

Caching of sessions is enabled by default to allow ease of login.\
This will cache the session used when logging in to the Xero website.\
If this is not desired, then this functionality can be disabled with the `CacheCookies` property in the `TokenHelperOptions`.\
Alternatively, the current session can be cleared using the `ClearLoginSession` method in the `ITokenHelper`.

## Code & Usage

### API

The package comes with documentation, please check the documentation on interfaces and methods for help and usage.

```csharp
namespace AIKEJI.Xero.OAuth2
{
    public static class TokenHelper
    {
        public static ITokenHelper Create(TokenHelperOptions options);
    }

    public class TokenHelperOptions
    {
        public string ClientId { get; set; }
        public string ClientSecret { get; set; }
        public int ListenPort { get; }
        public Uri RedirectUri { get; set; }
        public TimeSpan RefreshTime { get; set; }
        public string TokenFile { get; set; }
        public string WaitMessage { get; set; }
        public bool Encrypt { get; set; }
        public bool CacheCookies { get; set; }
        public int FormWidth { get; set; }
        public int FormHeight { get; set; }
        public string Scope { get; set; }
        public bool HighDPI { get; set; }
    }

    public interface ITokenHelper
    {
        Task<XeroToken> GetToken(CancellationToken cancellationToken = default);

        void ClearToken();

        void ClearLoginSession();

        Task<XeroToken> RefreshToken(CancellationToken cancellationToken = default);

        Task<XeroToken> LoadToken(CancellationToken cancellationToken = default);

        Task SetToken(XeroToken token, CancellationToken cancellationToken = default);
    }
```

### Configuration

Configuration is done with the `TokenHelperOptions` class, which is then passed into `TokenHelper.Create` to create an `ITokenHelper` instance that can be used to get a Xero access token.\
\
The `ClientId`, `ClientSecret`, and `RedirectUri` all come from your configured Xero app.

### Usage

Example `C#` WinForms usage.

```csharp
using System;
using System.Windows.Forms;
using AIKEJI.Xero.OAuth2;

public partial class Form1 : Form
{
    public Form1()
    {
        InitializeComponent();
    }

    private void button1_Click(object sender, EventArgs e)
    {
        var options = new TokenHelperOptions
        {
            ClientId = "id",
            ClientSecret = "secret",
            RedirectUri = new Uri("http://localhost:5000/")
        };
        var helper = TokenHelper.Create(options);
        try
        {
            var token = helper.GetToken().GetAwaiter().GetResult();
        }
        catch (Exception ex)
        {
            MessageBox.Show(ex.Message, "Error", MessageBoxButtons.OK, MessageBoxIcon.Warning);
        }
    }
}
```

Example `VB.Net` WinForms usage.

```vbnet
Imports AIKEJI.Xero.OAuth2

Public Class Form1
    Private Sub Button1_Click(sender As Object, e As EventArgs) Handles Button1.Click
        Dim options = New TokenHelperOptions()
        With options
            .ClientId = "id"
            .ClientSecret = "secret"
            .RedirectUri = New Uri("http://localhost:5000/")
        End With
        Dim helper = TokenHelper.Create(options)
        Try
            Dim token = helper.GetToken().GetAwaiter().GetResult()
        Catch ex As Exception
            MessageBox.Show(ex.Message, "Error", MessageBoxButtons.OK, MessageBoxIcon.Warning)
        End Try
    End Sub
End Class
```

### Xero API Example

Example `C#` token usage with Xero's Client \
Note: Xero's .NET API is async

```csharp
using AIKEJI.Xero.OAuth2;
using System.Linq;
using System.Threading.Tasks;

static class UseTokenExample
{
    public static async Task UseToken(ITokenHelper helper)
    {
        var token = await helper.GetToken();
        var accountingApi = new Xero.NetStandard.OAuth2.Api.AccountingApi();
        var tenantId = token.Tenants.First().TenantId;
        var contacts = await accountingApi.GetContactsAsync(token.AccessToken, tenantId);
    }
}
```

Example `VB.Net` token usage with Xero's Client\
Note: Xero's .NET API is async

```vbnet
Imports AIKEJI.Xero.OAuth2

Public Module UseTokenExample
    Async Function UseToken(helper As ITokenHelper) As Task
        Dim token = Await helper.GetToken()
        Dim accountingApi = New Xero.NetStandard.OAuth2.Api.AccountingApi()
        Dim tenantId = token.Tenants.First().TenantId
        Dim contacts = Await accountingApi.GetContactsAsync(token.AccessToken, tenantId)
    End Function
End Module
```

## Compatibility

### High DPI Mode

If you are running your application on a monitor with high DPI scaling, then you should enable the HighDPI option to true.

## License

Licensing is provided with a file `aikeji.lic`.
This file must be placed in the same location as the library, assembly `AIKEJI.Xero.OAuth2.dll`.

## Support

For any questions or issues related to using the library itself in code, please use [GitHub issues](https://github.com/AIKEJI-AU/Xero.OAuth2.Docs/issues).

For any questions or issues related to licensing, please contact us directly using [Contact Us](#contact-us) below.

## Contact Us

[http://aikeji.com.au/contact.html](http://aikeji.com.au/contact.html)

## Purchase License

[http://aikeji.com.au/products.html](http://aikeji.com.au/products.html)
