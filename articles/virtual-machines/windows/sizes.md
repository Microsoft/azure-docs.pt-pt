---
title: Tamanhos de VM do Windows no Azure | Microsoft Docs
description: Lista os diferentes tamanhos disponíveis para máquinas virtuais do Windows no Azure.
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
ms.date: 11/06/2019
ms.author: jonbeck
ms.openlocfilehash: 0553255cdcdc37aad4e996e2c6cb22d17e336ef3
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721527"
---
# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Tamanhos das máquinas virtuais do Windows no Azure

Este artigo descreve os tamanhos e as opções disponíveis para as máquinas virtuais do Azure que você pode usar para executar seus aplicativos e cargas de trabalho do Windows. Ele também fornece considerações de implantação a serem consideradas quando você pretende usar esses recursos.  Este artigo também está disponível para [máquinas virtuais do Linux](../linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


| Tipo                     | Tamanhos           |    Descrição       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Fins gerais](sizes-general.md)          | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC  | Relação CPU/memória equilibrada. Ideal para teste e desenvolvimento, bases de dados pequenas a médias e servidores Web com tráfego baixo a médio. |
| [Com otimização de computação](sizes-compute.md)        | Fsv2           | Relação CPU/memória elevada. Ideal para servidores Web com tráfego médio, aplicações de rede, processos em lote e servidores de aplicações.        |
| [Com otimização de memória](sizes-memory.md)         | Esv3, Ev3, Easv4, Eav4, Mv2, M, DSv2, Dv2  | Alta taxa de memória/CPU. Ideal para servidores de base de dados relacionais, caches médias a grandes e análise dentro da memória.                 |
| [Com otimização de armazenamento](sizes-storage.md)        | Lsv2                | Alta taxa de transferência e e/s de disco ideal para Big data, SQL, bancos de dados NoSQL, data warehousing e grandes bancos de dados transacionais.  |
| [GPU](sizes-gpu.md)            | NC, NCv2, NCv3, ND, NDv2 (visualização), NV, NVv3 | Máquinas virtuais especializadas direcionadas para processamento gráfico pesado e edição de vídeo, bem como treinamento de modelo e inferência (ND) com aprendizado profundo. Disponível com uma ou várias GPUs.       |
| [Computação de elevado desempenho](sizes-hpc.md) | HB, HC, H | As nossas máquinas virtuais com CPU mais rápidas e poderosas com interfaces de rede de alto débito (RDMA) opcionais. |


<br> 

- Para obter informações sobre os preços de vários tamanhos, consulte [preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). 
- Para ver os limites gerais em VMs do Azure, confira [assinatura do Azure e limites de serviço, cotas e restrições](../../azure-subscription-service-limits.md).
- Os custos de armazenamento são calculados em separado com base nas páginas utilizadas na conta de armazenamento. Para obter detalhes, [preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).
- Saiba mais sobre como as [unidades de computação do Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho de computação entre SKUs do Azure.


## <a name="rest-api"></a>API REST

Para obter informações sobre como usar a API REST para consultar tamanhos de VM, consulte o seguinte:

- [Listar tamanhos de máquina virtual disponíveis para redimensionamento](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Listar tamanhos de máquina virtual disponíveis para uma assinatura](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Listar tamanhos de máquina virtual disponíveis em um conjunto de disponibilidade](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Saiba mais sobre como as [unidades de computação do Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho de computação entre SKUs do Azure.

## <a name="benchmark-scores"></a>Pontuações de referência

Saiba mais sobre o desempenho de computação para VMs do Windows usando as pontuações de parâmetro de [comparação de cocomentário](compute-benchmark-scores.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os diferentes tamanhos de VM que estão disponíveis:
- [Fins gerais](sizes-general.md)
- [Com otimização de computação](sizes-compute.md)
- [Com otimização de memória](../virtual-machines-windows-sizes-memory.md)
- [Com otimização de armazenamento](../virtual-machines-windows-sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- Verifique a página de [geração anterior](sizes-previous-gen.md) para uma série standard, Dv1 (D1-4 e D11-14 v1) e A8-a11




