---
ms.assetid: ''
title: O Azure Key Vault - como utilizar a eliminação de forma recuperável com o PowerShell
description: Utilize os exemplos de casos de eliminação de forma recuperável com recortes de código do PowerShell
services: key-vault
author: bryanla
manager: mbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.workload: identity
ms.date: 10/16/2018
ms.author: bryanla
ms.openlocfilehash: 99f81e14ca631eccee154a5658bf717cbe07b3da
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364375"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>Como utilizar a eliminação de forma recuperável do Key Vault com o PowerShell

Funcionalidade de eliminação de forma recuperável do Azure do Key Vault permite que a recuperação de cofres eliminados e os objetos de cofre. Especificamente, eliminação de forma recuperável endereços os seguintes cenários:

- Suporte para eliminação recuperável de um cofre de chaves
- Suporte para eliminação recuperável de objetos de Cofre de chaves; as chaves, segredos e, em certificados

## <a name="prerequisites"></a>Pré-requisitos

- O Azure PowerShell 4.0.0 ou superior - se não tiver já neste programa de configuração, instalar o Azure PowerShell e associá-lo a sua subscrição do Azure, veja [como instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). 

>[!NOTE]
> Existe uma versão desatualizada do nosso formatação de saída do PowerShell do Cofre de chaves de ficheiro que **poderá** ser carregados para o seu ambiente em vez da versão correta. Nós são prevendo uma versão atualizada do PowerShell para conter a correção necessária para a formatação de saída e atualizaremos este tópico nesse momento. A solução atual, caso encontre esse problema de formatação, é:
> - Utilize a seguinte consulta se notar que não está a ver a eliminação de forma recuperável ativada propriedade descrita neste tópico: `$vault = Get-AzureRmKeyVault -VaultName myvault; $vault.EnableSoftDelete`.


Para informações de refernece específico do Cofre de chaves para o PowerShell, consulte [referência do PowerShell do Azure Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-4.2.0).

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

```powershell
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzureRmResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Novo cofre de chaves

Ativar a eliminação de forma recuperável para um novo cofre de chaves é feita no momento da criação ao adicionar o sinalizador de ativação de eliminação de forma recuperável para o comando de criação.

```powershell
New-AzureRmKeyVault -Name "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>Certifique-se a ativação da eliminação de forma recuperável

Para verificar que um cofre de chaves tenha a eliminação de forma recuperável ativada, execute o *mostrar* de comandos e procure o "Soft eliminar Enabled?" atributo:

```powershell
Get-AzureRmKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>A eliminar um cofre de chaves protegido por eliminação de forma recuperável

O comando para eliminar um cofre de chaves de alterações no comportamento, dependendo se a eliminação de forma recuperável está ativada.

> [!IMPORTANT]
>Se executar o seguinte comando para um cofre de chaves que não tenha a eliminação de forma recuperável ativada, irá eliminar permanentemente este Cofre de chaves e todo o seu conteúdo sem opções para recuperação!

```powershell
Remove-AzureRmKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Como a eliminação de forma recuperável protege seus cofres de chaves

Com a eliminação de forma recuperável ativada:

- Um cofre de chaves eliminado é removido do seu grupo de recursos e colocado num espaço de nomes reservado associado ao local onde foi criado. 
- Objetos eliminados como chaves, segredos e certificados, estão inacessíveis, desde que seu Cofre de chaves que contêm está no Estado eliminado. 
- O nome DNS para um cofre de chaves eliminado está reservado, impedir que um novo cofre de chaves com o mesmo nome que está sendo criado.  

Pode visualizar cofres de chaves de estado de eliminado, associados à subscrição, utilizando o seguinte comando:

```powershell
PS C:\> Get-AzureRmKeyVault -InRemovedState 
```

- *ID* pode ser utilizado para identificar o recurso quando recuperar ou remoção. 
- *ID de recurso* é o ID de recurso original deste cofre. Uma vez que este Cofre de chaves está agora num Estado eliminado, nenhum recurso existe com esse ID de recurso. 
- *Data de remoção agendada* é quando o Cofre será eliminado permanentemente, se foi efetuada nenhuma ação. O período de retenção padrão, utilizado para calcular a *data de remoção agendada*, é de 90 dias.

## <a name="recovering-a-key-vault"></a>Recuperar um cofre de chaves

Para recuperar um cofre de chaves, especifique o nome do Cofre de chaves, grupo de recursos e localização. Tenha em atenção a localização e o grupo de recursos do Cofre de chaves eliminado, sempre que precisar para o processo de recuperação.

```powershell
Undo-AzureRmKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

Quando um cofre de chaves é recuperado, um novo recurso é criado com original ID de recurso. o Cofre de chaves Se o grupo de recursos original for removido, deve ser criado com o mesmo nome antes de tentar a recuperação.

## <a name="key-vault-objects-and-soft-delete"></a>Objetos de Cofre de chaves e eliminação de forma recuperável

O seguinte comando irá eliminar a chave de "ContosoFirstKey", num cofre de chaves com o nome "ContosoVault', que tem a eliminação de forma recuperável ativada:

```powershell
Remove-AzureKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

Com o seu Cofre de chaves ativado para eliminação de forma recuperável, uma chave eliminada ainda parece ser eliminadas, a menos que liste explicitamente chaves eliminadas. A maioria das operações numa chave no Estado eliminado falhará, exceto para listagem, recuperar, remover uma chave foi eliminada. 

Por exemplo, o comando seguinte apresenta uma lista de chaves eliminadas no Cofre de chaves de "ContosoVault":

```powershell
Get-AzureKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Estado de transição 

Quando elimina uma chave no Cofre de chaves com eliminação de forma recuperável ativada, poderá demorar alguns segundos para a transição para concluir. Durante essa transição, que pode parecer que a chave não está no Estado ativo ou estado eliminado. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Utilizar a eliminação de forma recuperável com objetos de Cofre de chaves

Assim como os cofres de chaves, uma chave de eliminado, o segredo ou o certificado, permanece no Estado eliminado até 90 dias, a menos que recuperá-la ou removê-lo. 

#### <a name="keys"></a>Chaves

Para recuperar uma chave eliminados de forma recuperável:

```powershell
Undo-AzureKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

Eliminar permanentemente (também conhecido como remoção) uma chave eliminados de forma recuperável:

> [!IMPORTANT]
> Remover uma chave irá eliminá-la permanentemente e não serão recuperável! 

```powershell
Remove-AzureKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

O **recuperar** e **remover** ações têm suas próprias permissões associadas a uma política de acesso do Cofre de chaves. Para um utilizador ou principal de serviço poder executar uma **recuperar** ou **remover** ação, tem de ter a respetiva permissão para essa chave ou segredo. Por predefinição, **remover** não for adicionado à política de acesso de um cofre de chaves, quando o atalho "todos" é usado para conceder todas as permissões. Tem de conceder especificamente **remover** permissão. 

#### <a name="set-a-key-vault-access-policy"></a>Definir uma política de acesso do Cofre de chaves

O seguinte comando concede user@contoso.com permissão para usar diversas operações de chaves numa *ContosoVault* incluindo **remover**:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Se tiver um cofre de chaves que apenas tenha tido a eliminação de forma recuperável ativada, pode não ter **recuperar** e **remover** permissões.

#### <a name="secrets"></a>Segredos

Como chaves, segredos geridos com seus próprios comandos:

- Elimine um segredo designado SQLPassword: 
```powershell
Remove-AzureKeyVaultSecret -VaultName ContosoVault -name SQLPassword
```

- Liste eliminados todos os segredos num cofre de chaves: 
```powershell
Get-AzureKeyVaultSecret -VaultName ContosoVault -InRemovedState
```

- Recupere um segredo no Estado eliminado: 
```powershell
Undo-AzureKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
```

- Remover um segredo no estado de eliminado: 

  > [!IMPORTANT]
  > Remover um segredo irá eliminá-la permanentemente e não serão recuperável!

  ```powershell
  Remove-AzureKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```

## <a name="purging-and-key-vaults"></a>Cofres de chaves e limpeza

### <a name="key-vault-objects"></a>Objetos de Cofre de chaves

Remover uma chave, o segredo ou o certificado, faz com que a eliminação permanente e não serão recuperável. O Cofre de chaves que continha o objeto excluído entretanto permanecerão intacto assim como todos os outros objetos no Cofre de chaves. 

### <a name="key-vaults-as-containers"></a>Cofres de chaves como contentores
Quando um cofre de chaves é removido, todo o conteúdo é permanentemente eliminado, incluindo chaves, segredos e certificados. Para remover um cofre de chaves, utilize o `Remove-AzureRmKeyVault` comando com a opção `-InRemovedState` e ao especificar a localização do Cofre de chaves eliminado com o `-Location location` argumento. Pode encontrar a localização de um cofre eliminado com o comando `Get-AzureRmKeyVault -InRemovedState`.

>[!IMPORTANT]
>Remover um cofre de chaves e eliminará permanentemente, que significa que não serão recuperável!

```powershell
Remove-AzureRmKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

### <a name="purge-permissions-required"></a>Remover as permissões necessárias
- Para remover um cofre de chaves eliminado, o utilizador necessita da permissão RBAC para o *Microsoft.KeyVault/locations/deletedVaults/purge/action* operação. 
- Para listar um cofre de chaves eliminado, o utilizador necessita da permissão RBAC para o *Microsoft.KeyVault/deletedVaults/read* operação. 
- Por predefinição, apenas um administrador de subscrição tem estas permissões. 

### <a name="scheduled-purge"></a>Remoção agendada

A listagem de objetos de Cofre de chaves eliminado também mostra quando eles estão agendados para ser limpos pelo Cofre de chaves. *Data de remoção agendada* indica quando um objeto do Cofre de chaves serão permanentemente eliminado, se foi efetuada nenhuma ação. Por predefinição, o período de retenção para um objeto do Cofre de chaves eliminado é de 90 dias.

>[!IMPORTANT]
>Um objeto de cofre eliminados, acionado por seus *data de remoção agendada* campo, é eliminado permanentemente. Não é recuperável!

## <a name="other-resources"></a>Outros recursos

- Para uma descrição geral da funcionalidade de eliminação de forma recuperável do Key Vault, consulte [descrição geral da eliminação de forma recuperável do Azure Key Vault](key-vault-ovw-soft-delete.md).
- Para obter uma visão geral da utilização do Azure Key Vault, consulte [introdução ao Azure Key Vault](key-vault-get-started.md).

