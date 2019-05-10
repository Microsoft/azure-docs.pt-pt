---
title: Alterações efetuadas a um projeto MVC, quando se liga ao Azure AD
description: Descreve o que acontece ao seu projeto MVC quando ligar ao Azure AD com os serviços ligados do Visual Studio
services: active-directory
author: ghogen
manager: douge
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e40a1fd72adf353d5c4d856544482cb7bd68f43
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413991"
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>O que aconteceu ao meu projeto MVC (serviço ligado do Visual Studio do Azure Active Directory)?

> [!div class="op_single_selector"]
> - [Introdução](vs-active-directory-dotnet-getting-started.md)
> - [O que aconteceu](vs-active-directory-dotnet-what-happened.md)

Este artigo identifica as alterações exatas feitas am ASP.NET MVC do projeto ao adicionar o [com o Visual Studio do serviço de ligado do Azure Active Directory](vs-active-directory-add-connected-service.md).

Para obter informações sobre como trabalhar com o serviço ligado, consulte [introdução ao](vs-active-directory-dotnet-getting-started.md).

## <a name="added-references"></a>Foi adicionadas referências

Afeta as referências *.NET do arquivo de projeto) e `packages.config` (referências de NuGet).

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

Referências adicionais, se tiver selecionado o **ler dados do diretório** opção:

| Type | Referência |
| --- | --- |
| .NET; NuGet | EntityFramework |
| .NET        | EntityFramework.SqlServer (Visual Studio 2015 apenas) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms (Visual Studio 2015 apenas) |
| .NET; NuGet | System.Spatial |

As seguintes referências são removidas (ASP.NET 4 projetos apenas, como no Visual Studio 2015):

| Type | Referência |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Alterações de ficheiros de projeto

- Defina a propriedade `IISExpressSSLPort` para um número diferente.
- Defina a propriedade `WebProject_DirectoryAccessLevelKey` como 0 ou 1, se tiver selecionado o **ler dados do diretório** opção.
- Defina a propriedade `IISUrl` para `https://localhost:<port>/` onde `<port>` corresponde a `IISExpressSSLPort` valor.

## <a name="webconfig-or-appconfig-changes"></a>alterações de Web. config ou App. config

- Adicionadas as seguintes entradas de configuração:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="<your selected Azure domain>" />
        <add key="ida:TenantId" value="<the Id of your selected Azure AD tenant>" />
        <add key="ida:PostLogoutRedirectUri" value="<project start page, such as https://localhost:44335>" />
    </appSettings>
    ```

- Adicionado `<dependentAssembly>` elementos sob o `<runtime><assemblyBinding>` nó para `System.IdentityModel.Tokens.Jwt` e `Microsoft.IdentityModel.Protocol.Extensions`.

Alterações adicionais, se tiver selecionado o **ler dados do diretório** opção:

- Adicionar a seguinte entrada de configuração em `<appSettings>`:

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- Adicionados os seguintes elementos em `<configuration>`; os valores para o arquivo de projeto mdf e o id de catálogo de projeto irão variar:

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

- Adicionado `<dependentAssembly>` elementos sob o `<runtime><assemblyBinding>` nó para `Microsoft.Data.Services.Client`, `Microsoft.Data.Edm`, e `Microsoft.Data.OData`.

## <a name="code-changes-and-additions"></a>Alterações de código e adições

- Adicionado o `[Authorize]` atributo para `Controllers/HomeController.cs` e quaisquer outros controladores existentes.

- Adicionado uma classe de startup da autenticação, `App_Start/Startup.Auth.cs`, que contém a lógica de arranque para a autenticação do Azure AD. Se tiver selecionado o **ler dados do diretório** opção, este ficheiro também contém código para receber um código de OAuth e o exchange-lo para um token de acesso.

- Adicionado uma classe de controlador `Controllers/AccountController.cs`, que contém `SignIn` e `SignOut` métodos.

- Adicionado um modo de exibição parcial `Views/Shared/_LoginPartial.cshtml`, que contém uma hiperligação de ação para `SignIn` e `SignOut`.

- Adicionado um modo de exibição parcial, `Views/Account/SignoutCallback.cshtml`, que contém o HTML para o fim de sessão da interface do Usuário.

- Atualizado o `Startup.Configuration` método para incluir uma chamada ao `ConfigureAuth(app)` se a classe já existe; caso contrário, adicionados um `Startup` classe inclui chama o método.

- Adicionado `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) ou `Service References/Azure AD/ConnectedService.json` informações de contentor (Visual Studio 2015), que utiliza o Visual Studio para controlar a adição do serviço ligado.

- Se tiver selecionado o **ler dados do diretório** opção, adicionada `Models/ADALTokenCache.cs` e `Models/ApplicationDbContext.cs` para oferecer suporte a tokens em cache. Também adicionou um controlador adicional e a vista para ilustrar as informações do perfil de utilizador ao aceder ao utilizar graph APIs do Azure: `Controllers/UserProfileController.cs`, `Views/UserProfile/Index.cshtml`, e `Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Cópia de segurança de ficheiros (Visual Studio 2015)

Ao adicionar o serviço ligado, Visual Studio 2015 efetua uma cópia de segurança de ficheiros alterados e removidos. Todos os arquivos afetados são guardados na pasta `Backup/AzureAD`. Visual Studio 2017 e posterior não cria cópias de segurança.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Alterações no Azure

- Criar uma aplicação do Azure AD no domínio selecionado ao adicionar o serviço ligado.
- Atualizada da aplicação para incluir o **ler dados do diretório** permissão se essa opção tiver sido selecionada.

[Saiba mais sobre o Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Passos Seguintes

- [Cenários de autenticação do Azure Active Directory](authentication-scenarios.md)
- [Adicionar início de sessão com a Microsoft a uma aplicação web ASP.NET](quickstart-v1-aspnet-webapp.md)
