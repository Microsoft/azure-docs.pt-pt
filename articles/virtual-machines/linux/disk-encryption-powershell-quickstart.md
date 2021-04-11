---
title: Criar e encriptar um Linux VM com Azure Powershell
description: Neste arranque rápido, aprende-se a usar o Azure Powershell para criar e encriptar uma máquina virtual Linux
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1833b25554df53a480af9a8fc643755dbc2bbdb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564684"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-in-azure-with-azure-powershell"></a>Quickstart: Criar e encriptar um Linux VM em Azure com Azure PowerShell

O módulo do Azure PowerShell é utilizado para criar e gerir recursos do Azure a partir da linha de comandos do PowerShell ou em scripts. Este quickstart mostra-lhe como usar o módulo Azure PowerShell para criar uma máquina virtual Linux (VM), criar um Cofre-Chave para o armazenamento de chaves de encriptação e encriptar o VM. Este quickstart utiliza a imagem de mercado Ubuntu 16.04 LTS da Canonical e um tamanho Standard_D2S_V3 VM. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um grupo de recursos Azure com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos:

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma máquina virtual Azure com [New-AzVM,](/powershell/module/az.compute/new-azvm)passando-lhe o objeto de configuração VM que criou acima.

```powershell-interactive
$cred = Get-Credential

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image Canonical:UbuntuServer:18.04-LTS:latest -Size Standard_D2S_V3
```

Irá demorar alguns minutos até a VM ser implementada. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Criar um Cofre de Chaves configurado para chaves de encriptação

A encriptação do disco Azure armazena a sua chave de encriptação num Cofre de Chaves Azure. Crie um [cofre-chave com New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault). Para ativar o Cofre de Chaves para armazenar chaves de encriptação, utilize o parâmetro de encriptação -EnablediskEncryption.

> [!Important]
> Cada cofre deve ter um nome único em Azure. Nos exemplos abaixo, substitua <o seu nome único> pelo nome que escolher.

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>Criptografe a máquina virtual

Criptografe o seu VM com [Set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

Set-AzVmDiskEncryptionExtension requer alguns valores do seu objeto Key Vault. Pode obter estes valores passando o nome único do seu cofre chave para [Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault).

```azurepowershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -SkipVmBackup -VolumeType All
```

Após alguns minutos, o processo devolverá o seguinte:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

Pode verificar o processo de encriptação executando [o Get-AzVmDiskEncrypationStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus).

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

Quando a encriptação estiver ativada, verá o seguinte na saída devolvida:

```
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, pode utilizar o cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, VM e todos os recursos relacionados:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou uma máquina virtual, criou um Key Vault que permitia chaves de encriptação e encriptava o VM.  Avance para o próximo artigo para saber mais sobre a encriptação do disco Azure para Os VMs Linux.

> [!div class="nextstepaction"]
> [Visão geral da encriptação do disco Azure](disk-encryption-overview.md)
