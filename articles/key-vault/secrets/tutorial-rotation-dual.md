---
title: Tutorial de rotação para recursos com dois conjuntos de credenciais
description: Use este tutorial para aprender a automatizar a rotação de um segredo para recursos que usam dois conjuntos de credenciais de autenticação.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.openlocfilehash: 5da31d45e068f414c8afa38bcb46cdf1f790a9e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843282"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-two-sets-of-authentication-credentials"></a>Automatizar a rotação de um segredo para recursos com dois conjuntos de credenciais de autenticação

A melhor forma de autenticar os serviços da Azure é utilizando uma [identidade gerida](../general/authentication.md), mas existem alguns cenários em que isso não é uma opção. Nesses casos, são utilizadas chaves de acesso ou senhas de acesso. As chaves de acesso e as palavras-passe devem ser rodadas com frequência.

Este tutorial mostra como automatizar a rotação periódica de segredos para bases de dados e serviços que utilizam dois conjuntos de credenciais de autenticação. Especificamente, este tutorial gira as teclas de conta de armazenamento Azure armazenadas no Cofre da Chave Azure como segredos usando uma função desencadeada pela notificação da Azure Event Grid. :

> [!NOTE]
> As Chaves de Conta de Armazenamento podem ser geridas automaticamente no Cofre de Chaves, fornecendo fichas de assinatura de acesso partilhado para acesso delegado à Conta de Armazenamento. Existem serviços que requerem cadeia de ligação de conta de armazenamento com chave de acesso e para esse cenário esta solução é recomendada

![Diagrama de solução de rotação](../media/secrets/rotation-dual/rotation-diagram.png)

Na solução acima, a Azure Key Vault armazena chaves de acesso individuais da Conta de Armazenamento como versões do mesmo segredo alternando entre a chave primária e secundária nas versões subsequentes. Como uma chave de acesso é armazenada na versão mais recente do segredo, a chave alternativa é regenerada e adicionada ao Key Vault como nova e mais recente versão do segredo. Esta solução fornece aplicações todo o ciclo de rotação para refrescar para a mais recente chave regenerada. 

1. 30 dias antes da data de validade de um segredo, Key Vault publica o evento "quase expiração" para a Grade de Eventos.
1. A Grelha de Eventos verifica as subscrições do evento e utiliza HTTP POST para ligar para o ponto final da aplicação de função subscrito ao evento.
1. A aplicação de função identifica a chave alternativa (que não a mais recente) e chama a Conta de Armazenamento para a regenerar
1. A aplicação de função adiciona uma nova chave regenerada ao Azure Key Vault como nova versão do segredo.

## <a name="prerequisites"></a>Pré-requisitos
* Uma subscrição Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure Key Vault
* Duas contas de armazenamento Azure

Abaixo o link de implementação pode ser usado, se você não tiver contas de cofre e armazenamento existentes:

[![Imagem mostrando um botão marcado "Implementar para Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. No **grupo de Recursos,** selecione **Criar novo**. Nomeie a **akvrotation do** grupo e clique em **Ok**.
1. Selecione **Review+Create**.
1. Selecione **Criar**

    ![Criar um grupo de recursos](../media/secrets/rotation-dual/dual-rotation-1.png)

Agora terá um cofre e duas contas de armazenamento. Pode verificar esta configuração no Azure CLI executando o seguinte comando:

```azurecli
az resource list -o table -g akvrotation
```

O resultado será algo da seguinte saída:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv         akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotationstorage     akvrotation      eastus      Microsoft.Storage/storageAccounts
akvrotationstorage2    akvrotation      eastus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-storage-account-key-rotation-function"></a>Criar e implementar função de rotação da chave da conta de armazenamento
> [!IMPORTANT]
> Abaixo o modelo requer Cofre chave, conta de armazenamento Azure e Função Azure para estar no mesmo grupo de recursos

Em seguida, crie uma app de função com uma identidade gerida pelo sistema, além dos outros componentes necessários, e implemente funções de rotação de chaves de conta de armazenamento

As funções de rotação da aplicação de função requerem estes componentes e configuração:
- Um plano de serviço de aplicações Azure
- Uma conta de armazenamento necessária para a gestão do gatilho da aplicação de função
- Uma política de acesso a segredos de acesso em Key Vault
- Atribuir a função de Serviço do Operador chave de armazenamento para funcionar app para aceder às chaves de acesso à Conta de Armazenamento
- Funções de rotação da chave da conta de armazenamento com gatilho de evento e gatilho http (rotação a pedido)
- Subscrição de eventos Comagrid para evento **SecretNearExpiry**

1. Selecione a ligação de implementação do modelo Azure: 

   [![Imagem mostrando um botão marcado "Implementar para Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FFunction%2Fazuredeploy.json)

1. Na lista de **grupos de recursos,** selecione **akvrotation**.
1. No Nome da **Conta de Armazenamento,** digite o nome da conta de armazenamento com as teclas de acesso para rodar
1. No Nome do **Cofre de Chaves,** digite o nome do cofre da chave
1. No Nome da **Aplicação de Função,** digite o nome da aplicação de função
1. No **Nome Secreto**, escreva o nome secreto onde as chaves de acesso seriam armazenadas
1. No **Url Repo**, tipo de função gitHub localização **https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell.git** ()
1. Selecione **Review+Create**.
1. Selecione **Criar**

   ![Rever e criar a primeira conta de armazenamento](../media/secrets/rotation-dual/dual-rotation-2.png)

Depois de completar os passos anteriores, terá uma conta de armazenamento, uma fazenda de servidores, uma aplicação de função, insights de aplicações. Deve ver abaixo o ecrã uma vez concluída a ![ implementação: Implementação concluída](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> Em caso de falhas, pode clicar em **Recolocação** para terminar a colocação dos restantes componentes.


Os modelos de implementação e o código das funções de rotação podem ser encontrados no [GitHub](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell).

## <a name="add-storage-account-access-key-to-key-vault"></a>Adicionar chave de acesso à conta de armazenamento ao Cofre-Chave

Em primeiro lugar, desagreda a sua política de acesso para conceder permissões *de segredos* aos utilizadores:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Pode agora criar um novo segredo com uma chave de acesso à Conta de Armazenamento como valor. Também necessitará do ID do recurso de conta de armazenamento, do período de validade secreta e do ID chave para adicionar ao segredo, para que a função de rotação possa regenerar a chave na Conta de Armazenamento.

Recuperar identificação de recurso da conta de armazenamento. Valor pode ser encontrado sob `id` propriedade
```azurecli
az storage account show -n akvrotationstorage
```

Listar as chaves de acesso à Conta de Armazenamento para recuperar valores-chave

```azurecli
az storage account keys list -n akvrotationstorage 
```

Valores de recuperação de populato para **key1Value** e **armazenamentoAccountResourceId**

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey --vault-name akvrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Criar um segredo com uma data de validade curta publicará um `SecretNearExpiry` evento dentro de vários minutos, o que por sua vez irá desencadear a função para rodar o segredo.

Pode verificar se as chaves de acesso são regeneradas recuperando e comparando as chaves da conta de armazenamento e o segredo do Cofre chave.

Pode mostrar informações secretas usando abaixo o comando:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey
```
Note que `CredentialId` é atualizado para alternar e é `keyName` `value` regenerado Saída de ![ az keyvault secret show para a primeira conta de armazenamento](../media/secrets/rotation-dual/dual-rotation-4.png)

Recupere as chaves de acesso para validar o valor
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Saída da lista de chaves de conta de armazenamento AZ para a primeira conta de armazenamento](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-additional-storage-accounts-for-rotation"></a>Adicionar contas de armazenamento adicionais para rotação

A mesma aplicação de função pode ser reutilizada para rodar várias Contas de Armazenamento. 

A adição de chaves adicionais de conta de armazenamento para a rotação à função existente requer:
- Atribuir a função de Serviço do Operador chave de armazenamento para funcionar app para aceder às chaves de acesso à Conta de Armazenamento
- Subscrição de eventos Comagrid para evento **SecretNearExpiry**

1. Selecione a ligação de implementação do modelo Azure: 

   [![Imagem mostrando um botão marcado "Implementar para Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FAdd-Event-Subscription%2Fazuredeploy.json)

1. Na lista de **grupos de recursos,** selecione **akvrotation**.
1. No Nome da **Conta de Armazenamento,** digite o nome da conta de armazenamento com as teclas de acesso para rodar
1. No Nome do **Cofre de Chaves,** digite o nome do cofre da chave
1. No Nome da **Aplicação de Função,** digite o nome da aplicação de função
1. No **Nome Secreto**, escreva o nome secreto onde as chaves de acesso seriam armazenadas
1. Selecione **Review+Create**.
1. Selecione **Criar**

   ![Rever e criar a segunda conta de armazenamento](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Adicione outra chave de acesso à conta de armazenamento ao Cofre de Chaves

Recuperar identificação de recurso da conta de armazenamento. Valor pode ser encontrado sob `id` propriedade
```azurecli
az storage account show -n akvrotationstorage2
```

Listar as chaves de acesso à Conta de Armazenamento para recuperar o valor da chave2

```azurecli
az storage account keys list -n akvrotationstorage2 
```

Povoo recuperou valores para **key2Value** e **armazenamentoAccountResourceId**

```azurecli
tomorrowDate=`date -d tomorrow -Iseconds -u | awk -F'+' '{print $1"Z"}'`
az keyvault secret set --name storageKey2 --vault-name akvrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Mostrar informações secretas usando abaixo o comando:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey2
```
Note que `CredentialId` é atualizado para alternar e é `keyName` `value` regenerado Saída de ![ az keyvault secret show para a segunda conta de armazenamento](../media/secrets/rotation-dual/dual-rotation-8.png)

Recupere as chaves de acesso para validar o valor
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Saída da lista de chaves de conta de armazenamento AZ para a segunda conta de armazenamento](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="available-key-vault-dual-credential-rotation-functions"></a>Funções de rotação dupla credencial do Cofre da Chave disponível

- [Conta de Armazenamento](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)
- [Cache de Redis](https://github.com/jlichwa/KeyVault-Rotation-RedisCacheKey-PowerShell)

## <a name="learn-more"></a>Saiba mais
- Visão geral: [Cofre de chaves de monitorização com grade de eventos Azure](../general/event-grid-overview.md)
- Como: Criar a [sua primeira função no portal Azure](../../azure-functions/functions-create-first-azure-function.md)
- Como: [Receber e-mail quando um segredo de cofre chave muda](../general/event-grid-logicapps.md)
- [Esquema de eventos Azure Event Grid para Azure Key Vault](../../event-grid/event-schema-key-vault.md)
