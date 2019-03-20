---
title: Tamanhos de VM do Windows no Azure | Documentos da Microsoft
description: Lista os tamanhos diferentes disponíveis para máquinas de virtuais do Windows no Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: aabf0d30-04eb-4d34-b44a-69f8bfb84f22
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/14/2018
ms.author: jonbeck
ms.openlocfilehash: 45fc4655d8afd2127c2f3057eb8a42ee80be4021
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58009834"
---
# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Tamanhos de máquinas de virtuais do Windows no Azure

Este artigo descreve os tamanhos e opções disponíveis para as máquinas virtuais do Azure, que pode utilizar para executar as aplicações do Windows e cargas de trabalho. Ele também fornece considerações de implementação estar ciente de quando estiver planejando usar esses recursos.  Este artigo também está disponível para [máquinas virtuais do Linux](../linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


| Type                     | Tamanhos           |    Descrição       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Fins gerais](sizes-general.md)          | B, Dsv3, Dv3, DSv2, Dv2, Av2, DC | Relação CPU/memória equilibrada. Ideal para teste e desenvolvimento, bases de dados pequenas a médias e servidores Web com tráfego baixo a médio. |
| [Com otimização de computação](sizes-compute.md)        | Fsv2, Fs, F             | Relação CPU/memória elevada. Ideal para servidores Web com tráfego médio, aplicações de rede, processos em lote e servidores de aplicações.        |
| [Com otimização de memória](../virtual-machines-windows-sizes-memory.md)         | Esv3, Ev3, M, GS, G, DSv2, Dv2  | Rácio de memória de CPU elevado. Ideal para servidores de base de dados relacionais, caches médias a grandes e análise dentro da memória.                 |
| [Com otimização de armazenamento](../virtual-machines-windows-sizes-storage.md)        | Lsv2, Ls                | O débito de disco elevados e e/s ideais para macrodados, SQL, NoSQL bases de dados, armazenamento de dados e grandes bancos de dados transacionais.  |
| [GPU](sizes-gpu.md)            | NV, NVv2, NC, NCv2, NCv3, ND, NDv2 (pré-visualização)            | Máquinas virtuais especializadas destinadas a composição gráfica e edição de vídeo, bem como inferência (ND) com aprendizagem profunda e formação de modelos. Disponíveis com GPUs únicas ou múltiplas.       |
| [Computação de elevado desempenho](sizes-hpc.md) | H       | As nossas máquinas virtuais com CPU mais rápidas e poderosas com interfaces de rede de alto débito (RDMA) opcionais. |


<br> 

- Para obter informações sobre os preços de vários tamanhos, veja [preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). 
- Para ver os limites gerais em VMs do Azure, consulte [subscrição do Azure e limites do serviço, quotas e restrições](../../azure-subscription-service-limits.md).
- Os custos de armazenamento são calculados em separado com base nas páginas utilizadas na conta de armazenamento. Para obter detalhes, [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).
- Saiba mais sobre como [computação do Azure (ACU) de unidades](acu.md) pode ajudá-lo a comparar o desempenho de computação nos SKUs do Azure.


## <a name="rest-api"></a>API REST

Para obter informações sobre como utilizar a API REST para a consulta para tamanhos de VM, consulte o seguinte:

- [Listar tamanhos de máquina virtual de disponibilidade para redimensionar](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Listar tamanhos de máquina virtual de disponibilidade para uma subscrição](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Listar tamanhos de máquina virtual de disponibilidade num conjunto de disponibilidade](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Saiba mais sobre como [computação do Azure (ACU) de unidades](acu.md) pode ajudá-lo a comparar o desempenho de computação nos SKUs do Azure.

## <a name="benchmark-scores"></a>Pontuações de referência

Saiba mais sobre o desempenho de VMs do Windows através de computação a [pontuações de referência de resultados de CoreMark](compute-benchmark-scores.md).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre os diferentes tamanhos de VM que estão disponíveis:
- [Fins gerais](sizes-general.md)
- [Com otimização de computação](sizes-compute.md)
- [Com otimização de memória](../virtual-machines-windows-sizes-memory.md)
- [Com otimização de armazenamento](../virtual-machines-windows-sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- Verifique os [geração anterior](sizes-previous-gen.md) página para A Standard, Dv1 (v1 D1-4 e D11 a 14) e séries A8-A11




