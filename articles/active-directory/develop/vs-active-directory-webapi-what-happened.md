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
ms.openlocfilehash: 595a89ca58e970a9c886d0b6c2dd05aecd1411ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159391"
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
| .net; Nuget | Microsoft.Owin |
| .net; Nuget | Microsoft.Owin.Host.SystemWeb |
| .net; Nuget | Microsoft.Owin.Security |
| .net; Nuget | Microsoft.Owin.Security.ActiveDirectory |
| .net; Nuget | Microsoft.Owin.Security.Jwt |
| .net; Nuget | Microsoft.Owin.Security.OAuth |
| .net; Nuget | Rio Owin |
| .net; Nuget | System.IdentityModel.Tokens.Jwt |

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
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(Apenas Visual Studio 2015) |
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
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Visual Studio 2017 apenas: Também `<appSettings>`acrescentou a seguinte entrada em "

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Elementos `<dependentAssembly>` adicionados `<runtime><assemblyBinding>` sob `System.IdentityModel.Tokens.Jwt`o nó para .

- Se selecionou a opção de dados do `<appSettings>` **diretório Read,** adicione a seguinte entrada de configuração em :

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Alterações e adições de código

- Acrescentou `[Authorize]` o `Controllers/ValueController.cs` atributo e quaisquer outros controladores existentes.

- Adicionou uma classe `App_Start/Startup.Auth.cs`de startup de autenticação, contendo lógica de arranque para autenticação ad.audazes da Azure AD, ou modificou-a em conformidade. Se selecionou a opção de dados do **diretório Read,** este ficheiro também contém código para receber um código OAuth e trocá-lo por um token de acesso.

- (Visual Studio 2015 apenas com ASP.NET 4 aplicações) Removido `App_Start/IdentityConfig.cs` e `Controllers/AccountController.cs` `Models/IdentityModel.cs`adicionado, `Providers/ApplicationAuthProvider.cs`e .

- Adicionado `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) ou `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), contendo informações que o Visual Studio usa para acompanhar a adição do serviço conectado.

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
- [Adicione o sessão com a Microsoft a uma aplicação web ASP.NET](quickstart-v2-aspnet-webapp.md)
