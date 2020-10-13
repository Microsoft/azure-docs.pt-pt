---
title: Pré-visualização - Crie uma versão de imagem encriptada com as suas próprias teclas
description: Crie uma versão de imagem numa Galeria de Imagens Partilhadas, utilizando chaves de encriptação geridas pelo cliente.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/12/2020
ms.author: cynthn
ms.openlocfilehash: dd2d42bb83afa5a97bd5bd71d7b1a4bcc506d93e
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91976169"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Pré-visualização: Utilize chaves geridas pelo cliente para encriptar imagens

As imagens da galeria são armazenadas como discos geridos, pelo que são automaticamente encriptadas utilizando encriptação do lado do servidor. A encriptação do lado do servidor utiliza [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits , uma das cifras de blocos mais fortes disponíveis, e é compatível com FIPS 140-2. Para obter mais informações sobre os módulos criptográficos subjacentes aos discos geridos pelo Azure, consulte [Cryptography API: Next Generation](/windows/desktop/seccng/cng-portal)

Pode confiar em chaves geridas pela plataforma para a encriptação das suas imagens, usar as suas próprias chaves, ou pode usar as duas em conjunto, para uma dupla encriptação. Se optar por gerir a encriptação com as suas próprias chaves, pode especificar uma *chave gerida pelo cliente* para encriptar e desencriptar todos os discos nas suas imagens. 

A encriptação do lado do servidor utilizando teclas geridas pelo cliente utiliza o Cofre da Chave Azure. Pode importar [as chaves RSA](../key-vault/keys/hsm-protected-keys.md) para o cofre de chaves ou gerar novas chaves RSA no Cofre da Chave Azure.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo requer que já tenha um conjunto de encriptação de disco para usar para a sua imagem.

- Para utilizar apenas uma chave gerida pelo cliente, consulte **Ativar as teclas geridas pelo cliente com encriptação do lado** do servidor utilizando o [portal Azure](./disks-enable-customer-managed-keys-portal.md) ou [PowerShell](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-your-azure-key-vault-and-diskencryptionset).

- Para utilizar as teclas geridas pela plataforma e geridas pelo cliente (para dupla encriptação), consulte **Ativar a dupla encriptação em repouso** utilizando o [portal Azure](./disks-enable-double-encryption-at-rest-portal.md) ou [PowerShell](./windows/disks-enable-double-encryption-at-rest-powershell.md).
    > [!IMPORTANT]
    > Tem de utilizar este link [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) para aceder ao portal Azure. A dupla encriptação em repouso não é atualmente visível no portal público Azure sem utilizar o link.

## <a name="limitations"></a>Limitações

Existem várias limitações ao utilizar as chaves geridas pelo cliente para encriptar imagens de galerias de imagens partilhadas:  

- Os conjuntos de chaves de encriptação devem estar na mesma subscrição que a sua imagem.

- Os conjuntos de chaves de encriptação são recursos regionais, pelo que cada região requer um conjunto de chaves de encriptação diferente.

- Não é possível partilhar imagens que utilizem as chaves geridas pelo cliente. 

- Uma vez que tenha usado as suas próprias chaves para encriptar um disco ou imagem, não pode voltar a usar chaves geridas pela plataforma para encriptar esses discos ou imagens.


> [!IMPORTANT]
> A encriptação utilizando chaves geridas pelo cliente está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="powershell"></a>PowerShell

Para a pré-visualização pública, primeiro tem de registar a funcionalidade.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Leva alguns minutos para a inscrição ser concluída. Utilize Get-AzProviderFeature para verificar o estado do registo de funcionalidades.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Quando o Estado de Registo retornar registado, pode passar ao passo seguinte.

Verifique o registo do seu fornecedor. Certifique-se de que `Registered` regressa.

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Se não `Registered` regressar, utilize o seguinte para registar os fornecedores:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Para especificar um conjunto de encriptação de disco para uma versão de imagem, utilize  [a Definição de Imagem New-AzGallery](/powershell/module/az.compute/new-azgalleryimageversion) com o `-TargetRegion` parâmetro. 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$eastUS2osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet'}

$eastUS2dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet1';Lun=1}

$eastUS2dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet2';Lun=2}

$eastUS2DataDiskImageEncryptions = @($eastUS2dataDiskImageEncryption1,$eastUS2dataDiskImageEncryption2)

$encryption2 = @{OSDiskImage=$eastUS2osDiskImageEncryption;DataDiskImages=$eastUS2DataDiskImageEncryptions}

$region2 = @{Name='East US 2';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption2}

$targetRegion = @($region1, $region2)


# Create the image
New-AzGalleryImageVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryName `
   -GalleryImageDefinitionName $imageDefinitionName `
   -Name $versionName -Location $location `
   -SourceImageId $sourceId `
   -ReplicaCount 2 `
   -StorageAccountType Standard_LRS `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -TargetRegion $targetRegion
```

### <a name="create-a-vm"></a>Criar uma VM

Pode criar um VM a partir de uma galeria de imagens partilhada e utilizar chaves geridas pelo cliente para encriptar os discos. A sintaxe é o mesmo que criar um VM [generalizado](vm-generalized-image-version-powershell.md) ou [especializado](vm-specialized-image-version-powershell.md) a partir de uma imagem, você precisa usar o conjunto de parâmetros estendidos e adicionar `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` à configuração VM.

Para os discos de dados, é necessário adicionar o `-DiskEncryptionSetId $setID` parâmetro quando utilizar o [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk).


## <a name="cli"></a>CLI 

Para a pré-visualização pública, primeiro tem de registar a funcionalidade.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

Verifique o estado do registo de funcionalidades.

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

Quando isto `"state": "Registered"` voltar, pode passar ao passo seguinte.

Verifique o seu registo.

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Se não disser registado, corra o seguinte:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


Para especificar um conjunto de encriptação de disco para uma versão de imagem, utilize  [a az image gallery criar versão de imagem](/cli/azure/sig/image-version#az-sig-image-version-create) com o `--target-region-encryption` parâmetro. O formato é uma lista de chaves separadas por `--target-region-encryption` vírgulas para encriptar o SISTEMA e os discos de dados. Devia ser assim: `<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>` . 

Se a fonte do disco DE for um disco gerido ou um VM, utilize `--managed-image` para especificar a fonte para a versão de imagem. Neste exemplo, a fonte é uma imagem gerida que tem um disco DE, bem como um disco de dados na LUN 0. O disco OS será encriptado com DiskEncryptionSet1 e o disco de dados será encriptado com DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus \
   --target-regions westus=2=standard_lrs eastus2 \
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

Se a fonte do disco de so é uma imagem instantânea, utilize `--os-snapshot` para especificar o disco de so. Se existirem instantâneos de disco de dados que também devem fazer parte da versão de imagem, adicione os que usam `--data-snapshot-luns` para especificar o LUN e `--data-snapshots` especificar os instantâneos.

Neste exemplo, as fontes são imagens de disco. Há um disco de SO, e também um disco de dados na LUN 0. O disco OS será encriptado com DiskEncryptionSet1 e o disco de dados será encriptado com DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus\
   --target-regions westus=2=standard_lrs eastus\
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot" \
   --data-snapshot-luns 0 \
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot" \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>Criar a VM

Pode criar um VM a partir de uma galeria de imagens partilhada e utilizar chaves geridas pelo cliente para encriptar os discos. A sintaxe é o mesmo que criar um VM [generalizado](vm-generalized-image-version-cli.md) ou [especializado](vm-specialized-image-version-cli.md) a partir de uma imagem, basta adicionar o `--os-disk-encryption-set` parâmetro com o ID do conjunto de encriptação. Para os discos de dados, adicione `--data-disk-encryption-sets` com uma lista delimitada do espaço dos conjuntos de encriptação do disco para os discos de dados.


## <a name="portal"></a>Portal

Quando criar a sua versão de imagem no portal, pode utilizar o **separador Encriptação** para introduzir os seus conjuntos de encriptação de armazenamento.

> [!IMPORTANT]
> Para utilizar a dupla encriptação, tem de utilizar este link [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) para aceder ao portal Azure. A dupla encriptação em repouso não é atualmente visível no portal público Azure sem utilizar o link.


1. Na página **'Criar uma versão de imagem',** selecione o **separador Encriptação.**
2. No **tipo de encriptação**, selecione **Encriptação em repouso com uma chave gerida pelo cliente** ou **encriptação dupla com teclas geridas pela plataforma e geridas pelo cliente**. 
3. Para cada disco na imagem, selecione o conjunto de **encriptação** do disco para utilizar a partir do drop-down. 

### <a name="create-the-vm"></a>Criar a VM

Pode criar um VM a partir de uma versão de imagem e utilizar chaves geridas pelo cliente para encriptar os discos. Quando criar o VM no portal, no separador **Discos,** selecione **encriptação em repouso com teclas geridas pelo cliente** ou **encriptação dupla com chaves geridas pela plataforma e geridas pelo cliente** para o tipo de **encriptação**. Em seguida, pode selecionar o conjunto de encriptação a partir do drop-down.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a encriptação do disco do lado do servidor.](./windows/disk-encryption.md)

Para obter informações sobre como fornecer informações sobre o plano de compra, consulte [as informações do plano de compra do Supply Azure Marketplace ao criar imagens.](marketplace-images.md)
