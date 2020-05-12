---
title: Criptografe discos para conjuntos de escala Azure com Azure PowerShell
description: Aprenda a usar o Azure PowerShell para encriptar instâncias VM e discos anexados num conjunto de escala de máquina virtual do Windows
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: a20abec6ab9925408dd769c5238186af9b7c3d1c
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195893"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>Criptografe os OS e discos de dados anexados num conjunto de escala de máquina virtual com O PowerShell Azure

O módulo do Azure PowerShell é utilizado para criar e gerir recursos do Azure a partir da linha de comandos do PowerShell ou em scripts.  Este artigo mostra-lhe como usar o Azure PowerShell para criar e encriptar um conjunto de escala de máquina virtual. Para obter mais informações sobre a aplicação da encriptação do disco Azure num conjunto de escala de máquina virtual, consulte a encriptação do [disco Azure para conjuntos](disk-encryption-overview.md)de escala de máquina virtual .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Criar um Cofre de Chave Azure habilitado para encriptação de disco

O Azure Key Vault pode armazenar chaves, segredos ou senhas que lhe permitam implementá-las de forma segura nas suas aplicações e serviços. As chaves criptográficas são armazenadas no Cofre de Chaves Azure utilizando a proteção de software, ou pode importar ou gerar as suas chaves em Módulos de Segurança de Hardware (HSMs) certificados para normas de nível 2 FIPS 140-2. Estas teclas criptográficas são usadas para encriptar e desencriptar discos virtuais ligados ao seu VM. Mantém o controlo destas chaves criptográficas e pode auditar a sua utilização.

Crie um cofre chave com [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Para permitir que o Cofre de Chaves seja utilizado para encriptação de disco, defina o parâmetro *EnabledForDiskEncryption.* O exemplo que se segue também define variáveis para o nome do grupo de recursos, nome do cofre chave e localização. Forneça o seu nome exclusivo do Cofre chave:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Use um cofre de chaves existente

Este passo só é necessário se tiver um Cofre chave existente que deseja utilizar com encriptação de disco. Ignore este passo se criou um Cofre chave na secção anterior.

Pode ativar um Cofre chave existente na mesma subscrição e região que o conjunto de escala para encriptação de discos com [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy). Defina o nome do seu Cofre-Chave existente na *variável $vaultName* seguinte:


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Criar um conjunto de dimensionamento

Primeiro, defina um nome de utilizador e uma palavra-passe para as instâncias da VM com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Agora crie um conjunto de máquinas virtuais com [New-AzVmss](/powershell/module/az.compute/new-azvmss). Para distribuir o tráfego pelas instâncias de VM individuais, é também criado um balanceador de carga. O balanceador de carga inclui regras para distribuir o tráfego na porta TCP 80, bem como para permitir o tráfego de ambiente de trabalho remoto na porta TCP 3389 e a comunicação remota do PowerShell na porta TCP 5985:

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

## <a name="enable-encryption"></a>Ativar encriptação

Para encriptar as instâncias vm num conjunto de escala, obtenha primeiro algumas informações sobre o Cofre de Chaves URI e id de recursos com [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault). Estas variáveis são usadas para iniciar o processo de encriptação com [set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension):


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

Quando solicitado, escreva *y* para continuar o processo de encriptação do disco nas instâncias vM definidas pela escala.

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Ativar a encriptação usando KEK para embrulhar a chave

Também pode utilizar uma chave de encriptação para uma maior segurança ao encriptar o conjunto de escala de máquina virtual.

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

> [!NOTE]
>  A sintaxe para o valor do parâmetro de cofre de encriptação de disco é a cadeia de identificação completa:</br>
/subscrições/[subscrição-id-guid]/recursosGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[[keyvault-name]</br></br>
> A sintaxe para o valor do parâmetro chave-encriptação-chave é o URI completo para o KEK como em:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>Verifique o progresso da encriptação

Para verificar o estado da encriptação do disco, utilize [a Encriptação Get-AzVmssDisk:](/powershell/module/az.compute/Get-AzVmssDiskEncryption)


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

Quando as instâncias vm são encriptadas, o código *CryptonSummary* reporta *ProvisioningState/succeeded* como mostrado na seguinte saída de exemplo:

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

Se já não pretender utilizar discos de instâncias VM encriptados, pode desativar a encriptação com [desactivação-AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption) da seguinte forma:


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>Passos seguintes

- Neste artigo, utilizou o Azure PowerShell para encriptar um conjunto de escala de máquina virtual. Também pode utilizar os modelos [Azure CLI](disk-encryption-cli.md) ou [Azure Resource Manager](disk-encryption-azure-resource-manager.md).
- Se desejar que a Encriptação do Disco Azure seja aplicada após a disponibilização de outra extensão, pode utilizar [a sequenciação](virtual-machine-scale-sets-extension-sequencing.md)de extensões .
