---
title: Azure Key Vault-como usar a exclusão reversível com a CLI
description: Exemplos de caso de uso de exclusão reversível com código de CLI
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: aef4061a8349e6602ac4394cb31bbe76b6cb63c0
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976300"
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>Como usar a exclusão reversível de Key Vault com a CLI

O recurso de exclusão reversível do Azure Key Vault permite a recuperação de cofres excluídos e objetos de cofre. Especificamente, a exclusão reversível resolve os seguintes cenários:

- Suporte para exclusão recuperável de um cofre de chaves
- Suporte para exclusão recuperável de objetos do cofre de chaves; chaves, segredos e certificados

## <a name="prerequisites"></a>Pré-requisitos

- CLI do Azure-se você não tiver essa configuração para seu ambiente, consulte [gerenciar Key Vault usando CLI do Azure](key-vault-manage-with-cli2.md).

Para obter Key Vault informações de referência específicas para a CLI, consulte [CLI do Azure Key Vault referência](https://docs.microsoft.com/cli/azure/keyvault).

## <a name="required-permissions"></a>Permissões obrigatórias

As operações de Key Vault são gerenciadas separadamente por meio de permissões de RBAC (controle de acesso baseado em função) da seguinte maneira:

| Operação | Descrição | Permissão de usuário |
|:--|:--|:--|
|Lista|Lista os cofres de chaves excluídos.|Microsoft.KeyVault/deletedVaults/read|
|Recuperar|Restaura um cofre de chaves excluído.|Microsoft.KeyVault/vaults/write|
|Limpar|Remove permanentemente um cofre de chaves excluído e todo o seu conteúdo.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Para obter mais informações sobre permissões e controle de acesso, consulte [proteger seu cofre de chaves](key-vault-secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Habilitando a exclusão reversível

Você habilita a "exclusão reversível" para permitir a recuperação de um cofre de chaves excluído ou de objetos armazenados em um cofre de chaves.

> [!IMPORTANT]
> A habilitação de ' exclusão reversível ' em um cofre de chaves é uma ação irreversível. Depois que a propriedade de exclusão reversível tiver sido definida como "true", ela não poderá ser alterada ou removida.  

### <a name="existing-key-vault"></a>Cofre de chaves existente

Para um cofre de chaves existente chamado ContosoVault, habilite a exclusão reversível da seguinte maneira. 

```azurecli
az resource update --id $(az keyvault show --name ContosoVault -o tsv | awk '{print $1}') --set properties.enableSoftDelete=true
```

### <a name="new-key-vault"></a>Novo cofre de chaves

A habilitação da exclusão reversível para um novo cofre de chaves é feita no momento da criação, adicionando o sinalizador de habilitação de exclusão reversível ao comando Create.

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>Verificar a habilitação de exclusão reversível

Para verificar se um cofre de chaves tem a exclusão reversível habilitada, execute o comando *show* e procure a ' exclusão reversível habilitada? ' Attribute

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Excluindo um cofre de chaves protegido por exclusão reversível

O comando para excluir um cofre de chaves muda de comportamento, dependendo se a exclusão reversível está habilitada.

> [!IMPORTANT]
>Se você executar o comando a seguir para um cofre de chaves que não tenha a exclusão reversível habilitada, você excluirá permanentemente esse cofre de chaves e todo o seu conteúdo sem opções de recuperação!

```azurecli
az keyvault delete --name ContosoVault
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Como a exclusão reversível protege seus cofres de chaves

Com a exclusão reversível habilitada:

- Um cofre de chaves excluído é removido de seu grupo de recursos e colocado em um namespace reservado, associado ao local onde ele foi criado. 
- Objetos excluídos, como chaves, segredos e certificados, ficam inacessíveis desde que o cofre de chaves que o contém esteja no estado excluído. 
- O nome DNS para um cofre de chaves excluído é reservado, impedindo que um novo cofre de chaves com o mesmo nome seja criado.  

Você pode exibir cofres de chaves de estado excluídos, associados à sua assinatura, usando o seguinte comando:

```azurecli
az keyvault list-deleted
```
- A *ID* pode ser usada para identificar o recurso durante a recuperação ou a limpeza. 
- *ID de recurso* é a ID de recurso original deste cofre. Como esse cofre de chaves agora está em um estado excluído, não existe nenhum recurso com essa ID de recurso. 
- A *data de limpeza agendada* é quando o cofre será excluído permanentemente, se nenhuma ação for executada. O período de retenção padrão, usado para calcular a *data de limpeza agendada*, é de 90 dias.

## <a name="recovering-a-key-vault"></a>Recuperando um cofre de chaves

Para recuperar um cofre de chaves, especifique o nome do cofre de chaves, o grupo de recursos e o local. Observe o local e o grupo de recursos do cofre de chaves excluído, pois você precisa deles para o processo de recuperação.

```azurecli
az keyvault recover --location westus --resource-group ContosoRG --name ContosoVault
```

Quando um cofre de chaves é recuperado, um novo recurso é criado com a ID de recurso original do cofre de chaves. Se o grupo de recursos original for removido, será necessário criá-lo com o mesmo nome antes de tentar a recuperação.

## <a name="deleting-and-purging-key-vault-objects"></a>Excluindo e limpando objetos do cofre de chaves

O comando a seguir excluirá a chave ' ContosoFirstKey ' em um cofre de chaves chamado ' ContosoVault ', que tem a exclusão reversível habilitada:

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

Com o cofre de chaves habilitado para exclusão reversível, uma chave excluída ainda aparece como sendo excluída, exceto quando você lista explicitamente ou recupera chaves excluídas. A maioria das operações em uma chave no estado excluído falhará, exceto para listar uma chave excluída, recuperar ou limpá-la. 

Por exemplo, para solicitar a lista de chaves excluídas em um cofre de chaves, use o seguinte comando:

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>Estado de transição 

Quando você exclui uma chave em um cofre de chaves com exclusão reversível habilitada, pode levar alguns segundos para que a transição seja concluída. Durante essa transição, pode parecer que a chave não está no estado ativo ou no estado excluído. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Usando a exclusão reversível com objetos do Key Vault

Assim como os cofres de chaves, uma chave excluída, um segredo ou um certificado, permanece em estado excluído por até 90 dias, a menos que você o recupere ou limpe.

#### <a name="keys"></a>Chaves

Para recuperar uma chave excluída de maneira reversível:

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

Para excluir permanentemente (também conhecido como limpeza) uma chave excluída por software:

> [!IMPORTANT]
> Limpar uma chave irá excluí-la permanentemente e ela não será recuperável! 

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

As ações de **recuperação** e **limpeza** têm suas próprias permissões associadas em uma política de acesso do cofre de chaves. Para que um usuário ou entidade de serviço possa executar uma ação de **recuperação** ou **limpeza** , ele deve ter a respectiva permissão para essa chave ou segredo. Por padrão, a **limpeza** não é adicionada à política de acesso do Key Vault, quando o atalho ' all' é usado para conceder todas as permissões. Você deve conceder especificamente a permissão de **limpeza** . 

#### <a name="set-a-key-vault-access-policy"></a>Definir uma política de acesso do cofre de chaves

O comando a seguir user@contoso.com concede permissão para usar várias operações em chaves no *ContosoVault* , incluindo a **limpeza**:

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> Se você tiver um cofre de chaves existente que tenha tido a exclusão reversível habilitada, talvez você não tenha permissões de **recuperação** e **limpeza** .

#### <a name="secrets"></a>Segredos

Como as chaves, os segredos são gerenciados com seus próprios comandos:

- Exclua um segredo chamado SQLPassword: 
  ```azurecli
  az keyvault secret delete --vault-name ContosoVault -name SQLPassword
  ```

- Listar todos os segredos excluídos em um cofre de chaves: 
  ```azurecli
  az keyvault secret list-deleted --vault-name ContosoVault
  ```

- Recuperar um segredo no estado excluído: 
  ```azurecli
  az keyvault secret recover --name SQLPassword --vault-name ContosoVault
  ```

- Limpar um segredo no estado excluído: 

  > [!IMPORTANT]
  > A limpeza de um segredo irá excluí-lo permanentemente e ele não será recuperável! 

  ```azurecli
  az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
  ```

## <a name="purging-a-soft-delete-protected-key-vault"></a>Limpando um cofre de chaves protegido por exclusão reversível

> [!IMPORTANT]
> A limpeza de um cofre de chaves ou de um de seus objetos contidos irá excluí-lo permanentemente, o que significa que ele não será recuperável!

A função de limpeza é usada para excluir permanentemente um objeto do cofre de chaves ou um cofre de chaves inteiro, que anteriormente foi excluído de forma reversível. Conforme demonstrado na seção anterior, os objetos armazenados em um cofre de chaves com o recurso de exclusão reversível habilitada podem passar por vários Estados:

- **Ativo**: antes da exclusão.
- **Excluído de**forma reversível: após a exclusão, pode ser listado e recuperado novamente para o estado ativo.
- **Excluído permanentemente**: após a limpeza, não é possível recuperá-lo.

O mesmo é verdadeiro para o cofre de chaves. Para excluir permanentemente um cofre de chaves com exclusão reversível e seu conteúdo, você deve limpar o cofre de chaves em si.

### <a name="purging-a-key-vault"></a>Limpando um cofre de chaves

Quando um cofre de chaves é limpo, seu conteúdo inteiro é excluído permanentemente, incluindo chaves, segredos e certificados. Para limpar um cofre de chaves com exclusão reversível, `az keyvault purge` use o comando. Você pode encontrar o local em que os cofres de chaves excluídos da sua `az keyvault list-deleted`assinatura usam o comando.

```azurecli
az keyvault purge --location westus --name ContosoVault
```

### <a name="purge-permissions-required"></a>Permissões de limpeza necessárias
- Para limpar um cofre de chaves excluído, o usuário precisa de permissão RBAC para a operação *Microsoft. keyvault/Locations/deletedVaults/limpeza/ação* . 
- Para listar um cofre de chaves excluído, o usuário precisa de permissão RBAC para a operação *Microsoft. keyvault/deletedVaults/Read* . 
- Por padrão, somente um administrador de assinatura tem essas permissões. 

### <a name="scheduled-purge"></a>Limpeza agendada

A listagem de objetos do cofre de chaves excluídos também mostra quando eles estão agendados para serem limpos pelo Key Vault. *Data de limpeza agendada* indica quando um objeto do cofre de chaves será excluído permanentemente, se nenhuma ação for executada. Por padrão, o período de retenção para um objeto de cofre de chaves excluído é de 90 dias.

>[!IMPORTANT]
>Um objeto de cofre limpo, disparado por seu campo de *data de limpeza agendado* , é excluído permanentemente. Não é recuperável!

## <a name="enabling-purge-protection"></a>Habilitando a proteção de limpeza

Quando a proteção de limpeza é ativada, um cofre ou um objeto no estado excluído não pode ser limpo até que o período de retenção de 90 dias tenha passado. Esse cofre ou objeto ainda pode ser recuperado. Esse recurso fornece garantia adicional de que um cofre ou um objeto nunca pode ser excluído permanentemente até que o período de retenção tenha passado.

Você poderá habilitar a proteção de limpeza somente se a exclusão reversível também estiver habilitada. 

Para ativar a exclusão reversível e limpar a proteção ao criar um cofre, use o comando [AZ keyvault Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) :

```
az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
```

Para adicionar a proteção de limpeza a um cofre existente (que já tem a exclusão reversível habilitada), use o comando [AZ keyvault Update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) :

```
az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
```

## <a name="other-resources"></a>Outros recursos

- Para obter uma visão geral do recurso de exclusão reversível do Key Vault, consulte [visão geral da exclusão reversível Azure Key Vault](key-vault-ovw-soft-delete.md).
- Para obter uma visão geral do uso de Azure Key Vault, consulte [o que é Azure Key Vault?](key-vault-overview.md).

