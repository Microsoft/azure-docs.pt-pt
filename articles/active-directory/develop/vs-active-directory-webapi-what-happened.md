---
title: Alterações feitas aos projetos da WebAPI na ligação à Azure AD
description: Descreve o que acontece ao seu projeto WebAPI quando se conecta ao Azure AD usando o Visual Studio
author: ghogen
manager: jillfra
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.workload: azure-vs
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 9858fdb7da80fe03498392ea93bf3c83b8a7e4dd
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76699874"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>O que aconteceu ao meu projeto WebAPI (serviço conectado visual Studio Azure Ative Directory)

> [!div class="op_single_selector"]
> - [Introdução](vs-active-directory-webapi-getting-started.md)
> - [O que aconteceu](vs-active-directory-webapi-what-happened.md)

Este artigo identifica as alterações exatas feitas ao ASP.NET WebAPI, ASP.NET Aplicação de Uma Página Única e ASP.NET projetos da API Azure ao adicionar o serviço ligado ao [Azure Ative Directory utilizando](vs-active-directory-add-connected-service.md)o Visual Studio. Aplica-se também aos projetos ASP.NET Azure Mobile Service no Visual Studio 2015.

Para obter informações sobre o trabalho com o serviço conectado, consulte [Getting Started](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Referências adicionais

Afeta o ficheiro do projeto *.NET referências) e `packages.config` (referências NuGet).

| Tipo | Referência |
| --- | --- |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.ActiveDirectory |
| .NET; NuGet | Microsoft.Owin.Security.Jwt |
| .NET; NuGet | Microsoft.Owin.Security.OAuth |
| .NET; NuGet | Rio Owin |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |

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
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(Apenas Visual Studio 2015) |
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
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Visual Studio 2017 apenas: Acrescentou também a seguinte entrada sob `<appSettings>`"

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Adicione `<dependentAssembly>` elementos sob o nó `<runtime><assemblyBinding>` para `System.IdentityModel.Tokens.Jwt`.

- Se selecionou a opção de dados do **diretório Read,** adicione a seguinte entrada de configuração em `<appSettings>`:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Alterações e adições de código

- Acrescentou o atributo `[Authorize]` à `Controllers/ValueController.cs` e a quaisquer outros controladores existentes.

- Adicionou uma classe de startupde autenticação, `App_Start/Startup.Auth.cs`, contendo lógica de startup para autenticação AD Azure, ou modificou-a em conformidade. Se selecionou a opção de dados do **diretório Read,** este ficheiro também contém código para receber um código OAuth e trocá-lo por um token de acesso.

- (Visual Studio 2015 apenas com ASP.NET 4 aplicações) Removido `App_Start/IdentityConfig.cs` e adicionado `Controllers/AccountController.cs`, `Models/IdentityModel.cs`e `Providers/ApplicationAuthProvider.cs`.

- Acrescentou `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) ou `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), contendo informações que o Visual Studio utiliza para acompanhar a adição do serviço conectado.

### <a name="file-backup-visual-studio-2015"></a>Backup de ficheiros (Estúdio Visual 2015)

Ao adicionar o serviço conectado, o Visual Studio 2015 recua e remove ficheiros. Todos os ficheiros afetados são guardados na pasta `Backup/AzureAD`. O Visual Studio 2017 não cria backups.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Alterações no Azure

- Criou uma Aplicação AD Azure no domínio que selecionou ao adicionar o serviço conectado.
- Atualizou a aplicação para incluir a permissão de dados do **diretório Read** se essa opção fosse selecionada.

[Saiba mais sobre o Diretório Ativo Azure.](https://azure.microsoft.com/services/active-directory/)

## <a name="next-steps"></a>Passos seguintes

- [Cenários de autenticação para o Diretório Ativo do Azure](authentication-scenarios.md)
- [Adicione o sessão com a Microsoft a uma aplicação web ASP.NET](quickstart-v1-aspnet-webapp.md)
