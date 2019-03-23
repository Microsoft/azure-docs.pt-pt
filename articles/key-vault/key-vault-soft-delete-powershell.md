---
title: O Azure Key Vault - como utilizar a eliminação de forma recuperável com o PowerShell
description: Utilize os exemplos de casos de eliminação de forma recuperável com recortes de código do PowerShell
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: mbaldwin
ms.openlocfilehash: ecc87e03a80ce10bedbe26b3ebb452ec704eefcb
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368695"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>Como utilizar a eliminação de forma recuperável do Key Vault com o PowerShell

Funcionalidade de eliminação de forma recuperável do Azure do Key Vault permite que a recuperação de cofres eliminados e os objetos de cofre. Especificamente, eliminação de forma recuperável endereços os seguintes cenários:

- Suporte para eliminação recuperável de um cofre de chaves
- Suporte para eliminação recuperável de objetos de Cofre de chaves; as chaves, segredos e, em certificados

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- O Azure PowerShell 1.0.0 ou superior - se não tiver já neste programa de configuração, instalar o Azure PowerShell e associá-lo a sua subscrição do Azure, veja [como instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). 

>[!NOTE]
> Existe uma versão desatualizada do nosso formatação de saída do PowerShell do Cofre de chaves de ficheiro que **poderá** ser carregados para o seu ambiente em vez da versão correta. Nós são prevendo uma versão atualizada do PowerShell para conter a correção necessária para a formatação de saída e atualizaremos este tópico nesse momento. A solução atual, caso encontre esse problema de formatação, é:
> - Utilize a seguinte consulta se notar que não está a ver a eliminação de forma recuperável ativada propriedade descrita neste tópico: `$vault = Get-AzKeyVault -VaultName myvault; $vault.EnableSoftDelete`.


Para informações de referência específica do Key Vault para o PowerShell, consulte [referência do PowerShell do Azure Key Vault](/powershell/module/az.keyvault).

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

### <a name="existing-key-vault"></a>Cofre de chaves existente

Para um cofre de chaves existente com o nome ContosoVault, ative a eliminação de forma recuperável da seguinte forma. 

```powershell
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Novo cofre de chaves

Ativar a eliminação de forma recuperável para um novo cofre de chaves é feita no momento da criação ao adicionar o sinalizador de ativação de eliminação de forma recuperável para o comando de criação.

```powershell
New-AzKeyVault -Name "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>Certifique-se a ativação da eliminação de forma recuperável

Para verificar que um cofre de chaves tenha a eliminação de forma recuperável ativada, execute o *mostrar* de comandos e procure o "Soft eliminar Enabled?" atributo:

```powershell
Get-AzKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>A eliminar uma eliminação de forma recuperável protegidos Cofre de chaves

O comando para eliminar um cofre de chaves de alterações no comportamento, dependendo se a eliminação de forma recuperável está ativada.

> [!IMPORTANT]
>Se executar o seguinte comando para um cofre de chaves que não tenha a eliminação de forma recuperável ativada, irá eliminar permanentemente este Cofre de chaves e todo o seu conteúdo sem opções para recuperação!

```powershell
Remove-AzKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Como a eliminação de forma recuperável protege seus cofres de chaves

Com a eliminação de forma recuperável ativada:

- Um cofre de chaves eliminado é removido do seu grupo de recursos e colocado num espaço de nomes reservado associado ao local onde foi criado. 
- Objetos eliminados como chaves, segredos e certificados, estão inacessíveis, desde que seu Cofre de chaves que contêm está no Estado eliminado. 
- O nome DNS para um cofre de chaves eliminado está reservado, impedir que um novo cofre de chaves com o mesmo nome que está sendo criado.  

Pode visualizar cofres de chaves de estado de eliminado, associados à subscrição, utilizando o seguinte comando:

```powershell
Get-AzKeyVault -InRemovedState 
```

- *ID* pode ser utilizado para identificar o recurso quando recuperar ou remoção. 
- *ID de recurso* é o ID de recurso original deste cofre. Uma vez que este Cofre de chaves está agora num Estado eliminado, nenhum recurso existe com esse ID de recurso. 
- *Data de remoção agendada* é quando o Cofre será eliminado permanentemente, se foi efetuada nenhuma ação. O período de retenção padrão, utilizado para calcular a *data de remoção agendada*, é de 90 dias.

## <a name="recovering-a-key-vault"></a>Recuperar um cofre de chaves

Para recuperar um cofre de chaves, especifique o nome do Cofre de chaves, grupo de recursos e localização. Tenha em atenção a localização e o grupo de recursos do Cofre de chaves eliminado, sempre que precisar para o processo de recuperação.

```powershell
Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

Quando um cofre de chaves é recuperado, um novo recurso é criado com original ID de recurso. o Cofre de chaves Se o grupo de recursos original for removido, deve ser criado com o mesmo nome antes de tentar a recuperação.

## <a name="deleting-and-purging-key-vault-objects"></a>A eliminar e a limpeza de objetos de Cofre de chaves

O seguinte comando irá eliminar a chave de "ContosoFirstKey", num cofre de chaves com o nome "ContosoVault', que tem a eliminação de forma recuperável ativada:

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

Com o seu Cofre de chaves ativado para eliminação de forma recuperável, uma chave eliminada ainda parece ser eliminadas, a menos que liste explicitamente chaves eliminadas. A maioria das operações numa chave no Estado eliminado falhará, exceto para listagem, recuperar, remover uma chave foi eliminada. 

Por exemplo, o comando seguinte apresenta uma lista de chaves eliminadas no Cofre de chaves de "ContosoVault":

```powershell
Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Estado de transição 

Quando elimina uma chave no Cofre de chaves com eliminação de forma recuperável ativada, poderá demorar alguns segundos para a transição para concluir. Durante essa transição, que pode parecer que a chave não está no Estado ativo ou estado eliminado. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Utilizar a eliminação de forma recuperável com objetos de Cofre de chaves

Assim como os cofres de chaves, uma chave de eliminado, o segredo ou o certificado, permanece no Estado eliminado até 90 dias, a menos que recuperá-la ou removê-lo. 

#### <a name="keys"></a>Chaves

Para recuperar uma chave eliminados de forma recuperável:

```powershell
Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

Eliminar permanentemente (também conhecido como remoção) uma chave eliminados de forma recuperável:

> [!IMPORTANT]
> Remover uma chave irá eliminá-la permanentemente e não serão recuperável! 

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

O **recuperar** e **remover** ações têm suas próprias permissões associadas a uma política de acesso do Cofre de chaves. Para um utilizador ou principal de serviço poder executar uma **recuperar** ou **remover** ação, tem de ter a respetiva permissão para essa chave ou segredo. Por predefinição, **remover** não for adicionado à política de acesso de um cofre de chaves, quando o atalho "todos" é usado para conceder todas as permissões. Tem de conceder especificamente **remover** permissão. 

#### <a name="set-a-key-vault-access-policy"></a>Definir uma política de acesso do Cofre de chaves

O seguinte comando concede user@contoso.com permissão para usar diversas operações de chaves numa *ContosoVault* incluindo **remover**:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Se tiver um cofre de chaves que apenas tenha tido a eliminação de forma recuperável ativada, pode não ter **recuperar** e **remover** permissões.

#### <a name="secrets"></a>Segredos

Como chaves, segredos geridos com seus próprios comandos:

- Elimine um segredo designado SQLPassword: 
  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

- Liste eliminados todos os segredos num cofre de chaves: 
  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

- Recupere um segredo no Estado eliminado: 
  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

- Remover um segredo no estado de eliminado: 

  > [!IMPORTANT]
  > Remover um segredo irá eliminá-la permanentemente e não serão recuperável!

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
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

Quando um cofre de chaves é removido, todo o conteúdo é permanentemente eliminado, incluindo chaves, segredos e certificados. Para remover um cofre de chaves eliminado de forma recuperável, utilize o `Remove-AzKeyVault` comando com a opção `-InRemovedState` e ao especificar a localização do Cofre de chaves eliminado com o `-Location location` argumento. Pode encontrar a localização de um cofre eliminado com o comando `Get-AzKeyVault -InRemovedState`.

```powershell
Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
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

Para ativar a ambos os eliminação de forma recuperável e remover a proteção ao criar um cofre, utilize o [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault?view=azps-1.5.0) cmdlet:

```powershell
New-AzKeyVault -Name ContosoVault -ResourceGroupName ContosoRG -Location westus -EnableSoftDelete -EnablePurgeProtection
```

Adicionar proteção contra remoção para um cofre existente (que já tenha a eliminação de forma recuperável ativada), utilize o [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault?view=azps-1.5.0), [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-1.5.0), e [Set-AzResource](/powershell/module/az.resources/set-azresource?view=azps-1.5.0) cmdlets:

```
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

## <a name="other-resources"></a>Outros recursos

- Para uma descrição geral da funcionalidade de eliminação de forma recuperável do Key Vault, consulte [descrição geral da eliminação de forma recuperável do Azure Key Vault](key-vault-ovw-soft-delete.md).
- Para obter uma visão geral da utilização do Azure Key Vault, consulte [o que é o Azure Key Vault?](key-vault-overview.md). ar = Succeeded}