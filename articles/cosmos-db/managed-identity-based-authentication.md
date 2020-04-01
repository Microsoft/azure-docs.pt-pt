---
title: Como usar uma identidade gerida atribuída pelo sistema para aceder aos dados da Azure Cosmos DB
description: Saiba como configurar uma identidade gerida atribuída ao sistema Azure AD para aceder a chaves do Azure Cosmos DB. msi, identidade de serviço gerida, ad, diretório ativo azul, identidade
author: j-patrick
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.openlocfilehash: 102efba5d028eef621f392ef1739ea9ebeca0b44
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80417236"
---
# <a name="how-to-use-a-system-assigned-managed-identity-to-access-azure-cosmos-db-data"></a>Como usar uma identidade gerida atribuída pelo sistema para aceder aos dados da Azure Cosmos DB

Neste artigo vai criar uma solução robusta e chave de **rotação agnóstica,** para aceder às teclas Db da Azure Cosmos, alavancando [identidades geridas.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) O exemplo neste artigo usa uma Função Azure. No entanto, pode alcançar esta solução utilizando qualquer serviço que suporte identidades geridas. 

Você vai aprender a criar uma Função Azure que pode aceder a Azure Cosmos DB sem precisar de copiar quaisquer chaves Azure Cosmos DB. A função acordará a cada minuto e registará a temperatura atual de um aquário. Para aprender a configurar um temporizador acionado Função Azure, consulte a [função Criar uma função em Azure que é desencadeada por um artigo temporizador.](../azure-functions/functions-create-scheduled-function.md)

Para simplificar o cenário, a limpeza de documentos de temperatura mais antigos é tratada por uma configuração já configurada [time to live.](./time-to-live.md) 

## <a name="assign-a-system-assigned-managed-identity-to-an-azure-function"></a>Atribuir uma identidade gerida atribuída pelo sistema a uma Função Azure

Neste passo, atribuirá uma identidade gerida atribuída ao sistema à sua Função Azure.

1. No [portal Azure,](https://portal.azure.com/)abra o painel **função Azure** e navegue para a sua aplicação de função. 

1. Abra a **Plataforma com** > separador**identidade:** 

   ![Separador de identidade](./media/managed-identity-based-authentication/identity-tab-selection.png)

1. No separador **Identidade,** **ligue** o estado de identidade do **sistema.** Certifique-se de selecionar **Save**, e confirmar que pretende ativar a identidade do sistema. No final, o painel de identidade do **sistema** deve ser o seguinte:  

   ![Identidade do sistema ligada](./media/managed-identity-based-authentication/identity-tab-system-managed-on.png)

## <a name="grant-the-managed-identity-access-to-your-azure-cosmos-account"></a>Conceda o acesso de identidade gerido à sua conta Azure Cosmos

Neste passo, atribuirá um papel à identidade gerida atribuída pelo sistema da Função Azure. A Azure Cosmos DB tem múltiplas funções incorporadas que pode atribuir à identidade gerida. Para esta solução, utilizará as seguintes duas funções:

|Papel incorporado  |Descrição  |
|---------|---------|
|[Contribuinte de Conta DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Pode gerir as contas da Azure Cosmos DB. Permite a recuperação de teclas de leitura/escrita. |
|[Leitor de Conta Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Pode ler os dados da conta Azure Cosmos DB. Permite a recuperação das chaves de leitura. |

> [!IMPORTANT]
> O suporte rBAC no Azure Cosmos DB é aplicável apenas ao controlo das operações de aviões. As operações de avião de dados são asseguradas utilizando chaves-me-mestre ou fichas de recursos. Para saber mais, consulte o acesso seguro ao artigo de [dados.](secure-access-to-data.md)

> [!TIP] 
> Ao atribuir funções, atribua apenas o acesso necessário. Se o seu serviço necessitar apenas de dados de leitura, atribua a identidade gerida ao papel de Leitor de **Conta Cosmos DB.** Para mais informações sobre a importância do acesso menos privilegiado, consulte a [menor exposição das contas privilegiadas.](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts)

Para o seu cenário, você vai ler a temperatura e, em seguida, escrever esses dados para um recipiente em Azure Cosmos DB. Como tem de escrever os dados, utilizará a função **de Contribuinte de Conta DocumentDB.** 

1. Inscreva-se no portal Azure e navegue na sua conta Azure Cosmos DB. Abra o Painel de Gestão de **Acesso (IAM)** e, em seguida, o separador de atribuições de **funções:**

   ![Painel IAM](./media/managed-identity-based-authentication/cosmos-db-iam-tab.png)

1. Selecione o botão **+ Adicionar** e, em seguida, **adicionar a atribuição de funções**.

1. O painel de atribuição de **funções adicionais** abre-se para a direita:

   ![Adicionar Papel](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png)

   * **Função** - Selecionar Colaborador de **Conta DocumentDB**
   * **Atribuir acesso a** - Sob a subsecção de **identidade gerida atribuída** pelo Sistema Select, selecione App **de Funções**.
   * **Selecione** - O painel será preenchido com todas as aplicações de função, na sua subscrição, que tenham uma **Identidade de Sistema Gerido**. No nosso caso, seleciono a aplicação de função **SummaryService:** 

      ![Selecione Atribuição](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png)

1. Depois de a identidade da aplicação de função ser selecionada, clique em **Guardar**.

## <a name="programmatically-access-the-azure-cosmos-db-keys-from-the-azure-function"></a>Aceda programáticamente as teclas Azure Cosmos DB da Função Azure

Agora temos uma aplicação de função que tem uma identidade gerida atribuída pelo sistema. Essa identidade é dada ao papel de Contribuinte de **Conta DocumentDB** nas permissões do Azure Cosmos DB. O código de aplicação de funções seguinte receberá as chaves Azure Cosmos DB, criará um objeto CosmosClient, obterá a temperatura e, em seguida, guardá-lo para cosmos DB.

Esta amostra utiliza a [API list Keys](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListKeys) para aceder às chaves da conta Azure Cosmos DB.

> [!IMPORTANT] 
> Se quiser atribuir o papel de Leitor de [ **Conta Cosmos DB,** ](#grant-the-managed-identity-access-to-your-azure-cosmos-account) terá de utilizar apenas list [Keys api](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListReadOnlyKeys). Isto só vai povoar as chaves de leitura.

A API da `DatabaseAccountListKeysResult` Lista de Chaves devolve o objeto. Este tipo não é definido nas bibliotecas C# O seguinte código mostra a implementação desta classe:  

```csharp 
namespace SummarizationService 
{
  public class DatabaseAccountListKeysResult
  {
      public string primaryMasterKey {get;set;}
      public string primaryReadonlyMasterKey {get; set;}
      public string secondaryMasterKey {get; set;}
      public string secondaryReadonlyMasterKey {get;set;}
  }
}
```

O exemplo também usa um documento simples chamado "TemperatureRecord", que é definido da seguinte forma:

```csharp
using System;

namespace Monitor
{
    public class TemperatureRecord
    {
        public string id { get; set; } = Guid.NewGuid().ToString();
        public DateTime RecordTime { get; set; }
        public int Temperature { get; set; }

    }
}
```

Utilizará a biblioteca [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) para obter o símbolo de identidade gerido atribuído pelo sistema. Para saber outras formas de obter `Microsoft.Azure.Service.AppAuthentication` o símbolo e mais informações sobre a biblioteca, consulte o artigo [Serviço de Autenticação](../key-vault/service-to-service-authentication.md) de Serviços.

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;

namespace Monitor
{
    public static class TemperatureMonitor
    {
        private static string subscriptionId =
        "<azure subscription id>";
        private static string resourceGroupName =
        "<name of your azure resource group>";
        private static string accountName =
        "<Azure Cosmos DB account name>";
        private static string cosmosDbEndpoint =
        "<Azure Cosmos DB endpoint>";
        private static string databaseName =
        "<Azure Cosmos DB name>";
        private static string containerName =
        "<container to store the temperature in>";

        [FunctionName("TemperatureMonitor")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // In order to get the Service Managed token we need to authenticate to the Azure Resource Manager.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // To get the Azure Cosmos DB keys setup the List Keys API:
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the Result back as a DatabaseAccountListKeysResult.
            DatabaseAccountListKeysResult keys = await result.Content.ReadAsAsync<DatabaseAccountListKeysResult>();

            log.LogInformation("Starting to create the client");

            CosmosClient client = new CosmosClient(cosmosDbEndpoint, keys.primaryMasterKey);

            log.LogInformation("Client created");

            var database = client.GetDatabase(databaseName);
            var container = database.GetContainer(containerName);

            log.LogInformation("Get the temperature.");

            var tempRecord = new TemperatureRecord() { RecordTime = DateTime.UtcNow, Temperature = GetTemperature() };

            log.LogInformation("Store temperature");

            await container.CreateItemAsync<TemperatureRecord>(tempRecord);

            log.LogInformation($"Ending temperature monitor: {DateTime.Now}");
        }

        private static int GetTemperature()
        {
            // fake the temperature sensor for this demo
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

Está agora pronto para implementar a [sua Função Azure.](../azure-functions/functions-create-first-function-vs-code.md)

## <a name="next-steps"></a>Passos seguintes

* [Autenticação baseada em certificado saca-se com Azure Cosmos DB e Diretório Ativo](certificate-based-authentication.md)
* [Proteger as chaves do Azure Cosmos com o Azure Key Vault](access-secrets-from-keyvault.md)
* [Base de segurança para Azure Cosmos DB](security-baseline.md)
