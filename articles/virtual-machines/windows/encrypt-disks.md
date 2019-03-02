---
title: Encriptar discos numa VM do Windows no Azure | Documentos da Microsoft
description: Encriptar discos virtuais numa VM do Windows para uma maior segurança com o Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: cc0eee9dc36878f7a02b97453c859d94ea99b901
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57217143"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>Encriptar discos virtuais numa VM do Windows
Para segurança aprimorada de máquina virtual (VM) e de conformidade, os discos virtuais no Azure podem ser encriptados. Os discos são encriptados utilizando chaves criptográficas que são protegidas num Azure Key Vault. Pode controlar estas chaves criptográficas e pode auditar o seu uso. Este artigo descreve como encriptar discos virtuais numa VM do Windows com o Azure PowerShell. Também pode [encriptar uma VM do Linux ao utilizar a CLI do Azure](../linux/encrypt-disks.md).

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="overview-of-disk-encryption"></a>Descrição geral da encriptação de disco
Discos virtuais em VMs do Windows são encriptados em descanso ao utilizar o BitLocker. Não existe nenhum custo associado para encriptar discos virtuais no Azure. As chaves criptográficas são armazenadas num Azure Key Vault ao utilizar a proteção de software, ou pode importar ou gerar as suas chaves em módulos de segurança de Hardware (HSMs) com certificação FIPS 140-2 de padrões no nível 2. As chaves criptográficas são utilizadas para encriptar e desencriptar os discos virtuais anexados a sua VM. Manter o controlo sobre essas chaves criptográficas e pode auditar o seu uso. 

O processo para encriptar uma VM é o seguinte:

1. Crie uma chave criptográfica num Azure Key Vault.
1. Configure a chave criptográfica para ser utilizada para encriptar discos.
1. Ative a encriptação de disco para os discos virtuais.
1. As chaves criptográficas necessárias são solicitadas no Azure Key Vault.
1. Os discos virtuais são encriptados com a chave criptográfica fornecida.


## <a name="requirements-and-limitations"></a>Requisitos e limitações

Cenários suportados e os requisitos para encriptação de disco:

* Ativar a encriptação no novo Windows VMs a partir de imagens do Azure Marketplace ou imagens VHD personalizadas.
* Ativar a encriptação em VMs existentes do Windows no Azure.
* Ativar a encriptação em VMs do Windows que são configuradas através de espaços de armazenamento.
* A desativação da encriptação no SO e dados de unidades para as VMs do Windows.
* Escalão Standard VMs, como A, série D, DS, G e GS VMs.

    > [!NOTE]
    > Todos os recursos (incluindo o Key Vault, a conta de armazenamento e a VM) tem de ser na mesma região do Azure e subscrição.

Encriptação de disco não é atualmente suportada nos seguintes cenários:

* VMs de escalão básico.
* VMs criadas utilizando o modelo de implementação clássica.
* A atualizar as chaves criptográficas numa VM já encriptada.
* Integração com o serviço de gestão de chaves no local.


## <a name="create-an-azure-key-vault-and-keys"></a>Criar um Azure Key Vault e chaves
Antes de começar, certifique-se de que a versão mais recente do módulo Azure PowerShell foi instalada. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview). Nos seguintes exemplos de comando, substitua todos os parâmetros de exemplo com seus próprios nomes, localização e valores de chave, como *myResourceGroup*, *myKeyVault*, *myVM*, e assim por diante.

A primeira etapa é criar um Azure Key Vault para armazenar as chaves criptográficas. O Azure Key Vaults pode armazenar chaves, segredos ou palavras-passe que permitem-lhe implementá-los em segurança nas suas aplicações e serviços. Para a encriptação de disco virtual, irá criar um Key Vault para armazenar uma chave criptográfica utilizada para encriptar ou desencriptar os discos virtuais. 

Ativar o fornecedor do Azure Key Vault com a sua subscrição do Azure com [Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider), em seguida, crie um grupo de recursos com [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). O exemplo seguinte cria um nome de grupo de recursos *myResourceGroup* no *E.U.A. Leste* localização:

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzResourceGroup -Location $location -Name $rgName
```

O Cofre de chaves do Azure que contém as chaves criptográficas e os recursos de computação associado como o armazenamento e a própria VM têm de estar todos na mesma região. Criar um Azure Key Vault com [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) e ativar o Key Vault para utilização com a encriptação de disco. Especifique um nome exclusivo do Key Vault para *keyVaultName* da seguinte forma:

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Pode armazenar chaves de criptografia com o software ou proteção de modelo de segurança de Hardware (HSM).  Um cofre de chave padrão apenas armazena chaves protegidas por software. Utilizar um HSM requer um cofre de chaves de premium a um custo adicional. Para criar um cofre de chaves de premium, no passo anterior adicione a *- Sku "Premium"* parâmetro. O exemplo seguinte utiliza as chaves protegidas por software, uma vez que criámos um cofre de chave padrão. 

Para ambos os modelos de proteção, a plataforma do Azure tem de ter acesso para solicitar as chaves criptográficas quando a VM arranca para desencriptar os discos virtuais. Criar uma chave criptográfica no seu Cofre de chaves com [Add-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey). O exemplo seguinte cria uma chave denominada *myKey*:

```azurepowershell-interactive
Add-AzureKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Para testar o processo de criptografia, crie uma VM com [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). O exemplo seguinte cria uma VM com o nome *myVM* usando um *do Windows Server 2016 Datacenter* imagem. Quando lhe forem pedidas credenciais, introduza o nome de utilizador e palavra-passe a ser utilizado para a sua VM:

```azurepowershell-interactive
$cred = Get-Credential

New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-a-virtual-machine"></a>Encriptar uma máquina virtual
Encriptar VM com [Set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) utilizando a chave de Cofre de chaves do Azure. O exemplo seguinte obtém todas as informações da chave, em seguida, criptografa a VM com o nome *myVM*:

```azurepowershell-interactive
$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Aceite o pedido para continuar com a encriptação de VM. A VM é reiniciada durante o processo. Assim que tiver concluído o processo de encriptação e reiniciou a VM, reveja o estado de encriptação com [Get-AzVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus):

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

O resultado é semelhante ao seguinte exemplo:

```azurepowershell-interactive
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>Passos Seguintes
* Para obter mais informações sobre a gestão de um Azure Key Vault, consulte [configurar um cofre de chaves para máquinas virtuais](key-vault-setup.md).
* Para obter mais informações sobre a encriptação de disco, como preparar uma VM encriptada personalizada para carregar para o Azure, consulte [do Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
