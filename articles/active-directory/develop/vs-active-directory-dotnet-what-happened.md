---
title: Alterações feitas a um projeto MVC quando se conecta ao Azure AD
description: Descreve o que acontece ao seu projeto MVC quando se conecta ao Azure AD utilizando serviços conectados ao Estúdio Visual
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
ms.openlocfilehash: ac38adba4ca0d978dd48a546bed1b1faf4fe40fe
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79036962"
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>O que aconteceu ao meu projeto MVC (serviço conectado visual Studio Azure Ative Directory)?

> [!div class="op_single_selector"]
> - [Introdução](vs-active-directory-dotnet-getting-started.md)
> - [O que aconteceu](vs-active-directory-dotnet-what-happened.md)

Este artigo identifica as alterações exatas feitas a um projeto ASP.NET MVC ao adicionar o serviço ligado ao [Azure Ative Directory utilizando o Visual Studio](vs-active-directory-add-connected-service.md).

Para obter informações sobre o trabalho com o serviço conectado, consulte [Getting Started](vs-active-directory-dotnet-getting-started.md).

## <a name="added-references"></a>Referências adicionais

Afeta o ficheiro do projeto (referências*.NET) e `packages.config` (referências NuGet).

| Tipo | Referência |
| --- | --- |
| .NET; NuGet | Microsoft.IdentityModel.Protocol.Extensions |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.Cookies |
| .NET; NuGet | Microsoft.Owin.Security.OpenIdConnect |
| .NET; NuGet | Rio Owin |
| .NET        | System.IdentityModel |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |
| .NET        | System.Runtime.Serialization |

Referências adicionais se selecionou a opção de dados do **diretório Ler:**

| Tipo | Referência |
| --- | --- |
| .NET; NuGet | EntityFramework |
| .NET        | EntityFramework.SqlServer (apenas Visual Studio 2015) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms (apenas Visual Studio 2015) |
| .NET; NuGet | System.Spatial |

As seguintes referências são removidas (ASP.NET apenas 4 projetos, como no Visual Studio 2015):

| Tipo | Referência |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Alterações no ficheiro do projeto

- Deteto a propriedade `IISExpressSSLPort` para um número distinto.
- Detete a propriedade `WebProject_DirectoryAccessLevelKey` a 0, ou 1 se tiver selecionado a opção de dados do **diretório Read.**
- Detete a `IISUrl` da propriedade para `https://localhost:<port>/` onde `<port>` corresponda ao valor `IISExpressSSLPort`.

## <a name="webconfig-or-appconfig-changes"></a>web.config ou app.config alterações

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

- Adicione `<dependentAssembly>` elementos sob o nó `<runtime><assemblyBinding>` para `System.IdentityModel.Tokens.Jwt` e `Microsoft.IdentityModel.Protocol.Extensions`.

Alterações adicionais se selecionou a opção de dados do **diretório Ler:**

- Acrescentou a seguinte entrada de configuração em `<appSettings>`:

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- Adicione os seguintes elementos sob `<configuration>`; Os valores para o project-mdf-file e project-catalog-id variarão:

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

- Adicione `<dependentAssembly>` elementos sob o nó `<runtime><assemblyBinding>` para `Microsoft.Data.Services.Client`, `Microsoft.Data.Edm`e `Microsoft.Data.OData`.

## <a name="code-changes-and-additions"></a>Alterações e adições de código

- Acrescentou o atributo `[Authorize]` à `Controllers/HomeController.cs` e a quaisquer outros controladores existentes.

- Adicionou uma classe de startupde autenticação, `App_Start/Startup.Auth.cs`, contendo lógica de startup para autenticação AD Azure. Se selecionou a opção de dados do **diretório Read,** este ficheiro também contém código para receber um código OAuth e trocá-lo por um token de acesso.

- Acrescentou uma classe de controlador, `Controllers/AccountController.cs`, contendo métodos `SignIn` e `SignOut`.

- Acrescentou uma visão parcial, `Views/Shared/_LoginPartial.cshtml`, contendo uma ligação de ação para `SignIn` e `SignOut`.

- Acrescentou uma visão parcial, `Views/Account/SignoutCallback.cshtml`, contendo HTML para uI de inscrição.

- Atualizou o método `Startup.Configuration` para incluir uma chamada para `ConfigureAuth(app)` se a classe já existisse; caso contrário, acrescentou uma aula `Startup` que inclui chamadas de método.

- Acrescentou `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) ou `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), contendo informações que o Visual Studio utiliza para acompanhar a adição do serviço conectado.

- Se selecionou a opção de dados do **diretório Read,** acrescentou `Models/ADALTokenCache.cs` e `Models/ApplicationDbContext.cs` para suportar o cacheto de token. Adicionou ainda um controlador adicional e uma visão para ilustrar o acesso à informação do perfil do utilizador através de APIs de gráfico sinuoso: `Controllers/UserProfileController.cs`, `Views/UserProfile/Index.cshtml`e `Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Backup de ficheiros (Estúdio Visual 2015)

Ao adicionar o serviço conectado, o Visual Studio 2015 recua e remove ficheiros. Todos os ficheiros afetados são guardados na pasta `Backup/AzureAD`. Visual Studio 2017 e mais tarde não cria backups.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Alterações no Azure

- Criou uma Aplicação AD Azure no domínio que selecionou ao adicionar o serviço conectado.
- Atualizou a aplicação para incluir a permissão de dados do **diretório Read** se essa opção fosse selecionada.

[Saiba mais sobre o Diretório Ativo Azure.](https://azure.microsoft.com/services/active-directory/)

## <a name="next-steps"></a>Passos seguintes

- [Cenários de autenticação para o Diretório Ativo do Azure](authentication-scenarios.md)
- [Adicione o sessão com a Microsoft a uma aplicação web ASP.NET](quickstart-v2-aspnet-webapp.md)
