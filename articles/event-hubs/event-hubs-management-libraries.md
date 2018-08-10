---
title: Bibliotecas de gestão de Hubs de eventos do Azure | Documentos da Microsoft
description: Gerir espaços de nomes de Hubs de eventos e entidades do .NET
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 06/13/2018
ms.author: shvija
ms.openlocfilehash: 1b158fd3ac3f36b71a76c149e8b6815f576be1c5
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2018
ms.locfileid: "40006646"
---
# <a name="event-hubs-management-libraries"></a>Bibliotecas de gestão dos Hubs de Eventos

Pode usar as bibliotecas de gestão de Event Hubs do Azure para aprovisionar dinamicamente os espaços de nomes de Hubs de eventos e entidades. Este caráter dinâmico permite implementações complexas e cenários de mensagens, para que por meio de programação pode determinar quais entidades para aprovisionar. Essas bibliotecas estão atualmente disponíveis para .NET.

## <a name="supported-functionality"></a>Funcionalidades suportadas

* Criação de espaço de nomes, atualização, eliminação
* Criação de Hubs de eventos, atualização, eliminação
* A criação do grupo de consumidor, atualização, eliminação

## <a name="prerequisites"></a>Pré-requisitos

Para começar a utilizar as bibliotecas de gestão de Hubs de eventos, tem de autenticar com o Azure Active Directory (AAD). AAD requer que se autenticar como um principal de serviço, que fornece acesso aos recursos do Azure. Para obter informações sobre a criação de um serviço principal, consulte um dos seguintes artigos:  

* [Utilizar o portal do Azure para criar um principal de serviço que pode aceder aos recursos e de aplicação do Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md)
* [Utilizar o Azure PowerShell para criar um principal de serviço para aceder aos recursos](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [Utilizar a CLI do Azure para criar um principal de serviço para aceder aos recursos](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

Estes tutoriais fornecem uma `AppId` (ID de cliente), `TenantId`, e `ClientSecret` (chave de autenticação), as quais são utilizadas para autenticação, as bibliotecas de gestão. Tem de ter **proprietário** permissões para o grupo de recursos no qual pretende executar.

## <a name="programming-pattern"></a>Padrão de programação

O padrão para manipular a qualquer recurso de Hubs de eventos segue um protocolo comum:

1. Obter um token do AAD utilizando o `Microsoft.IdentityModel.Clients.ActiveDirectory` biblioteca.
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

1. Definir o `CreateOrUpdate` parâmetros para os valores especificados.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. Execute a chamada.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>Passos Seguintes
* [Amostra de gestão .NET](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Referência de Microsoft.Azure.Management.EventHub](/dotnet/api/Microsoft.Azure.Management.EventHub) 
