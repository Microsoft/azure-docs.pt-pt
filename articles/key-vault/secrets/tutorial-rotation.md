---
title: Tutorial de rotação para recursos com um conjunto de credenciais de autenticação
description: Use este tutorial para aprender a automatizar a rotação de um segredo para recursos que usam um conjunto de credenciais de autenticação.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 9bff8c040f4cfed612278dd83ebb354b31a3a1f3
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85801449"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-one-set-of-authentication-credentials"></a>Automatizar a rotação de um segredo para recursos que usam um conjunto de credenciais de autenticação

A melhor forma de autenticar os serviços da Azure é utilizando uma [identidade gerida](../general/managed-identity.md), mas existem alguns cenários em que isso não é uma opção. Nesses casos, são utilizadas chaves de acesso ou segredos. Deve rodar periodicamente as chaves de acesso ou segredos.

Este tutorial mostra como automatizar a rotação periódica de segredos para bases de dados e serviços que utilizam um conjunto de credenciais de autenticação. Especificamente, este tutorial gira as palavras-passe do SQL Server armazenadas no Cofre da Chave Azure utilizando uma função desencadeada pela notificação da Grelha de Eventos Azure:

![Diagrama de solução de rotação](../media/rotate1.png)

1. Trinta dias antes da data de validade de um segredo, Key Vault publica o evento "quase expiração" para a Grade de Eventos.
1. A Grelha de Eventos verifica as subscrições do evento e utiliza HTTP POST para ligar para o ponto final da aplicação de função subscrito ao evento.
1. A aplicação de função recebe a informação secreta, gera uma nova senha aleatória e cria uma nova versão para o segredo com a nova palavra-passe no Key Vault.
1. A aplicação de função atualiza o SQL Server com a nova palavra-passe.

> [!NOTE]
> Pode haver um desfasamento entre os passos 3 e 4. Durante esse tempo, o segredo em Key Vault não será capaz de autenticar para o SQL Server. Em caso de falha de qualquer uma das etapas, a Grade de Eventos retrijo por duas horas.

## <a name="create-a-key-vault-and-sql-server-instance"></a>Crie um cofre chave e exemplo de servidor SQL

O primeiro passo é criar um cofre chave e uma instância e base de dados do SQL Server e armazenar a palavra-passe de administrador do SQL Server no Key Vault.

Este tutorial utiliza um modelo de Gestor de Recursos Azure existente para criar componentes. Pode encontrar o código aqui: [Amostra de modelo de rotação secreta básica.](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates)

1. Selecione a ligação de implementação do modelo Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. No **grupo de Recursos,** selecione **Criar novo**. Nomeie a **versão mais simples do**grupo.
1. Selecione **Comprar**.

    ![Criar um grupo de recursos](../media/rotate2.png)

Agora terá um cofre chave, uma instância sql server e uma base de dados SQL. Pode verificar esta configuração no Azure CLI executando o seguinte comando:

```azurecli
az resource list -o table
```

O resultado será algo da seguinte saída:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation      eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation      eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-a-function-app"></a>Criar uma aplicação de função

Em seguida, crie uma aplicação de função com uma identidade gerida pelo sistema, além dos outros componentes necessários.

A aplicação de função requer estes componentes:
- Um plano de serviço de aplicações Azure
- Uma conta de armazenamento
- Uma política de acesso a segredos de acesso em Key Vault através de app de função gerida identidade

1. Selecione a ligação de implementação do modelo Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Na lista de **grupos de Recursos,** selecione **a simplificação**.
1. Selecione **Comprar**.

   ![Selecione Compra](../media/rotate3.png)

Depois de completar os passos anteriores, terá uma conta de armazenamento, uma fazenda de servidores e uma aplicação de função. Pode verificar esta configuração no Azure CLI executando o seguinte comando:

```azurecli
az resource list -o table
```

O resultado será algo parecido com a seguinte saída:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation       eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation       eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation       eastus      Microsoft.Sql/servers/databases
simplerotationstrg         simplerotation       eastus      Microsoft.Storage/storageAccounts
simplerotation-plan        simplerotation       eastus      Microsoft.Web/serverFarms
simplerotation-fn          simplerotation       eastus      Microsoft.Web/sites
```

Para obter informações sobre como criar uma aplicação de função e utilizar identidade gerida para aceder ao Key Vault, consulte [Criar uma aplicação de função a partir do portal Azure](../../azure-functions/functions-create-function-app-portal.md) e [fornecer autenticação key vault com uma identidade gerida.](../general/managed-identity.md)

### <a name="rotation-function"></a>Função de rotação
A função utiliza um evento para desencadear a rotação de um segredo atualizando o Cofre de Chaves e a base de dados SQL.

#### <a name="function-trigger-event"></a>Evento de gatilho de função

Esta função lê os dados do evento e executa a lógica de rotação:

```csharp
public static class SimpleRotationEventHandler
{
    [FunctionName("SimpleRotation")]
       public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
       {
            log.LogInformation("C# Event trigger function processed a request.");
            var secretName = eventGridEvent.Subject;
            var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
            var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
            log.LogInformation($"Key Vault Name: {keyVaultName}");
            log.LogInformation($"Secret Name: {secretName}");
            log.LogInformation($"Secret Version: {secretVersion}");

            SeretRotator.RotateSecret(log, secretName, secretVersion, keyVaultName);
        }
}
```

#### <a name="secret-rotation-logic"></a>Lógica de rotação secreta
Este método de rotação lê informações de base de dados a partir do segredo, cria uma nova versão do segredo, e atualiza a base de dados com o novo segredo:

```csharp
public class SecretRotator
    {
       private const string UserIdTagName = "UserID";
       private const string DataSourceTagName = "DataSource";
       private const int SecretExpirationDays = 31;

    public static void RotateSecret(ILogger log, string secretName, string secretVersion, string keyVaultName)
    {
           //Retrieve current secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve secret info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //Create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check DB connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update DB password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
Pode encontrar o código completo no [GitHub.](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function)

#### <a name="function-deployment"></a>Implantação de funções

1. Descarregue o ficheiro zip da aplicação de função do [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip).

1. Faça o upload do ficheiro simplerotationsample-fn.zip para a Azure Cloud Shell.

   ![Faça o upload do ficheiro](../media/rotate4.png)
1. Utilize este comando Azure CLI para implantar o ficheiro zip na aplicação de função:

   ```azurecli
   az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
   ```

Depois de a função ser implantada, deverá ver duas funções em simplificação-fn:

![Funções SimpleRotation e SimpleRotationHttpTest](../media/rotate5.png)

## <a name="add-an-event-subscription-for-the-secretnearexpiry-event"></a>Adicione uma subscrição de evento para o evento SecretNearExpiry

Copie a chave da aplicação de `eventgrid_extension` função:

   ![Selecione as definições de aplicações de funções](../media/rotate6.png)

   ![eventgrid_extension chave](../media/rotate7.png)

Utilize a chave copiada `eventgrid_extension` e o ID de subscrição no seguinte comando para criar uma subscrição de Grade de Eventos para `SecretNearExpiry` eventos:

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

## <a name="add-the-secret-to-key-vault"></a>Adicione o segredo ao Cofre de Chaves
Desagre a sua política de acesso para conceder permissões *de segredos* aos utilizadores:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Crie um novo segredo com tags que contenham a fonte de dados da base de dados SQL e o ID do utilizador. Inclua uma data de validade marcada para amanhã.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

Criar um segredo com uma data de validade curta publicará imediatamente um `SecretNearExpiry` evento, que por sua vez irá desencadear a função para rodar o segredo.

## <a name="test-and-verify"></a>Testar e verificar
Após alguns minutos, o `sqluser` segredo deve girar automaticamente.

Para verificar se o segredo gira, vá aos **Segredos do Cofre Chave:**  >  **Secrets**

![Ir para segredos](../media/rotate8.png)

Abra o segredo **sqluser** e veja as versões originais e rotativas:

![Abra o segredo sqluser](../media/rotate9.png)

### <a name="create-a-web-app"></a>Criar uma aplicação Web

Para verificar as credenciais SQL, crie uma aplicação web. Esta aplicação web obterá o segredo do Key Vault, extrair informações e credenciais da base de dados SQL do segredo, e testar a ligação ao SQL Server.

A aplicação web requer estes componentes:
- Uma aplicação web com identidade gerida pelo sistema
- Uma política de acesso a segredos de acesso em Key Vault via web app identidade gerida

1. Selecione a ligação de implementação do modelo Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Selecione o grupo de recursos **de simplificação.**
1. Selecione **Comprar**.

### <a name="deploy-the-web-app"></a>Implementar a aplicação web

Pode encontrar código fonte para a aplicação web no [GitHub.](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp)

Para implementar a aplicação web, complete estes passos:

1. Descarregue o ficheiro zip da aplicação de função do [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip).
1. Faça o upload do ficheiro simplerotationsample-app.zip para a Azure Cloud Shell.
1. Utilize este comando Azure CLI para implantar o ficheiro zip na aplicação de função:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

### <a name="open-the-web-app"></a>Abra a aplicação web

Vá à aplicação implementada e selecione o URL:
 
![Selecione o URL](../media/rotate10.png)

Quando a aplicação abrir no navegador, verá o **Valor Secreto Gerado** e um valor conectado à base de **dados** de *verdade.*

## <a name="learn-more"></a>Saber mais

- Visão geral: [Monitoring Key Vault com Azure Event Grid (pré-visualização)](../general/event-grid-overview.md)
- Como: [Receber e-mail quando um segredo de cofre chave muda](../general/event-grid-logicapps.md)
- [Esquema de eventos Azure Event Grid para Azure Key Vault (pré-visualização)](../../event-grid/event-schema-key-vault.md)
