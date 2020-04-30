---
title: Como usar uma identidade gerida atribuída pelo sistema para aceder aos dados da Azure Cosmos DB
description: Saiba como configurar uma identidade gerida de acesso a um Azure Ative Directory (Azure AD) para aceder às chaves do Azure Cosmos DB.
author: j-patrick
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.openlocfilehash: 8136ad7a1fe29bc3394e959c10aafc52988c0a23
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641173"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>Utilize identidades geridas atribuídas pelo sistema para aceder aos dados do Azure Cosmos DB

Neste artigo, você vai configurar uma solução *agnóstica robusta e chave de rotação* para aceder às teclas Db Azure Cosmos usando [identidades geridas](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). O exemplo neste artigo utiliza funções Azure, mas pode utilizar qualquer serviço que suporte identidades geridas. 

Você vai aprender a criar uma app de funções que pode aceder a dados do Azure Cosmos DB sem precisar de copiar quaisquer chaves Azure Cosmos DB. A aplicação de funções acordará a cada minuto e registará a temperatura atual de um aquário. Para aprender a configurar uma aplicação de função acionada pelo temporizador, consulte a [função Create a em Azure que é desencadeada por um artigo temporizador.](../azure-functions/functions-create-scheduled-function.md)

Para simplificar o cenário, já está configurada uma definição time [to live](./time-to-live.md) para limpar documentos de temperatura mais antigos. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>Atribuir uma identidade gerida atribuída pelo sistema a uma aplicação de função

Neste passo, você atribuirá uma identidade gerida atribuída ao sistema para a sua aplicação de função.

1. No [portal Azure,](https://portal.azure.com/)abra o painel **função Azure** e vá para a sua aplicação de funções. 

1. Abra a **Plataforma com** > separador**identidade:** 

   ![Screenshot mostrando funcionalidades da Plataforma e opções de identidade para a aplicação de função.](./media/managed-identity-based-authentication/identity-tab-selection.png)

1. No separador **Identidade,** **ligue** o **Estado** de identidade do sistema e selecione **Guardar**. O painel **de identidade** deve ser o seguinte:  

   ![Screenshot mostrando o estado de identidade do sistema definido para On.](./media/managed-identity-based-authentication/identity-tab-system-managed-on.png)

## <a name="grant-access-to-your-azure-cosmos-account"></a>Conceda acesso à sua conta Azure Cosmos

Neste passo, você atribuirá uma função à identidade gerida atribuída pelo sistema da aplicação de função. A Azure Cosmos DB tem múltiplas funções incorporadas que pode atribuir à identidade gerida. Para esta solução, utilizará as seguintes duas funções:

|Papel incorporado  |Descrição  |
|---------|---------|
|[Contribuinte de Conta DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Pode gerir as contas da Azure Cosmos DB. Permite a recuperação de teclas de leitura/escrita. |
|[Leitor de Conta Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Pode ler os dados da conta Azure Cosmos DB. Permite a recuperação das chaves de leitura. |

> [!IMPORTANT]
> O apoio ao controlo de acesso baseado em funções no Azure Cosmos DB aplica-se apenas ao controlo das operações dos aviões. As operações de avião de dados são asseguradas através de chaves-mestre ou fichas de recursos. Para saber mais, consulte o acesso seguro ao artigo de [dados.](secure-access-to-data.md)

> [!TIP] 
> Ao atribuir funções, atribua apenas o acesso necessário. Se o seu serviço necessitar apenas de dados de leitura, atribua a função de Leitor de **Conta Db Cosmos** à identidade gerida. Para mais informações sobre a importância do menor acesso privilegiado, consulte o artigo [De menor exposição de contas privilegiadas.](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts)

Neste cenário, a aplicação de funções irá ler a temperatura do aquário e depois escrever esses dados para um contentor em Azure Cosmos DB. Como a aplicação de funções deve escrever os dados, terá de atribuir a função de Contribuinte de **Conta DocumentDB.** 

1. Inscreva-se no portal Azure e vá à sua conta Azure Cosmos DB. Abra o painel de controlo de **acesso (IAM)** e, em seguida, o separador de **atribuições de funções:**

   ![Screenshot mostrando o painel de controlo de acesso e o separador de tarefas role.](./media/managed-identity-based-authentication/cosmos-db-iam-tab.png)

1. Selecione **+ Adicionar** > **atribuição de funções**.

1. O painel de atribuição de **funções Add** abre-se para a direita:

   ![Screenshot mostrando o painel de atribuição de funções Adicionar.](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png)

   * **Função**: Selecione Colaborador de **Conta DocumentDB**
   * **Atribuir acesso a**: Sob a subsecção de identidade gerida atribuída pelo **sistema Select,** selecione **App de Funções**.
   * **Selecione**: O painel será preenchido com todas as aplicações de função na sua subscrição que tenham uma **Identidade de Sistema Gerido**. Neste caso, selecione a aplicação de função **FishTankTemperatureService:** 

      ![Screenshot mostrando o painel de atribuição de funções Adicionar povoado com exemplos.](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png)

1. Depois de ter selecionado a sua aplicação de funções, selecione **Guardar**.

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>Aceda programáticamente as teclas Azure Cosmos DB

Agora temos uma aplicação de função que tem uma identidade gerida atribuída pelo sistema com o papel de **Contribuinte de Conta DocumentDB** nas permissões do Azure Cosmos DB. O código de aplicação de funções seguinte receberá as chaves Db Do Azure Cosmos, criará um objeto CosmosClient, obterá a temperatura do aquário e, em seguida, guardá-lo para O Azure Cosmos DB.

Esta amostra utiliza a [API list Keys](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListKeys) para aceder às chaves da conta Azure Cosmos DB.

> [!IMPORTANT] 
> Se quiser atribuir o papel de Leitor de [Conta Cosmos DB,](#grant-access-to-your-azure-cosmos-account) terá de utilizar a [Lista Ler API apenas chaves](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListReadOnlyKeys). Isto vai povoar apenas as chaves de leitura.

A API da `DatabaseAccountListKeysResult` Lista de Chaves devolve o objeto. Este tipo não é definido nas bibliotecas C# O seguinte código mostra a implementação desta classe:  

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

Utilizará a biblioteca [Microsoft.Azure.Services.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) para obter o símbolo de identidade gerido atribuído pelo sistema. Para saber outras formas de obter o `Microsoft.Azure.Service.AppAuthentication` símbolo e obter mais informações sobre a biblioteca, consulte o artigo de [autenticação Serviço-a-serviço.](../key-vault/general/service-to-service-authentication.md)


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

Está agora pronto para [implementar a sua aplicação de funções.](../azure-functions/functions-create-first-function-vs-code.md)

## <a name="next-steps"></a>Passos seguintes

* [Autenticação baseada em certificado saca-se com o Azure Cosmos DB e o Azure Ative Directory](certificate-based-authentication.md)
* [Chaves DB Secure Azure Cosmos usando cofre de chaves Azure](access-secrets-from-keyvault.md)
* [Base de segurança para Azure Cosmos DB](security-baseline.md)
