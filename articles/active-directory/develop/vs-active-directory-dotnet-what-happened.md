---
title: Alterações feitas a um projeto MVC quando se conecta ao Azure AD
description: Descreve o que acontece ao seu projeto MVC quando se conecta ao Azure AD utilizando serviços conectados ao Estúdio Visual
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: d42d905bf35c015213e76bc50c4bc339a5c4a062
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80886114"
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
| .net; Nuget | Microsoft.IdentityModel.Protocol.Extensions |
| .net; Nuget | Microsoft.Owin |
| .net; Nuget | Microsoft.Owin.Host.SystemWeb |
| .net; Nuget | Microsoft.Owin.Security |
| .net; Nuget | Microsoft.Owin.Security.Cookies |
| .net; Nuget | Microsoft.Owin.Security.OpenIdConnect |
| .net; Nuget | Rio Owin |
| .NET        | System.IdentityModel |
| .net; Nuget | System.IdentityModel.Tokens.Jwt |
| .NET        | System.Runtime.Serialization |

Referências adicionais se selecionou a opção de dados do **diretório Ler:**

| Tipo | Referência |
| --- | --- |
| .net; Nuget | Estrutura de Entidades |
| .NET        | EntityFramework.SqlServer (apenas Visual Studio 2015) |
| .net; Nuget | Microsoft.Azure.ActiveDirectory.GraphClient |
| .net; Nuget | Microsoft.Data.Edm |
| .net; Nuget | Microsoft.data.OData |
| .net; Nuget | Microsoft.Data.Services.Client |
| .net; Nuget | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms (apenas Visual Studio 2015) |
| .net; Nuget | Sistema.Espacial |

As seguintes referências são removidas (ASP.NET apenas 4 projetos, como no Visual Studio 2015):

| Tipo | Referência |
| --- | --- |
| .net; Nuget | Microsoft.AspNet.Identity.Core |
| .net; Nuget | Microsoft.AspNet.Identity.EntityFramework |
| .net; Nuget | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Alterações no ficheiro do projeto

- Coloque a `IISExpressSSLPort` propriedade num número distinto.
- Desloque a propriedade `WebProject_DirectoryAccessLevelKey` para 0, ou 1 se tiver selecionado a opção de dados do **diretório Read.**
- Detete `IISUrl` `https://localhost:<port>/` a `<port>` propriedade `IISExpressSSLPort` para onde corresponda ao valor.

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

- Elementos `<dependentAssembly>` adicionados `<runtime><assemblyBinding>` sob `System.IdentityModel.Tokens.Jwt` o `Microsoft.IdentityModel.Protocol.Extensions`nó para e .

Alterações adicionais se selecionou a opção de dados do **diretório Ler:**

- Adicione a seguinte `<appSettings>`entrada de configuração em:

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- Adicione os seguintes `<configuration>`elementos em ; Os valores para o project-mdf-file e project-catalog-id variarão:

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

- Elementos `<dependentAssembly>` adicionados `<runtime><assemblyBinding>` sob `Microsoft.Data.Services.Client`o `Microsoft.Data.Edm`nó `Microsoft.Data.OData`para, e .

## <a name="code-changes-and-additions"></a>Alterações e adições de código

- Acrescentou `[Authorize]` o `Controllers/HomeController.cs` atributo e quaisquer outros controladores existentes.

- Adicionou uma classe `App_Start/Startup.Auth.cs`de startupde autenticação, contendo lógica de arranque para autenticação ad. Se selecionou a opção de dados do **diretório Read,** este ficheiro também contém código para receber um código OAuth e trocá-lo por um token de acesso.

- Acrescentou uma `Controllers/AccountController.cs`classe `SignIn` de `SignOut` controlador, contendo e métodos.

- Acrescentou uma `Views/Shared/_LoginPartial.cshtml`visão parcial, contendo `SignIn` `SignOut`uma ligação de ação para e .

- Acrescentou uma `Views/Account/SignoutCallback.cshtml`visão parcial, contendo HTML para uI de inscrição.

- Atualizou `Startup.Configuration` o método para `ConfigureAuth(app)` incluir uma chamada para se a classe já existia; caso contrário, `Startup` acrescentou uma classe que inclui chamadas de método.

- Adicionado `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) ou `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), contendo informações que o Visual Studio usa para acompanhar a adição do serviço conectado.

- Se selecionou a opção de `Models/ADALTokenCache.cs` `Models/ApplicationDbContext.cs` dados do **diretório Read,** adicionado e para suportar o cache token. Também adicionou um controlador adicional e vista para ilustrar o `Controllers/UserProfileController.cs`acesso `Views/UserProfile/Index.cshtml`à informação do perfil do utilizador usando APIs de gráfico Azure: , e`Views/UserProfile/Relogin.cshtml`

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
