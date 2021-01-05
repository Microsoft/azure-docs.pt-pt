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
ms.openlocfilehash: c2496959f851b55f8cc66c0e793b641cdafb003a
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97808339"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-have-two-sets-of-authentication-credentials"></a>Automatizar a rotação de um segredo para recursos que têm dois conjuntos de credenciais de autenticação

A melhor forma de autenticar os serviços da Azure é utilizando uma [identidade gerida](../general/authentication.md), mas existem alguns cenários em que isso não é uma opção. Nesses casos, são utilizadas chaves de acesso ou senhas de acesso. Deve rodar frequentemente as teclas de acesso e as palavras-passe.

Este tutorial mostra como automatizar a rotação periódica de segredos para bases de dados e serviços que utilizam dois conjuntos de credenciais de autenticação. Especificamente, este tutorial mostra como rodar as chaves da conta Azure Storage armazenadas no Cofre da Chave Azure como segredos. Utilizará uma função desencadeada pela notificação da Azure Event Grid. 

> [!NOTE]
> As chaves da conta de armazenamento podem ser geridas automaticamente no Key Vault se fornecer fichas de assinatura de acesso partilhado para acesso delegado à conta de armazenamento. Existem serviços que requerem cadeias de ligação da conta de armazenamento com teclas de acesso. Para esse cenário, recomendamos esta solução.

Aqui está a solução de rotação descrita neste tutorial: 

![Diagrama que mostra a solução de rotação.](../media/secrets/rotation-dual/rotation-diagram.png)

Nesta solução, o Azure Key Vault armazena chaves de acesso individuais de conta de armazenamento como versões do mesmo segredo, alternando entre a chave primária e secundária nas versões subsequentes. Quando uma chave de acesso é armazenada na versão mais recente do segredo, a chave alternativa é regenerada e adicionada ao Key Vault como a nova versão mais recente do segredo. A solução fornece todo o ciclo de rotação da aplicação para refrescar para a mais recente chave regenerada. 

1. Trinta dias antes da data de validade de um segredo, Key Vault publica o evento de quase expiração para a Grade de Eventos.
1. A Grelha de Eventos verifica as subscrições do evento e utiliza HTTP POST para ligar para o ponto final da aplicação de função que está subscrito ao evento.
1. A aplicação de função identifica a chave alternativa (não a mais recente) e chama a conta de armazenamento para a regenerar.
1. A aplicação de função adiciona a nova chave regenerada ao Azure Key Vault como a nova versão do segredo.

## <a name="prerequisites"></a>Pré-requisitos
* Uma subscrição do Azure. [Crie um de graça.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Concha de nuvem](https://shell.azure.com/)azul . Este tutorial está a usar o portal Cloud Shell com powerShell env
* Azure Key Vault.
* Duas contas de armazenamento Azure.

Pode utilizar este link de implantação se não tiver um cofre de chaves existente e contas de armazenamento existentes:

[![Link que está rotulado Implementar para Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FInitial-Setup%2Fazuredeploy.json)

1. No **grupo de Recursos,** selecione **Criar novo**. Nomeie a **abotoação do** grupo e, em seguida, selecione **OK**.
1. Selecione **Rever + criar**.
1. Selecione **Criar**.

    ![Screenshot que mostra como criar um grupo de recursos.](../media/secrets/rotation-dual/dual-rotation-1.png)

Agora terá um cofre e duas contas de armazenamento. Pode verificar esta configuração no CLI Azure executando este comando:

```azurecli
az resource list -o table -g vaultrotation
```

O resultado será algo parecido com esta saída:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
vaultrotation-kv         vaultrotation      westus      Microsoft.KeyVault/vaults
vaultrotationstorage     vaultrotation      westus      Microsoft.Storage/storageAccounts
vaultrotationstorage2    vaultrotation      westus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-the-key-rotation-function"></a>Criar e implementar a função de rotação chave

Em seguida, irá criar uma aplicação de função com uma identidade gerida pelo sistema, além de outros componentes necessários. Também irá utilizar a função de rotação para as teclas da conta de armazenamento.

A função de rotação da aplicação de função requer os seguintes componentes e configuração:
- Um plano de serviço de aplicações Azure
- Uma conta de armazenamento para gerir os gatilhos de aplicações de função
- Uma política de acesso a segredos de acesso em Key Vault
- A função de Serviço do Operador chave de conta de armazenamento atribuída à app de função para que possa aceder às chaves de acesso à conta de armazenamento
- Uma função de rotação chave com um gatilho de evento e um gatilho HTTP (rotação a pedido)
- Uma subscrição de eventos grid para o evento **SecretNearExpiry**

1. Selecione a ligação de implementação do modelo Azure: 

   [![Ligação de implementação do modelo Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FFunction%2Fazuredeploy.json)

1. Na lista de **grupos de recursos,** selecione **a prata do abobadamento**.
1. Na caixa **RG da Conta de Armazenamento,** insira o nome do grupo de recursos no qual a sua conta de armazenamento está localizada. Mantenha o valor predefinido **[grupo de recursos().nome]** se a sua conta de armazenamento já estiver localizada no mesmo grupo de recursos onde irá implementar a função de rotação da chave.
1. Na caixa **'Nome da Conta de Armazenamento',** insira o nome da conta de armazenamento que contém as teclas de acesso para rodar. Mantenha o valor predefinido **[concat (resourceGroup().name, 'storage')]** se utilizar a conta de armazenamento criada em [Pré-requisitos](#prerequisites).
1. Na caixa **Key Vault RG,** insira o nome do grupo de recursos no qual está localizado o cofre-chave. Mantenha o valor predefinido **[grupo de recursos().nome]** se o seu cofre de teclas já existir no mesmo grupo de recursos onde irá implantar a função de rotação da chave.
1. Na caixa **key Vault Name,** insira o nome do cofre da chave. Mantenha o valor predefinido **[concat (resourceGroup().name, 'kv')]** se utilizar o cofre-chave criado em [Pré-requisitos](#prerequisites).
1. Na caixa do Plano de Serviço de **Aplicação,** selecione o plano de hospedagem. **O Plano Premium** só é necessário quando o cofre está atrás da firewall.
1. Na caixa **'Nome da aplicação de funções',** insira o nome da aplicação de função.
1. Na caixa **de Nome Secreto,** insira o nome do segredo onde irá armazenar as chaves de acesso.
1. Na caixa **URL do Repo,** introduza a localização GitHub do código de função. Neste tutorial pode **https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell.git** utilizar.
1. Selecione **Rever + criar**.
1. Selecione **Criar**.

   ![Screenshot que mostra como criar e implementar função.](../media/secrets/rotation-dual/dual-rotation-2.png)

Depois de completar os passos anteriores, terá uma conta de armazenamento, uma fazenda de servidores, uma aplicação de função e Insights de Aplicação. Quando a implementação estiver concluída, verá esta página:

   ![Screenshot que mostra a sua implementação é página completa.](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> Se encontrar uma falha, pode selecionar **Reposicionar-se** para terminar a colocação dos componentes.


Pode encontrar modelos de implementação e código para a função de rotação em [Amostras Azure](https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell).

## <a name="add-the-storage-account-access-keys-to-key-vault"></a>Adicione as chaves de acesso à conta de armazenamento ao Key Vault

Em primeiro lugar, desagreda a sua política de acesso para conceder permissões **de segredos** ao seu utilizador principal:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name vaultrotation-kv --secret-permissions set delete get list
```

Pode agora criar um novo segredo com uma chave de acesso à conta de armazenamento como seu valor. Você também precisará do ID do recurso de armazenamento, período de validade secreta e ID chave para adicionar ao segredo para que a função de rotação possa regenerar a chave na conta de armazenamento.

Determine o ID do recurso de armazenamento. Você pode encontrar este valor na `id` propriedade.

```azurecli
az storage account show -n vaultrotationstorage
```

Enuma as teclas de acesso à conta de armazenamento para que possa obter os valores-chave:

```azurecli
az storage account keys list -n vaultrotationstorage 
```

Adicione segredo ao cofre chave com data de validade definida para amanhã, período de validade de 60 dias e id de recursos de armazenamento. Executar este comando, utilizando os seus valores recuperados para `key1Value` `storageAccountResourceId` e:

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddTHH:mm:ssZ")
az keyvault secret set --name storageKey --vault-name vaultrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Acima do segredo irá desencadear `SecretNearExpiry` o evento em alguns minutos. Este evento irá, por sua vez, ativar a função para rodar o segredo com expiração definida para 60 dias. Nessa configuração, o evento 'SecretNearExpiry' seria acionado a cada 30 dias (30 dias antes do termo) e a função de rotação alternaria a rotação entre a chave1 e a chave2.

Pode verificar se as chaves de acesso se regeneraram recuperando a chave da conta de armazenamento e o segredo do Cofre chave e comparando-as.

Use este comando para obter as informações secretas:
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey
```

Note que `CredentialId` é atualizado para o suplente e que é `keyName` `value` regenerado:

![Screenshot que mostra a saída do comando de um show secreto z keyvault para a primeira conta de armazenamento.](../media/secrets/rotation-dual/dual-rotation-4.png)

Recupere as teclas de acesso para comparar os valores:
```azurecli
az storage account keys list -n vaultrotationstorage 
```
Note que `value` a chave é o mesmo que o segredo no cofre chave:

![Screenshot que mostra a saída do comando da lista de chaves de conta de armazenamento z para a primeira conta de armazenamento.](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-storage-accounts-for-rotation"></a>Adicionar contas de armazenamento para rotação

Pode reutilizar a mesma aplicação de função para rodar as teclas para várias contas de armazenamento. 

Para adicionar as chaves da conta de armazenamento a uma função existente para a rotação, é necessário:
- A função de Serviço do Operador chave de conta de armazenamento atribuída à aplicação de função para que possa aceder às chaves de acesso à conta de armazenamento.
- Uma subscrição de eventos grid para o evento **SecretNearExpiry.**

1. Selecione a ligação de implementação do modelo Azure: 

   [![Ligação de implementação do modelo Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FAdd-Event-Subscriptions%2Fazuredeploy.json)

1. Na lista de **grupos de recursos,** selecione **a prata do abobadamento**.
1. Na caixa **RG da Conta de Armazenamento,** insira o nome do grupo de recursos no qual a sua conta de armazenamento está localizada. Mantenha o valor predefinido **[grupo de recursos().nome]** se a sua conta de armazenamento já estiver localizada no mesmo grupo de recursos onde irá implementar a função de rotação da chave.
1. Na caixa **'Nome da Conta de Armazenamento',** insira o nome da conta de armazenamento que contém as teclas de acesso para rodar.
1. Na caixa **Key Vault RG,** insira o nome do grupo de recursos no qual está localizado o cofre-chave. Mantenha o valor predefinido **[grupo de recursos().nome]** se o seu cofre de teclas já existir no mesmo grupo de recursos onde irá implantar a função de rotação da chave.
1. Na caixa **key Vault Name,** insira o nome do cofre da chave.
1. Na caixa **'Nome da aplicação de funções',** insira o nome da aplicação de função.
1. Na caixa **de Nome Secreto,** insira o nome do segredo onde irá armazenar as chaves de acesso.
1. Selecione **Rever + criar**.
1. Selecione **Criar**.

   ![Screenshot que mostra como criar uma conta de armazenamento adicional.](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Adicione outra chave de acesso à conta de armazenamento ao Key Vault

Determine o ID do recurso de armazenamento. Você pode encontrar este valor na `id` propriedade.
```azurecli
az storage account show -n vaultrotationstorage2
```

Enuma as chaves de acesso à conta de armazenamento para que possa obter o valor da chave2:

```azurecli
az storage account keys list -n vaultrotationstorage2 
```

Adicione segredo ao cofre chave com data de validade definida para amanhã, período de validade de 60 dias e id de recursos de armazenamento. Executar este comando, utilizando os seus valores recuperados para `key2Value` `storageAccountResourceId` e:

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddTHH:mm:ssZ")
az keyvault secret set --name storageKey2 --vault-name vaultrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Use este comando para obter as informações secretas:
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey2
```

Note que `CredentialId` é atualizado para o suplente e que é `keyName` `value` regenerado:

![Screenshot que mostra a saída do comando de um show secreto z keyvault para a segunda conta de armazenamento.](../media/secrets/rotation-dual/dual-rotation-8.png)

Recupere as teclas de acesso para comparar os valores:
```azurecli
az storage account keys list -n vaultrotationstorage 
```

Note que `value` a chave é o mesmo que o segredo no cofre chave:

![Screenshot que mostra a saída do comando da lista de chaves de conta de armazenamento z para a segunda conta de armazenamento.](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="key-vault-rotation-functions-for-two-sets-of-credentials"></a>Funções de rotação do cofre chave para dois conjuntos de credenciais

- [Conta de armazenamento](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)
- [Cache de Redis](https://github.com/jlichwa/KeyVault-Rotation-RedisCacheKey-PowerShell)

## <a name="next-steps"></a>Passos seguintes

- Tutorial: [Rotação de segredos para um conjunto de credenciais](https://docs.microsoft.com/azure/key-vault/secrets/tutorial-rotation)
- Visão geral: [Cofre de chaves de monitorização com grade de eventos Azure](../general/event-grid-overview.md)
- Como: Criar a [sua primeira função no portal Azure](../../azure-functions/functions-create-first-azure-function.md)
- Como: [Receber e-mail quando um segredo do Cofre chave muda](../general/event-grid-logicapps.md)
- Referência: [Esquema de eventos Azure Event Grid para Azure Key Vault](../../event-grid/event-schema-key-vault.md)
