---
title: Criptografe discos num VM do Windows em Azure
description: Criptografe discos virtuais num VM do Windows para maior segurança utilizando o Azure PowerShell
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033534"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>Criptografe discos virtuais num VM do Windows
Para uma maior segurança e conformidade da máquina virtual (VM), os discos virtuais em Azure podem ser encriptados. Os discos são encriptados utilizando chaves criptográficas que estão protegidas num Cofre de Chaves Azure. Controla estas chaves criptográficas e pode auditar o seu uso. Este artigo descreve como encriptar discos virtuais num VM do Windows utilizando o Azure PowerShell. Também pode [encriptar máquinas virtuais Linux.](../linux/disk-encryption-overview.md)

 

## <a name="overview-of-disk-encryption"></a>Visão geral da encriptação do disco
Os discos virtuais nos VMs do Windows são encriptados em repouso utilizando o BitLocker. Não há nenhuma acusação para encriptar discos virtuais em Azure. As chaves criptográficas são armazenadas num Cofre de Chave Azure utilizando a proteção de software, ou pode importar ou gerar as suas chaves em Módulos de Segurança de Hardware (HSMs) certificados para os padrões de nível 2 do FIPS 140-2. As teclas criptográficas são usadas para encriptar e desencriptar discos virtuais ligados ao seu VM. Mantém o controlo destas chaves criptográficas e pode auditar o seu uso. 

O processo de encriptação de um VM é o seguinte:

1. Crie uma chave criptográfica num Cofre de Chaves Azure.
1. Configure a chave criptográfica para ser utilizável para encriptar discos.
1. Ative a encriptação do disco para os seus discos virtuais.
1. As chaves criptográficas necessárias são solicitadas do Cofre chave Azure.
1. Os discos virtuais são encriptados utilizando a chave criptográfica fornecida.


## <a name="requirements-and-limitations"></a>Requisitos e limitações

Cenários e requisitos suportados para encriptação de disco:

* Ativar a encriptação em novos VMs do Windows a partir de imagens do Azure Marketplace ou imagens VHD personalizadas.
* Ativar a encriptação dos VMs do Windows existentes no Azure.
* Ativar a encriptação nos VMs do Windows que são configurados utilizando espaços de armazenamento.
* Desativar a encriptação no OS e unidades de dados para VMs do Windows.
* VMs de nível padrão, tais como VMs séries A, D, DS, G e GS.

    > [!NOTE]
    > Todos os recursos (incluindo o Cofre Chave, conta de armazenamento e VM) devem estar na mesma região azure e subscrição.

A encriptação do disco não é suportada nos seguintes cenários:

* VMs de nível básico.
* VMs criados usando o modelo de implementação Clássico.
* Atualizar as teclas criptográficas num VM já encriptado.
* Integração com o Serviço de Gestão de Chaves no local.


## <a name="create-an-azure-key-vault-and-keys"></a>Crie um cofre de chaves azure e chaves
Antes de começar, certifique-se de que a versão mais recente do módulo PowerShell Azure foi instalada. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview). Nos seguintes exemplos de comando, substitua todos os parâmetros de exemplo pelos seus próprios nomes, localização e valores-chave, tais como *myResourceGroup,* *myKeyVault,* *myVM*, e assim por diante.

O primeiro passo é criar um Cofre chave Azure para armazenar as suas chaves criptográficas. Os Cofres Chave Azure podem armazenar chaves, segredos ou senhas que lhe permitam implementá-las de forma segura nas suas aplicações e serviços. Para encriptação de discos virtuais, irá criar um Cofre chave para armazenar uma chave criptográfica que é usada para encriptar ou desencriptar os seus discos virtuais. 

Ative o fornecedor de cofre sinuoso azure dentro da sua subscrição Azure com [o Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)e, em seguida, crie um grupo de recursos com o [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). O exemplo seguinte cria um nome de grupo de recursos *myResourceGroup* na localização *dos EUA Orientais:*

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzResourceGroup -Location $location -Name $rgName
```

O Cofre chave Azure que detém as chaves criptográficas e os recursos computacionais associados, como o armazenamento e o próprio VM, devem estar todos na mesma região. Crie um Cofre de Chave Azure com [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) e ative o Cofre chave para utilização com encriptação de disco. Especifique um nome exclusivo do Cofre de Chaves para *o nome do keyVault da* seguinte forma:

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Pode armazenar chaves criptográficas utilizando a proteção do software ou do Hardware Security Model (HSM).  Um Cofre Chave padrão só armazena chaves protegidas por software. A utilização de um HSM requer um cofre de chave premium a um custo adicional. Para criar um Cofre chave premium, no passo anterior adicione o parâmetro *"Premium" Sku.* O exemplo que se segue utiliza chaves protegidas por software desde que criámos um Key Vault padrão. 

Para ambos os modelos de proteção, a plataforma Azure precisa de ter acesso para solicitar as teclas criptográficas quando as botas VM desencriptam os discos virtuais. Crie uma chave criptográfica no seu Cofre chave com [Add-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey). O exemplo seguinte cria uma chave chamada *myKey:*

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Para testar o processo de encriptação, crie um VM com [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). O exemplo seguinte cria um VM chamado *myVM* usando uma imagem *do Datacenter do Windows Server 2016.* Quando solicitado para obter credenciais, introduza o nome de utilizador e a palavra-passe a utilizar para o seu VM:

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


## <a name="encrypt-a-virtual-machine"></a>Criptografe uma máquina virtual
Criptografie o seu VM com a extensão de [encriptação set-AzVMDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) utilizando a chave Do cofre de chaves Azure. O exemplo seguinte recupera todas as informações chave e depois encripta o VM chamado *myVM:*

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

Aceite a solicitação para continuar com a encriptação VM. O VM reinicia durante o processo. Assim que o processo de encriptação estiver concluído e o VM tiver reiniciado, reveja o estado de encriptação com O Estatuto de [Encriptação Get-AzVmDisk:](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus)

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
* Para obter mais informações sobre a gestão de um Cofre de Chaves Azure, consulte [Aconfiguração de um Cofre chave para máquinas virtuais](key-vault-setup.md).
* Para obter mais informações sobre encriptação de disco, como preparar um VM personalizado encriptado para fazer o upload para o Azure, consulte a [Encriptação do Disco Azure](../../security/fundamentals/encryption-overview.md).
