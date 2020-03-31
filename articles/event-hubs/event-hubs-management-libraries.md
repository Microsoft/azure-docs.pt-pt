---
title: Bibliotecas de gestão - Azure Event Hubs Microsoft Docs
description: Este artigo fornece informações sobre a biblioteca que pode usar para gerir espaços e entidades do Azure Event Hubs a partir de .NET.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 431fe04461f422274697d1e91c4b56e914ce2d4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60746663"
---
# <a name="event-hubs-management-libraries"></a>Bibliotecas de gestão dos Hubs de Eventos

Você pode usar as bibliotecas de gestão de Hubs de Eventos Azure para fornecer espaços e entidades de eventos de forma dinâmica. Esta natureza dinâmica permite implementações complexas e cenários de mensagens, para que possa determinar programáticamente quais as entidades a fornecer. Estas bibliotecas estão atualmente disponíveis para .NET.

## <a name="supported-functionality"></a>Funcionalidade suportada

* Criação de espaço de nome, atualização, eliminação
* Criação de Hubs de Eventos, atualização, eliminação
* Criação, atualização, eliminação do Grupo de Consumidores

## <a name="prerequisites"></a>Pré-requisitos

Para começar a usar as bibliotecas de gestão de Hubs de Eventos, deve autenticar com o Azure Ative Directory (AAD). A AAD exige que se autentique como diretor de serviço, que fornece acesso aos seus recursos Azure. Para obter informações sobre a criação de um diretor de serviço, consulte um destes artigos:  

* [Utilize o portal Azure para criar aplicação e diretor ativo e diretor de serviço que possa aceder a recursos](../active-directory/develop/howto-create-service-principal-portal.md)
* [Utilize o Azure PowerShell para criar um principal de serviço para aceder aos recursos](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Utilize o CLI do Azure para criar um principal de serviço para aceder aos recursos](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

Estes tutoriais `AppId` fornecem-lhe `TenantId`um `ClientSecret` (ID do cliente), e (chave de autenticação), todos eles utilizados para autenticação pelas bibliotecas de gestão. Deve ter permissões do **Proprietário** para o grupo de recursos em que pretende executar.

## <a name="programming-pattern"></a>Padrão de programação

O padrão para manipular qualquer recurso do Event Hubs segue um protocolo comum:

1. Obtenha um sinal da AAD usando a `Microsoft.IdentityModel.Clients.ActiveDirectory` biblioteca.
    ```csharp
    var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Crie `EventHubManagementClient` o objeto.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Defina `CreateOrUpdate` os parâmetros para os valores especificados.
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
* [.NET Amostra de gestão](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Microsoft.Azure.Management.EventHub Reference](/dotnet/api/Microsoft.Azure.Management.EventHub) 
