---
title: Tamanhos Linux VM em Azure
description: Lista os diferentes tamanhos disponíveis para máquinas virtuais Linux em Azure.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: eef0304248c7cc9a1bf63e06247f86d607afce46
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86510111"
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Sizes for Linux virtual machines in Azure

Este artigo descreve os tamanhos e opções disponíveis para as máquinas virtuais Azure que pode usar para executar as suas aplicações e cargas de trabalho Linux. Também fornece considerações de implantação para estar ciente de quando você está planejando usar estes recursos. Este artigo também está disponível para [máquinas virtuais do Windows.](../windows/sizes.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json)

| Tipo | Tamanhos | Descrição |
|------|-------|-------------|
| [Fins gerais](../sizes-general.md)   | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC, DCv2, Dv4, Dsv4, Ddv4, Ddsv4  | Relação CPU-memória equilibrada. Ideais para testes e desenvolvimento, bases de dados pequenas a médias e servidores Web de tráfego baixo a médio. |
| [Com otimização de computação](../sizes-compute.md) | Fsv2 | Elevado rácio CPU-memória. Bom para servidores web de tráfego médio, aparelhos de rede, processos de lote e servidores de aplicações. |
| [Com otimização de memória](../sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Ev4, Esv4, Edv4, Edsv4, Mv2, M, DSv2, Dv2 | Relação memória alta-CPU. Ideais para servidores de bases de dados relacionais, caches médias a grandes e análise dentro da memória.                 |
| [Com otimização de armazenamento](../sizes-storage.md) | Lsv2 | Alta produção de disco e IO ideal para Big Data, SQL, bases de dados NoSQL, armazenamento de dados e grandes bases de dados transacionais.  |
| [GPU](../sizes-gpu.md) | NC, NCv2, NCv3, ND, NDv2 (Preview), NV, NVv3, NVv4 | Máquinas virtuais especializadas direcionadas para renderização gráfica pesada e edição de vídeo, bem como treino de modelos e inferenização (ND) com aprendizagem profunda. Disponível com GPUs individuais ou múltiplas. |
| [Computação de elevado desempenho](../sizes-hpc.md) | HB, HBv2, HC, H | As nossas máquinas virtuais mais rápidas e poderosas do CPU com interfaces de rede de alta produção opcional (RDMA). |

- Para obter informações sobre preços dos vários tamanhos, consulte [o Preço das Máquinas Virtuais.](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux) 
- Para disponibilidade de tamanhos VM nas regiões de Azure, consulte [os produtos disponíveis por região.](https://azure.microsoft.com/regions/services/)
- Para ver os limites gerais dos VMs Azure, consulte [os limites de subscrição e serviços da Azure, quotas e restrições.](../../azure-resource-manager/management/azure-subscription-service-limits.md)
- Saiba mais sobre como [as unidades de computação Azure (ACU)](../acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.

## <a name="rest-api"></a>API REST

Para obter informações sobre a utilização da API REST para consultar os tamanhos de VM, consulte o seguinte:

- [Lista de tamanhos de máquinas virtuais disponíveis para redimensionamento](/rest/api/compute/virtualmachines/listavailablesizes)
- [Lista tamanhos de máquinas virtuais disponíveis para uma subscrição](/rest/api/compute/resourceskus/list)
- [Lista tamanhos de máquinas virtuais disponíveis em um conjunto de disponibilidade](/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Saiba mais sobre como [as unidades de computação Azure (ACU)](../acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.

## <a name="benchmark-scores"></a>Pontuações de referência

Saiba mais sobre o desempenho do cálculo para Os VMs do Linux utilizando as [pontuações de referência](compute-benchmark-scores.md)do CoreMark .

## <a name="manage-costs"></a>Gerir os custos

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre os diferentes tamanhos VM que estão disponíveis:

- [Fins gerais](../sizes-general.md)
- [Com otimização de computação](../sizes-compute.md)
- [Com otimização de memória](../sizes-memory.md)
- [Com otimização de armazenamento](../sizes-storage.md)
- [GPU](../sizes-gpu.md)
- [Computação de elevado desempenho](../sizes-hpc.md)
- Consulte a página de [geração anterior](../sizes-previous-gen.md) para obter uma série Standard, Dv1 (D1-4 e D11-14 v1) e A8-A11
