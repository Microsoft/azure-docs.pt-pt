---
title: Tutorial de rotação de utilizador/palavra-passe único
description: Utilize este tutorial para automatizar a rotação de um único utilizador/senha
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 890932f7c0e46a2c9c0b6e1cf1461e4d7d25b409
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239372"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-single-userpassword-authentication"></a>Automatizar a rotação de um segredo para recursos com autenticação de utilizador/senha única

Embora a melhor forma de autenticar os serviços do Azure seja utilizando uma [identidade gerida,](managed-identity.md)existem alguns cenários em que esta não é uma opção. Nestes casos, chaves de acesso ou segredos são usados. As chaves de acesso ou segredos devem ser periodicamente rodados.

Este tutorial demonstra como automatizar a rotação periódica de segredos para bases de dados e serviços com autenticação de utilizador/senha única. Especificamente, este cenário gira as palavras-passe do servidor SQL armazenadas no cofre da chave utilizando uma função desencadeada pela notificação da Rede de Eventos:

![Diagrama de rotação](./media/rotate1.png)

1. Trinta dias antes da data de validade de um segredo, key vault publica o evento "quase expiração" para Event Grid.
1. A Grelha de Eventos verifica as subscrições do evento e, usando o post http, chama o ponto final da App de Função subscrito a este evento.
1. A função App recebe as informações secretas, gera uma nova senha aleatória, e cria uma nova versão para o segredo com uma nova senha no Key Vault.
1. A função App atualiza o SQL com nova senha.

> [!NOTE]
> Pode haver um intervalo entre o passo 3 e o 4 e durante esse tempo o segredo no Cofre chave não seria válido para autenticar a SQL. Em caso de falha em qualquer uma das etapas, a Grelha de Eventos retenta durante 2 horas.

## <a name="setup"></a>Configuração

## <a name="create-a-key-vault-and-sql-server"></a>Criar um cofre chave e servidor SQL

Antes de começarmos, temos de criar um Cofre chave, criar um Servidor SQL e uma base de dados, e armazenar a senha de administração do SQL Server no Key Vault.

Este tutorial usa um modelo de Gestor de Recursos Azure pré-criado para criar componentes. Pode encontrar código inteiro aqui: Amostra de modelo de [rotação secreta básica](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Clique no link de implementação do modelo Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Para "Grupo de Recursos", selecione "Create New" e dê-lhe o nome "simplerotation".
1. Selecione "Comprar".

    ![Criar um novo grupo de recursos](./media/rotate2.png)

Depois de completar estes passos, terá um cofre chave, um servidor SQL e uma base de dados SQL. Pode verificar isto num terminal Azure CLI funcionando:

```azurecli
az resource list -o table
```

Os resultados vão parecer algo isto:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
```

## <a name="create-function-app"></a>Criar app de função

Criar uma App de Funções com uma identidade gerida pelo sistema, bem como os componentes adicionais necessários: 

A aplicação de funções requer componentes abaixo e configuração:
- Plano do Serviço de Aplicações
- Conta de Armazenamento
- Política de acesso a segredos no Cofre chave usando identidade gerida por app de função

1. Clique no link de implementação do modelo Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Para "Grupo de Recursos", selecione "simplerotation".
1. Selecione "Comprar".

   ![Ecrã de compra](./media/rotate3.png)

Depois de completar os passos acima, terá uma conta de armazenamento, uma quinta de servidores e uma App de Funções.  Pode verificar isto num terminal Azure CLI funcionando:

```azurecli
az resource list -o table
```

Os resultados vão parecer algo isto:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
simplerotationstrg            simplerotation             eastus      Microsoft.Storage/storageAccounts
simplerotation-plan           simplerotation             eastus      Microsoft.Web/serverFarms
simplerotation-fn             simplerotation             eastus      Microsoft.Web/sites
```

Para obter informações sobre como criar a App de Funções e utilizar a Identidade Gerida para aceder ao Cofre chave, consulte [Criar uma aplicação de função a partir do portal Azure](../azure-functions/functions-create-function-app-portal.md) e [fornecer autenticação chave vault com uma identidade gerida](managed-identity.md)

### <a name="rotation-function-and-deployment"></a>Função de rotação e implantação
A função está a usar o evento como gatilho e executar a rotação de uma base de dados key vault e SQL de atualização secreta.

#### <a name="function-event-trigger-handler"></a>Manipulador de gatilho de evento de função

Função abaixo lê dados do evento e executa lógica de rotação

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

#### <a name="secret-rotation-logic"></a>Lógica de Rotação Secreta
Este método de rotação lê informações de base de dados do segredo, cria uma nova versão do segredo e atualiza a base de dados com um novo segredo.

```csharp
public class SecretRotator
    {
       private const string UserIdTagName = "UserID";
       private const string DataSourceTagName = "DataSource";
       private const int SecretExpirationDays = 31;

    public static void RotateSecret(ILogger log, string secretName, string secretVersion, string keyVaultName)
    {
           //Retrieve Current Secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve Secret Info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check db connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update db password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
Pode encontrar código inteiro aqui:https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function

#### <a name="function-deployment"></a>Implantação de funções

1. Ficheiro zip de aplicativo de descarregamento:https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip

1. Faça upload de ficheiro simplerotationsample-fn.zip para Azure Cloud Shell.
 
1. Utilize abaixo o comando CLI para implementar ficheiro zip para a aplicação de função:

```azurecli
az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
```
![Ecrã de compra](./media/rotate4.png)

Após a implantação, deve notar duas funções em simplerotation-fn:

![Azure Cloud Shell](./media/rotate5.png)

### <a name="add-event-subscription-for-secretnearexpiry-event"></a>Adicione a subscrição do evento para o evento "SecretNearExpiry"

Copie a aplicação de função eventgrid_extension chave.

![Azure Cloud Shell](./media/rotate6.png)

![Testar e verificar](./media/rotate7.png)

Utilize a chave de extensão de eventgrid copiada e o seu ID de subscrição no comando abaixo para criar uma subscrição de grelha de eventos para eventos SecretNearExpiry.

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

### <a name="add-secret-to-key-vault"></a>Adicione segredo ao cofre da chave
Desponha a sua política de acesso para dar permissão de "gerir segredos" aos utilizadores.

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Agora crie um novo segredo com etiquetas que contenham fonte de dados de base de dados sql e ID do utilizador, com a data de validade definida para amanhã.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

A criação de um segredo com uma data de validade curta publicará imediatamente um evento SecretNearExpirey, que por sua vez irá desencadear a função para rodar o segredo.

### <a name="test-and-verify"></a>Testar e verificar
Após alguns minutos, o segredo sqluser deve rodar automaticamente.

Para verificar a verificação de rotação secreta, vá ao Cofre chave > Segredos

![Testar e verificar](./media/rotate8.png)

Abra o segredo "sqluser" e veja a versão original e rotativa

![Testar e verificar](./media/rotate9.png)

## <a name="create-web-app"></a>Criar Aplicação Web

Para verificar as credenciais SQL, crie uma aplicação web. Esta aplicação web obterá o segredo do cofre chave, extrairá informações de base de dados e credenciais do segredo, e testará a ligação ao sql.

A aplicação web requer componentes abaixo e configuração:
- Web App com identidade gerida pelo sistema
- Política de acesso a segredos no Key Vault usando identidade gerida por aplicações web

1. Clique no link de implementação do modelo Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Selecione o grupo de recursos **de simples rotação**
1. Clique em Comprar

### <a name="deploy-web-app"></a>Implementar Aplicação Web

Código fonte para a aplicação https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp web que pode encontrar para a implementação da aplicação web, faça o seguinte:

1. Descarregue o ficheiro zip da aplicação de função a partir dehttps://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip
1. Faça o `simplerotationsample-app.zip` upload do ficheiro para Azure Cloud Shell.
1. Utilize este comando Azure CLI para implantar o ficheiro zip na aplicação de função:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

#### <a name="open-web-application"></a>Aplicação web aberta

Vá à aplicação implementada e clique em "URL":
 
![Testar e verificar](./media/rotate10.png)

O Valor Secreto Gerado deve ser mostrado com base de dados ligada como verdadeira.

## <a name="learn-more"></a>Saiba mais:

- Visão geral: Cofre de chaves de monitorização com grelha de [eventos Azure (pré-visualização)](event-grid-overview.md)
- Como: [Receber e-mail quando um cofre chave muda](event-grid-logicapps.md)
- [Esquema de evento sinuoso do evento Azure Event Grid para cofre de chaves Azure (pré-visualização)](../event-grid/event-schema-key-vault.md)
