---
title: Escolher tamanhos de VM para conjuntos do Azure Batch | Documentos da Microsoft
description: Como escolher entre os tamanhos VM disponíveis para nós de computação em conjuntos do Azure Batch
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
ms.author: danlep
ms.openlocfilehash: 987cbcc642152a4077cc895ad06e43ac56113497
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2018
ms.locfileid: "45544069"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Escolha um tamanho VM para nós de computação num conjunto do Azure Batch

Quando seleciona um tamanho de nó para um conjunto do Batch do Azure, pode escolher entre quase todos os tamanhos de VM disponíveis no Azure. O Azure oferece uma variedade de tamanhos para Linux e VMs do Windows para diferentes cargas de trabalho. 

Existem algumas limitações para escolher um tamanho de VM e de exceções:
* Algumas famílias de VM ou tamanhos de VM não são suportados no Batch. 
* Alguns tamanhos VM são restritos e têm de ser especificamente ativadas antes que eles podem ser alocados.


## <a name="supported-vm-families-and-sizes"></a>Famílias de VM e tamanhos suportados

### <a name="pools-in-virtual-machine-configuration"></a>Conjuntos na configuração de Máquina Virtual

Os conjuntos do batch na configuração da Máquina Virtual suportam todos os tamanhos VM ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)) *exceto* para o seguinte:

| Família  | Tamanhos não suportados  |
|---------|---------|
| Série básico | Basic_A0 (A0) |
| Série A | Standard_A0 |
| Série B | Todos |

VMs de série M são suportadas apenas para nós de baixa prioridade.


### <a name="pools-in-cloud-service-configuration"></a>Conjuntos na configuração de serviço em nuvem

Os conjuntos do batch na configuração do serviço em nuvem suportam todas [tamanhos de VM para serviços Cloud](../cloud-services/cloud-services-sizes-specs.md) *exceto* para o seguinte:

| Família  | Tamanhos não suportados  |
|---------|---------|
| Série A | ExtraSmall |
| Série Av2 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="restricted-vm-families"></a>Restritas famílias de VM
As famílias VM seguintes podem ser alocadas em conjuntos do Batch, mas tem de pedir um aumento de quota específico (consulte [este artigo](batch-quota-limit.md#increase-a-quota)):
* Série NCv2
* Série NCv3
* Série ND

Estes tamanhos só podem ser utilizados em conjuntos na configuração da Máquina Virtual.

## <a name="size-considerations"></a>Considerações de tamanho

* **Requisitos da aplicação** -considere as características e requisitos da aplicação, irá executar em nós. Alguns aspetos, como se a aplicação tem vários threads e a quantidade de memória que consome, podem ajudar a determinar o tamanho de nó mais adequado e económico. Para várias instâncias [cargas de trabalho MPI](batch-mpi.md) ou aplicações de CUDA, considere especializada [HPC](../virtual-machines/linux/sizes-hpc.md) ou [ativadas para GPU](../virtual-machines/linux/sizes-gpu.md) tamanhos de VM, respectivamente. (Consulte [utilização com capacidade RDMA ou ativadas para GPU instâncias em conjuntos do Batch](batch-pool-compute-intensive-sizes.md).) 

* **Tarefas por nó** -é selecionado ao selecionar um tamanho de nó supondo que uma tarefa seja executada num nó de cada vez. No entanto, poderá ser vantajoso ter várias tarefas (e, portanto, várias instâncias da aplicação) [executadas em paralelo](batch-parallel-node-tasks.md) em nós de computação durante a execução de tarefa. Neste caso, é comum escolher um tamanho de nó com vários núcleos para acomodar a maior necessidade de execução de tarefas paralelas.

* **Níveis para diferentes tarefas de carga** -todos os nós num conjunto têm o mesmo tamanho. Se quiser executar aplicações com requisitos de sistema e/ou níveis de carga diferentes, recomendamos utilizar conjuntos separados. 

* **Disponibilidade de região** -família de uma VM ou tamanho poderá não estar disponível nas regiões onde criar as contas do Batch. Para verificar que um tamanho está disponível, consulte [produtos disponíveis por região](https://azure.microsoft.com/regions/services/).

* **Quotas** – a [núcleos quotas](batch-quota-limit.md#resource-quotas) no seu lote conta pode limitar o número de nós de um determinado tamanho, pode adicionar a um conjunto do Batch. Para pedir um aumento de quota, consulte [este artigo](batch-quota-limit.md#increase-a-quota). 

* **Configuração do pool** -em geral, tem mais opções de tamanho VM quando cria um conjunto na configuração da Máquina Virtual, em comparação com a configuração de serviço em nuvem.

## <a name="next-steps"></a>Passos Seguintes

* Para uma descrição geral aprofundada do Batch, consulte [soluções com o Batch de computação paralelas em grande escala de desenvolver](batch-api-basics.md).
* Para obter informações sobre a utilização de tamanhos de VM de computação intensiva, consulte [utilização com capacidade RDMA ou ativadas para GPU instâncias em conjuntos do Batch](batch-pool-compute-intensive-sizes.md). 


