---
title: Criar uma imagem a partir de uma VM
description: Saiba como criar uma imagem numa Galeria de Imagem Partilhada a partir de um VM em Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: f53a6b63c744b0e3e41f7ad22270cd842da57674
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796580"
---
# <a name="create-an-image-version-from-a-vm-in-azure-using-the-azure-cli"></a>Criar uma versão de imagem a partir de um VM em Azure usando o Azure CLI

Se tiver um VM existente que gostaria de usar para fazer MMs múltiplos e idênticos, pode usar esse VM para criar uma imagem numa Galeria de Imagem Partilhada utilizando o AZURE CLI. Também pode criar uma imagem a partir de um VM utilizando [o Azure PowerShell](image-version-vm-powershell.md).

Uma **versão de imagem** é o que se usa para criar um VM quando se utiliza uma Galeria de Imagem Partilhada. Pode ter várias versões de uma imagem necessária para o seu ambiente. Quando se utiliza uma versão de imagem para criar um VM, a versão de imagem é utilizada para criar discos para o novo VM. As versões de imagem podem ser usadas várias vezes.


## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, você deve ter uma Galeria de Imagem Partilhada existente. 

Você também deve ter um VM existente em Azure, na mesma região que a sua galeria. 

Se o VM tiver um disco de dados ligado, o tamanho do disco de dados não pode ser superior a 1 TB.

Ao trabalhar através deste artigo, substitua os nomes de recursos sempre que necessário.

## <a name="get-information-about-the-vm"></a>Obtenha informações sobre o VM

Pode ver uma lista de VMs disponíveis através [da lista Az Vm](/cli/azure/vm#az-vm-list). 

```azurecli-interactive
az vm list --output table
```

Assim que souber o nome VM e em que grupo de recursos se encontra, obtenha a identificação do VM usando [az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view). 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```


## <a name="create-an-image-definition"></a>Criar uma definição de imagem

As definições de imagem criam um agrupamento lógico para imagens. São utilizados para gerir informação sobre as versões de imagem que são criadas dentro delas. 

Os nomes de definição de imagem podem ser compostos por letras maiúsculas ou minúsculas, dígitos, pontos, traços e períodos. 

Certifique-se de que a sua definição de imagem é do tipo certo. Se tiver generalizado o VM (utilizando sysprep para Windows, ou waagent -deprovisionamento para Linux), então deve criar uma definição de imagem generalizada utilizando `--os-state generalized`. Se pretender utilizar o VM sem remover as contas de utilizador `--os-state specialized`existentes, crie uma definição de imagem especializada utilizando .

Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, consulte [definições](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)de imagem .

Crie uma definição de imagem na galeria utilizando a [criação de definição de imagem az sig](/cli/azure/sig/image-definition#az-sig-image-definition-create).

Neste exemplo, a definição de imagem é denominada *myImageDefinition*, e é para uma imagem o Linux OS [especializada.](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images) Para criar uma definição para imagens utilizando um Sistema operativo Windows, utilize. `--os-type Windows` 

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

Crie uma versão de imagem a partir do VM utilizando a imagem [da galeria az criar-versão de imagem.](/cli/azure/sig/image-version#az-sig-image-version-create)  

Os caracteres permitidos para a versão de imagem são números e períodos. Os números devem estar dentro do alcance de um inteiro de 32 bits. Formato: *MajorVersion*. *Versão menor.* *Patch*.

Neste exemplo, a versão da nossa imagem é *de 1.0.0* e vamos criar 2 réplicas na região *centro-oeste dos EUA,* 1 réplica na região *centro-sul dos EUA* e 1 réplica na região leste dos EUA *2* utilizando armazenamento redundante. As regiões de replicação devem incluir a região onde se encontra a fonte VM.

Substitua o `--managed-image` valor deste exemplo pela identificação do seu VM do passo anterior.

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
> É preciso esperar que a versão de imagem termine completamente de ser construída e replicada antes de poder utilizar a mesma imagem gerida para criar outra versão de imagem.
>
> Também pode armazenar a sua imagem no `--storage-account-type  premium_lrs`armazenamento Premiun através `--storage-account-type  standard_zrs` de um armazenamento de adição ou [Zona Redundante](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) adicionando quando criar a versão de imagem.
>

## <a name="next-steps"></a>Passos seguintes

Crie um VM a partir da [imagem generalizada](vm-generalized-image-version-cli.md) utilizando o Azure CLI.
