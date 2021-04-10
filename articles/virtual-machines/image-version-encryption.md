---
title: Pré-visualização - Crie uma versão de imagem encriptada com as suas próprias teclas
description: Crie uma versão de imagem numa galeria de imagens partilhada, utilizando chaves de encriptação geridas pelo cliente.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/3/2020
ms.author: cynthn
ms.openlocfilehash: 258d8ab6ab23a95d73b8ed0c2549f373cf097674
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554093"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Pré-visualização: Utilize chaves geridas pelo cliente para encriptar imagens

As imagens numa galeria de imagens partilhadas são armazenadas como imagens, pelo que são automaticamente encriptadas através da encriptação do lado do servidor. A encriptação do lado do servidor utiliza [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits , uma das cifras de blocos mais fortes disponíveis. A encriptação do lado do servidor também está em conformidade com o FIPS 140-2. Para obter mais informações sobre os módulos criptográficos subjacentes aos discos geridos pela Azure, consulte [Cryptography API: Next Generation](/windows/desktop/seccng/cng-portal).

Pode confiar em chaves geridas pela plataforma para a encriptação das suas imagens ou usar as suas próprias chaves. Também pode usar os dois juntos, para dupla encriptação. Se optar por gerir a encriptação com as suas próprias chaves, pode especificar uma *chave gerida pelo cliente* para encriptar e desencriptar todos os discos nas suas imagens. 

A encriptação do lado do servidor através de teclas geridas pelo cliente utiliza o Cofre da Chave Azure. Pode importar [as chaves RSA](../key-vault/keys/hsm-protected-keys.md) para o cofre ou gerar novas chaves RSA no Cofre da Chave Azure.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo requer que já tenha uma encriptação de disco definida em cada região onde pretende replicar a sua imagem:

- Para utilizar apenas uma chave gerida pelo cliente, consulte os artigos sobre como permitir as chaves geridas pelo cliente com encriptação do lado do servidor utilizando o [portal Azure](./disks-enable-customer-managed-keys-portal.md) ou [o PowerShell](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-without-automatic-key-rotation).

- Para utilizar as teclas geridas pela plataforma e geridas pelo cliente (para dupla encriptação), consulte os artigos sobre como permitir a dupla encriptação em repouso utilizando o [portal Azure](./disks-enable-double-encryption-at-rest-portal.md) ou [PowerShell](./windows/disks-enable-double-encryption-at-rest-powershell.md).

   > [!IMPORTANT]
   > Tem de utilizar o link [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) para aceder ao portal Azure. A dupla encriptação em repouso não é atualmente visível no portal público Azure, a menos que utilize esse link.

## <a name="limitations"></a>Limitações

Quando está a usar chaves geridas pelo cliente para encriptar imagens numa galeria de imagens partilhada, estas limitações aplicam-se:   

- Os conjuntos de chaves de encriptação devem estar na mesma subscrição que a sua imagem.

- Os conjuntos-chave de encriptação são recursos regionais, por isso cada região requer um conjunto de chaves de encriptação diferente.

- Não é possível copiar ou partilhar imagens que utilizem chaves geridas pelo cliente. 

- Depois de ter usado as suas próprias chaves para encriptar um disco ou imagem, não pode voltar a usar chaves geridas pela plataforma para encriptar esses discos ou imagens.


> [!IMPORTANT]
> A encriptação através de chaves geridas pelo cliente está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="powershell"></a>PowerShell

Para a pré-visualização pública, primeiro tem de registar a funcionalidade:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Leva alguns minutos para a inscrição terminar. Utilize `Get-AzProviderFeature` para verificar o estado do registo da funcionalidade:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Quando `RegistrationState` `Registered` regressar, pode passar ao passo seguinte.

Verifique o registo do seu fornecedor. Certifique-se de que `Registered` regressa.

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Se não `Registered` regressar, utilize o seguinte código para registar os fornecedores:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Para especificar um conjunto de encriptação de disco para uma versão de imagem, utilize  [a Definição de Imagem New-AzGallery](/powershell/module/az.compute/new-azgalleryimageversion) com o `-TargetRegion` parâmetro: 

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

Pode criar uma máquina virtual (VM) a partir de uma galeria de imagens partilhada e utilizar chaves geridas pelo cliente para encriptar os discos. A sintaxe é o mesmo que criar um VM [generalizado](vm-generalized-image-version-powershell.md) ou [especializado a](vm-specialized-image-version-powershell.md) partir de uma imagem. Utilize o conjunto de parâmetros estendidos e adicione `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` à configuração VM.

Para discos de dados, adicione o `-DiskEncryptionSetId $setID` parâmetro quando utilizar o [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk).


## <a name="cli"></a>CLI 

Para a pré-visualização pública, tem primeiro de se registar para a funcionalidade. As inscrições demoram cerca de 30 minutos.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

Verifique o estado do registo da funcionalidade:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

Quando este código `"state": "Registered"` voltar, pode passar para o próximo passo.

Verifique o seu registo:

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Se não disser registado, executar o seguinte comando:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


Para especificar um conjunto de encriptação de disco para uma versão de imagem, utilize [a az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create) com o `--target-region-encryption` parâmetro. O formato é uma lista de chaves separadas por `--target-region-encryption` vírgulas para encriptar o SISTEMA e os discos de dados. Devia ser assim: `<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>` . 

Se a fonte do disco DE for um disco gerido ou um VM, utilize `--managed-image` para especificar a fonte para a versão de imagem. Neste exemplo, a fonte é uma imagem gerida que tem um disco DE E e um disco de dados na LUN 0. O disco OS será encriptado com DiskEncryptionSet1 e o disco de dados será encriptado com DiskEncryptionSet2.

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

Se a fonte do disco de so é uma imagem instantânea, utilize `--os-snapshot` para especificar o disco de so. Se houver instantâneos de disco de dados que também devam fazer parte da versão de imagem, adicione-os. Utilize `--data-snapshot-luns` para especificar o LUN e use `--data-snapshots` para especificar as imagens.

Neste exemplo, as fontes são imagens de disco. Há um disco de soss e um disco de dados na LUN 0. O disco OS será encriptado com DiskEncryptionSet1 e o disco de dados será encriptado com DiskEncryptionSet2.

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

Pode criar um VM a partir de uma galeria de imagens partilhada e utilizar chaves geridas pelo cliente para encriptar os discos. A sintaxe é o mesmo que criar um VM [generalizado](vm-generalized-image-version-cli.md) ou [especializado a](vm-specialized-image-version-cli.md) partir de uma imagem. Basta adicionar o `--os-disk-encryption-set` parâmetro com o ID do conjunto de encriptação. Para os discos de dados, adicione `--data-disk-encryption-sets` uma lista delimitadas pelo espaço dos conjuntos de encriptação do disco para os discos de dados.


## <a name="portal"></a>Portal

Quando criar a sua versão de imagem no portal, pode utilizar o **separador Encriptação** para aplicar os seus conjuntos de encriptação de armazenamento.

> [!IMPORTANT]
> Para utilizar a dupla encriptação, tem de utilizar o link [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) para aceder ao portal Azure. A dupla encriptação em repouso não é atualmente visível no portal público Azure, a menos que utilize esse link.


1. Na página **'Criar uma versão de imagem',** selecione o **separador Encriptação.**
2. No **tipo de encriptação**, selecione **Encriptação em repouso com uma chave gerida pelo cliente** ou **encriptação dupla com teclas geridas pela plataforma e geridas pelo cliente**. 
3. Para cada disco na imagem, selecione um conjunto de encriptação da lista de definição de encriptação do **disco.** 

### <a name="create-the-vm"></a>Criar a VM

Pode criar um VM a partir de uma versão de imagem e utilizar chaves geridas pelo cliente para encriptar os discos. Quando criar o VM no portal, no separador **Discos,** selecione **encriptação em repouso com teclas geridas pelo cliente** ou **encriptação dupla com chaves geridas pela plataforma e geridas pelo cliente** para o tipo de **encriptação**. Em seguida, pode selecionar o conjunto de encriptação da lista de drop-down.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a encriptação do disco do lado do servidor.](./disk-encryption.md)

Para obter informações sobre como fornecer informações sobre o plano de compra, consulte [as informações do plano de compra do Supply Azure Marketplace ao criar imagens.](marketplace-images.md)