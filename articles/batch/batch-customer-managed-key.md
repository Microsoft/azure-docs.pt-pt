---
title: Configure as chaves geridas pelo cliente para a sua conta Azure Batch com cofre de chave Azure e identidade gerida
description: Saiba como encriptar os dados do Lote usando as teclas
author: pkshultz
ms.topic: how-to
ms.date: 07/17/2020
ms.author: peshultz
ms.openlocfilehash: 404103caf376b792d363996664a69f655d5bd202
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326017"
---
# <a name="configure-customer-managed-keys-for-your-azure-batch-account-with-azure-key-vault-and-managed-identity"></a>Configure as chaves geridas pelo cliente para a sua conta Azure Batch com cofre de chave Azure e identidade gerida

Por predefinição, o Azure Batch utiliza chaves geridas pela plataforma para encriptar todos os dados do cliente armazenados no Serviço de Lote Azure, como certificados, metadados de trabalho/tarefa. Opcionalmente, pode utilizar as suas próprias chaves, ou seja, chaves geridas pelo cliente, para encriptar os dados armazenados no Azure Batch.

As chaves que fornece devem ser geradas no [Cofre da Chave Azure,](../key-vault/general/basic-concepts.md)e as contas batch que pretende configurar com as chaves geridas pelo cliente têm de ser ativadas com identidade gerida pelo [Azure](../active-directory/managed-identities-azure-resources/overview.md).

> [!IMPORTANT]
> O apoio às chaves geridas pelo cliente em Azure Batch está atualmente em pré-visualização pública para as regiões da Europa Ocidental, Norte da Europa, Suíça Norte, Central dos EUA, Centro-Sul dos EUA, Leste dos EUA 2, West US 2, EUA Gov Virginia e eua Gov Arizona regiões.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-batch-account-with-system-assigned-managed-identity"></a>Criar uma Conta de Lote com identidade gerida atribuída ao sistema

### <a name="azure-portal"></a>Portal do Azure

No [portal Azure,](https://portal.azure.com/)quando criar contas Batch, escolha o **Sistema atribuído** no tipo de identidade no separador **Avançado.**

![Nova conta batch com tipo de identidade atribuído ao sistema](./media/batch-customer-managed-key/create-batch-account.png)

Após a criação da conta, pode encontrar um GUID único no campo **de identificação principal de identidade** sob a secção **Propriedade.** O **Tipo de Identidade** mostrará `SystemAssigned` .

![GUID exclusivo no campo de id principal identidade de identidade](./media/batch-customer-managed-key/linked-batch-principal.png)
 
### <a name="azure-cli"></a>CLI do Azure

Quando criar uma nova conta Batch, especifique `SystemAssigned` o `--identity` parâmetro.

```powershell
resourceGroupName='myResourceGroup'
accountName='mybatchaccount'

az batch account create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' \
    --identity 'SystemAssigned'
```

Após a criação da conta, pode verificar se a identidade gerida atribuída pelo sistema foi ativada nesta conta. Não se esqueça de notar o `PrincipalId` , pois este valor será necessário para conceder a esta conta de lote acesso ao Cofre chave.

```powershell
az batch account show \
    -n $accountName \
    -g $resourceGroupName \
    --query identity
```

> [!NOTE]
> A identidade gerida pelo sistema criada numa conta Batch é utilizada apenas para recuperar chaves geridas pelo cliente a partir do Cofre de Chaves. Esta identidade não está disponível nas piscinas do Lote.

## <a name="configure-your-azure-key-vault-instance"></a>Configure o seu exemplo de Cofre de Chave Azure

### <a name="create-an-azure-key-vault"></a>Criar um Azure Key Vault

Ao criar uma instância Azure Key Vault com chaves geridas pelo cliente para o Azure Batch, certifique-se de que a **Proteção** **de Eliminação** e Purga Suave está ativada.

![Tela de criação do Cofre chave](./media/batch-customer-managed-key/create-key-vault.png)

### <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Adicione uma política de acesso ao seu Azure Key Vault

No portal Azure, após a criação do Cofre de Chaves, Na **Política de Acesso** em **Definição**, adicione o acesso da conta Batch utilizando a identidade gerida. Sob **permissões de chave**, selecione **Get**, **Wrap Key** e **Desembrulhar A Tecla**. 

![Adicionar política de acesso](./media/batch-customer-managed-key/key-permissions.png)

No campo **Seleção** em **Princípio,** preencha o `principalId` que recuperou anteriormente ou o nome da conta do lote.

![Insira o principalId](./media/batch-customer-managed-key/principal-id.png)

### <a name="generate-a-key-in-azure-key-vault"></a>Gere uma chave no Cofre da Chave Azure

No portal Azure, aceda à instância Key Vault na secção **chave,** **selecione Gerar/Importar**. Selecione o **tipo de** chave para ser `RSA` e o tamanho da chave **RSA** para ser pelo menos `2048` bits. `EC` os tipos-chave não são atualmente suportados como uma chave gerida pelo cliente numa conta Batch.

![Criar uma chave](./media/batch-customer-managed-key/create-key.png)

Após a criação da chave, clique na tecla recém-criada e na versão atual, copie o **Identificador chave** na secção **de propriedades.**  Certifique-se de que, em **Operações Permitidas,** **a chave de embrulho** e a chave de **desembrulhar** estão verificadas.

## <a name="enable-customer-managed-keys-on-azure-batch-account"></a>Ativar chaves geridas pelo cliente na Conta Azure Batch

### <a name="azure-portal"></a>Portal do Azure

No [portal Azure,](https://portal.azure.com/)aceda à página da conta Do Lote. Na secção **de encriptação,** ative a **tecla gerida pelo Cliente**. Pode utilizar diretamente o identificador de chave ou pode selecionar o cofre de teclas e clicar **em Selecionar um cofre e uma tecla de teclas**.

![Em Encriptação, ativar a chave gerida pelo Cliente](./media/batch-customer-managed-key/encryption-page.png)

### <a name="azure-cli"></a>CLI do Azure

Após a criação da conta Batch com identidade gerida atribuída pelo sistema e o acesso ao Key Vault é concedido, atualize a conta Batch com o `{Key Identifier}` URL sob `keyVaultProperties` parâmetro. Também definir **encryption_key_source** como `Microsoft.KeyVault` .

```powershell
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_source Microsoft.KeyVault \
    --encryption_key_identifier {YourKeyIdentifier} 
```

## <a name="update-the-customer-managed-key-version"></a>Atualize a versão chave gerida pelo cliente

Quando criar uma nova versão de uma chave, atualize a conta Batch para utilizar a nova versão. Siga estes passos:

1. Navegue na sua conta Batch no portal Azure e apresente as definições de Encriptação.
2. Introduza o URI para a nova versão chave. Em alternativa, pode selecionar o cofre da chave e a chave novamente para atualizar a versão.
3. Guarde as alterações.

Também pode utilizar o Azure CLI para atualizar a versão.

```powershell
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourKeyIdentifierWithNewVersion} 
```
## <a name="use-a-different-key-for-batch-encryption"></a>Use uma chave diferente para encriptação do Lote

Para alterar a chave utilizada para encriptação do Lote, siga estes passos:

1. Navegue na sua conta Batch e apresente as definições de Encriptação.
2. Insira o URI para a nova chave. Alternadamente, pode selecionar o cofre da chave e escolher uma nova chave.
3. Guarde as alterações.

Também pode utilizar o Azure CLI para utilizar uma chave diferente.

```powershell
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourNewKeyIdentifier} 
```
## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
  * **As chaves geridas pelo cliente são suportadas para as contas existentes do Batch?** Não. As chaves geridas pelo cliente só são suportadas para novas contas Batch.
  * **Posso selecionar tamanhos de chave RSA maiores que 2048 bits?** Sim, os tamanhos e bits das chaves RSA `3072` `4096` também são suportados.
  * **Que operações estão disponíveis após a revogação de uma chave gerida pelo cliente?** A única operação permitida é a eliminação de conta se o Batch perder o acesso à chave gerida pelo cliente.
  * **Como devo restaurar o acesso à minha conta batch se eu acidentalmente apagar a chave key vault?** Uma vez que a proteção da purga e a eliminação suave estão ativadas, pode restaurar as teclas existentes. Para mais informações, consulte [Recuperar um Cofre de Chaves Azure](../key-vault/general/key-vault-recovery.md).
  * **Posso desativar as chaves geridas pelo cliente?** Pode configurar o tipo de encriptação da Conta Lote de volta para "Tecla gerida pela Microsoft" a qualquer momento. Depois disso, pode apagar ou alterar a chave.
  * **Como posso rodar as minhas chaves?** As chaves geridas pelo cliente não são automaticamente giradas. Para rodar a chave, atualize o identificador chave a que a conta está associada.
  * **Depois de restaurar o acesso, quanto tempo demorará a conta batch a funcionar novamente?** Pode levar até 10 minutos para que a conta volte a estar acessível uma vez que o acesso seja restaurado.
  * **Enquanto a Conta lote não está disponível o que acontece com os meus recursos?** Quaisquer piscinas que estejam em funcionamento quando o acesso do Batch às teclas geridas pelo cliente for perdida continuará a funcionar. No entanto, os nóleiros transitarãoão para um estado indisponível, e as tarefas deixarão de funcionar (e serão requeadas). Uma vez restaurado o acesso, os nós voltarão a estar disponíveis e as tarefas serão reiniciadas.
  * **Este mecanismo de encriptação aplica-se aos discos VM numa piscina de Lote?** Não. Para piscinas de configuração de serviço de nuvem, não é aplicada nenhuma encriptação para o SISTEMA e disco temporário. Para piscinas de configuração de máquinas virtuais, o SISTEMA e quaisquer discos de dados especificados serão encriptados com uma chave gerida pela microsoft por padrão. Atualmente, não é possível especificar a sua própria chave para estes discos. Para encriptar o disco temporário de VMs para um pool de Lote com uma chave gerida pela plataforma Microsoft, tem de ativar a propriedade [diskEncrypationConfiguration](/rest/api/batchservice/pool/add#diskencryptionconfiguration) no seu [Pool de Configuração de Máquina Virtual.](/rest/api/batchservice/pool/add#virtualmachineconfiguration) Para ambientes altamente sensíveis, recomendamos permitir a encriptação temporária do disco e evitar armazenar dados sensíveis em SISTEMA e discos de dados. Para obter mais informações, consulte [Criar um pool com encriptação de disco ativada](./disk-encryption.md)
  * **A identidade gerida atribuída pelo sistema na conta Batch está disponível nos nós de computação?** Não. Esta identidade gerida é atualmente utilizada apenas para aceder ao Cofre chave Azure para a chave gerida pelo cliente.
