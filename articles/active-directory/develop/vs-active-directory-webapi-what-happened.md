---
title: Alterações es feitas aos projetos WebAPI ao ligar-se ao Azure AD
description: Descreve o que acontece ao seu projeto WebAPI quando se conecta ao Azure AD usando o Visual Studio
author: ghogen
manager: jillfra
ms.workload: azure-vs
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.openlocfilehash: a070bee7a9fa836eeac7c739cf2757295533ad7f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88165367"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>O que aconteceu com o meu projeto WebAPI (serviço de ligação ao Visual Studio Azure Ative Directory)

> [!div class="op_single_selector"]
> - [Introdução](vs-active-directory-webapi-getting-started.md)
> - [O que aconteceu](vs-active-directory-webapi-what-happened.md)

Este artigo identifica as alterações exatas feitas aos projetos ASP.NET webAPI, ASP.NET Single-Page e ASP.NET Azure API ao adicionar o [serviço de ligação ao Azure Ative Directory utilizando](vs-active-directory-add-connected-service.md)o Visual Studio . Aplica-se também aos projetos ASP.NET Azure Mobile Service no Visual Studio 2015.

Para obter informações sobre o trabalho com o serviço conectado, consulte ["Começar".](vs-active-directory-webapi-getting-started.md)

## <a name="added-references"></a>Referências adicionadas

Afeta o ficheiro do projeto *.NET referências) e `packages.config` (referências NuGet).

| Tipo | Referência |
| --- | --- |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.ActiveDirectory |
| .NET; NuGet | Microsoft.Owin.Security.Jwt |
| .NET; NuGet | Microsoft.Owin.Security.OAuth |
| .NET; NuGet | Owin |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |

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
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(Estúdio Visual 2015 apenas) |
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
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Estúdio Visual 2017 apenas: Também acrescentou a seguinte entrada em `<appSettings>` "

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Elementos `<dependentAssembly>` adicionados sob o `<runtime><assemblyBinding>` nó para `System.IdentityModel.Tokens.Jwt` .

- Se selecionou a opção **de dados do diretório ler,** adicione a seguinte entrada de configuração em `<appSettings>` :

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Alterações e adições de código

- Adicionou o `[Authorize]` atributo a `Controllers/ValueController.cs` e quaisquer outros controladores existentes.

- Adicionou uma classe de startup de autenticação, `App_Start/Startup.Auth.cs` contendo lógica de arranque para autenticação AZure AD, ou modificou-a em conformidade. Se selecionou a opção **de dados do diretório de leitura,** este ficheiro também contém código para receber um código OAuth e trocá-lo por um token de acesso.

- (Visual Studio 2015 apenas com ASP.NET 4) Removido `App_Start/IdentityConfig.cs` e `Controllers/AccountController.cs` `Models/IdentityModel.cs` adicionado, e `Providers/ApplicationAuthProvider.cs` .

- Adicionado `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) ou `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), contendo informações que o Visual Studio utiliza para acompanhar a adição do serviço conectado.

### <a name="file-backup-visual-studio-2015"></a>Backup de ficheiros (Visual Studio 2015)

Ao adicionar o serviço conectado, o Visual Studio 2015 retrosseca os ficheiros alterados e removidos. Todos os ficheiros afetados são guardados na pasta `Backup/AzureAD` . Visual Studio 2017 não cria backups.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Alterações no Azure

- Criei uma Aplicação AD Azure no domínio que selecionou ao adicionar o serviço conectado.
- Atualizou a aplicação para incluir a permissão **de dados do diretório de Leitura** se essa opção fosse selecionada.

[Saiba mais sobre o Azure Ative Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Passos seguintes

- [Cenários de autenticação para o Azure Ative Directory](./authentication-vs-authorization.md)
- [Adicione o s-in com a Microsoft a uma aplicação web ASP.NET](quickstart-v2-aspnet-webapp.md)