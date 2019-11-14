---
title: Criptografar discos em uma VM do Windows no Azure
description: Criptografar discos virtuais em uma VM do Windows para aumentar a segurança usando Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: 096fe779c077424b01df3ead5965cb799866a03b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033534"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>Criptografar discos virtuais em uma VM do Windows
Para segurança e conformidade aprimoradas da VM (máquina virtual), os discos virtuais no Azure podem ser criptografados. Os discos são criptografados usando chaves de criptografia que são protegidas em um Azure Key Vault. Você controla essas chaves de criptografia e pode auditar seu uso. Este artigo descreve como criptografar discos virtuais em uma VM do Windows usando Azure PowerShell. Você também pode [criptografar máquinas virtuais do Linux](../linux/disk-encryption-overview.md).

 

## <a name="overview-of-disk-encryption"></a>Visão geral da criptografia de disco
Os discos virtuais em VMs do Windows são criptografados em repouso usando o BitLocker. Não há nenhuma cobrança pela criptografia de discos virtuais no Azure. As chaves de criptografia são armazenadas em um Azure Key Vault usando a proteção de software ou você pode importar ou gerar suas chaves em HSMs (módulos de segurança de hardware) certificados para os padrões FIPS 140-2 nível 2. As chaves de criptografia são usadas para criptografar e descriptografar discos virtuais anexados à sua VM. Você mantém o controle dessas chaves de criptografia e pode auditar seu uso. 

O processo de criptografia de uma VM é o seguinte:

1. Crie uma chave de criptografia em um Azure Key Vault.
1. Configure a chave de criptografia para ser usada para criptografar discos.
1. Habilite a criptografia de disco para seus discos virtuais.
1. As chaves de criptografia necessárias são solicitadas de Azure Key Vault.
1. Os discos virtuais são criptografados usando a chave de criptografia fornecida.


## <a name="requirements-and-limitations"></a>Requisitos e limitações

Cenários e requisitos com suporte para criptografia de disco:

* Habilitando a criptografia em novas VMs do Windows de imagens do Azure Marketplace ou imagens VHD personalizadas.
* Habilitando a criptografia em VMs do Windows existentes no Azure.
* Habilitando a criptografia em VMs do Windows configuradas usando espaços de armazenamento.
* Desabilitar a criptografia no sistema operacional e nas unidades de dados para VMs do Windows.
* VMs da camada Standard, como VMs da série A, D, DS, G e GS.

    > [!NOTE]
    > Todos os recursos (incluindo o Key Vault, a conta de armazenamento e a VM) devem estar na mesma região e assinatura do Azure.

Atualmente, a criptografia de disco não tem suporte nos seguintes cenários:

* VMs de camada básica.
* VMs criadas usando o modelo de implantação clássico.
* Atualizando as chaves de criptografia em uma VM já criptografada.
* Integração com o serviço de gerenciamento de chaves local.


## <a name="create-an-azure-key-vault-and-keys"></a>Criar uma Azure Key Vault e chaves
Antes de começar, verifique se a versão mais recente do módulo Azure PowerShell foi instalada. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview). Nos exemplos de comando a seguir, substitua todos os parâmetros de exemplo pelos seus próprios nomes, local e valores de chave, como *MyResource*, *myKeyVault*, *myVM*e assim por diante.

A primeira etapa é criar um Azure Key Vault para armazenar suas chaves de criptografia. Os Azure Key Vaults podem armazenar chaves, segredos ou senhas que permitem implementá-los com segurança em seus aplicativos e serviços. Para a criptografia de disco virtual, você criará um Key Vault para armazenar uma chave de criptografia que é usada para criptografar ou descriptografar seus discos virtuais. 

Habilite o provedor de Azure Key Vault em sua assinatura do Azure com [Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)e crie um grupo de recursos com [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). O exemplo a seguir cria um nome de grupo de recursos *MyResource* Group no local *leste dos EUA* :

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzResourceGroup -Location $location -Name $rgName
```

A Azure Key Vault que mantém as chaves criptográficas e os recursos de computação associados como armazenamento e a própria VM devem estar na mesma região. Crie um Azure Key Vault com [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) e habilite o Key Vault para uso com a criptografia de disco. Especifique um nome de Key Vault exclusivo para *Keyvaultname* da seguinte maneira:

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Você pode armazenar chaves criptográficas usando o software ou a proteção de HSM (modelo de segurança de hardware).  Um Key Vault padrão só armazena chaves protegidas por software. O uso de um HSM exige um Key Vault Premium a um custo adicional. Para criar um Key Vault Premium, na etapa anterior, adicione o parâmetro *-SKU "Premium"* . O exemplo a seguir usa chaves protegidas por software, pois criamos um Key Vault padrão. 

Para ambos os modelos de proteção, a plataforma Azure precisa receber acesso para solicitar as chaves criptográficas quando a VM é inicializada para descriptografar os discos virtuais. Crie uma chave de criptografia em seu Key Vault com [Add-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey). O exemplo a seguir cria uma chave chamada *MyKey*:

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Para testar o processo de criptografia, crie uma VM com [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). O exemplo a seguir cria uma VM chamada *myVM* usando uma imagem *do Windows Server 2016 datacenter* . Quando solicitado a fornecer credenciais, insira o nome de usuário e a senha a serem usados para sua VM:

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


## <a name="encrypt-a-virtual-machine"></a>Criptografar uma máquina virtual
Criptografe sua VM com [set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) usando a chave de Azure Key Vault. O exemplo a seguir recupera todas as informações de chave e criptografa a VM denominada *myVM*:

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

Aceite o prompt para continuar com a criptografia de VM. A VM é reiniciada durante o processo. Depois que o processo de criptografia for concluído e a VM for reinicializada, examine o status de criptografia com [Get-AzVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus):

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

## <a name="next-steps"></a>Passos seguintes
* Para obter mais informações sobre como gerenciar um Azure Key Vault, consulte [configurar um Key Vault para máquinas virtuais](key-vault-setup.md).
* Para obter mais informações sobre a criptografia de disco, como preparar uma VM personalizada criptografada para carregar no Azure, consulte [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md).
