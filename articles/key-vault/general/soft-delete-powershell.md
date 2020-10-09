---
title: Cofre de chaves Azure - Como usar soft-delete com PowerShell
description: Aprenda a usar o Azure PowerShell para utilizar a funcionalidade de eliminação suave do Cofre da Chave Azure que permite a recuperação de cofres-chave e objetos-chave do cofre.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/11/2020
ms.author: sudbalas
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 05ff5d2f7ff87d2ce58f135330487f746cc5c1c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90528368"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>Como utilizar a eliminação de forma recuperável do Key Vault com o PowerShell

A funcionalidade de eliminação suave do Azure Key Vault permite a recuperação de cofres apagados e objetos de abóbada. Especificamente, os endereços soft-delete são os seguintes cenários:

- Suporte para a supressão recuperável de um cofre chave
- Suporte para eliminação recuperável de objectos-chave do cofre; chaves, segredos e, certificados

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Azure PowerShell 1.0.0 ou mais tarde - Se ainda não tiver esta configuração, instale a Azure PowerShell e associe-a à sua subscrição Azure, veja [como instalar e configurar a Azure PowerShell](https://docs.microsoft.com/powershell/azure/). 

>[!NOTE]
> Existe uma versão desatualizada do nosso ficheiro formatação de saída Key Vault PowerShell que **pode** ser carregado para o seu ambiente em vez da versão correta. Estamos a antecipar uma versão atualizada do PowerShell para conter a correção necessária para a formatação de saída e atualizaremos este tópico nessa altura. A solução atual, caso se depare com este problema de formatação, é:
> - Utilize a seguinte consulta se notar que não está a ver a propriedade ativada para eliminar suavemente descrita neste tópico: `$vault = Get-AzKeyVault -VaultName myvault; $vault.EnableSoftDelete` .


Para obter informações específicas de referência do Cofre de Chaves para PowerShell, consulte [a referência Azure Key Vault PowerShell](/powershell/module/az.keyvault).

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

```powershell
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Novo cofre-chave

A eliminação suave é automaticamente acesa por padrão para todos os novos cofres-chave. Até 31 de dezembro de 2020 deixará de ser possível desativar a exclusão suave em qualquer cofre de chaves. 

### <a name="verify-soft-delete-enablement"></a>Verifique o habilitação para eliminar o soft-delete

Para verificar se um cofre de chaves tem a eliminação suave ativada, executar o comando do *show* e procurar o 'Soft Delete Enabled?' atributo:

```powershell
Get-AzKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Apagar um cofre de chaves protegido de eliminação suave

O comando para eliminar uma chave de mudanças de comportamento no cofre, dependendo se a eliminação suave está ativada.

> [!IMPORTANT]
>Se executar o seguinte comando para um cofre de chave que não tenha soft-delete ativado, irá eliminar permanentemente este cofre de teclas e todo o seu conteúdo sem opções de recuperação!

```powershell
Remove-AzKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Como a eliminação suave protege os seus cofres chave

Com eliminação suave ativada:

- Um cofre de chaves eliminado é removido do seu grupo de recursos e colocado num espaço de nome reservado, associado ao local onde foi criado. 
- Objetos eliminados, tais como chaves, segredos e certificados, são inacessíveis desde que o cofre de chaves que contém esteja no estado apagado. 
- O nome DNS para um cofre de chaves apagado é reservado, impedindo a criação de um novo cofre-chave com o mesmo nome.  

Pode visualizar cofres-chave de estado apagados, associados à sua subscrição, utilizando o seguinte comando:

```powershell
Get-AzKeyVault -InRemovedState 
```

- *O ID* pode ser usado para identificar o recurso quando se recupera ou purga. 
- *O ID de recursos* é a identificação original deste cofre. Uma vez que este cofre chave está agora em um estado apagado, nenhum recurso existe com esse ID de recurso. 
- *A data de purga programada* é quando o cofre será permanentemente apagado, se não forem tomadas medidas. O período de retenção predefinido, utilizado para calcular a *Data de Purga Programada,* é de 90 dias.

## <a name="recovering-a-key-vault"></a>Recuperação de um cofre chave

Para recuperar um cofre chave, especifique o nome do cofre, o grupo de recursos e a localização. Observe a localização e o grupo de recursos do cofre de chaves apagado, pois necessita deles para o processo de recuperação.

```powershell
Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

Quando um cofre chave é recuperado, um novo recurso é criado com o ID original do cofre. Se o grupo de recursos originais for removido, deve-se ser criado com o mesmo nome antes de tentar a recuperação.

## <a name="deleting-and-purging-key-vault-objects"></a>Apagar e purgar objetos de cofre

O seguinte comando eliminará a tecla 'ContosoFirstKey', num cofre-chave denominado 'ContosoVault', que tem soft-delete ativado:

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

Com o cofre da chave ativado para eliminar suavemente, uma chave apagada ainda parece ser eliminada, a menos que liste explicitamente as teclas eliminadas. A maioria das operações numa chave no estado eliminado falhará, exceto para a listagem, recuperação, purga de uma chave eliminada. 

Por exemplo, as seguintes listas de comandos eliminadas no cofre de chaves 'ContosoVault':

```powershell
Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Estado de transição 

Quando apagar uma chave num cofre com exclusão suave ativada, pode demorar alguns segundos até que a transição esteja concluída. Durante esta transição, pode parecer que a chave não está no estado ativo ou no estado eliminado. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Usando soft-delete com objetos de abóbada chave

Assim como cofres-chave, uma chave, segredo ou certificado apagado, permanece em estado de eliminação por até 90 dias, a menos que o recupere ou purgue. 

#### <a name="keys"></a>Chaves

Para recuperar uma chave de apagação suave:

```powershell
Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

Para eliminar permanentemente (também conhecida como purga) uma chave de eliminação suave:

> [!IMPORTANT]
> A purga de uma chave irá eliminá-la permanentemente, e não será recuperável! 

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

As ações **de recuperação** e **purga** têm as suas próprias permissões associadas a uma política de acesso ao cofre chave. Para que um utilizador ou diretor de serviço possa executar uma ação **de recuperação** ou **purga,** deve ter a respetiva permissão para essa chave ou segredo. Por defeito, **a purga** não é adicionada à política de acesso de um cofre chave, quando o atalho "tudo" é usado para conceder todas as permissões. Deve conceder especificamente permissão **de purga.** 

#### <a name="set-a-key-vault-access-policy"></a>Definir uma política de acesso ao cofre chave

O comando seguinte autoriza user@contoso.com a utilização de várias operações em chaves em *ContosoVault,* incluindo **a purga:**

```powershell
Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Se tiver um cofre de chaves existente que acaba de ter a eliminação suave ativada, pode não ter permissões de **recuperação** e **purga.**

#### <a name="secrets"></a>Segredos

Tal como as chaves, os segredos são geridos com os seus próprios comandos:

- Excluir um segredo chamado SQLPassword: 
  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

- Lista todos os segredos apagados num cofre chave: 
  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

- Recuperar um segredo no estado apagado: 
  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

- Purgue um segredo em estado apagado: 

  > [!IMPORTANT]
  > Purgar um segredo irá apagá-lo permanentemente, e não será recuperável!

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```

#### <a name="certificates"></a>Certificados

Pode gerir certificados utilizando comandos abaixo:

- Eliminar um Certificado: 
  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

- Lista todos os certificados eliminados num cofre chave: 
  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

- Recuperar um certificado no estado apagado: 
  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

- Purgar um certificado em estado apagado: 

  > [!IMPORTANT]
  > A purga de um certificado irá eliminá-lo permanentemente, e não será recuperável!

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState 
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

Quando um cofre-chave é purgado, todo o seu conteúdo é permanentemente eliminado, incluindo chaves, segredos e certificados. Para purgar um cofre de chave apagado suavemente, utilize o `Remove-AzKeyVault` comando com a opção e `-InRemovedState` especificando a localização do cofre de chaves eliminado com o `-Location location` argumento. Pode encontrar a localização de um cofre apagado utilizando o comando `Get-AzKeyVault -InRemovedState` .

```powershell
Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
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

Quando a proteção contra a purga é ligada, um cofre ou um objeto em estado apagado não podem ser purgados até que o período de retenção tenha passado. Tal cofre ou objeto ainda pode ser recuperado. Esta funcionalidade garante que um cofre ou um objeto nunca poderão ser permanentemente eliminados até que o período de retenção tenha passado. O período de retenção predefinido é de 90 dias, mas, durante a criação do cofre chave, é possível definir o intervalo de política de retenção para um valor de 7 a 90 dias. A política de proteção da purga utiliza o mesmo intervalo. Uma vez definido, o intervalo da política de retenção não pode ser alterado.

Só pode ativar a proteção contra a purga se também estiver ativada a eliminação suave. A proteção de purga incapacitante não é suportada. 

Para ligar a proteção de eliminação e purga suave ao criar um cofre, utilize o cmdlet [New-AzKeyVault:](/powershell/module/az.keyvault/new-azkeyvault?view=azps-1.5.0)

```powershell
New-AzKeyVault -Name ContosoVault -ResourceGroupName ContosoRG -Location westus -EnableSoftDelete -EnablePurgeProtection
```

Para adicionar proteção de purga a um cofre existente (que já tem soft-delete ativado), utilize os cmdlets [Get-AzKeyVault,](/powershell/module/az.keyvault/Get-AzKeyVault?view=azps-1.5.0) [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-1.5.0)e [Set-AzResource:](/powershell/module/az.resources/set-azresource?view=azps-1.5.0)

```
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

## <a name="other-resources"></a>Outros recursos

- Para obter uma visão geral da função de eliminação suave do Key Vault, consulte [a visão geral do Azure Key Vault .](soft-delete-overview.md)
- Para uma visão geral da utilização do Azure Key Vault, veja [o que é o Cofre da Chave Azure?](overview.md)
