---
title: Alterações feitas em um projeto MVC quando você se conecta ao Azure AD
description: Descreve o que acontece com seu projeto MVC quando você se conecta ao Azure AD usando os serviços conectados do Visual Studio
author: ghogen
manager: jillfra
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c4328ea6145d32616f1784d94976dab29216fbc
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852011"
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>O que aconteceu com meu projeto do MVC (Visual Studio Azure Active Directory serviço conectado)?

> [!div class="op_single_selector"]
> - [Introdução](vs-active-directory-dotnet-getting-started.md)
> - [O que aconteceu](vs-active-directory-dotnet-what-happened.md)

Este artigo identifica as alterações exatas feitas no projeto ASP.NET MVC ao adicionar o [serviço conectado do Azure Active Directory usando o Visual Studio](vs-active-directory-add-connected-service.md).

Para obter informações sobre como trabalhar com o serviço conectado, consulte [introdução](vs-active-directory-dotnet-getting-started.md).

## <a name="added-references"></a>Referências adicionadas

Afeta as referências do arquivo de projeto *. NET `packages.config` ) e (referências do NuGet).

| Type | Referência |
| --- | --- |
| .NET; NuGet | Microsoft.IdentityModel.Protocol.Extensions |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.Cookies |
| .NET; NuGet | Microsoft.Owin.Security.OpenIdConnect |
| .NET; NuGet | Owin |
| .NET        | System.IdentityModel |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |
| .NET        | System.Runtime.Serialization |

Referências adicionais se você selecionou a opção **ler dados do diretório** :

| Type | Referência |
| --- | --- |
| .NET; NuGet | EntityFramework |
| .NET        | EntityFramework. SqlServer (somente Visual Studio 2015) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft. IdentityModel. clients. ActiveDirectory. WindowsForms (somente Visual Studio 2015) |
| .NET; NuGet | System.Spatial |

As referências a seguir são removidas (apenas projetos ASP.NET 4, como no Visual Studio 2015):

| Type | Referência |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Alterações de arquivo de projeto

- Defina a propriedade `IISExpressSSLPort` como um número distinto.
- Defina a propriedade `WebProject_DirectoryAccessLevelKey` como 0 ou 1 se você tiver selecionado a opção **ler dados do diretório** .
- Defina a propriedade `IISUrl` como `https://localhost:<port>/` onde `<port>` corresponde ao `IISExpressSSLPort` valor.

## <a name="webconfig-or-appconfig-changes"></a>alterações de Web. config ou app. config

- Foram adicionadas as seguintes entradas de configuração:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="<your selected Azure domain>" />
        <add key="ida:TenantId" value="<the Id of your selected Azure AD tenant>" />
        <add key="ida:PostLogoutRedirectUri" value="<project start page, such as https://localhost:44335>" />
    </appSettings>
    ```

- Elementos `<dependentAssembly>` adicionados sob o `<runtime><assemblyBinding>` nó para `System.IdentityModel.Tokens.Jwt` e `Microsoft.IdentityModel.Protocol.Extensions`.

Alterações adicionais se você selecionou a opção **ler dados do diretório** :

- Adicionada a seguinte entrada de configuração `<appSettings>`em:

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- Foram adicionados os seguintes elementos `<configuration>`em; os valores para o Project-MDF-File e o Project-Catalog-ID variam:

    ```xml
    <configSections>
      <!-- For more information on Entity Framework configuration, visit https://go.microsoft.com/fwlink/?LinkID=237468 -->
      <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>

    <connectionStrings>
      <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\<project-mdf-file>.mdf;Initial Catalog=<project-catalog-id>;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>

    <entityFramework>
      <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
        <parameters>
          <parameter value="mssqllocaldb" />
        </parameters>
      </defaultConnectionFactory>
      <providers>
        <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
      </providers>
    </entityFramework>
    ```

- Elementos `<dependentAssembly>` adicionados sob o `<runtime><assemblyBinding>` nó para `Microsoft.Data.Services.Client`, `Microsoft.Data.Edm`e. `Microsoft.Data.OData`

## <a name="code-changes-and-additions"></a>Alterações e inclusões de código

- Adicionou o `[Authorize]` atributo a `Controllers/HomeController.cs` e qualquer outro controlador existente.

- Adicionada uma classe `App_Start/Startup.Auth.cs`de inicialização de autenticação,, que contém a lógica de inicialização para autenticação do Azure AD. Se você tiver selecionado a opção **ler dados do diretório** , esse arquivo também conterá o código para receber um código OAuth e o trocará por um token de acesso.

- Adicionada uma classe de controlador `Controllers/AccountController.cs`,, `SignIn` contendo `SignOut` métodos e.

- Adicionada uma exibição parcial, `Views/Shared/_LoginPartial.cshtml`, que contém um link de `SignIn` ação `SignOut`para e.

- Adicionada uma exibição parcial, `Views/Account/SignoutCallback.cshtml`, contendo HTML para a interface do usuário de saída.

- Atualizado o `Startup.Configuration` método para incluir uma chamada para `ConfigureAuth(app)` se a classe já existia; caso contrário `Startup` , adicionou uma classe que inclui chamadas ao método.

- Adicionado `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) ou `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), contendo informações que o Visual Studio usa para acompanhar a adição do serviço conectado.

- Se você selecionou a opção **ler dados do diretório** `Models/ADALTokenCache.cs` , `Models/ApplicationDbContext.cs` adicionou e para dar suporte ao cache de token. Também foi adicionado um controlador adicional e uma exibição para ilustrar o acesso às informações de perfil `Controllers/UserProfileController.cs`do `Views/UserProfile/Index.cshtml`usuário usando as APIs do Graph do Azure:, e`Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Backup de arquivo (Visual Studio 2015)

Ao adicionar o serviço conectado, o Visual Studio 2015 faz backup dos arquivos alterados e removidos. Todos os arquivos afetados são salvos na pasta `Backup/AzureAD`. O Visual Studio 2017 e posterior não cria backups.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Alterações no Azure

- Um aplicativo do Azure AD foi criado no domínio que você selecionou ao adicionar o serviço conectado.
- O aplicativo foi atualizado para incluir a permissão **ler dados do diretório** se essa opção tiver sido selecionada.

[Saiba mais sobre Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Passos Seguintes

- [Cenários de autenticação para Azure Active Directory](authentication-scenarios.md)
- [Adicionar entrada com a Microsoft a um aplicativo Web ASP.NET](quickstart-v1-aspnet-webapp.md)
