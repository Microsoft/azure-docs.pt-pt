---
title: Alterações efetuadas a um projeto WebAPI quando ligar ao Azure AD
description: Descreve o que acontece ao seu projeto WebAPI quando ligar ao Azure AD com o Visual Studio
services: active-directory
author: ghogen
manager: douge
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f4946251cf72d7869ec5fc2f0fd844b9c06ac34
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60353283"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>O que aconteceu ao meu projeto WebAPI (serviço ligado do Visual Studio do Azure Active Directory)

> [!div class="op_single_selector"]
> - [Introdução](vs-active-directory-webapi-getting-started.md)
> - [O que aconteceu](vs-active-directory-webapi-what-happened.md)

Este artigo identifica as alterações exatas feitas aos projetos de ASP.NET WebAPI, aplicação de página única do ASP.NET e ASP.NET Azure API ao adicionar o [com o Visual Studio do serviço de ligado do Azure Active Directory](vs-active-directory-add-connected-service.md). Também se aplica aos projetos de serviço do ASP.NET do Azure Mobile no Visual Studio 2015.

Para obter informações sobre como trabalhar com o serviço ligado, consulte [introdução ao](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Foi adicionadas referências

Afeta as referências *.NET do arquivo de projeto) e `packages.config` (referências de NuGet).

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

Referências adicionais, se tiver selecionado o **ler dados do diretório** opção:

| Tipo | Referência |
| --- | --- |
| .NET; NuGet | EntityFramework |
| .NET        | EntityFramework.SqlServer (Visual Studio 2015 apenas) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(Visual Studio 2015 apenas) |
| .NET; NuGet | System.Spatial |

As seguintes referências são removidas (ASP.NET 4 projetos apenas, como no Visual Studio 2015):

| Tipo | Referência |
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
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Visual Studio 2017 apenas: Também adicionou a seguinte entrada em `<appSettings>`"

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Adicionado `<dependentAssembly>` elementos sob o `<runtime><assemblyBinding>` nó para `System.IdentityModel.Tokens.Jwt`.

- Se tiver selecionado o **ler dados do diretório** opção, adicionar a seguinte entrada de configuração em `<appSettings>`:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Alterações de código e adições

- Adicionado o `[Authorize]` atributo para `Controllers/ValueController.cs` e quaisquer outros controladores existentes.

- Adicionado uma classe de startup da autenticação, `App_Start/Startup.Auth.cs`, que contém a lógica de arranque para a autenticação do Azure AD ou modificado-lo em conformidade. Se tiver selecionado o **ler dados do diretório** opção, este ficheiro também contém código para receber um código de OAuth e o exchange-lo para um token de acesso.

- (Visual Studio 2015 com o ASP.NET 4 apenas aplicação) Removido `App_Start/IdentityConfig.cs` e adicionada `Controllers/AccountController.cs`, `Models/IdentityModel.cs`, e `Providers/ApplicationAuthProvider.cs`.

- Adicionado `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) ou `Service References/Azure AD/ConnectedService.json` informações de contentor (Visual Studio 2015), que utiliza o Visual Studio para controlar a adição do serviço ligado.

### <a name="file-backup-visual-studio-2015"></a>Cópia de segurança de ficheiros (Visual Studio 2015)

Ao adicionar o serviço ligado, Visual Studio 2015 efetua uma cópia de segurança de ficheiros alterados e removidos. Todos os arquivos afetados são guardados na pasta `Backup/AzureAD`. Visual Studio 2017 não cria cópias de segurança.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Alterações no Azure

- Criar uma aplicação do Azure AD no domínio selecionado ao adicionar o serviço ligado.
- Atualizada da aplicação para incluir o **ler dados do diretório** permissão se essa opção tiver sido selecionada.

[Saiba mais sobre o Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Passos Seguintes

- [Cenários de autenticação do Azure Active Directory](authentication-scenarios.md)
- [Adicionar início de sessão com a Microsoft a uma aplicação web ASP.NET](quickstart-v1-aspnet-webapp.md)
