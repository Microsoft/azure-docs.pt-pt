---
title: Criptografar discos para conjuntos de dimensionamento do Azure com Azure PowerShell
description: Saiba como usar Azure PowerShell para criptografar instâncias de VM e discos anexados em um conjunto de dimensionamento de máquinas virtuais do Windows
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: mbaldwin
ms.openlocfilehash: bd7f92c104e06896f4b3c8bb2adef45983cf5d4d
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278992"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>Criptografar o sistema operacional e OS discos de dados anexados em um conjunto de dimensionamento de máquinas virtuais com Azure PowerShell

O módulo do Azure PowerShell é utilizado para criar e gerir recursos do Azure a partir da linha de comandos do PowerShell ou em scripts.  Este artigo mostra como usar Azure PowerShell para criar e criptografar um conjunto de dimensionamento de máquinas virtuais. Para obter mais informações sobre como aplicar Azure Disk Encryption a um conjunto de dimensionamento de máquinas virtuais, consulte [Azure Disk Encryption para conjuntos de dimensionamento de máquinas virtuais](disk-encryption-overview.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Criar um Azure Key Vault habilitado para criptografia de disco

Azure Key Vault pode armazenar chaves, segredos ou senhas que permitem implementá-los com segurança em seus aplicativos e serviços. As chaves de criptografia são armazenadas em Azure Key Vault usando a proteção de software ou você pode importar ou gerar suas chaves em HSMs (módulos de segurança de hardware) certificados para os padrões FIPS 140-2 nível 2. Essas chaves de criptografia são usadas para criptografar e descriptografar discos virtuais anexados à sua VM. Você mantém o controle dessas chaves de criptografia e pode auditar seu uso.

Crie um Key Vault com [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Para permitir que o Key Vault seja usado para criptografia de disco, defina o parâmetro *EnabledForDiskEncryption* . O exemplo a seguir também define variáveis para o nome do grupo de recursos, o nome do Key Vault e o local. Forneça seu próprio nome de Key Vault exclusivo:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Usar um Key Vault existente

Esta etapa só será necessária se você tiver um Key Vault existente que deseja usar com a criptografia de disco. Ignore esta etapa se você criou um Key Vault na seção anterior.

Você pode habilitar um Key Vault existente na mesma assinatura e região que o conjunto de dimensionamento para a criptografia de disco com [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy). Defina o nome do seu Key Vault existente na variável *$vaultName* da seguinte maneira:


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Criar um conjunto de dimensionamento

Primeiro, defina um nome de utilizador e uma palavra-passe para as instâncias da VM com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Agora, crie um conjunto de dimensionamento de máquinas virtuais com [New-AzVmss](/powershell/module/az.compute/new-azvmss). Para distribuir o tráfego para instâncias de VM individuais, é também criado um balanceador de carga. O balanceador de carga inclui regras para distribuir o tráfego na porta TCP 80, bem como para permitir o tráfego de ambiente de trabalho remoto na porta TCP 3389 e a comunicação remota do PowerShell na porta TCP 5985:

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

## <a name="enable-encryption"></a>Habilitar criptografia

Para criptografar instâncias de VM em um conjunto de dimensionamento, primeiro obtenha algumas informações sobre o URI de Key Vault e a ID de recurso com [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault). Essas variáveis são usadas para iniciar o processo de criptografia com [set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension):


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

Quando solicitado, digite *y* para continuar o processo de criptografia de disco nas instâncias de VM do conjunto de dimensionamento.

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Habilitar criptografia usando KEK para encapsular a chave

Você também pode usar uma chave de criptografia de chave para aumentar a segurança ao criptografar o conjunto de dimensionamento de máquinas virtuais.

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

> [!NOTE]
>  A sintaxe para o valor do parâmetro Disk-Encryption-keyvault é a cadeia de caracteres do identificador completo:</br>
/subscriptions/[Subscription-ID-GUID]/resourceGroups/[nome-do-grupo-de-recursos]/providers/Microsoft.KeyVault/vaults/[keyvault-nome]</br></br>
> A sintaxe para o valor do parâmetro Key-Encryption-Key é o URI completo para o KEK como em:</br>
https://[keyvault-Name]. Vault. Azure. net/Keys/[kekname]/[Kek-Unique-ID]

## <a name="check-encryption-progress"></a>Verificar o progresso da criptografia

Para verificar o status da criptografia de disco, use [Get-AzVmssDiskEncryption](/powershell/module/az.compute/Get-AzVmssDiskEncryption):


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

Quando as instâncias de VM são criptografadas, o código *EncryptionSummary* relata *ProvisioningState/Succeeded* , conforme mostrado na seguinte saída de exemplo:

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

Se você não deseja mais usar discos de instâncias de VM criptografadas, você pode desabilitar a criptografia com [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption) da seguinte maneira:


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>Passos seguintes

- Neste artigo, você usou Azure PowerShell para criptografar um conjunto de dimensionamento de máquinas virtuais. Você também pode usar os modelos [CLI do Azure](disk-encryption-cli.md) ou [Azure Resource Manager](disk-encryption-azure-resource-manager.md).
- Se desejar ter Azure Disk Encryption aplicado depois que outra extensão for provisionada, você poderá usar o [sequenciamento de extensão](virtual-machine-scale-sets-extension-sequencing.md).
