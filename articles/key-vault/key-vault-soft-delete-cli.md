---
title: Cofre de Chaves Azure - Como usar soft delete com CLI
description: Utilize exemplos de casos de soft-delete com snips de código CLI
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 940de5e100da934e0bc4efdfc6686f8040e10954
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79457326"
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>Como utilizar a eliminação de forma recuperável do Key Vault com a CLI

A função de eliminação suave do Cofre de Chaves Azure permite a recuperação de cofres apagados e objetos de cofre. Especificamente, a eliminação suave aborda os seguintes cenários:

- Suporte para eliminação recuperável de um cofre chave
- Suporte para eliminação recuperável de objetos chave do cofre; chaves, segredos e.certificados

## <a name="prerequisites"></a>Pré-requisitos

- Azure CLI - Se não tiver esta configuração para o seu ambiente, consulte Gerir o [Cofre da Chave utilizando o Azure CLI](key-vault-manage-with-cli2.md).

Para obter informações específicas de referência para o Cofre chave para o CLI, consulte a referência do [Cofre chave Azure CLI](https://docs.microsoft.com/cli/azure/keyvault).

## <a name="required-permissions"></a>Permissões obrigatórias

As operações do Key Vault são geridas separadamente através de permissões de controlo de acesso baseadas em funções (RBAC) da seguinte forma:

| Operação | Descrição | Permissão do utilizador |
|:--|:--|:--|
|Lista|Listas apagadas cofres chave.|Microsoft.KeyVault/deletedVaults/read|
|Recuperar|Restaura um cofre de chaves apagado.|Microsoft.KeyVault/vaults/write|
|Remover|Remove permanentemente um cofre de chaves apagado e todo o seu conteúdo.|Microsoft.KeyVault/locations/deletedVaults/purga/ação|

Para obter mais informações sobre permissões e controlo de acesso, consulte [Proteja o seu cofre de chaves](key-vault-secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Habilitar a eliminar suavemente

Ativa o "soft-delete" para permitir a recuperação de um cofre de chave apagado ou objetos armazenados num cofre de chaves.

> [!IMPORTANT]
> Permitir a "eliminação suave" num cofre chave é uma ação irreversível. Uma vez que a propriedade de eliminação suave tenha sido definida como "verdadeira", não pode ser alterada ou removida.  

### <a name="existing-key-vault"></a>Cofre-chave existente

Para um cofre de chaves existente chamado ContosoVault, ative a eliminação suave da seguinte forma. 

```azurecli
az keyvault update -n ContosoVault --enable-soft-delete true
```

### <a name="new-key-vault"></a>Novo cofre-chave

Permitir a eliminação suave de um novo cofre chave é feito no momento da criação, adicionando a bandeira de eliminação suave ao seu comando de criação.

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>Verifique a ativação de eliminação suave

Para verificar se um cofre de chaves tem soft-delete ativado, execute o comando do *show* e procure o 'Soft Delete Enabled?' atributo:

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Eliminação de um cofre de chaves protegido de eliminação suave

O comando para eliminar uma chave do cofre muda de comportamento, dependendo se o soft-delete está ativado.

> [!IMPORTANT]
>Se executar o seguinte comando para um cofre chave que não tenha soft-delete ativado, eliminará permanentemente este cofre chave e todo o seu conteúdo sem opções de recuperação!

```azurecli
az keyvault delete --name ContosoVault
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Como a eliminação suave protege os seus cofres chave

Com eliminação suave ativada:

- Um cofre de chave eliminado é removido do seu grupo de recursos e colocado num espaço de nome reservado, associado ao local onde foi criado. 
- Objetos apagados, tais como chaves, segredos e certificados, são inacessíveis desde que o cofre da chave que contenha esteja no estado apagado. 
- O nome DNS para um cofre de chave apagado está reservado, impedindo que um novo cofre com o mesmo nome seja criado.  

Pode visualizar cofres de chaves de estado eliminados, associados à sua subscrição, utilizando o seguinte comando:

```azurecli
az keyvault list-deleted
```
- *O ID* pode ser usado para identificar o recurso quando se recupera ou purga. 
- *A identificação* de recursos é a identificação original deste cofre. Uma vez que este cofre chave está agora num estado apagado, não existe nenhum recurso com o ID do recurso. 
- *Data de purga programada* é quando o cofre será permanentemente apagado, se não forem tomadas medidas. O período de retenção predefinido, utilizado para calcular a Data de *Purga Programada,* é de 90 dias.

## <a name="recovering-a-key-vault"></a>Recuperando um cofre chave

Para recuperar um cofre chave, especifique o nome do cofre chave, o grupo de recursos e a localização. Note a localização e o grupo de recursos do cofre de chaves apagados, pois precisa deles para o processo de recuperação.

```azurecli
az keyvault recover --location westus --resource-group ContosoRG --name ContosoVault
```

Quando um cofre chave é recuperado, um novo recurso é criado com o ID original do cofre chave. Se o grupo de recursos original for removido, deve ser criado com o mesmo nome antes de tentar a recuperação.

## <a name="deleting-and-purging-key-vault-objects"></a>Apagar e purgar objetos chave do cofre

O seguinte comando eliminará a tecla 'ContosoFirstKey', num cofre de chaves chamado 'ContosoVault', que tem soft-delete ativado:

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

Com o cofre da chave ativado para apagar suavemente, uma tecla apagada ainda aparece como se fosse eliminada exceto, quando lista ou recupera explicitamente as teclas apagadas. A maioria das operações numa chave no estado eliminado falhará, exceto para a listagem de uma chave eliminada, recuperando-a ou purgando-a. 

Por exemplo, para solicitar a lista de chaves apagadas num cofre de chaves, utilize o seguinte comando:

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>Estado de transição 

Quando eliminar uma chave num cofre com eliminação suave ativada, pode demorar alguns segundos para que a transição esteja concluída. Durante esta transição, pode parecer que a chave não está no estado ativo ou no estado apagado. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Utilização soft-delete com objetos de cofre chave

Tal como os cofres chave, uma chave apagada, segredo ou certificado, permanece em estado apagado por até 90 dias, a menos que recupere ou purgue.

#### <a name="keys"></a>Chaves

Para recuperar uma chave suavemente apagada:

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

Para eliminar permanentemente (também conhecido como purgar) uma chave suavemente eliminada:

> [!IMPORTANT]
> A purga de uma chave irá apagá-la permanentemente, e não será recuperável! 

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

As ações de **recuperação** e **purga** têm as suas próprias permissões associadas a uma política chave de acesso ao cofre. Para que um utilizador ou um diretor de serviço possa executar uma ação de **recuperação** ou **purga,** devem ter a respetiva permissão para essa chave ou segredo. Por padrão, a **purga** não é adicionada à política de acesso de um cofre chave, quando o atalho "all" é usado para conceder todas as permissões. Deve conceder especificamente permissão de **purga.** 

#### <a name="set-a-key-vault-access-policy"></a>Desestabeleça uma política de acesso ao cofre chave

O seguinte user@contoso.com comando concede permissão para a utilização de várias operações em chaves em *ContosoVault,* incluindo **a purga:**

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> Se tiver um cofre chave existente que acaba de ter uma eliminação suave ativada, pode não ter **permissões de recuperação** e **purga.**

#### <a name="secrets"></a>Segredos

Como chaves, os segredos são geridos com os seus próprios comandos:

- Elimine um segredo chamado SQLPassword: 
  ```azurecli
  az keyvault secret delete --vault-name ContosoVault -name SQLPassword
  ```

- Liste todos os segredos apagados num cofre chave: 
  ```azurecli
  az keyvault secret list-deleted --vault-name ContosoVault
  ```

- Recuperar um segredo no estado apagado: 
  ```azurecli
  az keyvault secret recover --name SQLPassword --vault-name ContosoVault
  ```

- Expurgar um segredo em estado apagado: 

  > [!IMPORTANT]
  > Purgar um segredo irá apagá-lo permanentemente, e não será recuperável! 

  ```azurecli
  az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
  ```

## <a name="purging-a-soft-delete-protected-key-vault"></a>Purgar um cofre de chave protegido de eliminação suave

> [!IMPORTANT]
> Purgar um cofre chave ou um dos seus objetos contidos, irá apagá-lo permanentemente, o que significa que não será recuperável!

A função de purga é usada para eliminar permanentemente um objeto de cofre chave ou um cofre completo, que foi previamente apagado. Como demonstrado na secção anterior, os objetos armazenados num cofre chave com a função soft-delete ativada podem passar por vários estados:

- **Ativo:** antes da eliminação.
- **Soft-Deleted**: após eliminação, capaz de ser listado e recuperado de volta ao estado ativo.
- **Eliminado permanentemente:** após purga, não pode ser recuperado.

O mesmo acontece com o cofre da chave. Para eliminar permanentemente um cofre de chaves apagado e o seu conteúdo, deve expurgar o cofre chave em si.

### <a name="purging-a-key-vault"></a>Purgando um cofre chave

Quando um cofre chave é purgado, todo o seu conteúdo é permanentemente apagado, incluindo chaves, segredos e certificados. Para purgar um cofre de `az keyvault purge` chaves apagado suavemente, use o comando. Pode encontrar a localização dos cofres de chaves `az keyvault list-deleted`apagados da sua subscrição utilizando o comando .

```azurecli
az keyvault purge --location westus --name ContosoVault
```

### <a name="purge-permissions-required"></a>Purgar permissões necessárias
- Para expurgar um cofre de chaves eliminado, o utilizador necessita de permissão RBAC para o *Microsoft.KeyVault/locations/deletedVaults/purge/action* operation. 
- Para listar um cofre de chaves eliminado, o utilizador necessita de permissão RBAC para a operação *Microsoft.KeyVault/deletedVaults/read.* 
- Por predefinição apenas um administrador de subscrição tem estas permissões. 

### <a name="scheduled-purge"></a>Purga programada

A listagem de objetos de cofre apagados também mostra quando estão programados para serem purgados pelo Cofre chave. Data de *purga programada* indica quando um objeto de cofre chave será permanentemente eliminado, se não forem tomadas medidas. Por padrão, o período de retenção para um objeto de cofre de chave eliminado é de 90 dias.

>[!IMPORTANT]
>Um objeto de abóbada purgado, desencadeado pelo seu campo de data de *purga programada,* é permanentemente eliminado. Não é recuperável!

## <a name="enabling-purge-protection"></a>Habilitar a proteção da purga

Quando a proteção da purga é ligada, um cofre ou um objeto em estado apagado não podem ser purgados até que o período de retenção de 90 dias tenha passado. Tal cofre ou objeto ainda pode ser recuperado. Esta funcionalidade dá garantias adicionais de que um cofre ou um objeto nunca podem ser eliminados permanentemente até que o período de retenção tenha passado.

Só é possível ativar a proteção da purga se estiver ativada uma eliminação suave. 

Para ligar a proteção de eliminação suave e purga quando criar um cofre, use o [cofre az criar](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) comando:

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
```

Para adicionar proteção de purga a um cofre existente (que já tem soft delete ativado), utilize o comando de [atualização az keyvault:](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)

```azurecli
az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
```

## <a name="other-resources"></a>Outros recursos

- Para uma visão geral da função de eliminação suave do Key Vault, consulte a visão geral do [Cofre de Chaves Azure](key-vault-ovw-soft-delete.md).
- Para uma visão geral do uso do Cofre chave Azure, veja [o que é o Cofre chave Azure?](key-vault-overview.md)

