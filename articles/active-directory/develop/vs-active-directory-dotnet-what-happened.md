---
title: Alterações feitas num projeto de MVC quando se conecta ao Azure AD
description: Descreve o que acontece ao seu projeto MVC quando se conecta ao Azure AD utilizando serviços conectados ao Visual Studio
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.openlocfilehash: ab72512b2e5d915dbd4b8a3d7d8446a5932eef9e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88165452"
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>O que aconteceu ao meu projeto MVC (serviço de ligação ao Visual Studio Azure Ative Directory)?

> [!div class="op_single_selector"]
> - [Introdução](vs-active-directory-dotnet-getting-started.md)
> - [O que aconteceu](vs-active-directory-dotnet-what-happened.md)

Este artigo identifica as alterações exatas feitas a um projeto de MVC ASP.NET ao adicionar o [serviço de ligação ao Azure Ative Directory utilizando](vs-active-directory-add-connected-service.md)o Visual Studio .

Para obter informações sobre o trabalho com o serviço conectado, consulte ["Começar".](vs-active-directory-dotnet-getting-started.md)

## <a name="added-references"></a>Referências adicionadas

Afeta o ficheiro do projeto (referências*.NET) e `packages.config` (referências NuGet).

| Tipo | Referência |
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

Referências adicionais se selecionar a opção **de dados do diretório de leitura:**

| Tipo | Referência |
| --- | --- |
| .NET; NuGet | Quadro de entidades |
| .NET        | EntityFramework.SqlServer (Estúdio Visual 2015 apenas) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms (apenas o Visual Studio 2015) |
| .NET; NuGet | Sistema.Espacial |

As seguintes referências são removidas (apenas ASP.NET 4 projetos, como no Visual Studio 2015):

| Tipo | Referência |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Alterações no ficheiro do projeto

- Coloque a propriedade `IISExpressSSLPort` num número distinto.
- Desajuste a propriedade `WebProject_DirectoryAccessLevelKey` para 0, ou 1 se selecionou a opção **de dados do diretório ler.**
- Desa parte da propriedade `IISUrl` para onde corresponde ao `https://localhost:<port>/` `<port>` `IISExpressSSLPort` valor.

## <a name="webconfig-or-appconfig-changes"></a>alterações web.config ou app.config

- Adicionei as seguintes entradas de configuração:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="<your selected Azure domain>" />
        <add key="ida:TenantId" value="<the Id of your selected Azure AD tenant>" />
        <add key="ida:PostLogoutRedirectUri" value="<project start page, such as https://localhost:44335>" />
    </appSettings>
    ```

- Elementos `<dependentAssembly>` adicionados sob o `<runtime><assemblyBinding>` nó para `System.IdentityModel.Tokens.Jwt` e `Microsoft.IdentityModel.Protocol.Extensions` .

Alterações adicionais se selecionar a opção **de dados do diretório ler:**

- Acrescentou a seguinte entrada de configuração em `<appSettings>` :

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- Adicionados os seguintes elementos em `<configuration>` ; valores para o projecto-mdf-file e projeto-catalog-id variarão:

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

- Elementos `<dependentAssembly>` adicionados sob o `<runtime><assemblyBinding>` nó para , e `Microsoft.Data.Services.Client` `Microsoft.Data.Edm` `Microsoft.Data.OData` .

## <a name="code-changes-and-additions"></a>Alterações e adições de código

- Adicionou o `[Authorize]` atributo a `Controllers/HomeController.cs` e quaisquer outros controladores existentes.

- Adicionou uma classe de startup de `App_Start/Startup.Auth.cs` autenticação, contendo lógica de arranque para a autenticação AZure AD. Se selecionou a opção **de dados do diretório de leitura,** este ficheiro também contém código para receber um código OAuth e trocá-lo por um token de acesso.

- Acrescentou uma classe `Controllers/AccountController.cs` controladora, contendo `SignIn` e `SignOut` métodos.

- Acrescentou uma visão parcial, `Views/Shared/_LoginPartial.cshtml` contendo uma ligação de ação para `SignIn` e `SignOut` .

- Adicionou uma visão `Views/Account/SignoutCallback.cshtml` parcial, contendo HTML para a UI de assinatura.

- Atualizou o `Startup.Configuration` método para incluir uma chamada para se a classe já `ConfigureAuth(app)` existisse; caso contrário, adicione uma `Startup` classe que inclua chamadas de método.

- Adicionado `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) ou `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), contendo informações que o Visual Studio utiliza para acompanhar a adição do serviço conectado.

- Se selecionou a opção **de dados do diretório ler,** adicione `Models/ADALTokenCache.cs` e suporte o `Models/ApplicationDbContext.cs` caching token. Também adicionou um controlador e uma visão adicionais para ilustrar o acesso a informações de perfil do utilizador usando APIs gráficos Azure: `Controllers/UserProfileController.cs` `Views/UserProfile/Index.cshtml` , e `Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Backup de ficheiros (Visual Studio 2015)

Ao adicionar o serviço conectado, o Visual Studio 2015 retrosseca os ficheiros alterados e removidos. Todos os ficheiros afetados são guardados na pasta `Backup/AzureAD` . Visual Studio 2017 e mais tarde não cria backups.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Alterações no Azure

- Criei uma Aplicação AD Azure no domínio que selecionou ao adicionar o serviço conectado.
- Atualizou a aplicação para incluir a permissão **de dados do diretório de Leitura** se essa opção fosse selecionada.

[Saiba mais sobre o Azure Ative Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Passos seguintes

- [Cenários de autenticação para o Azure Ative Directory](./authentication-vs-authorization.md)
- [Adicione o s-in com a Microsoft a uma aplicação web ASP.NET](quickstart-v2-aspnet-webapp.md)