---
title: Bibliotecas de gestão - Azure Event Hubs| Microsoft Docs
description: Este artigo fornece informações sobre a biblioteca que pode utilizar para gerir espaços de nomes e entidades do Azure Event Hubs a partir de .NET.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 74392fbf0b2c0b81898410af8027a4f13fc52b67
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89014001"
---
# <a name="event-hubs-management-libraries"></a>Bibliotecas de gestão dos Hubs de Eventos

Você pode usar as bibliotecas de gestão Azure Event Hubs para provisão dinâmica de espaços e entidades de nomes de Event Hubs. Esta natureza dinâmica permite implementações complexas e cenários de mensagens, para que possa determinar programáticamente quais as entidades a providenciar. Estas bibliotecas estão atualmente disponíveis para .NET.

## <a name="supported-functionality"></a>Funcionalidade suportada

* Criação de espaço de nome, atualização, eliminação
* Criação, atualização, eliminação de Hubs de Eventos
* Criação, atualização, eliminação do Grupo de Consumidores

## <a name="prerequisites"></a>Pré-requisitos

Para começar a utilizar as bibliotecas de gestão de Centros de Eventos, tem de autenticar com o Azure Ative Directory (AAD). A AAD requer que autente como diretor de serviço, o que dá acesso aos seus recursos Azure. Para obter informações sobre a criação de um principal serviço, consulte um destes artigos:  

* [Utilize o portal Azure para criar aplicação de Diretório Ativo e diretor de serviços que possa aceder a recursos](../active-directory/develop/howto-create-service-principal-portal.md)
* [Utilize o Azure PowerShell para criar um principal de serviço para aceder aos recursos](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Utilize o CLI do Azure para criar um principal de serviço para aceder aos recursos](/cli/azure/create-an-azure-service-principal-azure-cli)

Estes tutoriais fornecem-lhe um `AppId` (ID do Cliente), `TenantId` e `ClientSecret` (chave de autenticação), todos eles utilizados para autenticação pelas bibliotecas de gestão. Tem de ter permissões **do Proprietário** para o grupo de recursos em que pretende executar.

## <a name="programming-pattern"></a>Padrão de programação

O padrão para manipular qualquer recurso de Event Hubs segue um protocolo comum:

1. Obtenha um token da AAD utilizando a `Microsoft.IdentityModel.Clients.ActiveDirectory` biblioteca.
    ```csharp
    var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Criar o `EventHubManagementClient` objeto.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Desa estale os `CreateOrUpdate` parâmetros para os valores especificados.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. Execute a ligação.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>Passos seguintes
* [Amostra de gestão .NET](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Microsoft.Azure.Management.EventHub Referência](/dotnet/api/Microsoft.Azure.Management.EventHub) 
