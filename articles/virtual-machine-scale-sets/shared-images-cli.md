---
title: Use imagens VM partilhadas para criar um conjunto de escala em Azure
description: Aprenda a usar o Azure CLI para criar imagens VM partilhadas para usar para implementar conjuntos de escala de máquinas virtuais em Azure.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 83b3d5c904a65b28482acf8b685c939493c8c03b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76276274"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Criar e utilizar imagens partilhadas para conjuntos de escala de máquinas virtuais com o Azure CLI 2.0

Quando cria um conjunto de dimensionamento, tem de especificar uma imagem a ser utilizada quando as instâncias de VM são implementadas. [As Galerias](shared-image-galleries.md) de Imagem Partilhada simplificam consideravelmente a partilha de imagens personalizadas em toda a sua organização. As imagens personalizadas são como imagens do marketplace, mas são criadas por si. As imagens personalizadas podem ser utilizadas para configurações do programa de arranque do sistema, como o pré-carregamento de aplicações, configurações de aplicação e outras configurações do SO. A Galeria de Imagem Partilhada permite-lhe partilhar as suas imagens VM personalizadas com outras da sua organização, dentro ou em todas as regiões, dentro de um inquilino da AAD. Escolha quais as imagens que pretende partilhar, quais as regiões em que as quer disponibilizar e com quem as quer partilhar. Você pode criar várias galerias para que você possa logicamente agrupar imagens partilhadas. A galeria é um recurso de alto nível que fornece um controlo completo de acesso baseado em papéis (RBAC). As imagens podem ser versonizadas e pode optar por replicar cada versão de imagem para um conjunto diferente de regiões do Azure. A galeria só funciona com Imagens Geridas. 

>[!NOTE]
> Este artigo percorre o processo de utilização de uma imagem gerida generalizada. Não é suportado para criar uma escala definido a partir de uma imagem VM especializada.


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Criar um conjunto de dimensionamento a partir da imagem de VM personalizada
Crie um [`az vmss create`](/cli/azure/vmss#az-vmss-create)conjunto de escala com . Em vez de uma imagem de plataforma, como *UbuntuLTS* ou *CentOS*, especifique o nome da sua imagem de VM personalizada. O exemplo seguinte cria um conjunto de dimensionamento com o nome *myScaleSet*, que utiliza a imagem personalizada com o nome *myImage* do passo anterior:

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

Também pode criar recurso da Galeria de Imagem Partilhada utilizando modelos. Existem vários modelos Azure Quickstart disponíveis: 

- [Criar uma Galeria de Imagem Partilhada](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem numa Galeria de Imagem Partilhada](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma versão de imagem numa galeria de imagem partilhada](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar um VM a partir da versão de imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


Se tiver algum problema, pode resolver as galerias de [imagens partilhadas.](troubleshooting-shared-images.md)
