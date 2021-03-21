---
title: Utilize imagens VM partilhadas para criar uma escala definida em Azure PowerShell
description: Aprenda a usar o Azure PowerShell para criar imagens VM partilhadas para utilizar para implantar conjuntos de escala de máquina virtual em Azure.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 3ba826de47255143c8adefe4424448d0b80ba105
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98678343"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-powershell"></a>Crie e utilize imagens partilhadas para conjuntos de escala de máquina virtual com o Azure PowerShell

Quando cria um conjunto de dimensionamento, tem de especificar uma imagem a ser utilizada quando as instâncias de VM são implementadas. O serviço De Imagem Partilhada simplifica consideravelmente a partilha de imagens personalizadas em toda a sua organização. As imagens personalizadas são como imagens do marketplace, mas são criadas por si. As imagens personalizadas podem ser utilizadas para configurações do programa de arranque do sistema, como o pré-carregamento de aplicações, configurações de aplicação e outras configurações do SO. 

A Galeria de Imagens Partilhada permite-lhe partilhar as suas imagens VM personalizadas com outras pessoas da sua organização, dentro ou em todas as regiões, dentro de um inquilino da AAD. Escolha quais as imagens que pretende partilhar, em que regiões quer disponibilizá-las e com quem quer partilhá-las. Pode criar várias galerias para que possa, logicamente, agrupar imagens partilhadas. 

A galeria é um recurso de alto nível que fornece um controlo de acesso baseado em funções Azure (Azure RBAC). As imagens podem ser versadas e pode optar por replicar cada versão de imagem para um conjunto diferente de regiões do Azure. A galeria funciona apenas com Imagens Geridas. 

A funcionalidade Image Gallery partilhada tem vários tipos de recursos. 


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="before-you-begin"></a>Antes de começar

Os passos abaixo detalham como tornar uma VM existente numa imagem personalizada reutilizável que pode utilizar para criar novas instâncias da VM.

Para completar o exemplo neste artigo, você deve ter uma imagem gerida existente. Pode seguir [tutorial: Criar e utilizar uma imagem personalizada para conjuntos de escala de máquina virtual com Azure PowerShell](tutorial-use-custom-image-powershell.md) para criar uma, se necessário. Se a imagem gerida contiver um disco de dados, o tamanho do disco de dados não pode ser superior a 1 TB.

Ao trabalhar o artigo, substitua o grupo de recursos e os nomes VM sempre que necessário.


[!INCLUDE [virtual-machines-common-shared-images-ps](../../includes/virtual-machines-common-shared-images-powershell.md)]




## <a name="next-steps"></a>Passos seguintes

Também pode criar recursos da Galeria de Imagens Partilhadas utilizando modelos. Existem vários modelos Azure Quickstart disponíveis: 

- [Criar um Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma Versão de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM a partir de uma Versão de Imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Para mais informações sobre galerias de imagem partilhadas, consulte a [Visão Geral.](../virtual-machines/shared-image-galleries.md) Se tiver problemas, consulte [as galerias de imagem partilhadas .](../virtual-machines/troubleshooting-shared-images.md)