---
title: Pré-visualização - Crie uma versão de imagem encriptada com as suas próprias teclas
description: Crie uma versão de imagem numa Galeria de Imagem Partilhada, utilizando chaves de encriptação geridas pelo cliente.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/06/2020
ms.author: cynthn
ms.openlocfilehash: aeacfdc07e5349dfce45b209da1d78bddf870f33
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83269585"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Pré-visualização: Utilize as chaves geridas pelo cliente para encriptar imagens

As imagens da galeria são armazenadas como discos geridos, pelo que são automaticamente encriptadas utilizando encriptação do lado do servidor. A encriptação do lado do servidor utiliza [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits, uma das cifras de blocomais fortes disponíveis, e é compatível com o FIPS 140-2. Para obter mais informações sobre os módulos criptográficos subjacentes aos discos geridos pelo Azure, consulte [Cryptography API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

Pode confiar em chaves geridas pela plataforma para a encriptação das suas imagens, ou pode gerir a encriptação usando as suas próprias chaves. Se optar por gerir a encriptação com as suas próprias chaves, pode especificar uma *chave gerida pelo cliente* para encriptar e desencriptar todos os discos nas suas imagens. 

A encriptação do lado do servidor utilizando chaves geridas pelo cliente utiliza o Cofre de Chaves Azure. Pode importar [as suas chaves RSA](../key-vault/keys/hsm-protected-keys.md) para o seu Cofre chave ou gerar novas chaves RSA no Cofre de Chaves Azure.

Para utilizar as chaves geridas pelo cliente para imagens, precisa primeiro de um Cofre de Chave Azure. Em seguida, cria-se um conjunto de encriptação de disco. O conjunto de encriptação do disco é então utilizado ao criar versões de imagem.

Para obter mais informações sobre a criação e utilização de conjuntos de encriptação de discos, consulte [as chaves geridas pelo Cliente](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys).

## <a name="limitations"></a>Limitações

Existem várias limitações ao utilizar chaves geridas pelo cliente para encriptar imagens de galerias de imagens partilhadas:  

- Os conjuntos de chaves de encriptação devem estar na mesma subscrição e região que a sua imagem.

- Não é possível partilhar imagens que utilizem chaves geridas pelo cliente. 

- Não é possível replicar imagens que utilizam chaves geridas pelo cliente para outras regiões.

- Depois de ter usado as suas próprias chaves para encriptar um disco ou imagem, não pode voltar a utilizar chaves geridas pela plataforma para encriptar esses discos ou imagens.


> [!IMPORTANT]
> A encriptação utilizando chaves geridas pelo cliente está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .


## <a name="powershell"></a>PowerShell

Para a pré-visualização pública, primeiro é necessário registar a funcionalidade.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Leva alguns minutos para a inscrição ser completada. Utilize o Get-AzProviderFeature para verificar o estado do registo da funcionalidade.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Quando o RegistrationState regressar Registado, pode passar ao próximo passo.

Verifique o registo do seu fornecedor. Certifique-se de que `Registered` volta.

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Se não `Registered` devolver, utilize o seguinte para registar os fornecedores:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Para especificar um conjunto de encriptação de disco para uma versão de imagem, utilize a [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion) com o `-TargetRegion` parâmetro. 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$targetRegion = @{$region1}


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

Pode criar um VM a partir de uma galeria de imagens partilhada e utilizar chaves geridas pelo cliente para encriptar os discos. A sintaxe é a mesma que criar um VM [generalizado](vm-generalized-image-version-powershell.md) ou [especializado](vm-specialized-image-version-powershell.md) a partir de uma imagem, você precisa usar o conjunto de parâmetros estendido e adicionar `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` à configuração VM.

Para discos de dados, tem de adicionar o `-DiskEncryptionSetId $setID` parâmetro quando utilizar o [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk).


## <a name="cli"></a>CLI 

Para a pré-visualização pública, primeiro é necessário registar a funcionalidade.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

Verifique o estado do registo da funcionalidade.

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

Quando isto `"state": "Registered"` voltar, pode passar para o próximo passo.

Verifique o seu registo.

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Se não disser que está registado, execute o seguinte:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


Para especificar uma encriptação de disco definida para uma versão de imagem, utilize a [imagem-versão da galeria de imagens az](/cli/azure/sig/image-version#az-sig-image-version-create) com o `--target-region-encryption` parâmetro. O formato para é uma lista de chaves separadas pelo espaço para encriptar o SISTEMA e os discos de `--target-region-encryption` dados. Deve ser assim: `<encryption set for the OS disk>,<Lun number of the data disk>, <encryption set for the data disk>, <Lun number for the second data disk>, <encryption set for the second data disk>` . 

Se a fonte do disco OS for um disco gerido ou um VM, utilize para especificar a fonte para a versão de `--managed-image` imagem. Neste exemplo, a fonte é uma imagem gerida que tem um disco DE SO, bem como um disco de dados no LUN 0. O disco OS será encriptado com DiskCryptonSet1 e o disco de dados será encriptado com DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --target-regions westus=2=standard_lrs \
   --target-region-encryption DiskEncryptionSet1,0,DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

Se a fonte do disco OS for instantânea, utilize `--os-snapshot` para especificar o disco OS. Se existirem instantâneos de disco de dados que também deverão fazer parte da versão da imagem, adicione os que usam `--data-snapshot-luns` para especificar o LUN e `--data-snapshots` especifique as imagens.

Neste exemplo, as fontes são instantâneos de disco. Há um disco de OS, e também um disco de dados no LUN 0. O disco OS será encriptado com DiskCryptonSet1 e o disco de dados será encriptado com DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --target-regions westus=2=standard_lrs \
   --target-region-encryption DiskEncryptionSet1,0,DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot"
   --data-snapshot-luns 0
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot"
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>Crie a VM

Pode criar um VM a partir de uma galeria de imagens partilhada e utilizar chaves geridas pelo cliente para encriptar os discos. A sintaxe é a mesma que criar um VM [generalizado](vm-generalized-image-version-cli.md) ou [especializado](vm-specialized-image-version-cli.md) a partir de uma imagem, basta adicionar o `--os-disk-encryption-set` parâmetro com a identificação do conjunto de encriptação. Para discos de dados, adicione `--data-disk-encryption-sets` com uma lista delimitada de espaço dos conjuntos de encriptação do disco para os discos de dados.


## <a name="portal"></a>Portal

Quando criar a sua versão de imagem no portal, pode utilizar o **separador Encriptação** para introduzir as informações sobre os seus conjuntos de encriptação de armazenamento.

1. Na página Criar uma versão de **imagem,** selecione o **separador Encriptação.**
2. No **tipo de encriptação,** selecione **Encriptação em repouso com uma chave gerida pelo cliente**. 
3. Para cada disco na imagem, selecione o conjunto de **encriptação do Disco** para utilizar a partir da queda. 

### <a name="create-the-vm"></a>Crie a VM

Pode criar um VM a partir de uma galeria de imagens partilhada e utilizar chaves geridas pelo cliente para encriptar os discos. Quando criar o VM no portal, no separador **Disks,** selecione **Encriptação em repouso com chaves geridas pelo cliente** para o **tipo de encriptação**. Em seguida, pode selecionar o conjunto de encriptação a partir da queda.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [encriptação de disco do lado do servidor](/windows/disk-encryption.md)).
