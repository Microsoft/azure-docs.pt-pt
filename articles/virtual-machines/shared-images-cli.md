---
title: Criar galerias de imagem partilhadas com o Azure CLI
description: Neste artigo, você aprende a usar o CLI Azure para criar uma imagem partilhada de um VM em Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: ee7f7b524225845dc68100ee8ec9292eef111232
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882353"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Criar uma Galeria de Imagens Partilhada com o Azure CLI

Uma [Galeria de Imagens Partilhadas](./shared-image-galleries.md) simplifica a partilha de imagens personalizadas em toda a sua organização. As imagens personalizadas são como imagens do marketplace, mas são criadas por si. As imagens personalizadas podem ser utilizadas para configurações do programa de arranque do sistema, como o pré-carregamento de aplicações, configurações de aplicação e outras configurações do SO. 

A Galeria de Imagens Partilhada permite-lhe partilhar as suas imagens VM personalizadas com outras. Escolha quais as imagens que pretende partilhar, em que regiões quer disponibilizá-las e com quem quer partilhá-las. 

[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>Próximos passos

Crie uma versão de imagem a partir de um [VM,](image-version-vm-cli.md)ou uma [imagem gerida](image-version-managed-image-cli.md) utilizando o Azure CLI.

Para mais informações sobre galerias de imagem partilhadas, consulte a [Visão Geral.](./shared-image-galleries.md) Se tiver problemas, consulte [as galerias de imagem partilhadas .](troubleshooting-shared-images.md)