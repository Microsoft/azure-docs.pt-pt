---
title: Os tamanhos de VM do Linux no Azure | Documentos da Microsoft
description: Lista os tamanhos diferentes disponíveis para máquinas virtuais do Linux no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/07/2019
ms.author: jonbeck
ms.openlocfilehash: 878afbc29b30bbd9c0cdf11d9e1bada73f13d33a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069978"
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Tamanhos de máquinas de virtuais do Linux no Azure
Este artigo descreve os tamanhos e opções disponíveis para as máquinas virtuais do Azure, que pode utilizar para executar as aplicações do Linux e cargas de trabalho. Ele também fornece considerações de implementação estar ciente de quando estiver planejando usar esses recursos. Este artigo também está disponível para [as máquinas virtuais do Windows](../windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


| Tipo                     | Tamanhos           |    Descrição       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Fins gerais](sizes-general.md)          | B, Dsv3, Dv3, DSv2, Dv2, Av2, DC  | Relação CPU/memória equilibrada. Ideal para teste e desenvolvimento, bases de dados pequenas a médias e servidores Web com tráfego baixo a médio. |
| [Com otimização de computação](sizes-compute.md)        | Fsv2           | Relação CPU/memória elevada. Ideal para servidores Web com tráfego médio, aplicações de rede, processos em lote e servidores de aplicações.        |
| [Com otimização de memória](sizes-memory.md)         | Esv3, Ev3, M, DSv2, Dv2  | Rácio de memória de CPU elevado. Ideal para servidores de base de dados relacionais, caches médias a grandes e análise dentro da memória.                 |
| [Com otimização de armazenamento](sizes-storage.md)        | Lsv2                | O débito de disco elevados e e/s ideais para macrodados, SQL, NoSQL bases de dados, armazenamento de dados e grandes bancos de dados transacionais.  |
| [GPU](sizes-gpu.md)            | NV, NVv2, NC, NCv2, NCv3, ND, NDv2 (pré-visualização)            | Máquinas virtuais especializadas destinadas a composição gráfica e edição de vídeo, bem como inferência (ND) com aprendizagem profunda e formação de modelos. Disponíveis com GPUs únicas ou múltiplas.       |
| [Computação de elevado desempenho](sizes-hpc.md) | H       | As nossas máquinas virtuais com CPU mais rápidas e poderosas com interfaces de rede de alto débito (RDMA) opcionais. |


<br>

- Para obter informações sobre os preços de vários tamanhos, veja [preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux). 
- Disponibilidade de tamanhos de VM em regiões do Azure, veja [produtos disponíveis por região](https://azure.microsoft.com/regions/services/).
- Para ver os limites gerais em VMs do Azure, consulte [subscrição do Azure e limites do serviço, quotas e restrições](../../azure-subscription-service-limits.md).
- Saiba mais sobre como [computação do Azure (ACU) de unidades](acu.md) pode ajudá-lo a comparar o desempenho de computação nos SKUs do Azure.


## <a name="rest-api"></a>API REST

Para obter informações sobre como utilizar a API REST para a consulta para tamanhos de VM, consulte o seguinte:

- [Listar tamanhos de máquina virtual de disponibilidade para redimensionar](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Listar tamanhos de máquina virtual de disponibilidade para uma subscrição](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Listar tamanhos de máquina virtual de disponibilidade num conjunto de disponibilidade](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Saiba mais sobre como [computação do Azure (ACU) de unidades](acu.md) pode ajudá-lo a comparar o desempenho de computação nos SKUs do Azure.

## <a name="benchmark-scores"></a>Pontuações de referência

Saiba mais sobre o desempenho de VMs do Linux através de computação a [pontuações de referência de resultados de CoreMark](compute-benchmark-scores.md).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre os diferentes tamanhos de VM que estão disponíveis:
- [Fins gerais](sizes-general.md)
- [Com otimização de computação](sizes-compute.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- Verifique os [geração anterior](sizes-previous-gen.md) página para A Standard, Dv1 (v1 D1-4 e D11 a 14) e séries A8-A11



