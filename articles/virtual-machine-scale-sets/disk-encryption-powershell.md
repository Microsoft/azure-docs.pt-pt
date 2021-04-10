---
title: Criptografe discos para conjuntos de escala Azure com Azure PowerShell
description: Saiba como usar o Azure PowerShell para encriptar instâncias VM e discos anexos num conjunto de escala de máquina virtual do Windows
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 59b015232fcfe2fd98e1b9f28c2eb4fa86606049
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519576"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>Criptografe os discos de dados e os discos de dados anexados num conjunto de escala de máquina virtual com a Azure PowerShell

O módulo do Azure PowerShell é utilizado para criar e gerir recursos do Azure a partir da linha de comandos do PowerShell ou em scripts.  Este artigo mostra-lhe como usar o Azure PowerShell para criar e encriptar um conjunto de escala de máquina virtual. Para obter mais informações sobre a aplicação da encriptação do disco Azure a um conjunto de escala de máquina virtual, consulte [encriptação do disco Azure para conjuntos de escala de máquina virtual](disk-encryption-overview.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Criar um cofre de chave Azure habilitado para encriptação de disco

O Azure Key Vault pode armazenar chaves, segredos ou senhas que lhe permitam implementá-las de forma segura nas suas aplicações e serviços. As chaves criptográficas são armazenadas no Cofre da Chave Azure utilizando proteção de software, ou pode importar ou gerar as suas chaves em Módulos de Segurança de Hardware (HSMs) certificadas para normas fips 140-2 de nível 2. Estas chaves criptográficas são usadas para encriptar e desencriptar discos virtuais ligados ao seu VM. Mantém o controlo destas chaves criptográficas e pode auditar o seu uso.

Crie um [cofre-chave com New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Para permitir a utilização do Cofre de Chaves para encriptação do disco, desenfita o parâmetro *enabledForDiskEncryption.* O exemplo a seguir também define variáveis para nome de grupo de recursos, Nome do Cofre de Chaves e localização. Forneça o seu próprio nome exclusivo do Key Vault:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Use um cofre de chaves existente

Este passo só é necessário se tiver um Cofre de Chaves existente que deseja utilizar com encriptação de disco. Salte este passo se tiver criado um Cofre-Chave na secção anterior.

Pode ativar um Cofre-Chave existente na mesma subscrição e região que a escala definida para encriptação de disco com [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy). Defina o nome do cofre de chaves existente na variável *$vaultName* da seguinte forma:


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Criar um conjunto de dimensionamento

Primeiro, defina um nome de utilizador e uma palavra-passe para as instâncias da VM com [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Agora crie um conjunto de escala de máquina virtual com [New-AzVmss](/powershell/module/az.compute/new-azvmss). Para distribuir o tráfego pelas instâncias de VM individuais, é também criado um balanceador de carga. O balanceador de carga inclui regras para distribuir o tráfego na porta TCP 80, bem como para permitir o tráfego de ambiente de trabalho remoto na porta TCP 3389 e a comunicação remota do PowerShell na porta TCP 5985:

```azurepowershell-interactive
$vmssName="myScaleSet"

New-AzVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```

## <a name="enable-encryption"></a>Ativar a encriptação

Para encriptar as instâncias VM num conjunto de escala, primeiro obtenha algumas informações sobre o Key Vault URI e o ID de recursos com [o Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault). Estas variáveis são usadas para iniciar o processo de encriptação com [Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension):


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

Quando solicitado, escreva *y* para continuar o processo de encriptação do disco na escala definir instâncias VM.

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Ative a encriptação usando KEK para embrulhar a chave

Também pode utilizar uma chave de encriptação para obter segurança adicional ao encriptar o conjunto de escala de máquina virtual.

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

> [!NOTE]
>  A sintaxe para o valor do parâmetro de chave-chave de encriptação de disco é a cadeia de identificação completa:</br>
/subscrições/[subscrição-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> A sintaxe para o valor do parâmetro chave de encriptação-chave é o URI completo para o KEK como em:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>Verifique o progresso da encriptação

Para verificar o estado da encriptação do disco, utilize [a encriptação Get-AzVmssDiskEncryption](/powershell/module/az.compute/Get-AzVmssDiskEncryption):


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

Quando as instâncias VM são encriptadas, o código *de encriptaçãoSummary* *reporta ProvisioningState/succeeded* como mostrado na seguinte saída de exemplo:

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="disable-encryption"></a>Desativar a encriptação

Se já não pretender utilizar discos de instâncias VM encriptados, pode desativar a encriptação com [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption) da seguinte forma:


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>Passos seguintes

- Neste artigo, usou o Azure PowerShell para encriptar um conjunto de escala de máquina virtual. Também pode utilizar os modelos [Azure CLI](disk-encryption-cli.md) ou [Azure Resource Manager](disk-encryption-azure-resource-manager.md).
- Se desejar que a Encriptação do Disco Azure seja aplicada após a disponibilização de outra extensão, pode utilizar [a sequência de extensão](virtual-machine-scale-sets-extension-sequencing.md).
