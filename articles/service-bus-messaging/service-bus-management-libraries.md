---
title: Bibliotecas de gerenciamento do barramento de serviço do Azure | Microsoft Docs
description: Gerenciar namespaces do barramento de serviço e entidades de mensagens do .NET.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/05/2019
ms.author: aschhab
ms.openlocfilehash: faf0a5893b7de276b9a411745500daef4d39da6b
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816076"
---
# <a name="service-bus-management-libraries"></a>Bibliotecas de gestão do Service Bus

As bibliotecas de gerenciamento do barramento de serviço do Azure podem provisionar dinamicamente os namespaces e as entidades do barramento de serviço. Isso permite implantações complexas e cenários de mensagens e torna possível determinar programaticamente quais entidades provisionar. Essas bibliotecas estão atualmente disponíveis para .NET.

## <a name="supported-functionality"></a>Funcionalidades suportadas

* Criação de espaço de nomes, atualização, eliminação
* Criação de fila, atualização, exclusão
* Criação, atualização, exclusão de tópico
* Criação, atualização, exclusão de assinaturas

## <a name="prerequisites"></a>Pré-requisitos

Para começar a usar as bibliotecas de gerenciamento do barramento de serviço, você deve autenticar com o serviço do Azure Active Directory (AD do Azure). O AD do Azure requer que você autentique como uma entidade de serviço, que fornece acesso aos recursos do Azure. Para obter informações sobre a criação de um serviço principal, consulte um dos seguintes artigos:  

* [Utilizar o portal do Azure para criar um principal de serviço que pode aceder aos recursos e de aplicação do Active Directory](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Utilizar o Azure PowerShell para criar um principal de serviço para aceder aos recursos](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Utilizar a CLI do Azure para criar um principal de serviço para aceder aos recursos](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

Estes tutoriais fornecem uma `AppId` (ID de cliente), `TenantId`, e `ClientSecret` (chave de autenticação), as quais são utilizadas para autenticação, as bibliotecas de gestão. Você deve ter permissões de **proprietário** para o grupo de recursos no qual deseja executar.

## <a name="programming-pattern"></a>Padrão de programação

O padrão para manipular qualquer recurso do barramento de serviço segue um protocolo comum:

1. Obtenha um token do Azure AD usando a biblioteca **Microsoft. IdentityModel. clients. ActiveDirectory** :
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
   ```
2. Crie o `ServiceBusManagementClient` objeto:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Defina os `CreateOrUpdate` parâmetros para os valores especificados:

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. Execute a chamada:

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

## <a name="complete-code-to-create-a-queue"></a>Código completo para criar uma fila
Este é o código completo para criar uma fila do barramento de serviço: 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.Management.ServiceBus;
using Microsoft.Azure.Management.ServiceBus.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;

namespace SBusADApp
{
    class Program
    {
        static void Main(string[] args)
        {
            CreateQueue().GetAwaiter().GetResult();
        }

        private static async Task CreateQueue()
        {
            try
            {
                var subscriptionID = "<SUBSCRIPTION ID>";
                var resourceGroupName = "<RESOURCE GROUP NAME>";
                var namespaceName = "<SERVICE BUS NAMESPACE NAME>";
                var queueName = "<NAME OF QUEUE YOU WANT TO CREATE>";

                var token = await GetToken();

                var creds = new TokenCredentials(token);
                var sbClient = new ServiceBusManagementClient(creds)
                {
                    SubscriptionId = subscriptionID,
                };

                var queueParams = new SBQueue();

                Console.WriteLine("Creating queue...");
                await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, queueName, queueParams);
                Console.WriteLine("Created queue successfully.");
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not create a queue...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

        private static async Task<string> GetToken()
        {
            try
            {
                var tenantId = "<AZURE AD TENANT ID>";
                var clientId = "<APPLICATION/CLIENT ID>";
                var clientSecret = "<CLIENT SECRET>";

                var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

                var result = await context.AcquireTokenAsync(
                    "https://management.azure.com/",
                    new ClientCredential(clientId, clientSecret)
                );

                // If the token isn't a valid string, throw an error.
                if (string.IsNullOrEmpty(result.AccessToken))
                {
                    throw new Exception("Token result is empty!");
                }

                return result.AccessToken;
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not get a token...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

    }
}
```

> [!IMPORTANT]
> Para obter um exemplo completo, consulte o [exemplo de gerenciamento do .net no GitHub](https://github.com/Azure-Samples/service-bus-dotnet-management/). 

## <a name="next-steps"></a>Passos Seguintes
[Referência da API Microsoft. Azure. Management. ServiceBus](/dotnet/api/Microsoft.Azure.Management.ServiceBus)
