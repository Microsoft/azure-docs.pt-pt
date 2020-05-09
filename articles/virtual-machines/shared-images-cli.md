---
title: Criar galerias de imagem partilhadas com o Azure CLI
description: Neste artigo, aprende-se a usar o Azure CLI para criar uma imagem partilhada de um VM em Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: d64b5f94dae7ad0213fa231b5603064ad3647da1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796697"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Crie uma Galeria de Imagem Partilhada com o Azure CLI

Uma [Galeria de Imagem Partilhada](./linux/shared-image-galleries.md) simplifica a partilha de imagens personalizadas em toda a sua organização. As imagens personalizadas são como imagens do marketplace, mas são criadas por si. As imagens personalizadas podem ser utilizadas para configurações do programa de arranque do sistema, como o pré-carregamento de aplicações, configurações de aplicação e outras configurações do SO. 

A Galeria de Imagem Partilhada permite-lhe partilhar as suas imagens VM personalizadas com outras. Escolha quais as imagens que pretende partilhar, quais as regiões em que as quer disponibilizar e com quem as quer partilhar. 

[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>Passos seguintes

Crie uma versão de imagem a partir de um [VM,](image-version-vm-cli.md)ou uma [imagem gerida](image-version-managed-image-cli.md) utilizando o Azure CLI.

Para mais informações sobre galerias de imagem partilhadas, consulte a [visão geral.](./linux/shared-image-galleries.md) Se tiver problemas, veja as galerias de [imagens partilhadas de Troubleshooting.](troubleshooting-shared-images.md)
