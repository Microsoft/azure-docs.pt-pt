---
title: Tamanhos VM do Windows em Azure
description: Lista os diferentes tamanhos disponíveis para máquinas virtuais Windows em Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: aabf0d30-04eb-4d34-b44a-69f8bfb84f22
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 990cb2eef7e0f575de3e510abc38bcc2e3172477
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485391"
---
# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Tamanhos para máquinas virtuais Windows em Azure

Este artigo descreve os tamanhos e opções disponíveis para as máquinas virtuais Azure que pode utilizar para executar as suas aplicações e cargas de trabalho do Windows. Também fornece considerações de implantação para estar ciente de quando você está planejando usar estes recursos.  Este artigo também está disponível para [máquinas virtuais Linux.](/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

| Tipo | Tamanhos | Descrição |
|------|-------|-------------|
| [Fins gerais](../sizes-general.md) | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC | Rácio CPU-memória equilibrado. Ideal para testes e desenvolvimento, bases de dados pequenas a médias e servidores web de tráfego baixo a médio. |
| [Com otimização de computação](../sizes-compute.md) | Fsv2 | Alta relação CPU-memória. Bom para servidores web de tráfego médio, aparelhos de rede, processos de lote e servidores de aplicações. |
| [Com otimização de memória](../sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Mv2, M, DSv2, Dv2 | Alta relação memória-CPU. Ótimo para servidores de base de dados relacionais, caches médios a grandes e análise sintetizada. |
| [Com otimização de armazenamento](../sizes-storage.md)  | Lsv2 | Alta entrada de disco e IO ideal para Big Data, SQL, NoSQL bases de dados, armazenamento de dados e grandes bases de dados transacionais.  |
| [GPU](../sizes-gpu.md) | NC, Ncv2, Ncv3, ND, NDv2 (Pré-visualização), NV, Nvv3, Nvv4 | Máquinas virtuais especializadas direcionadas para renderização gráfica pesada e edição de vídeo, bem como treino de modelos e inferência (ND) com aprendizagem profunda. Disponível com GPUs individuais ou múltiplos. |
| [Computação de elevado desempenho](../sizes-hpc.md) | HB, HC, H | As nossas máquinas virtuais CPU mais rápidas e poderosas com interfaces de rede de alta-corrente opcional (RDMA). |

- Para obter informações sobre preços dos vários tamanhos, consulte [preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows).
- Para ver limites gerais para os VMs Azure, consulte [os limites de subscrição e serviço do Azure, quotas e restrições.](../../azure-subscription-service-limits.md)
- Os custos de armazenamento são calculados em separado com base nas páginas utilizadas na conta de armazenamento. Para mais detalhes, [preço de armazenamento Azure.](https://azure.microsoft.com/pricing/details/storage/)
- Saiba mais sobre como as unidades de [computação Azure (ACU)](../acu.md) podem ajudá-lo a comparar o desempenho da computação em Azure SKUs.

## <a name="rest-api"></a>API REST

Para obter informações sobre a utilização da API REST para consultar os tamanhos de VM, consulte o seguinte:

- [Lista disponível tamanhos de máquina virtual para redimensionamento](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Lista de tamanhos de máquina virtual disponíveis para uma subscrição](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Lista de tamanhos de máquina virtual disponíveis num conjunto de disponibilidade](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Saiba mais sobre como as unidades de [computação Azure (ACU)](../acu.md) podem ajudá-lo a comparar o desempenho da computação em Azure SKUs.

## <a name="benchmark-scores"></a>Pontuações de referência

Saiba mais sobre o desempenho da computação para VMs windows utilizando as pontuações de [referência coreMark](compute-benchmark-scores.md).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre os diferentes tamanhos vm que estão disponíveis:

- [Fins gerais](../sizes-general.md)
- [Com otimização de computação](../sizes-compute.md)
- [Com otimização de memória](../sizes-memory.md)
- [Com otimização de armazenamento](../sizes-storage.md)
- [Com otimização de GPU](../sizes-gpu.md)
- [Computação de elevado desempenho](../sizes-hpc.md)
- Consulte a página [de geração Anterior](../sizes-previous-gen.md) para A Standard, Dv1 (D1-4 e D11-14 v1) e série A8-A11