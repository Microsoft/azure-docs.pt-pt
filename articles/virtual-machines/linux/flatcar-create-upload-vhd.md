---
title: Criar e carregar um Recipiente DeCarbono Linux VHD para utilização em Azure
description: Aprenda a criar e carregar um VHD que contenha um sistema operativo Flatcar Container Linux.
author: marga-kinvolk
ms.author: danis
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/16/2020
ms.reviewer: cynthn
ms.openlocfilehash: 5d8be9493b7a312270301e3520f301f797fe2167
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565296"
---
# <a name="using-a-prebuilt-flatcar-image-for-azure"></a>Usando uma imagem plana pré-construída para Azure

Você pode baixar imagens de disco rígido virtual Azure pré-construídas do Flatcar Container Linux para cada um dos canais suportados por Flatcar:

- [estável](https://stable.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [beta](https://beta.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [alpha](https://alpha.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [borda](https://edge.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)

Esta imagem já está totalmente configurada e otimizada para correr no Azure. Só precisas de descomprimir.

## <a name="building-your-own-flatcar-based-virtual-machine-for-azure"></a>Construindo a sua própria máquina virtual baseada em Flatcar para o Azure

Em alternativa, pode optar por construir a sua própria imagem de Flatcar Container Linux.

Em qualquer máquina à base de linux, siga as instruções detalhadas no [guia SDK do desenvolvedor do recipiente Flatcar.](https://docs.flatcar-linux.org/os/sdk-modifying-flatcar/) Ao executar o `image_to_vm.sh` script, certifique-se de que passa `--format=azure` para criar um disco rígido virtual Azure.

## <a name="next-steps"></a>Passos seguintes

Assim que tiver o ficheiro .vhd, pode utilizar o ficheiro resultante para criar novas máquinas virtuais em Azure. Se esta for a primeira vez que está a enviar um ficheiro .vhd para a Azure, consulte [Create a Linux VM a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).
