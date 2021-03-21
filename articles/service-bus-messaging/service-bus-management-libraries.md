---
title: Criar programáticamente entidades de autocarros da Azure Service | Microsoft Docs
description: Este artigo explica como utilizar dinamicamente ou programáticamente a prestação de espaços e entidades de nomes de Service Bus.
ms.devlang: dotnet
ms.topic: article
ms.date: 01/13/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 57192ab2ee1624cb18de832ac91c95290da727df
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98539877"
---
# <a name="dynamically-provision-service-bus-namespaces-and-entities"></a>Fornecimento dinâmico de espaços e entidades de nomes de autocarros de serviço 
As bibliotecas de gestão de autocarros da Azure Service podem providenciar dinamicamente espaços e entidades de nomes de Service Bus. Isto permite implementações complexas e cenários de mensagens, e permite determinar programáticamente quais as entidades a providenciar. Estas bibliotecas estão atualmente disponíveis para .NET.

## <a name="overview"></a>Descrição geral
Existem três bibliotecas de gestão disponíveis para criar e gerir entidades de Service Bus. A saber:

- [Azure.Messaging.ServiceBus.Administration](#azuremessagingservicebusadministration)
- [Microsoft.Azure.ServiceBus.Management](#microsoftazureservicebusmanagement)
- [Microsoft.Azure.Management.ServiceBus](#microsoftazuremanagementservicebus)

Todos estes pacotes de suporte criam, obtêm, listam, apagam, atualizam, apagam e atualizam operações em **filas, tópicos e subscrições.** Mas, apenas [o Microsoft.Azure.Management.ServiceBus](#microsoftazuremanagementservicebus) suporta criar, atualizar, listar, obter e apagar operações em **espaços de nomes,** listar e regenerar chaves SAS, e muito mais. 

A biblioteca Microsoft.Azure.Management.ServiceBus funciona apenas com a autenticação do Azure Ative Directory (Azure AD) e não suporta a utilização de uma cadeia de ligação. Enquanto as outras duas bibliotecas (Azure.Messaging.ServiceBus e Microsoft.Azure.ServiceBus) suportam utilizando uma cadeia de ligação para autenticação com o serviço e são mais fáceis de usar. Entre estas bibliotecas, a Azure.Messaging.ServiceBus é a mais recente e é isso que recomendamos que utilize.

As seguintes secções fornecem mais detalhes sobre estas bibliotecas. 

## <a name="azuremessagingservicebusadministration"></a>Azure.Messaging.ServiceBus.Administration
Pode utilizar a classe [ServiceBusAdministrationClient](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient) no [espaço de nomes Azure.Messaging.ServiceBus.Administration](/dotnet/api/azure.messaging.servicebus.administration) para gerir espaços de nome, filas, tópicos e subscrições. Aqui está o código de amostra. Para um exemplo completo, consulte [o exemplo crud](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/servicebus/Azure.Messaging.ServiceBus/tests/Samples/Sample07_CrudOperations.cs).

```csharp
using System;
using System.Threading.Tasks;

using Azure.Messaging.ServiceBus.Administration;

namespace adminClientTrack2
{
    class Program
    {
        public static void Main()
        {
            MainAsync().GetAwaiter().GetResult();
        }

        private static async Task MainAsync()
        {
            string connectionString = "SERVICE BUS NAMESPACE CONNECTION STRING";
            string QueueName = "QUEUE NAME";
            string TopicName = "TOPIC NAME";
            string SubscriptionName = "SUBSCRIPTION NAME";

            var adminClient = new ServiceBusAdministrationClient(connectionString);
            bool queueExists = await adminClient.QueueExistsAsync(QueueName);
            if (!queueExists)
            {
                var options = new CreateQueueOptions(QueueName)
                {
                    MaxDeliveryCount = 3                    
                };
                await adminClient.CreateQueueAsync(options);
            }


            bool topicExists = await adminClient.TopicExistsAsync(TopicName);
            if (!topicExists)
            {
                var options = new CreateTopicOptions(TopicName)
                {
                    MaxSizeInMegabytes = 1024
                };
                await adminClient.CreateTopicAsync(options);
            }

            bool subscriptionExists = await adminClient.SubscriptionExistsAsync(TopicName, SubscriptionName);
            if (!subscriptionExists)
            {
                var options = new CreateSubscriptionOptions(TopicName, SubscriptionName)
                {
                    DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0)
                };
                await adminClient.CreateSubscriptionAsync(options);
            }
        }
    }
}

```


## <a name="microsoftazureservicebusmanagement"></a>Microsoft.Azure.ServiceBus.Management 
Pode utilizar a classe [ManagementClient](/dotnet/api/microsoft.azure.servicebus.management.managementclient) no espaço de [nomes Microsoft.Azure.ServiceBus.Management](/dotnet/api/microsoft.azure.servicebus.management) para gerir espaços de nome, filas, tópicos e subscrições. Aqui está o código da amostra: 

> [!NOTE]
> Recomendamos que utilize a `ServiceBusAdministrationClient` aula da `Azure.Messaging.ServiceBus.Administration` biblioteca, que é a mais recente SDK. Para mais detalhes, consulte a [primeira secção.](#azuremessagingservicebusadministration) 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.ServiceBus.Management;

namespace SBusManagementClient
{
    class Program
    {
        public static void Main()
        {
            MainAsync().GetAwaiter().GetResult();
        }

        private static async Task MainAsync()
        {
            string connectionString = "SERVICE BUS NAMESPACE CONNECTION STRING";
            string QueueName = "QUEUE NAME";
            string TopicName = "TOPIC NAME";
            string SubscriptionName = "SUBSCRIPTION NAME";

            var managementClient = new ManagementClient(connectionString);
            bool queueExists = await managementClient.QueueExistsAsync(QueueName);
            if (!queueExists)
            {
                QueueDescription qd = new QueueDescription(QueueName);
                qd.MaxSizeInMB = 1024;
                qd.MaxDeliveryCount = 3;
                await managementClient.CreateQueueAsync(qd);
            }


            bool topicExists = await managementClient.TopicExistsAsync(TopicName);
            if (!topicExists)
            {
                TopicDescription td = new TopicDescription(TopicName);
                td.MaxSizeInMB = 1024;
                td.DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0);
                await managementClient.CreateTopicAsync(td);
            }

            bool subscriptionExists = await managementClient.SubscriptionExistsAsync(TopicName, SubscriptionName);
            if (!subscriptionExists)
            {
                SubscriptionDescription sd = new SubscriptionDescription(TopicName, SubscriptionName);
                sd.DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0);
                sd.MaxDeliveryCount = 3;
                await managementClient.CreateSubscriptionAsync(sd);
            }
        }
    }
}
```


## <a name="microsoftazuremanagementservicebus"></a>Microsoft.Azure.Management.ServiceBus 
Esta biblioteca faz parte do plano de controlo baseado em recursos Azure SDK. 

### <a name="prerequisites"></a>Pré-requisitos

Para começar a utilizar esta biblioteca, tem de autenticar com o serviço Azure Ative Directory (Azure AD). A Azure AD requer que você autentica como um principal de serviço, que fornece acesso aos seus recursos Azure. Para obter informações sobre a criação de um principal serviço, consulte um destes artigos:  

* [Utilize o portal Azure para criar aplicação de Diretório Ativo e diretor de serviços que possa aceder a recursos](../active-directory/develop/howto-create-service-principal-portal.md)
* [Utilize o Azure PowerShell para criar um principal de serviço para aceder aos recursos](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Utilize o CLI do Azure para criar um principal de serviço para aceder aos recursos](/cli/azure/create-an-azure-service-principal-azure-cli)

Estes tutoriais fornecem-lhe um `AppId` (ID do Cliente), `TenantId` e `ClientSecret` (chave de autenticação), todos eles utilizados para autenticação pelas bibliotecas de gestão. Deve ter pelo menos permissões de Proprietário de Dados de Autocarros do [**Serviço Azure**](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) ou [**do Contribuinte**](../role-based-access-control/built-in-roles.md#contributor) para o grupo de recursos em que deseja executar.

### <a name="programming-pattern"></a>Padrão de programação

O padrão para manipular qualquer recurso de Service Bus segue um protocolo comum:

1. Obtenha um token da AD AZure utilizando a **biblioteca Microsoft.IdentityModel.Clients.ActiveDirectory:**
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
   ```
2. Criar o `ServiceBusManagementClient` objeto:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Desa esta medida os `CreateOrUpdate` parâmetros dos valores especificados:

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

### <a name="complete-code-to-create-a-queue"></a>Código completo para criar uma fila
Aqui está o código de amostra para criar uma fila de autocarros de serviço. Para um exemplo completo, consulte a [amostra de gestão .NET no GitHub](https://github.com/Azure-Samples/service-bus-dotnet-management/). 

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

## <a name="fluent-library"></a>Biblioteca fluente
Para um exemplo de utilização da biblioteca Fluent para gerir entidades de Service Bus, consulte [esta amostra.](https://github.com/Azure/azure-libraries-for-net/tree/master/Samples/ServiceBus) 

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes tópicos de referência: 

- [Azure.Messaging.ServiceBus.Administration](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient)
- [Microsoft.Azure.ServiceBus.Management](/dotnet/api/microsoft.azure.servicebus.management.managementclient)
- [Microsoft.Azure.Management.ServiceBus](/dotnet/api/microsoft.azure.management.servicebus.servicebusmanagementclient)
- [Fluent](/dotnet/api/microsoft.azure.management.servicebus.fluent)