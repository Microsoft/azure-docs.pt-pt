---
title: Bibliotecas de gestão de autocarros da Azure Service/ Microsoft Docs
description: Este artigo explica como usar bibliotecas de gestão de autocarros da Azure Service para provisão dinâmica de espaços e entidades de nomes de Service Bus.
ms.devlang: dotnet
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 915606bffc2037c8fcd1a7d33218143f40c78f2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89008051"
---
# <a name="service-bus-management-libraries"></a>Bibliotecas de gestão do Service Bus

As bibliotecas de gestão de autocarros da Azure Service podem providenciar dinamicamente espaços e entidades de nomes de Service Bus. Isto permite implementações complexas e cenários de mensagens, e permite determinar programáticamente quais as entidades a providenciar. Estas bibliotecas estão atualmente disponíveis para .NET.

## <a name="supported-functionality"></a>Funcionalidade suportada

* Criação de espaço de nome, atualização, eliminação
* Criação de fila, atualização, eliminação
* Criação de tópicos, atualização, eliminação
* Criação de assinatura, atualização, eliminação

## <a name="prerequisites"></a>Pré-requisitos

Para começar a utilizar as bibliotecas de gestão de serviços do Service Bus, tem de autenticar com o serviço Azure Ative Directory (Azure AD). A Azure AD requer que você autentica como um principal de serviço, que fornece acesso aos seus recursos Azure. Para obter informações sobre a criação de um principal serviço, consulte um destes artigos:  

* [Utilize o portal Azure para criar aplicação de Diretório Ativo e diretor de serviços que possa aceder a recursos](../active-directory/develop/howto-create-service-principal-portal.md)
* [Utilize o Azure PowerShell para criar um principal de serviço para aceder aos recursos](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Utilize o CLI do Azure para criar um principal de serviço para aceder aos recursos](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)

Estes tutoriais fornecem-lhe um `AppId` (ID do Cliente), `TenantId` e `ClientSecret` (chave de autenticação), todos eles utilizados para autenticação pelas bibliotecas de gestão. Deve ter pelo menos permissões de Proprietário de Dados de Autocarros do [**Serviço Azure**](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) ou [**do Contribuinte**](../role-based-access-control/built-in-roles.md#contributor) para o grupo de recursos em que deseja executar.

## <a name="programming-pattern"></a>Padrão de programação

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

## <a name="complete-code-to-create-a-queue"></a>Código completo para criar uma fila
Aqui está o código completo para criar uma fila de autocarros de serviço: 

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
> Para um exemplo completo, consulte a [amostra de gestão .NET no GitHub](https://github.com/Azure-Samples/service-bus-dotnet-management/). 

## <a name="next-steps"></a>Passos seguintes
[Referência Microsoft.Azure.Management.ServiceBus API](/dotnet/api/Microsoft.Azure.Management.ServiceBus)