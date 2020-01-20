---
title: Usar imagens de VM compartilhadas para criar um conjunto de dimensionamento no Azure
description: Saiba como usar o CLI do Azure para criar imagens de VM compartilhadas a serem usadas para implantar conjuntos de dimensionamento de máquinas virtuais no Azure.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 83b3d5c904a65b28482acf8b685c939493c8c03b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276274"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Criar e usar imagens compartilhadas para conjuntos de dimensionamento de máquinas virtuais com o CLI do Azure 2,0

Quando cria um conjunto de dimensionamento, tem de especificar uma imagem a ser utilizada quando as instâncias de VM são implementadas. As [galerias de imagens compartilhadas](shared-image-galleries.md) simplificam muito o compartilhamento de imagem personalizada em sua organização. As imagens personalizadas são como imagens do marketplace, mas são criadas por si. As imagens personalizadas podem ser utilizadas para configurações do programa de arranque do sistema, como o pré-carregamento de aplicações, configurações de aplicação e outras configurações do SO. A Galeria de imagens compartilhadas permite que você compartilhe suas imagens de VM personalizadas com outras pessoas em sua organização, dentro ou entre regiões, dentro de um locatário do AAD. Escolha quais imagens você deseja compartilhar, em quais regiões você deseja disponibilizá-las e com quem você deseja compartilhá-las. Você pode criar várias galerias para que seja possível agrupar logicamente imagens compartilhadas. A galeria é um recurso de nível superior que fornece RBAC (controle de acesso baseado em função) completo. As imagens podem ter controle de versão e você pode optar por replicar cada versão de imagem para um conjunto diferente de regiões do Azure. A galeria só funciona com imagens gerenciadas. 

>[!NOTE]
> Este artigo percorre o processo de uso de uma imagem gerenciada generalizada. Não é suportado para criar uma escala definido a partir de uma imagem VM especializada.


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Criar um conjunto de dimensionamento a partir da imagem de VM personalizada
Crie um conjunto de dimensionamento com [`az vmss create`](/cli/azure/vmss#az-vmss-create). Em vez de uma imagem de plataforma, como *UbuntuLTS* ou *CentOS*, especifique o nome da sua imagem de VM personalizada. O exemplo seguinte cria um conjunto de dimensionamento com o nome *myScaleSet*, que utiliza a imagem personalizada com o nome *myImage* do passo anterior:

```azurecli-interactive
az vmss create \
  -g myGalleryRG \
  -n myScaleSet \
  --image "/subscriptions/<subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0" \
  --admin-username azureuser \
  --generate-ssh-keys
```

A criação e configuração de todas as VMs e recursos do conjunto de dimensionamento demora alguns minutos.


[!INCLUDE [virtual-machines-common-gallery-list-cli](../../includes/virtual-machines-common-gallery-list-cli.md)]


## <a name="clean-up-resources"></a>Limpar recursos
Para remover o seu conjunto de dimensionamento e recursos adicionais, elimine o grupo de recursos e todos os respetivos recursos com [az group delete](/cli/azure/group). O parâmetro `--no-wait` devolve o controlo à linha de comandos, sem aguardar a conclusão da operação. O parâmetro `--yes` confirma que pretende eliminar os recursos sem uma linha de comandos adicional para fazê-lo.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Passos seguintes

Você também pode criar recursos da Galeria de imagens compartilhadas usando modelos. Há vários modelos de início rápido do Azure disponíveis: 

- [Criar uma galeria de imagens compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma definição de imagem em uma galeria de imagens compartilhada](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma versão de imagem em uma galeria de imagens compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM com base na versão da imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


Se você tiver problemas, poderá [solucionar problemas de galerias de imagens compartilhadas](troubleshooting-shared-images.md).
