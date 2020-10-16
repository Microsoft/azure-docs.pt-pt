---
title: Cofre de chaves Azure - Como usar soft-delete com CLI
description: Aprenda a usar o Azure CLI para utilizar a funcionalidade de eliminação suave do Cofre da Chave Azure que permite a recuperação de cofres-chave e objetos-chave do cofre.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/11/2020
ms.author: sudbalas
ms.openlocfilehash: da821da08594180b9dd94728252e1a43c04fbde2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531666"
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>Como utilizar a eliminação de forma recuperável do Key Vault com a CLI

A funcionalidade de eliminação suave do Azure Key Vault permite a recuperação de cofres apagados e objetos de abóbada. Especificamente, os endereços soft-delete são os seguintes cenários:

- Suporte para a supressão recuperável de um cofre chave
- Suporte para eliminação recuperável de objectos-chave do cofre; chaves, segredos e, certificados

## <a name="prerequisites"></a>Pré-requisitos

- Azure CLI - Se não tiver esta configuração para o seu ambiente, consulte [Manage Key Vault utilizando O Azure CLI](manage-with-cli2.md).

Para obter informações específicas de referência do Cofre de Chaves para o CLI, consulte [a referência do Cofre da Chave CLI Azure](https://docs.microsoft.com/cli/azure/keyvault).

## <a name="required-permissions"></a>Permissões obrigatórias

As operações key Vault são geridas separadamente através de permissões de controlo de acesso baseado em funções (RBAC) da seguinte forma:

| Operação | Descrição | Permissão do utilizador |
|:--|:--|:--|
|Lista|Listas eliminadas cofres-chave.|Microsoft.KeyVault/deletedVaults/read|
|Recuperar|Restaura um cofre de chaves apagado.|Microsoft.KeyVault/vaults/write|
|Remover|Remove permanentemente um cofre de chave apagado e todo o seu conteúdo.|Microsoft.KeyVault/locations/deletedVaults/purga/ação|

Para obter mais informações sobre permissões e controlo de acesso, consulte [Secure your key vault](secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Permitir a eliminação suave

Ativa a "eliminação suave" para permitir a recuperação de um cofre de teclas apagado ou objetos armazenados num cofre de chaves.

> [!IMPORTANT]
> Permitir a "eliminação suave" num cofre de chaves é uma ação irreversível. Uma vez definida a propriedade soft-delete para "verdadeiro", não pode ser alterada ou removida.  

### <a name="existing-key-vault"></a>Cofre de chaves existente

Para um cofre de chaves existente chamado ContosoVault, ative a eliminação suave da seguinte forma. 

```azurecli
az keyvault update -n ContosoVault --enable-soft-delete true
```

### <a name="new-key-vault"></a>Novo cofre-chave

A eliminação suave é automaticamente ativada em todos os cofres-chave por defeito. A partir de 31 de dezembro de 2020, deixará de ser possível criar um novo cofre-chave sem fácil eliminação.

### <a name="verify-soft-delete-enablement"></a>Verifique o habilitação para eliminar o soft-delete

Para verificar se um cofre de chaves tem a eliminação suave ativada, executar o comando do *show* e procurar o 'Soft Delete Enabled?' atributo:

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Apagar um cofre de chaves protegido de eliminação suave

O comando para eliminar uma chave de mudanças de comportamento no cofre, dependendo se a eliminação suave está ativada.

> [!IMPORTANT]
>Se executar o seguinte comando para um cofre de chave que não tenha soft-delete ativado, irá eliminar permanentemente este cofre de teclas e todo o seu conteúdo sem opções de recuperação!

```azurecli
az keyvault delete --name ContosoVault
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Como a eliminação suave protege os seus cofres chave

Com eliminação suave ativada:

- Um cofre de chaves eliminado é removido do seu grupo de recursos e colocado num espaço de nome reservado, associado ao local onde foi criado. 
- Objetos eliminados, tais como chaves, segredos e certificados, são inacessíveis desde que o cofre de chaves que contém esteja no estado apagado. 
- O nome DNS para um cofre de chaves apagado é reservado, impedindo a criação de um novo cofre-chave com o mesmo nome.  

Pode visualizar cofres-chave de estado apagados, associados à sua subscrição, utilizando o seguinte comando:

```azurecli
az keyvault list-deleted
```
- *O ID* pode ser usado para identificar o recurso quando se recupera ou purga. 
- *O ID de recursos* é a identificação original deste cofre. Uma vez que este cofre chave está agora em um estado apagado, nenhum recurso existe com esse ID de recurso. 
- *A data de purga programada* é quando o cofre será permanentemente apagado, se não forem tomadas medidas. O período de retenção predefinido, utilizado para calcular a *Data de Purga Programada,* é de 90 dias.

## <a name="recovering-a-key-vault"></a>Recuperação de um cofre chave

Para recuperar um cofre chave, especifique o nome do cofre, o grupo de recursos e a localização. Observe a localização e o grupo de recursos do cofre de chaves apagado, pois necessita deles para o processo de recuperação.

```azurecli
az keyvault recover --location westus --resource-group ContosoRG --name ContosoVault
```

Quando um cofre chave é recuperado, um novo recurso é criado com o ID original do cofre. Se o grupo de recursos originais for removido, deve-se ser criado com o mesmo nome antes de tentar a recuperação.

## <a name="deleting-and-purging-key-vault-objects"></a>Apagar e purgar objetos de cofre

O seguinte comando eliminará a tecla 'ContosoFirstKey', num cofre-chave denominado 'ContosoVault', que tem soft-delete ativado:

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

Com o cofre da chave ativado para eliminar suavemente, uma chave apagada ainda aparece como se fosse eliminada, exceto quando lista explicitamente ou recupera as teclas apagadas. A maioria das operações numa chave no estado eliminado falhará, exceto para listar uma chave eliminada, recuperá-la ou purirá-la. 

Por exemplo, para solicitar a lista de chaves apagadas num cofre de chaves, utilize o seguinte comando:

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>Estado de transição 

Quando apagar uma chave num cofre com exclusão suave ativada, pode demorar alguns segundos até que a transição esteja concluída. Durante esta transição, pode parecer que a chave não está no estado ativo ou no estado eliminado. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Usando soft-delete com objetos de abóbada chave

Assim como cofres-chave, uma chave, segredo ou certificado apagado, permanece em estado de eliminação por até 90 dias, a menos que o recupere ou purgue.

#### <a name="keys"></a>Chaves

Para recuperar uma chave de apagação suave:

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

Para eliminar permanentemente (também conhecida como purga) uma chave de eliminação suave:

> [!IMPORTANT]
> A purga de uma chave irá eliminá-la permanentemente, e não será recuperável! 

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

As ações **de recuperação** e **purga** têm as suas próprias permissões associadas a uma política de acesso ao cofre chave. Para que um utilizador ou diretor de serviço possa executar uma ação **de recuperação** ou **purga,** deve ter a respetiva permissão para essa chave ou segredo. Por defeito, **a purga** não é adicionada à política de acesso de um cofre chave, quando o atalho "tudo" é usado para conceder todas as permissões. Deve conceder especificamente permissão **de purga.** 

#### <a name="set-a-key-vault-access-policy"></a>Definir uma política de acesso ao cofre chave

O comando seguinte autoriza user@contoso.com a utilização de várias operações em chaves em *ContosoVault,* incluindo **a purga:**

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> Se tiver um cofre de chaves existente que acaba de ter a eliminação suave ativada, pode não ter permissões de **recuperação** e **purga.**

#### <a name="secrets"></a>Segredos

Tal como as chaves, os segredos são geridos com os seus próprios comandos:

- Excluir um segredo chamado SQLPassword: 
  ```azurecli
  az keyvault secret delete --vault-name ContosoVault -name SQLPassword
  ```

- Lista todos os segredos apagados num cofre chave: 
  ```azurecli
  az keyvault secret list-deleted --vault-name ContosoVault
  ```

- Recuperar um segredo no estado apagado: 
  ```azurecli
  az keyvault secret recover --name SQLPassword --vault-name ContosoVault
  ```

- Purgue um segredo em estado apagado: 

  > [!IMPORTANT]
  > Purgar um segredo irá apagá-lo permanentemente, e não será recuperável! 

  ```azurecli
  az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
  ```

## <a name="purging-a-soft-delete-protected-key-vault"></a>Purgar um cofre de chaves protegido de eliminação suave

> [!IMPORTANT]
> Purgar um cofre-chave ou um dos seus objetos contidos, irá apagá-lo permanentemente, o que significa que não será recuperável!

A função de purga é usada para eliminar permanentemente um objeto de cofre de chave ou um cofre de chaves inteiro, que foi previamente eliminado. Como demonstrado na secção anterior, os objetos armazenados num cofre com a função soft-delete ativados podem passar por vários estados:

- **Ativo:** antes da eliminação.
- **Soft-Deleted**: após a eliminação, podendo ser listado e recuperado de volta ao estado ativo.
- **Permanentemente eliminado:** após purga, não podendo ser recuperado.

O mesmo acontece com o cofre das chaves. Para eliminar permanentemente um cofre de chave apagado e o seu conteúdo, deve purgar o cofre da chave em si.

### <a name="purging-a-key-vault"></a>Purgar um cofre chave

Quando um cofre-chave é purgado, todo o seu conteúdo é permanentemente eliminado, incluindo chaves, segredos e certificados. Para purgar um cofre de chave apagado, use o `az keyvault purge` comando. Pode encontrar o local onde a sua subscrição é eliminada com cofres-chave usando o comando `az keyvault list-deleted` .

```azurecli
az keyvault purge --location westus --name ContosoVault
```

### <a name="purge-permissions-required"></a>Permissões de purga necessárias
- Para limpar um cofre de chaves eliminado, o utilizador precisa de permissão RBAC para a *operação Microsoft.KeyVault/locations/deletedVaults/purga/ação.* 
- Para listar um cofre de chaves eliminado, o utilizador precisa de permissão RBAC para a operação *Microsoft.KeyVault/deletedVaults/read.* 
- Por predefinição, apenas um administrador de subscrição tem estas permissões. 

### <a name="scheduled-purge"></a>Purga programada

A listagem de objetos de cofre apagados também mostra quando estão programados para serem purgados pelo Key Vault. *A data de purga programada* indica quando um objeto do cofre chave será permanentemente eliminado, se não forem tomadas medidas. Por predefinição, o período de retenção para um objeto de abóbada de chave eliminado é de 90 dias.

>[!IMPORTANT]
>Um objeto de abóbada purga purgado, desencadeado pelo seu campo *de data de purga programada,* é permanentemente eliminado. Não é recuperável!

## <a name="enabling-purge-protection"></a>Permitir a proteção da purga

Quando a proteção contra a purga é ligada, um cofre ou um objeto em estado apagado não podem ser purgados até que o período de retenção de 90 dias tenha passado. Tal cofre ou objeto ainda pode ser recuperado. Esta funcionalidade garante que um cofre ou um objeto nunca poderão ser permanentemente eliminados até que o período de retenção tenha passado.

Só pode ativar a proteção contra a purga se também estiver ativada a eliminação suave. A proteção de purga incapacitante não é suportada.

Para ligar a proteção de eliminação e purga suave ao criar um cofre, utilize o comando [de criação de chave-teclas az:](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
```

Para adicionar proteção de purga a um cofre existente (que já tem soft-delete ativado), utilize o comando [de atualização az keyvault:](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)

```azurecli
az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
```

## <a name="other-resources"></a>Outros recursos

- Para obter uma visão geral da função de eliminação suave do Key Vault, consulte [a visão geral do Azure Key Vault .](soft-delete-overview.md)
- Para uma visão geral da utilização do Azure Key Vault, veja [o que é o Cofre da Chave Azure?](overview.md)

