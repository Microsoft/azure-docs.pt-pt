---
title: Criar uma imagem a partir de uma VM
description: Aprenda a criar uma imagem numa Galeria de Imagens Partilhadas a partir de um VM em Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: b8aa2623722b5e75480e68324d76e4a1493501df
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685145"
---
# <a name="create-an-image-version-from-a-vm-in-azure-using-the-azure-cli"></a>Criar uma versão de imagem a partir de um VM em Azure usando o Azure CLI

Se tiver um VM existente que gostaria de usar para fazer VMs múltiplos e idênticos, pode usar esse VM para criar uma imagem numa Galeria de Imagens Partilhadas utilizando o CLI Azure. Também pode criar uma imagem a partir de um VM utilizando [a Azure PowerShell](image-version-vm-powershell.md).

Uma **versão de imagem** é o que se usa para criar um VM quando se utiliza uma Galeria de Imagens Partilhadas. Pode ter várias versões de uma imagem necessária para o seu ambiente. Quando se utiliza uma versão de imagem para criar um VM, a versão de imagem é usada para criar discos para o novo VM. As versões de imagem podem ser usadas várias vezes.


## <a name="before-you-begin"></a>Before you begin

Para completar este artigo, você deve ter uma Galeria de Imagem Partilhada existente. 

Você também deve ter um VM existente em Azure, na mesma região que a sua galeria. 

Se o VM tiver um disco de dados anexado, o tamanho do disco de dados não pode ser superior a 1 TB.

Ao trabalhar neste artigo, substitua os nomes dos recursos sempre que necessário.

## <a name="get-information-about-the-vm"></a>Obtenha informações sobre o VM

Pode ver uma lista de VMs que estão disponíveis usando [a lista az vm](/cli/azure/vm#az-vm-list). 

```azurecli-interactive
az vm list --output table
```

Assim que conhecer o nome VM e em que grupo de recursos se encontra, obtenha o ID do VM utilizando [a az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view). 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```


## <a name="create-an-image-definition"></a>Criar uma definição de imagem

As definições de imagem criam um agrupamento lógico para imagens. São utilizados para gerir informações sobre as versões de imagem que são criadas dentro delas. 

Os nomes da definição de imagem podem ser compostos por letras maiúsculas ou minúsculas, dígitos, pontos, traços e períodos. 

Certifique-se de que a definição de imagem é o tipo certo. Se generalizou o VM (utilizando o Sysprep para Windows, ou desprovisionamento waagent para o Linux), então deve criar uma definição de imagem generalizada utilizando `--os-state generalized` . Se pretender utilizar o VM sem remover as contas de utilizador existentes, crie uma definição de imagem especializada utilizando `--os-state specialized` .

Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, consulte [definições de imagem](./shared-image-galleries.md#image-definitions).

Crie uma definição de imagem na galeria utilizando [a az sig definição de imagem criar](/cli/azure/sig/image-definition#az-sig-image-definition-create).

Neste exemplo, a definição de imagem chama-se *myImageDefinition*, e [destina-se a](./shared-image-galleries.md#generalized-and-specialized-images) uma imagem especializada do Linux OS. Para criar uma definição para imagens que utilizem um SISTEMA Windows, utilize `--os-type Windows` . 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>Criar a versão de imagem

Crie uma versão de imagem a partir do VM utilizando [a az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create).  

Os caracteres permitidos para a versão de imagem são números e períodos. Os números devem estar dentro do alcance de um inteiro de 32 bits. Formato: *MajorVersion*. *Menorversão.* *Patch*.

Neste exemplo, a versão da nossa imagem é *1.0.0* e vamos criar 2 réplicas na região *centro-americana,* 1 réplica na região *centro-sul dos EUA* e 1 réplica na região *leste dos EUA 2 usando* armazenamento redundante de zona. As regiões de replicação devem incluir a região onde se situa a VM de origem.

Substitua o valor `--managed-image` deste exemplo pelo ID do seu VM do passo anterior.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> É necessário esperar que a versão de imagem termine completamente de ser construída e replicada antes de poder utilizar a mesma imagem gerida para criar outra versão de imagem.
>
> Também pode armazenar a sua imagem no armazenamento Premium `--storage-account-type  premium_lrs` adicionando, ou Zone Redundant [Storage,](../storage/common/storage-redundancy.md) adicionando `--storage-account-type  standard_zrs` quando cria a versão de imagem.
>

## <a name="next-steps"></a>Passos seguintes

Crie um VM a partir da [imagem generalizada](vm-generalized-image-version-cli.md) utilizando o Azure CLI.

Para obter informações sobre como fornecer informações sobre o plano de compra, consulte [as informações do plano de compra do Supply Azure Marketplace ao criar imagens.](marketplace-images.md)