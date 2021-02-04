---
title: Tutorial de rotação para recursos com um conjunto de credenciais de autenticação armazenadas no Cofre da Chave Azure
description: Use este tutorial para aprender a automatizar a rotação de um segredo para recursos que usam um conjunto de credenciais de autenticação.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp
ms.openlocfilehash: cd90043c8f03361244d3500f83f6df5347f1d867
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539404"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-one-set-of-authentication-credentials"></a>Automatizar a rotação de um segredo para recursos que usam um conjunto de credenciais de autenticação

A melhor forma de autenticar os serviços da Azure é utilizando uma [identidade gerida](../general/authentication.md), mas existem alguns cenários em que isso não é uma opção. Nesses casos, são utilizadas chaves de acesso ou segredos. Deve rodar periodicamente as chaves de acesso ou segredos.

Este tutorial mostra como automatizar a rotação periódica de segredos para bases de dados e serviços que utilizam um conjunto de credenciais de autenticação. Especificamente, este tutorial gira as palavras-passe do SQL Server armazenadas no Cofre da Chave Azure utilizando uma função desencadeada pela notificação da Grelha de Eventos Azure:


:::image type="content" source="../media/rotate-1.png" alt-text="Diagrama de solução de rotação":::

1. Trinta dias antes da data de validade de um segredo, Key Vault publica o evento "quase expiração" para a Grade de Eventos.
1. A Grelha de Eventos verifica as subscrições do evento e utiliza HTTP POST para ligar para o ponto final da aplicação de função subscrito ao evento.
1. A aplicação de função recebe a informação secreta, gera uma nova senha aleatória e cria uma nova versão para o segredo com a nova palavra-passe no Key Vault.
1. A aplicação de função atualiza o SQL Server com a nova palavra-passe.

> [!NOTE]
> Pode haver um desfasamento entre os passos 3 e 4. Durante esse tempo, o segredo em Key Vault não será capaz de autenticar para o SQL Server. Em caso de falha de qualquer uma das etapas, a Grade de Eventos retrijo por duas horas.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure Key Vault
* SQL Server

Abaixo o link de implementação pode ser usado, se não tiver o Cofre de Chaves e o Servidor SQL existentes:

[![Imagem mostrando um botão marcado "Implementar para Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmain%2FARM-Templates%2FInitial-Setup%2Fazuredeploy.json)

1. No **grupo de Recursos,** selecione **Criar novo**. Dê um nome ao grupo, **usamos akvrotation** neste tutorial.
1. Em **Sql Admin Login,** tipo Sql administrator login. 
1. Selecione **Rever + criar**.
1. Selecione **Criar**

:::image type="content" source="../media/rotate-2.png" alt-text="Criar um grupo de recursos":::

Agora terá um Cofre-Chave e uma instância do SQL Server. Pode verificar esta configuração no Azure CLI executando o seguinte comando:

```azurecli
az resource list -o table -g akvrotation
```

O resultado será algo da seguinte saída:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation      eastus      Microsoft.Sql/servers/databases
akvrotation-sql2         akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql2/master  akvrotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-and-deploy-sql-server-password-rotation-function"></a>Criar e implementar a função de rotação de senha do servidor sql
> [!IMPORTANT]
> Abaixo o modelo requer que o Key Vault, o servidor SQL e a Função Azure estejam no mesmo grupo de recursos

Em seguida, crie uma aplicação de função com uma identidade gerida pelo sistema, além dos outros componentes necessários, e implemente funções de rotação de senha de servidor sql

A aplicação de função requer estes componentes:
- Um plano de serviço de aplicações Azure
- Uma App de função com funções de rotação de senha sql com gatilho de evento e gatilho de http 
- Uma conta de armazenamento necessária para a gestão do gatilho da aplicação de função
- Uma política de acesso à identidade da App de Função para aceder a segredos no Cofre de Chaves
- Uma subscrição de eventos Comagrid para o evento **SecretNearExpiry**

1. Selecione a ligação de implementação do modelo Azure: 

   [![Imagem mostrando um botão marcado "Implementar para Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmain%2FARM-Templates%2FFunction%2Fazuredeploy.json)

1. Na lista de **grupos de recursos,** selecione **akvrotation**.
1. No Nome do **Servidor Sql,** digite o nome sql Server com senha para rodar
1. No Nome do **Cofre de Chaves,** digite o nome do cofre da chave
1. No Nome da **Aplicação de Função,** digite o nome da aplicação de função
1. No **Nome Secreto**, escreva o nome secreto onde a palavra-passe será armazenada
1. No **Url Repo**, tipo de função gitHub localização **https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp.git** ()
1. Selecione **Rever + criar**.
1. Selecione **Criar**.

:::image type="content" source="../media/rotate-3.png" alt-text="Selecione Review+criar":::
  

Depois de completar os passos anteriores, terá uma conta de armazenamento, uma fazenda de servidores e uma aplicação de função. Pode verificar esta configuração no Azure CLI executando o seguinte comando:

```azurecli
az resource list -o table -g akvrotation
```

O resultado será algo parecido com a seguinte saída:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation       eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation       eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation       eastus      Microsoft.Sql/servers/databases
cfogyydrufs5wazfunctions akvrotation       eastus      Microsoft.Storage/storageAccounts
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/serverFarms
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/sites
akvrotation-fnapp        akvrotation       eastus      Microsoft.insights/components
```

Para obter informações sobre como criar uma aplicação de função e utilizar a identidade gerida para aceder ao Key Vault, consulte [Criar uma aplicação de função a partir do portal Azure](../../azure-functions/functions-create-function-app-portal.md), [Como utilizar a identidade gerida para o Serviço de Aplicações e Funções Azure](../../app-service/overview-managed-identity.md), e atribuir uma política de [acesso ao Cofre de Chaves utilizando o portal Azure.](../general/assign-access-policy-portal.md)

### <a name="rotation-function"></a>Função de rotação
Implantado na função de passo anterior utiliza um evento para desencadear a rotação de um segredo atualizando o Cofre de Chaves e a base de dados SQL. 

#### <a name="function-trigger-event"></a>Evento de gatilho de função

Esta função lê os dados do evento e executa a lógica de rotação:

```csharp
public static class SimpleRotationEventHandler
{
   [FunctionName("AKVSQLRotation")]
   public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
   {
      log.LogInformation("C# Event trigger function processed a request.");
      var secretName = eventGridEvent.Subject;
      var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
      var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
      log.LogInformation($"Key Vault Name: {keyVaultName}");
      log.LogInformation($"Secret Name: {secretName}");
      log.LogInformation($"Secret Version: {secretVersion}");

      SecretRotator.RotateSecret(log, secretName, keyVaultName);
   }
}
```

#### <a name="secret-rotation-logic"></a>Lógica de rotação secreta
Este método de rotação lê informações de base de dados a partir do segredo, cria uma nova versão do segredo, e atualiza a base de dados com o novo segredo:

```csharp
    public class SecretRotator
    {
        private const string CredentialIdTag = "CredentialId";
        private const string ProviderAddressTag = "ProviderAddress";
        private const string ValidityPeriodDaysTag = "ValidityPeriodDays";

        public static void RotateSecret(ILogger log, string secretName, string keyVaultName)
        {
            //Retrieve Current Secret
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";
            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
            KeyVaultSecret secret = client.GetSecret(secretName);
            log.LogInformation("Secret Info Retrieved");

            //Retrieve Secret Info
            var credentialId = secret.Properties.Tags.ContainsKey(CredentialIdTag) ? secret.Properties.Tags[CredentialIdTag] : "";
            var providerAddress = secret.Properties.Tags.ContainsKey(ProviderAddressTag) ? secret.Properties.Tags[ProviderAddressTag] : "";
            var validityPeriodDays = secret.Properties.Tags.ContainsKey(ValidityPeriodDaysTag) ? secret.Properties.Tags[ValidityPeriodDaysTag] : "";
            log.LogInformation($"Provider Address: {providerAddress}");
            log.LogInformation($"Credential Id: {credentialId}");

            //Check Service Provider connection
            CheckServiceConnection(secret);
            log.LogInformation("Service  Connection Validated");
            
            //Create new password
            var randomPassword = CreateRandomPassword();
            log.LogInformation("New Password Generated");

            //Add secret version with new password to Key Vault
            CreateNewSecretVersion(client, secret, randomPassword);
            log.LogInformation("New Secret Version Generated");

            //Update Service Provider with new password
            UpdateServicePassword(secret, randomPassword);
            log.LogInformation("Password Changed");
            log.LogInformation($"Secret Rotated Successfully");
        }
}
```
Pode encontrar o código completo no [GitHub.](https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp)

## <a name="add-the-secret-to-key-vault"></a>Adicione o segredo ao Cofre de Chaves
Desagre a sua política de acesso para conceder permissões *de segredos* aos utilizadores:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Crie um novo segredo com tags que contenham o ID de recursos do SQL Server, o nome de login do SQL Server e o período de validade para o segredo em dias. Forneça o nome do segredo, palavra-passe inicial da base de dados SQL (no nosso exemplo "Simple123") e inclua uma data de validade marcada para amanhã.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqlPassword --vault-name akvrotation-kv --value "Simple123" --tags "CredentialId=sqlAdmin" "ProviderAddress=<sql-database-resource-id>" "ValidityPeriodDays=90" --expires $tomorrowDate
```

Criar um segredo com uma data de validade curta publicará um `SecretNearExpiry` evento dentro de 15 minutos, o que por sua vez irá desencadear a função para rodar o segredo.

## <a name="test-and-verify"></a>Testar e verificar

Para verificar se o segredo gira, vá aos **Segredos do Cofre Chave:**  >  

:::image type="content" source="../media/rotate-8.png" alt-text="Ir para segredos":::

Abra o **segredo sqlPassword** e veja as versões originais e rotativas:

:::image type="content" source="../media/rotate-9.png" alt-text="Ir para segredos":::

### <a name="create-a-web-app"></a>Criar uma aplicação Web

Para verificar as credenciais SQL, crie uma aplicação web. Esta aplicação web obterá o segredo do Key Vault, extrair informações e credenciais da base de dados SQL do segredo, e testar a ligação ao SQL Server.

A aplicação web requer estes componentes:
- Uma aplicação web com identidade gerida pelo sistema
- Uma política de acesso a segredos de acesso em Key Vault via web app identidade gerida

1. Selecione a ligação de implementação do modelo Azure: 

   [![Imagem mostrando um botão marcado "Implementar para Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp-WebApp%2Fmain%2FARM-Templates%2FWeb-App%2Fazuredeploy.json)

1. Selecione o grupo de recursos **de akvrotation.**
1. No Nome do **Servidor Sql,** digite o nome sql Server com senha para rodar
1. No Nome do **Cofre de Chaves,** digite o nome do cofre da chave
1. No **Nome Secreto**, escreva o nome secreto onde a palavra-passe está armazenada
1. No **Url Repo**, digite código de aplicação web GitHub **https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp-WebApp.git** ()
1. Selecione **Rever + criar**.
1. Selecione **Criar**.


### <a name="open-the-web-app"></a>Abra a aplicação web

Aceda ao URL de aplicação implantado:
 
'https://akvrotation-app.azurewebsites.net/'

Quando a aplicação abrir no navegador, verá o **Valor Secreto Gerado** e um valor conectado à base de **dados** de *verdade.*

## <a name="learn-more"></a>Saber mais

- Tutorial: [Rotação para recursos com dois conjuntos de credenciais](tutorial-rotation-dual.md)
- Visão geral: [Cofre de chaves de monitorização com grade de eventos Azure](../general/event-grid-overview.md)
- Como: [Receber e-mail quando um segredo de cofre chave muda](../general/event-grid-logicapps.md)
- [Esquema de eventos Azure Event Grid para Azure Key Vault](../../event-grid/event-schema-key-vault.md)
