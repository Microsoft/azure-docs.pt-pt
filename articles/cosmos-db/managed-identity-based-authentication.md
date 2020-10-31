---
title: Como utilizar uma identidade gerida atribuída pelo sistema para aceder aos dados do Azure Cosmos DB
description: Saiba como configurar um Azure Ative Directory (Azure AD) com identidade gerida (identidade de serviço gerida) para aceder às chaves da Azure Cosmos DB.
author: j-patrick
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 918033f736a28534cd36a4637b41d0a6b3b4cdc7
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93088578"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>Utilize identidades geridas atribuídas ao sistema para aceder aos dados do Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Neste artigo, irá configurar uma solução *agnóstica de rotação robusta e chave* para aceder às teclas DB da Azure Cosmos utilizando [identidades geridas.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) O exemplo neste artigo utiliza funções Azure, mas pode utilizar qualquer serviço que suporte identidades geridas. 

Você vai aprender como criar uma app de função que pode aceder a dados DB da Azure Cosmos sem precisar de copiar quaisquer teclas DB do Azure Cosmos. A aplicação de função acordará a cada minuto e registará a temperatura atual de um aquário. Para aprender a configurar uma aplicação de função desencadeada pelo temporizador, consulte a [Criar uma função em Azure que é desencadeada por um artigo do temporizador.](../azure-functions/functions-create-scheduled-function.md)

Para simplificar o cenário, uma definição [time to live](./time-to-live.md) já está configurada para limpar documentos de temperatura mais antigos. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>Atribuir uma identidade gerida atribuída ao sistema a uma aplicação de função

Neste passo, irá atribuir uma identidade gerida pelo sistema à sua aplicação de função.

1. No [portal Azure,](https://portal.azure.com/)abra o painel **Azure Function** e vá para a sua aplicação de função. 

1. Abra o **separador identidade** da  >  **Identity** Plataforma: 

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-selection.png" alt-text="Screenshot mostrando funcionalidades da Plataforma e opções de identidade para a aplicação de função.":::

1. No **separador Identidade,** **ligue** o **estado** de identidade do sistema e selecione **Guardar** . O **painel de identidade** deve olhar da seguinte forma:  

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-system-managed-on.png" alt-text="Screenshot mostrando funcionalidades da Plataforma e opções de identidade para a aplicação de função.":::

## <a name="grant-access-to-your-azure-cosmos-account"></a>Conceder acesso à sua conta Azure Cosmos

Neste passo, atribuirá um papel à identidade gerida atribuída pelo sistema da aplicação de funções. A Azure Cosmos DB tem múltiplos papéis incorporados que pode atribuir à identidade gerida. Para esta solução, utilizará as seguintes duas funções:

|Papel incorporado  |Description  |
|---------|---------|
|[Colaborador de Conta DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Pode gerir as contas de DB da Azure Cosmos. Permite a recuperação de teclas de leitura/escrita. |
|[Papel do leitor de conta de cosm de Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Pode ler os dados da conta DB da Azure Cosmos. Permite a recuperação das chaves de leitura. |

> [!IMPORTANT]
> O suporte ao controlo de acesso baseado em funções na Azure Cosmos DB aplica-se apenas às operações de controlo do avião. As operações de planos de dados são protegidas através de chaves primárias ou fichas de recursos. Para saber mais, consulte o Acesso Seguro ao artigo [dados.](secure-access-to-data.md)

> [!TIP] 
> Quando atribuir funções, atribua apenas o acesso necessário. Se o seu serviço necessitar apenas de ler dados, então atribua a função **de Leitor de Conta Cosmos DB** à identidade gerida. Para mais informações sobre a importância do acesso menos privilegiado, consulte a exposição menor do artigo [contas privilegiadas.](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts)

Neste cenário, a aplicação de função irá ler a temperatura do aquário e, em seguida, escrever esses dados para um recipiente em Azure Cosmos DB. Como a aplicação de função deve escrever os dados, terá de atribuir a **função de Contribuinte de Conta DocumentDB.** 

### <a name="assign-the-role-using-azure-portal"></a>Atribuir o papel usando o portal Azure

1. Inscreva-se no portal Azure e vá à sua conta DB Azure Cosmos. Abra o painel **de controlo de acesso (IAM)** e, em seguida, o **separador atribuições de funções:**

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab.png" alt-text="Screenshot mostrando funcionalidades da Plataforma e opções de identidade para a aplicação de função.":::

1. Selecione **+ Adicionar** > **Adicionar atribuição de função** .

1. O painel **de atribuição de funções Add** abre à direita:

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png" alt-text="Screenshot mostrando funcionalidades da Plataforma e opções de identidade para a aplicação de função.":::

   * **Função** : Selecione **Colaborador de Conta DocumentDB**
   * **Atribua acesso a:** Sob a **subsecção de identidade gerida atribuída pelo sistema Select,** selecione **App de Função** .
   * **Selecione** : O painel será preenchido com todas as aplicações de função na sua subscrição que tenham uma **Identidade do Sistema Gerido.** Neste caso, selecione a aplicação de função **FishTankTemperatureService:** 

      :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png" alt-text="Screenshot mostrando funcionalidades da Plataforma e opções de identidade para a aplicação de função.":::

1. Depois de ter selecionado a aplicação de função, **selecione Save** .

### <a name="assign-the-role-using-azure-cli"></a>Atribuir o papel usando O Azure CLI

Para atribuir o papel utilizando o Azure CLI, abra a Azure Cloud Shell e execute os seguintes comandos:

```azurecli-interactive

scope=$(az cosmosdb show --name '<Your_Azure_Cosmos_account_name>' --resource-group '<CosmosDB_Resource_Group>' --query id)

principalId=$(az webapp identity show -n '<Your_Azure_Function_name>' -g '<Azure_Function_Resource_Group>' --query principalId)

az role assignment create --assignee $principalId --role "DocumentDB Account Contributor" --scope $scope
```

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>Aceda programáticamente às teclas DB do Azure Cosmos

Agora temos uma aplicação de função que tem uma identidade gerida atribuída ao sistema com o papel **de Contribuinte de Conta DocumentDB** nas permissões DB do Azure Cosmos. O seguinte código de aplicação de função obterá as teclas DB do Azure Cosmos, criará um objeto CosmosClient, obterá a temperatura do aquário e, em seguida, guardá-lo para Azure Cosmos DB.

Esta amostra utiliza a [API de Chaves de Lista](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listkeys) para aceder às chaves da conta Azure Cosmos DB.

> [!IMPORTANT] 
> Se quiser [atribuir a função Cosmos DB Account Reader,](#grant-access-to-your-azure-cosmos-account) terá de utilizar a [Lista De Leitura única chaves API](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listreadonlykeys). Isto vai preencher apenas as chaves só de leitura.

A API de Chaves de Lista devolve o `DatabaseAccountListKeysResult` objeto. Este tipo não está definido nas bibliotecas C. O seguinte código mostra a implementação desta classe:  

```csharp 
namespace Monitor 
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

Você usará a biblioteca [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) para obter o token de identidade gerido atribuído pelo sistema. Para aprender outras formas de obter o token e descobrir mais informações sobre a `Microsoft.Azure.Service.AppAuthentication` biblioteca, consulte o artigo [de autenticação serviço-a-serviço.](../key-vault/general/service-to-service-authentication.md)


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
    public static class FishTankTemperatureService
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

        [FunctionName("FishTankTemperatureService")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // Authenticate to the Azure Resource Manager to get the Service Managed token.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // Setup the List Keys API to get the Azure Cosmos DB keys.
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // Setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the result back as a DatabaseAccountListKeysResult.
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
            // Fake the temperature sensor for this demo.
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

Está agora pronto para implementar a [sua aplicação de função.](../azure-functions/functions-create-first-function-vs-code.md)

## <a name="next-steps"></a>Passos seguintes

* [Autenticação baseada em certificados com Azure Cosmos DB e Azure Ative Directory](certificate-based-authentication.md)
* [Chaves DB Secure Azure Cosmos usando cofre de chave Azure](access-secrets-from-keyvault.md)
* [Linha de segurança para Azure Cosmos DB](security-baseline.md)
