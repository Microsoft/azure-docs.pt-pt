---
title: Bibliotecas de gestão de autocarros de serviço saem Microsoft Docs
description: Este artigo explica como usar bibliotecas de gestão de ônibus de serviço azure para fornecer espaços e entidades de ônibus de serviço dinamicamente.
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: e1531d9b70860f498a3e38305f26eb862c9513f3
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901500"
---
# <a name="service-bus-management-libraries"></a>Bibliotecas de gestão do Service Bus

As bibliotecas de gestão de ônibus de serviço Azure podem fornecer espaços e entidades de ônibus de serviço dinamicamente. Isto permite implementações complexas e cenários de mensagens, e permite determinar programáticamente quais as entidades a fornecer. Estas bibliotecas estão atualmente disponíveis para .NET.

## <a name="supported-functionality"></a>Funcionalidade suportada

* Criação de espaço de nome, atualização, eliminação
* Criação de fila, atualização, eliminação
* Criação de tópicos, atualização, eliminação
* Criação de assinaturas, atualização, eliminação

## <a name="prerequisites"></a>Pré-requisitos

Para começar a utilizar as bibliotecas de gestão de ônibus de serviço, você deve autenticar com o serviço Azure Ative Directory (Azure AD). A Azure AD exige que se autentique como diretor de serviço, que fornece acesso aos seus recursos Azure. Para obter informações sobre a criação de um diretor de serviço, consulte um destes artigos:  

* [Utilize o portal Azure para criar aplicação e diretor ativo e diretor de serviço que possa aceder a recursos](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Utilize o Azure PowerShell para criar um principal de serviço para aceder aos recursos](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Utilize o CLI do Azure para criar um principal de serviço para aceder aos recursos](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

Estes tutoriais `AppId` fornecem-lhe `TenantId`um `ClientSecret` (ID do cliente), e (chave de autenticação), todos eles utilizados para autenticação pelas bibliotecas de gestão. Deve ter pelo menos permissões de proprietário de ônibus de [**serviço Azure**](/azure/role-based-access-control/built-in-roles#azure-service-bus-data-owner) ou [**solicitação**](/azure/role-based-access-control/built-in-roles#contributor) para o grupo de recursos em que deseja executar.

## <a name="programming-pattern"></a>Padrão de programação

O padrão para manipular qualquer recurso de ônibus de serviço segue um protocolo comum:

1. Obtenha um símbolo da Azure AD utilizando a biblioteca **Microsoft.IdentityModel.Clients.ActiveDirectory:**
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
   ```
2. Criar `ServiceBusManagementClient` o objeto:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Defina `CreateOrUpdate` os parâmetros para os valores especificados:

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. Executar a chamada:

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

## <a name="complete-code-to-create-a-queue"></a>Código completo para criar uma fila
Aqui está o código completo para criar uma fila de ônibus de serviço: 

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
> Para um exemplo completo, consulte a amostra de [gestão .NET no GitHub](https://github.com/Azure-Samples/service-bus-dotnet-management/). 

## <a name="next-steps"></a>Passos seguintes
[Referência microsoft.Azure.Management.ServiceBus API](/dotnet/api/Microsoft.Azure.Management.ServiceBus)
