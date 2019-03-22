---
title: O Azure Key Vault - como utilizar a eliminação de forma recuperável com a CLI
description: Utilize os exemplos de casos de eliminação de forma recuperável com o CLI recortes de código
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: mbaldwin
ms.openlocfilehash: aa9b89b9afec069e97236b7652e0f1d37644f5cf
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58336081"
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>Como utilizar a eliminação de forma recuperável do Key Vault com a CLI

Funcionalidade de eliminação de forma recuperável do Azure do Key Vault permite que a recuperação de cofres eliminados e os objetos de cofre. Especificamente, eliminação de forma recuperável endereços os seguintes cenários:

- Suporte para eliminação recuperável de um cofre de chaves
- Suporte para eliminação recuperável de objetos de Cofre de chaves; as chaves, segredos e, em certificados

## <a name="prerequisites"></a>Pré-requisitos

- CLI do Azure - se não tiver esta configuração para o seu ambiente, consulte [gerir com a CLI do Azure Key Vault](key-vault-manage-with-cli2.md).

Para informações de referência específica do Key Vault para a CLI, veja [referência da CLI do Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault).

## <a name="required-permissions"></a>Permissões obrigatórias

Operações do Cofre de chave separadamente geridas através de permissões de controlo (RBAC) de acesso baseado em função da seguinte forma:

| Operação | Descrição | Permissão de utilizador |
|:--|:--|:--|
|Lista|Excluídas listas de cofres de chaves.|Microsoft.KeyVault/deletedVaults/read|
|Recuperar|Restaura um cofre de chaves eliminado.|Microsoft.KeyVault/vaults/write|
|Remover|Remove permanentemente a um cofre de chaves eliminado e todos os respetivos conteúdos.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Para obter mais informações sobre o controlo de acesso e permissões, consulte [proteger o seu Cofre de chave](key-vault-secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Ativar a eliminação de forma recuperável

Ativar "eliminação de forma recuperável" permitir a recuperação de um cofre de chaves eliminado ou objetos armazenados num cofre de chaves.

> [!IMPORTANT]
> A ativação de "eliminação de forma recuperável" num cofre de chaves é uma ação irreversível. Assim que a propriedade de eliminação de forma recuperável foi definida como "true", não pode ser alterado ou removido.  

### <a name="existing-key-vault"></a>Cofre de chaves existente

Para um cofre de chaves existente com o nome ContosoVault, ative a eliminação de forma recuperável da seguinte forma. 

```azurecli
az resource update --id $(az keyvault show --name ContosoVault -o tsv | awk '{print $1}') --set properties.enableSoftDelete=true
```

### <a name="new-key-vault"></a>Novo cofre de chaves

Ativar a eliminação de forma recuperável para um novo cofre de chaves é feita no momento da criação ao adicionar o sinalizador de ativação de eliminação de forma recuperável para o comando de criação.

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>Certifique-se a ativação da eliminação de forma recuperável

Para verificar que um cofre de chaves tenha a eliminação de forma recuperável ativada, execute o *mostrar* de comandos e procure o "Soft eliminar Enabled?" atributo:

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>A eliminar uma eliminação de forma recuperável protegidos Cofre de chaves

O comando para eliminar um cofre de chaves de alterações no comportamento, dependendo se a eliminação de forma recuperável está ativada.

> [!IMPORTANT]
>Se executar o seguinte comando para um cofre de chaves que não tenha a eliminação de forma recuperável ativada, irá eliminar permanentemente este Cofre de chaves e todo o seu conteúdo sem opções para recuperação!

```azurecli
az keyvault delete --name ContosoVault
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Como a eliminação de forma recuperável protege seus cofres de chaves

Com a eliminação de forma recuperável ativada:

- Um cofre de chaves eliminado é removido do seu grupo de recursos e colocado num espaço de nomes reservado associado ao local onde foi criado. 
- Objetos eliminados como chaves, segredos e certificados, estão inacessíveis, desde que seu Cofre de chaves que contêm está no Estado eliminado. 
- O nome DNS para um cofre de chaves eliminado está reservado, impedir que um novo cofre de chaves com o mesmo nome que está sendo criado.  

Pode visualizar cofres de chaves de estado de eliminado, associados à subscrição, utilizando o seguinte comando:

```azurecli
az keyvault list-deleted
```
- *ID* pode ser utilizado para identificar o recurso quando recuperar ou remoção. 
- *ID de recurso* é o ID de recurso original deste cofre. Uma vez que este Cofre de chaves está agora num Estado eliminado, nenhum recurso existe com esse ID de recurso. 
- *Data de remoção agendada* é quando o Cofre será eliminado permanentemente, se foi efetuada nenhuma ação. O período de retenção padrão, utilizado para calcular a *data de remoção agendada*, é de 90 dias.

## <a name="recovering-a-key-vault"></a>Recuperar um cofre de chaves

Para recuperar um cofre de chaves, especifique o nome do Cofre de chaves, grupo de recursos e localização. Tenha em atenção a localização e o grupo de recursos do Cofre de chaves eliminado, sempre que precisar para o processo de recuperação.

```azurecli
az keyvault recover --location westus --resource-group ContosoRG --name ContosoVault
```

Quando um cofre de chaves é recuperado, um novo recurso é criado com original ID de recurso. o Cofre de chaves Se o grupo de recursos original for removido, deve ser criado com o mesmo nome antes de tentar a recuperação.

## <a name="deleting-and-purging-key-vault-objects"></a>A eliminar e a limpeza de objetos de Cofre de chaves

O seguinte comando irá eliminar a chave de "ContosoFirstKey", num cofre de chaves com o nome "ContosoVault', que tem a eliminação de forma recuperável ativada:

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

Com o seu Cofre de chaves ativado para eliminação de forma recuperável, uma chave eliminada ainda é apresentado como este é eliminado, exceto, quando listar ou obter chaves eliminadas explicitamente. A maioria das operações numa chave no Estado eliminado irá falhar, exceto para a listagem de uma chave de eliminado, recuperá-lo ou removê-lo. 

Por exemplo, para pedir às chaves de lista eliminada num cofre de chaves, utilize o seguinte comando:

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>Estado de transição 

Quando elimina uma chave no Cofre de chaves com eliminação de forma recuperável ativada, poderá demorar alguns segundos para a transição para concluir. Durante essa transição, que pode parecer que a chave não está no Estado ativo ou estado eliminado. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Utilizar a eliminação de forma recuperável com objetos de Cofre de chaves

Assim como os cofres de chaves, uma chave de eliminado, o segredo ou o certificado, permanece no Estado eliminado até 90 dias, a menos que recuperá-la ou removê-lo.

#### <a name="keys"></a>Chaves

Para recuperar uma chave eliminados de forma recuperável:

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

Eliminar permanentemente (também conhecido como remoção) uma chave eliminados de forma recuperável:

> [!IMPORTANT]
> Remover uma chave irá eliminá-la permanentemente e não serão recuperável! 

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

O **recuperar** e **remover** ações têm suas próprias permissões associadas a uma política de acesso do Cofre de chaves. Para um utilizador ou principal de serviço poder executar uma **recuperar** ou **remover** ação, tem de ter a respetiva permissão para essa chave ou segredo. Por predefinição, **remover** não for adicionado à política de acesso de um cofre de chaves, quando o atalho "todos" é usado para conceder todas as permissões. Tem de conceder especificamente **remover** permissão. 

#### <a name="set-a-key-vault-access-policy"></a>Definir uma política de acesso do Cofre de chaves

O seguinte comando concede user@contoso.com permissão para usar diversas operações de chaves numa *ContosoVault* incluindo **remover**:

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> Se tiver um cofre de chaves que apenas tenha tido a eliminação de forma recuperável ativada, pode não ter **recuperar** e **remover** permissões.

#### <a name="secrets"></a>Segredos

Como chaves, segredos geridos com seus próprios comandos:

- Elimine um segredo designado SQLPassword: 
  ```azurecli
  az keyvault secret delete --vault-name ContosoVault -name SQLPassword
  ```

- Liste eliminados todos os segredos num cofre de chaves: 
  ```azurecli
  az keyvault secret list-deleted --vault-name ContosoVault
  ```

- Recupere um segredo no Estado eliminado: 
  ```azurecli
  az keyvault secret recover --name SQLPassword --vault-name ContosoVault
  ```

- Remover um segredo no estado de eliminado: 

  > [!IMPORTANT]
  > Remover um segredo irá eliminá-la permanentemente e não serão recuperável! 

  ```azurecli
  az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
  ```

## <a name="purging-a-soft-delete-protected-key-vault"></a>Remover uma eliminação de forma recuperável protegidos Cofre de chaves

> [!IMPORTANT]
> Remover um cofre de chaves ou um dos respetivos objetos contidos, eliminará permanentemente, que significa que não serão recuperável!

A função de remoção é utilizada para eliminar permanentemente um objeto do Cofre de chaves ou de um cofre de chaves inteiro, que anteriormente era eliminados de forma recuperável. Como demonstrado na secção anterior, os objetos armazenados num cofre de chaves com a funcionalidade de eliminação de forma recuperável ativada, pode passar por vários Estados:

- **Active Directory**: antes da eliminação.
- **Eliminados de forma recuperável**: após a eliminação, capaz de ser listados e recuperados voltar ao Estado ativo.
- **Eliminado permanentemente**: após a remoção, não é possível de ser recuperados.

O mesmo é válido para o Cofre de chaves. Para eliminar permanentemente um cofre de chaves eliminado de forma recuperável e o respetivo conteúdo, tem de remover o próprio Cofre de chaves.

### <a name="purging-a-key-vault"></a>Remover um cofre de chaves

Quando um cofre de chaves é removido, todo o conteúdo é permanentemente eliminado, incluindo chaves, segredos e certificados. Para remover um cofre de chaves eliminado de forma recuperável, utilize o `az keyvault purge` comando. Pode encontrar a localização cofres de chaves eliminados da sua subscrição utilizando o comando `az keyvault list-deleted`.

```azurecli
az keyvault purge --location westus --name ContosoVault
```

### <a name="purge-permissions-required"></a>Remover as permissões necessárias
- Para remover um cofre de chaves eliminado, o utilizador necessita da permissão RBAC para o *Microsoft.KeyVault/locations/deletedVaults/purge/action* operação. 
- Para listar um cofre de chaves eliminado, o utilizador necessita da permissão RBAC para o *Microsoft.KeyVault/deletedVaults/read* operação. 
- Por predefinição, apenas um administrador de subscrição tem estas permissões. 

### <a name="scheduled-purge"></a>Remoção agendada

A listagem de objetos de Cofre de chaves eliminado também mostra quando eles estão agendados para ser limpos pelo Cofre de chaves. *Data de remoção agendada* indica quando um objeto do Cofre de chaves serão permanentemente eliminado, se foi efetuada nenhuma ação. Por predefinição, o período de retenção para um objeto do Cofre de chaves eliminado é de 90 dias.

>[!IMPORTANT]
>Um objeto de cofre eliminados, acionado por seus *data de remoção agendada* campo, é eliminado permanentemente. Não é recuperável!

## <a name="enabling-purge-protection"></a>Ativar a proteção contra remoção

Quando a proteção contra remoção está ativada, um cofre ou num objeto eliminada não é possível limpar estado até que tenha passado o período de retenção de 90 dias. Esse cofre ou o objeto ainda pode ser recuperado. Esse recurso oferece maior garantia que um cofre ou um objeto nunca pode ser permanentemente eliminados até que o período de retenção período tenha passado.

Pode ativar a proteção contra remoção apenas se a eliminação de forma recuperável também está ativada. 

Para ativar a ambos os eliminação de forma recuperável e remover a proteção ao criar um cofre, utilize o [az keyvault criar](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) comando:

```
az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
```

Adicionar proteção contra remoção para um cofre existente (que já tenha a eliminação de forma recuperável ativada), utilize o [atualização do Cofre de chaves de az](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) comando:

```
az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
```

## <a name="other-resources"></a>Outros recursos

- Para uma descrição geral da funcionalidade de eliminação de forma recuperável do Key Vault, consulte [descrição geral da eliminação de forma recuperável do Azure Key Vault](key-vault-ovw-soft-delete.md).
- Para obter uma visão geral da utilização do Azure Key Vault, consulte [o que é o Azure Key Vault?](key-vault-overview.md).

