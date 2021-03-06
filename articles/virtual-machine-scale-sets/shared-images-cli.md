---
title: Utilize imagens VM partilhadas para criar uma escala definida no Azure CLI
description: Aprenda a usar o CLI Azure para criar imagens VM partilhadas para usar para implantar conjuntos de escala de máquina virtual em Azure.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: b5e9d5995e8173950db483c5c26a11830a62862e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444008"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Crie e utilize imagens partilhadas para conjuntos de escala de máquina virtual com o Azure CLI 2.0

Quando cria um conjunto de dimensionamento, tem de especificar uma imagem a ser utilizada quando as instâncias de VM são implementadas. Uma [Galeria de Imagens Partilhadas](../virtual-machines/shared-image-galleries.md) simplifica a partilha de imagens personalizadas em toda a sua organização. As imagens personalizadas são como imagens do marketplace, mas são criadas por si. As imagens personalizadas podem ser utilizadas para configurações do programa de arranque do sistema, como o pré-carregamento de aplicações, configurações de aplicação e outras configurações do SO. 

A Galeria de Imagens Partilhada permite-lhe partilhar as suas imagens com outras pessoas. Escolha quais as imagens que pretende partilhar, em que regiões quer disponibilizá-las e com quem quer partilhá-las. 


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>Passos seguintes

Crie uma versão de imagem a partir de um [VM,](../virtual-machines/image-version-vm-cli.md)ou de uma [imagem gerida](../virtual-machines/image-version-managed-image-cli.md).

Para mais informações sobre galerias de imagem partilhadas, consulte a [Visão Geral.](../virtual-machines/shared-image-galleries.md) Se tiver problemas, consulte [as galerias de imagem partilhadas .](../virtual-machines/troubleshooting-shared-images.md)
