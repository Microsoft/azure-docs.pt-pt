---
title: Criar e criptografar uma VM do Windows com Azure PowerShell
description: Neste guia de início rápido, você aprende a usar Azure PowerShell para criar e criptografar uma máquina virtual do Windows
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: b5e5b44742c85591b913b94e622c76a2aba111ce
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72246082"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-in-azure-with-powershell"></a>Início rápido: criar e criptografar uma máquina virtual do Windows no Azure com o PowerShell

O módulo do Azure PowerShell é utilizado para criar e gerir recursos do Azure a partir da linha de comandos do PowerShell ou em scripts. Este guia de início rápido mostra como usar o módulo Azure PowerShell para criar uma VM (máquina virtual) do Windows, criar um Key Vault para o armazenamento de chaves de criptografia e criptografar a VM. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Crie uma máquina virtual

Crie uma máquina virtual do Azure com [New-AzVM](/powershell/module/az.compute/new-azvm). Você deve fornecer credenciais para o cmdlet. 

```powershell
$cred = Get-Credential 

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image win2016datacenter -Size Standard_D2S_V3
```

Irá demorar alguns minutos até a VM ser implementada. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Criar um Key Vault configurado para chaves de criptografia

A criptografia de disco do Azure armazena sua chave de criptografia em um Azure Key Vault. Crie um Key Vault com [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault). Para habilitar o Key Vault armazenar as chaves de criptografia, use o parâmetro-EnabledForDiskEncryption.

> [!Important]
> Cada Key Vault deve ter um nome exclusivo. O exemplo a seguir cria um Key Vault chamado *myKV*, mas você deve nomear algo diferente.

```powershell
New-AzKeyvault -name MyKV -ResourceGroupName myResourceGroup -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>Criptografar a máquina virtual

Criptografe sua VM com [set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

Set-AzVmDiskEncryptionExtension requer alguns valores de seu objeto Key Vault. Você pode obter esses valores passando o nome exclusivo do seu cofre de chaves para [Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault).

```powershell
$KeyVault = Get-AzKeyVault -VaultName MyKV -ResourceGroupName MyResourceGroup

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName MyVM -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId
```

Após alguns minutos, o processo retornará o seguinte:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

Você pode verificar o processo de criptografia executando [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus).

```powershell
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

Quando a criptografia estiver habilitada, você verá o seguinte na saída retornada:

```
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NoDiskFound
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, a VM e todos os recursos relacionados:

```powershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou uma máquina virtual, criou uma Key Vault que foi habilitada para chaves de criptografia e criptografou a VM.  Avance para o artigo seguinte para saber mais sobre os pré-requisitos do Azure Disk Encryption para VMs de IaaS.

> [!div class="nextstepaction"]
> [Visão geral de Azure Disk Encryption](disk-encryption-overview.md)