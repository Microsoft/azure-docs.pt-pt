---
title: Configure as chaves geridas pelo cliente para a sua conta Azure Batch com cofre de chave Azure e identidade gerida
description: Saiba como encriptar os dados do Batch utilizando as teclas geridas pelo cliente.
author: pkshultz
ms.topic: how-to
ms.date: 02/11/2021
ms.author: peshultz
ms.openlocfilehash: d3f10436b95aaeb5eb35a873c2a3862c1492bd47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100385069"
---
# <a name="configure-customer-managed-keys-for-your-azure-batch-account-with-azure-key-vault-and-managed-identity"></a>Configure as chaves geridas pelo cliente para a sua conta Azure Batch com cofre de chave Azure e identidade gerida

Por predefinição, o Azure Batch utiliza chaves geridas pela plataforma para encriptar todos os dados do cliente armazenados no Serviço de Lote Azure, como certificados, metadados de trabalho/tarefa. Opcionalmente, pode utilizar as suas próprias chaves, ou seja, chaves geridas pelo cliente, para encriptar os dados armazenados no Azure Batch.

As chaves que fornece devem ser geradas no [Cofre da Chave Azure,](../key-vault/general/basic-concepts.md)e devem ser acedidas com [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md).

Existem dois tipos de identidades geridas: [ *atribuídas pelo sistema* e *atribuídas pelo utilizador*](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).

Pode criar a sua conta Batch com identidade gerida atribuída ao sistema ou criar uma identidade gerida separada que terá acesso às teclas geridas pelo cliente. Reveja a [tabela de comparação](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) para entender as diferenças e considere qual a opção que funciona melhor para a sua solução. Por exemplo, se pretender utilizar a mesma identidade gerida para aceder a vários recursos Azure, será necessária uma identidade gerida atribuída pelo utilizador. Caso contrário, uma identidade gerida atribuída ao sistema associada à sua conta Batch pode ser suficiente. A utilização de uma identidade gerida pelo utilizador também lhe dá a opção de impor chaves geridas pelo cliente na criação de conta Batch, como mostra [o exemplo abaixo](#create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys).

## <a name="create-a-batch-account-with-system-assigned-managed-identity"></a>Criar uma conta Batch com identidade gerida atribuída ao sistema

Se não precisar de uma identidade gerida separada, pode ativar a identidade gerida atribuída pelo sistema quando criar a sua conta Batch.

### <a name="azure-portal"></a>Portal do Azure

No [portal Azure,](https://portal.azure.com/)quando criar contas Batch, escolha o **Sistema atribuído** no tipo de identidade no separador **Avançado.**

![Screenshot de uma nova conta Batch com o tipo de identidade atribuído ao sistema.](./media/batch-customer-managed-key/create-batch-account.png)

Após a criação da conta, pode encontrar um GUID único no campo **de identificação principal** de identidade sob a secção **Propriedades.** O **Tipo de Identidade** mostrará `System assigned` .

![Screenshot mostrando um GUID único no campo de identificação principal de identidade.](./media/batch-customer-managed-key/linked-batch-principal.png)

Você precisará deste valor para conceder a esta conta Batch acesso ao Cofre chave.

### <a name="azure-cli"></a>CLI do Azure

Quando criar uma nova conta Batch, especifique `SystemAssigned` o `--identity` parâmetro.

```azurecli
resourceGroupName='myResourceGroup'
accountName='mybatchaccount'

az batch account create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' \
    --identity 'SystemAssigned'
```

Após a criação da conta, pode verificar se a identidade gerida atribuída pelo sistema foi ativada nesta conta. Não se esqueça de notar o `PrincipalId` , pois este valor será necessário para conceder a esta conta Batch acesso ao Cofre de Chaves.

```azurecli
az batch account show \
    -n $accountName \
    -g $resourceGroupName \
    --query identity
```

> [!NOTE]
> A identidade gerida pelo sistema criada numa conta Batch é utilizada apenas para recuperar chaves geridas pelo cliente a partir do Cofre de Chaves. Esta identidade não está disponível nas piscinas do Lote. Para utilizar uma identidade gerida atribuída pelo utilizador numa piscina, consulte [identidades geridas Configure em piscinas de Lote](managed-identity-pools.md).

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador

Se preferir, pode [criar uma identidade gerida pelo utilizador](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) que pode ser usada para aceder às teclas geridas pelo cliente.

Necessitará do valor de **identificação** do Cliente desta identidade para que possa aceder ao Cofre-Chave.

## <a name="configure-your-azure-key-vault-instance"></a>Configure o seu exemplo de Cofre de Chave Azure

O Cofre da Chave Azure no qual as suas chaves serão geradas deve ser criado no mesmo inquilino que a sua conta Batch. Não precisa de estar no mesmo grupo de recursos ou mesmo na mesma subscrição.

### <a name="create-an-azure-key-vault"></a>Criar um Azure Key Vault

Ao [criar uma instância Azure Key Vault](../key-vault/general/quick-create-portal.md) com chaves geridas pelo cliente para o Azure Batch, certifique-se de que a **Proteção** **de Eliminação** e Purga Suave está ativada.

![Screenshot do ecrã de criação do Cofre chave.](./media/batch-customer-managed-key/create-key-vault.png)

### <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Adicione uma política de acesso ao seu Azure Key Vault

No portal Azure, após a criação do Cofre de Chaves, Na **Política de Acesso** em **Definição**, adicione o acesso da conta Batch utilizando a identidade gerida. Sob **permissões de chave**, selecione **Get**, **Wrap Key** e **Desembrulhar A Tecla**.

![Screenshot mostrando o ecrã de política de acesso adicionar.](./media/batch-customer-managed-key/key-permissions.png)

No campo **Select** sob **o principal,** preencha um dos seguintes:

- Para identidade gerida atribuída ao sistema: Introduza o `principalId` que recuperou anteriormente ou o nome da conta Batch.
- Para a identidade gerida atribuída pelo utilizador: Introduza o **ID** do Cliente que recuperou anteriormente ou o nome da identidade gerida atribuída pelo utilizador.

![Imagem do ecrã principal.](./media/batch-customer-managed-key/principal-id.png)

### <a name="generate-a-key-in-azure-key-vault"></a>Gere uma chave no Cofre da Chave Azure

No portal Azure, aceda à instância Key Vault na secção **chave,** **selecione Gerar/Importar**. Selecione o **tipo de** chave para ser `RSA` e o tamanho da chave **RSA** para ser pelo menos `2048` bits. `EC` os tipos-chave não são atualmente suportados como uma chave gerida pelo cliente numa conta Batch.

![Criar uma chave](./media/batch-customer-managed-key/create-key.png)

Após a criação da chave, clique na tecla recém-criada e na versão atual, copie o **Identificador chave** na secção **de propriedades.**  Certifique-se de que, em **Operações Permitidas,** **a chave de embrulho** e a chave de **desembrulhar** estão verificadas.

## <a name="enable-customer-managed-keys-on-a-batch-account"></a>Ativar as chaves geridas pelo cliente numa conta Batch

Depois de seguir os passos acima, pode ativar as teclas geridas pelo cliente na sua conta Batch.

### <a name="azure-portal"></a>Portal do Azure

No [portal Azure,](https://portal.azure.com/)aceda à página da conta Do Lote. Na secção **de encriptação,** ative a **tecla gerida pelo Cliente**. Pode utilizar diretamente o identificador de chave ou pode selecionar o cofre de teclas e clicar **em Selecionar um cofre e uma tecla de teclas**.

![Screenshot mostrando a secção de encriptação e opção para ativar a chave gerida pelo cliente](./media/batch-customer-managed-key/encryption-page.png)

### <a name="azure-cli"></a>CLI do Azure

Após a criação da conta Batch com identidade gerida atribuída pelo sistema e o acesso ao Key Vault é concedido, atualize a conta Batch com o `{Key Identifier}` URL sob `keyVaultProperties` parâmetro. Também definir **encryption_key_source** como `Microsoft.KeyVault` .

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_source Microsoft.KeyVault \
    --encryption_key_identifier {YourKeyIdentifier} 
```

## <a name="create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys"></a>Criar uma conta Batch com identidade gerida pelo utilizador e chaves geridas pelo cliente

Utilizando o cliente Batch management .NET, pode criar uma conta Batch que terá uma identidade gerida pelo utilizador e chaves geridas pelo cliente.

```c#
EncryptionProperties encryptionProperties = new EncryptionProperties()
{
    KeySource = KeySource.MicrosoftKeyVault,
    KeyVaultProperties = new KeyVaultProperties()
    {
        KeyIdentifier = "Your Key Azure Resource Manager Resource ID"
    }
};

BatchAccountIdentity identity = new BatchAccountIdentity()
{
    Type = ResourceIdentityType.UserAssigned,
    UserAssignedIdentities = new Dictionary<string, BatchAccountIdentityUserAssignedIdentitiesValue>
    {
            ["Your Identity Azure Resource Manager ResourceId"] = new BatchAccountIdentityUserAssignedIdentitiesValue()
    }
};
var parameters = new BatchAccountCreateParameters(TestConfiguration.ManagementRegion, encryption:encryptionProperties, identity: identity);

var account = await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount", parameters); 
```

## <a name="update-the-customer-managed-key-version"></a>Atualize a versão chave gerida pelo cliente

Quando criar uma nova versão de uma chave, atualize a conta Batch para utilizar a nova versão. Siga estes passos:

1. Navegue na sua conta Batch no portal Azure e apresente as definições de Encriptação.
2. Introduza o URI para a nova versão chave. Em alternativa, pode selecionar o Cofre de Chaves e a chave novamente para atualizar a versão.
3. Guarde as alterações.

Também pode utilizar o Azure CLI para atualizar a versão.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourKeyIdentifierWithNewVersion} 
```

## <a name="use-a-different-key-for-batch-encryption"></a>Use uma chave diferente para encriptação do Lote

Para alterar a chave utilizada para encriptação do Lote, siga estes passos:

1. Navegue na sua conta Batch e apresente as definições de Encriptação.
2. Insira o URI para a nova chave. Alternadamente, pode selecionar o Cofre de Chaves e escolher uma nova chave.
3. Guarde as alterações.

Também pode utilizar o Azure CLI para utilizar uma chave diferente.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourNewKeyIdentifier} 
```

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

- **As chaves geridas pelo cliente são suportadas para as contas existentes do Batch?** N.º As chaves geridas pelo cliente só são suportadas para novas contas Batch.
- **Posso selecionar tamanhos de chave RSA maiores que 2048 bits?** Sim, os tamanhos e bits das chaves RSA `3072` `4096` também são suportados.
- **Que operações estão disponíveis após a revogação de uma chave gerida pelo cliente?** A única operação permitida é a eliminação de conta se o Batch perder o acesso à chave gerida pelo cliente.
- **Como devo restaurar o acesso à minha conta batch se eu acidentalmente apagar a chave key vault?** Uma vez que a proteção da purga e a eliminação suave estão ativadas, pode restaurar as teclas existentes. Para mais informações, consulte [Recuperar um Cofre de Chaves Azure](../key-vault/general/key-vault-recovery.md).
- **Posso desativar as chaves geridas pelo cliente?** Pode configurar o tipo de encriptação da Conta Lote de volta para "Tecla gerida pela Microsoft" a qualquer momento. Depois disso, pode apagar ou alterar a chave.
- **Como posso rodar as minhas chaves?** As chaves geridas pelo cliente não são automaticamente giradas. Para rodar a chave, atualize o identificador chave a que a conta está associada.
- **Depois de restaurar o acesso, quanto tempo demorará a conta batch a funcionar novamente?** Pode levar até 10 minutos para que a conta volte a estar acessível uma vez que o acesso seja restaurado.
- **Enquanto a Conta lote não está disponível o que acontece com os meus recursos?** Quaisquer piscinas que estejam em funcionamento quando o acesso do Batch às teclas geridas pelo cliente for perdida continuará a funcionar. No entanto, os nóleiros transitarãoão para um estado indisponível, e as tarefas deixarão de funcionar (e serão requeadas). Uma vez restaurado o acesso, os nós voltarão a estar disponíveis e as tarefas serão reiniciadas.
- **Este mecanismo de encriptação aplica-se aos discos VM numa piscina de Lote?** N.º Para piscinas de configuração de serviço de nuvem, não é aplicada nenhuma encriptação para o SISTEMA e disco temporário. Para piscinas de configuração de máquinas virtuais, o SISTEMA e quaisquer discos de dados especificados serão encriptados com uma chave gerida pela microsoft por padrão. Atualmente, não é possível especificar a sua própria chave para estes discos. Para encriptar o disco temporário de VMs para um pool de Lote com uma chave gerida pela plataforma Microsoft, tem de ativar a propriedade [diskEncrypationConfiguration](/rest/api/batchservice/pool/add#diskencryptionconfiguration) no seu [Pool de Configuração de Máquina Virtual.](/rest/api/batchservice/pool/add#virtualmachineconfiguration) Para ambientes altamente sensíveis, recomendamos permitir a encriptação temporária do disco e evitar armazenar dados sensíveis em SISTEMA e discos de dados. Para obter mais informações, consulte [Criar um pool com encriptação de disco ativada](./disk-encryption.md)
- **A identidade gerida atribuída pelo sistema na conta Batch está disponível nos nós de computação?** N.º A identidade gerida pelo sistema é atualmente utilizada apenas para aceder ao Cofre da Chave Azure para a chave gerida pelo cliente. Para utilizar uma identidade gerida atribuída pelo utilizador em nós computacional, consulte [identidades geridas configure em piscinas de Lote](managed-identity-pools.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [as melhores práticas de segurança em Azure Batch](security-best-practices.md).
- Saiba mais sobre[o Azure Key Vault](../key-vault/general/basic-concepts.md).
