---
title: Cofre de Chaves Azure - Como usar soft-delete com powerShell
description: Utilize exemplos de casos de soft-delete com snips de código PowerShell
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: c74bea8aa1a8e2f9de47b501f9afd9540cfc61b9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422915"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>Como utilizar a eliminação de forma recuperável do Key Vault com o PowerShell

A função de eliminação suave do Cofre de Chaves Azure permite a recuperação de cofres apagados e objetos de cofre. Especificamente, a eliminação suave aborda os seguintes cenários:

- Suporte para eliminação recuperável de um cofre chave
- Suporte para eliminação recuperável de objetos chave do cofre; chaves, segredos e.certificados

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Azure PowerShell 1.0.0 ou mais tarde - Se ainda não tiver esta configuração, instale o Azure PowerShell e associe-o à sua subscrição Azure, consulte Como instalar e configurar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). 

>[!NOTE]
> Existe uma versão desatualizada do nosso ficheiro de formatação de saída Key Vault PowerShell que **pode** ser carregado para o seu ambiente em vez da versão correta. Estamos a antecipar uma versão atualizada do PowerShell para conter a correção necessária para a formatação de saída e atualizaremos este tópico nessa altura. A suposição atual, caso encontre este problema de formatação, é:
> - Utilize a seguinte consulta se notar que não está a ver a propriedade `$vault = Get-AzKeyVault -VaultName myvault; $vault.EnableSoftDelete`ativada soft-delete descrita neste tópico: .


Para obter informações específicas de referência para o PowerShell, consulte a [referência powerShell do Cofre de Chaves Azure](/powershell/module/az.keyvault).

## <a name="required-permissions"></a>Permissões obrigatórias

As operações do Key Vault são geridas separadamente através de permissões de controlo de acesso baseadas em funções (RBAC) da seguinte forma:

| Operação | Descrição | Permissão do utilizador |
|:--|:--|:--|
|Lista|Listas apagadas cofres chave.|Microsoft.KeyVault/deletedVaults/read|
|Recuperar|Restaura um cofre de chaves apagado.|Microsoft.KeyVault/vaults/write|
|Remover|Remove permanentemente um cofre de chaves apagado e todo o seu conteúdo.|Microsoft.KeyVault/locations/deletedVaults/purga/ação|

Para obter mais informações sobre permissões e controlo de acesso, consulte [Proteja o seu cofre de chaves).](secure-your-key-vault.md)

## <a name="enabling-soft-delete"></a>Habilitar a eliminar suavemente

Ativa o "soft-delete" para permitir a recuperação de um cofre de chave apagado ou objetos armazenados num cofre de chaves.

> [!IMPORTANT]
> Permitir a "eliminação suave" num cofre chave é uma ação irreversível. Uma vez que a propriedade de eliminação suave tenha sido definida como "verdadeira", não pode ser alterada ou removida.  

### <a name="existing-key-vault"></a>Cofre-chave existente

Para um cofre de chaves existente chamado ContosoVault, ative a eliminação suave da seguinte forma. 

```powershell
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Novo cofre-chave

Permitir a eliminação suave de um novo cofre chave é feito no momento da criação, adicionando a bandeira de eliminação suave ao seu comando de criação.

```powershell
New-AzKeyVault -Name "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>Verifique a ativação de eliminação suave

Para verificar se um cofre de chaves tem soft-delete ativado, execute o comando do *show* e procure o 'Soft Delete Enabled?' atributo:

```powershell
Get-AzKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Eliminação de um cofre de chaves protegido de eliminação suave

O comando para eliminar uma chave do cofre muda de comportamento, dependendo se o soft-delete está ativado.

> [!IMPORTANT]
>Se executar o seguinte comando para um cofre chave que não tenha soft-delete ativado, eliminará permanentemente este cofre chave e todo o seu conteúdo sem opções de recuperação!

```powershell
Remove-AzKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Como a eliminação suave protege os seus cofres chave

Com eliminação suave ativada:

- Um cofre de chave eliminado é removido do seu grupo de recursos e colocado num espaço de nome reservado, associado ao local onde foi criado. 
- Objetos apagados, tais como chaves, segredos e certificados, são inacessíveis desde que o cofre da chave que contenha esteja no estado apagado. 
- O nome DNS para um cofre de chave apagado está reservado, impedindo que um novo cofre com o mesmo nome seja criado.  

Pode visualizar cofres de chaves de estado eliminados, associados à sua subscrição, utilizando o seguinte comando:

```powershell
Get-AzKeyVault -InRemovedState 
```

- *O ID* pode ser usado para identificar o recurso quando se recupera ou purga. 
- *A identificação* de recursos é a identificação original deste cofre. Uma vez que este cofre chave está agora num estado apagado, não existe nenhum recurso com o ID do recurso. 
- *Data de purga programada* é quando o cofre será permanentemente apagado, se não forem tomadas medidas. O período de retenção predefinido, utilizado para calcular a Data de *Purga Programada,* é de 90 dias.

## <a name="recovering-a-key-vault"></a>Recuperando um cofre chave

Para recuperar um cofre chave, especifique o nome do cofre chave, o grupo de recursos e a localização. Note a localização e o grupo de recursos do cofre de chaves apagados, pois precisa deles para o processo de recuperação.

```powershell
Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

Quando um cofre chave é recuperado, um novo recurso é criado com o ID original do cofre chave. Se o grupo de recursos original for removido, deve ser criado com o mesmo nome antes de tentar a recuperação.

## <a name="deleting-and-purging-key-vault-objects"></a>Apagar e purgar objetos chave do cofre

O seguinte comando eliminará a tecla 'ContosoFirstKey', num cofre de chaves chamado 'ContosoVault', que tem soft-delete ativado:

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

Com o cofre da chave ativado para apagar suavemente, uma tecla apagada ainda parece ser eliminada, a menos que indique explicitamente as teclas apagadas. A maioria das operações numa chave no estado eliminado falhará, exceto para a listagem, recuperação, purgar uma chave eliminada. 

Por exemplo, as seguintes listas de comando apagaram as teclas no cofre da chave 'ContosoVault':

```powershell
Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Estado de transição 

Quando eliminar uma chave num cofre com eliminação suave ativada, pode demorar alguns segundos para que a transição esteja concluída. Durante esta transição, pode parecer que a chave não está no estado ativo ou no estado apagado. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Utilização soft-delete com objetos de cofre chave

Tal como os cofres chave, uma chave apagada, segredo ou certificado, permanece em estado apagado por até 90 dias, a menos que recupere ou purgue. 

#### <a name="keys"></a>Chaves

Para recuperar uma chave suavemente apagada:

```powershell
Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

Para eliminar permanentemente (também conhecido como purgar) uma chave suavemente eliminada:

> [!IMPORTANT]
> A purga de uma chave irá apagá-la permanentemente, e não será recuperável! 

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

As ações de **recuperação** e **purga** têm as suas próprias permissões associadas a uma política chave de acesso ao cofre. Para que um utilizador ou um diretor de serviço possa executar uma ação de **recuperação** ou **purga,** devem ter a respetiva permissão para essa chave ou segredo. Por padrão, a **purga** não é adicionada à política de acesso de um cofre chave, quando o atalho "all" é usado para conceder todas as permissões. Deve conceder especificamente permissão de **purga.** 

#### <a name="set-a-key-vault-access-policy"></a>Desestabeleça uma política de acesso ao cofre chave

O seguinte user@contoso.com comando concede permissão para a utilização de várias operações em chaves em *ContosoVault,* incluindo **a purga:**

```powershell
Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Se tiver um cofre chave existente que acaba de ter uma eliminação suave ativada, pode não ter **permissões de recuperação** e **purga.**

#### <a name="secrets"></a>Segredos

Como chaves, os segredos são geridos com os seus próprios comandos:

- Elimine um segredo chamado SQLPassword: 
  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

- Liste todos os segredos apagados num cofre chave: 
  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

- Recuperar um segredo no estado apagado: 
  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

- Expurgar um segredo em estado apagado: 

  > [!IMPORTANT]
  > Purgar um segredo irá apagá-lo permanentemente, e não será recuperável!

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```

#### <a name="certificates"></a>Certificados

Pode gerir certificados utilizando comandos abaixo:

- Eliminar um Certificado denominado SQLPassword: 
  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

- Enumerar todos os certificados apagados num cofre chave: 
  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

- Recuperar um certificado no estado eliminado: 
  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

- Expurgar um certificado em estado eliminado: 

  > [!IMPORTANT]
  > A purga de um certificado irá apagá-lo permanentemente e não será recuperável!

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState 
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

Quando um cofre chave é purgado, todo o seu conteúdo é permanentemente apagado, incluindo chaves, segredos e certificados. Para purgar um cofre de `Remove-AzKeyVault` chaves suavemente `-InRemovedState` eliminado, utilize o comando com a `-Location location` opção e especificando a localização do cofre de chaves apagado com o argumento. Pode encontrar a localização de um `Get-AzKeyVault -InRemovedState`cofre apagado usando o comando .

```powershell
Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
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

Para ligar a proteção de eliminação suave e purga quando criar um cofre, utilize o cmdlet [New-AzKeyVault:](/powershell/module/az.keyvault/new-azkeyvault?view=azps-1.5.0)

```powershell
New-AzKeyVault -Name ContosoVault -ResourceGroupName ContosoRG -Location westus -EnableSoftDelete -EnablePurgeProtection
```

Para adicionar proteção de purga a um cofre existente (que já tem soft delete enabled), utilize os cmdlets [Get-AzKeyVault,](/powershell/module/az.keyvault/Get-AzKeyVault?view=azps-1.5.0) [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-1.5.0)e [Set-AzResource:](/powershell/module/az.resources/set-azresource?view=azps-1.5.0)

```
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

## <a name="other-resources"></a>Outros recursos

- Para uma visão geral da função de eliminação suave do Key Vault, consulte a visão geral do [Cofre de Chaves Azure](overview-soft-delete.md)).
- Para uma visão geral do uso do Cofre chave Azure, veja [o que é o Cofre chave Azure?](overview.md)
