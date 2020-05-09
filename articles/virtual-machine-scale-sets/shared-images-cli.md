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
ms.openlocfilehash: 30a376755bf0041d5d6f5cb289f5aecd1a734dc5
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792807"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Criar e utilizar imagens partilhadas para conjuntos de escala de máquinas virtuais com o Azure CLI 2.0

Quando cria um conjunto de dimensionamento, tem de especificar uma imagem a ser utilizada quando as instâncias de VM são implementadas. Uma [Galeria de Imagem Partilhada](shared-image-galleries.md) simplifica a partilha de imagens personalizadas em toda a sua organização. As imagens personalizadas são como imagens do marketplace, mas são criadas por si. As imagens personalizadas podem ser utilizadas para configurações do programa de arranque do sistema, como o pré-carregamento de aplicações, configurações de aplicação e outras configurações do SO. 

A Galeria de Imagem Partilhada permite-lhe partilhar as suas imagens com outros. Escolha quais as imagens que pretende partilhar, quais as regiões em que as quer disponibilizar e com quem as quer partilhar. 


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>Passos seguintes

Criar uma versão de imagem a partir de um [VM,](../virtual-machines/image-version-vm-cli.md)ou uma [imagem gerida.](../virtual-machines/image-version-managed-image-cli.md)

Para mais informações sobre galerias de imagem partilhadas, consulte a [visão geral.](shared-image-galleries.md) Se tiver problemas, veja as galerias de [imagens partilhadas de Troubleshooting.](troubleshooting-shared-images.md)
