---
title: Alterações feitas em um projeto WebAPI quando você se conecta ao Azure AD
description: Descreve o que acontece com seu projeto WebAPI quando você se conecta ao Azure AD usando o Visual Studio
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32fc72d22d3f739018df22c315d7a1a3124c8823
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851808"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>O que aconteceu com meu projeto do WebAPI (Visual Studio Azure Active Directory serviço conectado)

> [!div class="op_single_selector"]
> - [Introdução](vs-active-directory-webapi-getting-started.md)
> - [O que aconteceu](vs-active-directory-webapi-what-happened.md)

Este artigo identifica as alterações exatas feitas no ASP.NET WebAPI, ASP.NET aplicativo de página única e projetos de API do ASP.NET do Azure ao adicionar o [serviço conectado do Azure Active Directory usando o Visual Studio](vs-active-directory-add-connected-service.md). Também se aplica aos projetos do serviço móvel do ASP.NET Azure no Visual Studio 2015.

Para obter informações sobre como trabalhar com o serviço conectado, consulte [introdução](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Referências adicionadas

Afeta as referências do arquivo de projeto *. NET `packages.config` ) e (referências do NuGet).

| Type | Referência |
| --- | --- |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.ActiveDirectory |
| .NET; NuGet | Microsoft.Owin.Security.Jwt |
| .NET; NuGet | Microsoft.Owin.Security.OAuth |
| .NET; NuGet | Owin |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |

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
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(Somente Visual Studio 2015) |
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
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Somente Visual Studio 2017: Também foi adicionada a seguinte entrada `<appSettings>`em "

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Elementos `<dependentAssembly>` adicionados sob o `<runtime><assemblyBinding>` nó para `System.IdentityModel.Tokens.Jwt`.

- Se você selecionou a opção **ler dados do diretório** , adicionou a seguinte entrada `<appSettings>`de configuração em:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Alterações e inclusões de código

- Adicionou o `[Authorize]` atributo a `Controllers/ValueController.cs` e qualquer outro controlador existente.

- Adicionada uma classe `App_Start/Startup.Auth.cs`de inicialização de autenticação,, que contém a lógica de inicialização para a autenticação do Azure ad ou a modificou de acordo. Se você tiver selecionado a opção **ler dados do diretório** , esse arquivo também conterá o código para receber um código OAuth e o trocará por um token de acesso.

- (Somente o Visual Studio 2015 com o aplicativo ASP.NET 4) Removido `App_Start/IdentityConfig.cs` e adicionado `Controllers/AccountController.cs`, `Models/IdentityModel.cs`e .`Providers/ApplicationAuthProvider.cs`

- Adicionado `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) ou `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), contendo informações que o Visual Studio usa para acompanhar a adição do serviço conectado.

### <a name="file-backup-visual-studio-2015"></a>Backup de arquivo (Visual Studio 2015)

Ao adicionar o serviço conectado, o Visual Studio 2015 faz backup dos arquivos alterados e removidos. Todos os arquivos afetados são salvos na pasta `Backup/AzureAD`. O Visual Studio 2017 não cria backups.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Alterações no Azure

- Um aplicativo do Azure AD foi criado no domínio que você selecionou ao adicionar o serviço conectado.
- O aplicativo foi atualizado para incluir a permissão **ler dados do diretório** se essa opção tiver sido selecionada.

[Saiba mais sobre Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Passos seguintes

- [Cenários de autenticação para Azure Active Directory](authentication-scenarios.md)
- [Adicionar entrada com a Microsoft a um aplicativo Web ASP.NET](quickstart-v1-aspnet-webapp.md)
