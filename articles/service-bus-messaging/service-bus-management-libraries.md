---
title: Bibliotecas de gestão do Service Bus do Azure | Documentos da Microsoft
description: Gerir espaços de nomes do Service Bus e entidades do .NET de mensagens.
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
ms.openlocfilehash: 3836eb87516eed546ae6bb69f53bf64e5df00906
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693206"
---
# <a name="service-bus-management-libraries"></a>Bibliotecas de gestão do Service Bus

As bibliotecas de gestão do Azure Service Bus podem aprovisionar dinamicamente espaços de nomes do Service Bus e entidades. Isto permite implementações complexas e cenários de mensagens e torna possível determinar por meio de programação que entidades a aprovisionar. Essas bibliotecas estão atualmente disponíveis para .NET.

## <a name="supported-functionality"></a>Funcionalidades suportadas

* Criação de espaço de nomes, atualização, eliminação
* Criação da fila, atualização, eliminação
* Criação de tópico, atualização, eliminação
* Criação da subscrição, atualização, eliminação

## <a name="prerequisites"></a>Pré-requisitos

Para começar a utilizar as bibliotecas de gestão do Service Bus, tem de autenticar com o serviço do Azure Active Directory (Azure AD). Azure AD requer que se autenticar como um principal de serviço, que fornece acesso aos recursos do Azure. Para obter informações sobre a criação de um serviço principal, consulte um dos seguintes artigos:  

* [Utilizar o portal do Azure para criar um principal de serviço que pode aceder aos recursos e de aplicação do Active Directory](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Utilizar o Azure PowerShell para criar um principal de serviço para aceder aos recursos](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Utilizar a CLI do Azure para criar um principal de serviço para aceder aos recursos](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

Estes tutoriais fornecem uma `AppId` (ID de cliente), `TenantId`, e `ClientSecret` (chave de autenticação), as quais são utilizadas para autenticação, as bibliotecas de gestão. Tem de ter **proprietário** permissões para o grupo de recursos no qual pretende executar.

## <a name="programming-pattern"></a>Padrão de programação

O padrão para manipular a qualquer recurso do Service Bus segue um protocolo comum:

1. Obter um token a partir do Azure AD com o **ActiveDirectory** biblioteca:
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.core.windows.net/", new ClientCredential(clientId, clientSecret));
   ```
2. Criar o `ServiceBusManagementClient` objeto:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Definir o `CreateOrUpdate` parâmetros para os valores especificados:

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
Eis o código completo para criar uma fila do Service Bus: 

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
                    "https://management.core.windows.net/",
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
> Para obter um exemplo completo, consulte a [exemplo de gestão do .NET no GitHub]((https://github.com/Azure-Samples/service-bus-dotnet-management/)). 

## <a name="next-steps"></a>Passos Seguintes
[Referência da API de Microsoft.Azure.Management.ServiceBus](/dotnet/api/Microsoft.Azure.Management.ServiceBus)
