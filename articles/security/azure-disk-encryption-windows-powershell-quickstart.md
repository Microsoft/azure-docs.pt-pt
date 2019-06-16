---
title: Criar e encriptar uma VM do Windows com o Azure PowerShell
description: Neste início rápido, irá aprender a utilizar o Azure PowerShell para criar e encriptar uma máquina virtual do Windows
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: c10c9c6bd757125fa1f91d4442fc1c8afd24d10e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081456"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-in-azure-with-powershell"></a>Início rápido: Criar e encriptar uma máquina virtual do Windows no Azure com o PowerShell

O módulo do Azure PowerShell é utilizado para criar e gerir recursos do Azure a partir da linha de comandos do PowerShell ou em scripts. Este guia de introdução mostra-lhe como utilizar o módulo Azure PowerShell para criar uma máquina virtual (VM) do Windows, crie um Key Vault para o armazenamento de chaves de encriptação e encriptar a VM. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Criar uma máquina virtual do Azure com [New-AzVM](/powershell/module/az.compute/new-azvm). Tem de fornecer credenciais para o cmdlet. 

```powershell
$cred = Get-Credential 

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image win2016datacenter -Size Standard_D2S_V3
```

Irá demorar alguns minutos até a VM ser implementada. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Criar um Key Vault configurado para chaves de encriptação

Encriptação de disco do Azure armazena a chave de encriptação num Azure Key Vault. Criar um cofre de chaves com [novo AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault). Para ativar o Key Vault para armazenar chaves de encriptação, utilize o parâmetro - EnabledForDiskEncryption.

> [!Important]
> Cada Cofre de chaves tem de ter um nome exclusivo. O exemplo seguinte cria um cofre de chaves com o nome *myKV*, mas deve dar o nome algo diferente.

```powershell
New-AzKeyvault -name MyKV -ResourceGroupName myResourceGroup -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>Encriptar a máquina virtual

Encriptar VM com [Set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

Conjunto AzVmDiskEncryptionExtension requer alguns valores do seu objeto do Cofre de chaves. Pode obter estes valores, passando o nome exclusivo do seu Cofre de chaves para [Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault).

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

Pode verificar o processo de criptografia, executando [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus).

```powershell
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

Quando a encriptação está ativada, verá o seguinte no resultado retornado:

```
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NoDiskFound
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet para remover o grupo de recursos, a VM e todos os recursos relacionados:

```powershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou uma máquina virtual, criada um Key Vault, que era a ativar para as chaves de encriptação e as VMS encriptadas.  Avance para o artigo seguinte para saber mais sobre os pré-requisitos do Azure Disk Encryption para VMs de IaaS.

> [!div class="nextstepaction"]
> [Pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md)