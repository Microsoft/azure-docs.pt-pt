---
title: Tamanhos de VM Windows do Azure-GPU
description: Lista os diferentes tamanhos otimizados para GPU disponíveis para máquinas virtuais do Windows no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como taxa de transferência de armazenamento e largura de banda de rede para tamanhos desta série.
services: virtual-machines-windows
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/11/2019
ms.author: jonbeck
ms.openlocfilehash: 5b2da3076f3970d2f11301b3ea412ce52c257111
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075354"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Tamanhos de máquina virtual otimizada para GPU

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos e controladores suportados

Para aproveitar os recursos de GPU das VMs da série N do Azure que executam o Windows, os drivers NVIDIA GPU devem ser instalados. A [extensão de Driver Nvidia GPU](../extensions/hpccompute-gpu-windows.md) instala drivers NVIDIA CUDA ou Grid apropriados em uma VM da série N. Instale ou gerencie a extensão usando o portal do Azure ou ferramentas como Azure PowerShell ou Azure Resource Manager modelos. Consulte a [documentação de extensão do Driver Nvidia GPU](../extensions/hpccompute-gpu-windows.md) para obter os sistemas operacionais e as etapas de implantação com suporte. Para obter informações gerais sobre extensões de VM, consulte [recursos e extensões de máquina virtual do Azure](../extensions/overview.md).

Se você optar por instalar os drivers NVIDIA GPU manualmente, consulte [instalação do driver de GPU da série N para Windows](n-series-driver-setup.md) para sistemas operacionais, drivers e etapas de instalação e verificação com suporte.

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

## <a name="other-sizes"></a>Outros tamanhos
- [Fins gerais](sizes-general.md)
- [Com otimização de computação](sizes-compute.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como as [unidades de computação do Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho de computação entre SKUs do Azure.

