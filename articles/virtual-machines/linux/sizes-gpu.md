---
title: Tamanhos de VM Linux do Azure-computação acelerada | Microsoft Docs
description: Lista os diferentes tamanhos otimizados para GPU disponíveis para máquinas virtuais Linux no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como taxa de transferência de armazenamento e largura de banda de rede para tamanhos desta série.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/11/2019
ms.author: jonbeck
ms.openlocfilehash: ed8af91701a5ed77636bb86e8798981f37546f23
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70082138"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Tamanhos de máquina virtual otimizada para GPU

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-distributions-and-drivers"></a>Distribuições e controladores suportados

Para aproveitar os recursos de GPU das VMs da série N do Azure que executam o Linux, os drivers NVIDIA GPU devem ser instalados. A [extensão de Driver Nvidia GPU](../extensions/hpccompute-gpu-linux.md) instala drivers NVIDIA CUDA ou Grid apropriados em uma VM da série N. Instale ou gerencie a extensão usando o portal do Azure ou ferramentas como os modelos CLI do Azure ou Azure Resource Manager. Consulte a [documentação de extensão do Driver Nvidia GPU](../extensions/hpccompute-gpu-linux.md) para obter as distribuições com suporte e as etapas de implantação. Para obter informações gerais sobre extensões de VM, consulte [recursos e extensões de máquina virtual do Azure](../extensions/overview.md).

Se você optar por instalar os drivers NVIDIA GPU manualmente, consulte [instalação do driver de GPU da série N para Linux](n-series-driver-setup.md) para obter as distribuições, drivers e etapas de instalação e verificação com suporte.


[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Você não deve instalar o X Server ou outros sistemas que `Nouveau` usam o driver em VMs do Ubuntu NC. Antes de instalar os drivers NVIDIA GPU, você precisa desabilitar `Nouveau` o driver.  

## <a name="other-sizes"></a>Outros tamanhos
- [Fins gerais](sizes-general.md)
- [Com otimização de computação](sizes-compute.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como as [unidades de computação do Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho de computação entre SKUs do Azure.