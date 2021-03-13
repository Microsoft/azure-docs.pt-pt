---
title: Listar, atualizar e apagar recursos de imagem usando o Azure CLI
description: Liste, atualize e elimine os recursos de imagem na sua galeria de imagens partilhada utilizando o Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3554eccf28c5abbe9741af5b9718fb83710dab38
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553634"
---
# <a name="list-update-and-delete-image-resources"></a>Listar, atualizar e eliminar recursos de imagem 

Pode gerir os seus recursos de galeria de imagens partilhados utilizando o Azure CLI.

[!INCLUDE [virtual-machines-common-gallery-list-cli.md](../../includes/virtual-machines-common-gallery-list-cli.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-cli](../../includes/virtual-machines-common-shared-images-update-delete-cli.md)]

## <a name="next-steps"></a>Passos seguintes

[O Azure Image Builder (pré-visualização)](./image-builder-overview.md) pode ajudar a automatizar a criação de versão de imagem, podendo até usá-la para atualizar e [criar uma nova versão de imagem a partir de uma versão de imagem existente.](./linux/image-builder-gallery-update-image-version.md)