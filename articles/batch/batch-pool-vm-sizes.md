---
title: Escolher tamanhos de VM para conjuntos - Azure Batch | Documentos da Microsoft
description: Como escolher entre os tamanhos VM disponíveis para nós de computação em conjuntos do Azure Batch
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 033e0865f23034b94e3133e0ba5890eca4e746ea
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080877"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Escolha um tamanho VM para nós de computação num conjunto do Azure Batch

Quando seleciona um tamanho de nó para um conjunto do Batch do Azure, pode escolher entre quase todos os tamanhos de VM disponíveis no Azure. O Azure oferece uma variedade de tamanhos para Linux e VMs do Windows para diferentes cargas de trabalho.

Existem algumas limitações para escolher um tamanho de VM e de exceções:

* Algumas séries VM ou tamanhos de VM não são suportados no Batch.
* Alguns tamanhos VM são restritos e têm de ser especificamente ativadas antes que eles podem ser alocados.

## <a name="supported-vm-series-and-sizes"></a>Série VM e tamanhos suportados

### <a name="pools-in-virtual-machine-configuration"></a>Conjuntos na configuração de Máquina Virtual

Os conjuntos do batch na configuração da Máquina Virtual suportam quase todos os tamanhos de VM ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). Consulte a tabela seguinte para saber mais sobre tamanhos suportados e restrições.

Qualquer promocionais ou tamanhos de VM de pré-visualização não listados não são garantidos para o suporte.

| Série VM  | Tamanhos suportados | Modo de alocação de conjuntos de conta de batch<sup>1</sup> |
|------------|---------|-----------------|
| Série básico | Todos os tamanhos *exceto* Basic_A0 (A0) | Qualquer |
| Série A | Todos os tamanhos *exceto* Standard_A0 | Qualquer |
| Série Av2 | Todos os tamanhos | Qualquer |
| Série B | Nenhuma | Não disponível |
| Série DC | Nenhuma | Não disponível |
| Dv2, Dsv2-series | Todos os tamanhos | Qualquer |
| Dv3, Dsv3-series | Todos os tamanhos | Qualquer |
| [Tamanhos de otimização de memória](../virtual-machines/linux/sizes-memory.md) | Nenhuma | Não disponível |
| Série Fsv2 | Todos os tamanhos | Qualquer |
| Série H | Todos os tamanhos | Qualquer |
| Série HB | Todos os tamanhos | Modo de subscrição do utilizador |
| Série HC | Todos os tamanhos | Modo de subscrição do utilizador |
| Série Ls | Todos os tamanhos | Qualquer |
| Série Lsv2 | Nenhuma | Não disponível |
| Série M | Standard_M64ms (baixa prioridade apenas), Standard_M128s (baixa prioridade apenas) | Qualquer |  
| Série NCv2<sup>2</sup> | Todos os tamanhos | Qualquer |
| Série NCv3<sup>2</sup> | Todos os tamanhos | Qualquer |
| Série ND<sup>2</sup> | Todos os tamanhos | Qualquer |
| Série NDv2 | Todos os tamanhos | Modo de subscrição do utilizador |
| Série NV | Todos os tamanhos | Qualquer |
| Série NVv3 | Nenhuma | Não disponível |
| SAP HANA | Nenhuma | Não disponível |

<sup>1</sup> algumas séries VM mais recentes são parcialmente suportadas inicialmente. Esta série VM pode ser alocado por contas do Batch com o **modo de alocação de conjuntos** definida como **subscrição de utilizador**. Ver [contas do Batch gerir](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode) para obter mais informações sobre a configuração de conta do Batch. Ver [Quotas e limites](batch-quota-limit.md) para saber como pedir quota estas parcialmente suportada série VM para **subscrição de utilizador** contas do Batch.  

<sup>2</sup> tamanhos de VM estes podem ser alocados em conjuntos do Batch na configuração da Máquina Virtual, mas tem de solicitar um específico [aumento de quota](batch-quota-limit.md#increase-a-quota).

### <a name="pools-in-cloud-service-configuration"></a>Conjuntos na configuração de serviço em nuvem

Os conjuntos do batch na configuração do serviço em nuvem suportam todas [tamanhos de VM para serviços Cloud](../cloud-services/cloud-services-sizes-specs.md) **exceto** para o seguinte:

| Série VM  | Tamanhos não suportados |
|------------|-------------------|
| Série A   | Extra pequena       |
| Série Av2 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Considerações de tamanho

* **Requisitos da aplicação** -considere as características e requisitos da aplicação, irá executar em nós. Alguns aspetos, como se a aplicação tem vários threads e a quantidade de memória que consome, podem ajudar a determinar o tamanho de nó mais adequado e económico. Para várias instâncias [cargas de trabalho MPI](batch-mpi.md) ou aplicações de CUDA, considere especializada [HPC](../virtual-machines/linux/sizes-hpc.md) ou [ativadas para GPU](../virtual-machines/linux/sizes-gpu.md) tamanhos de VM, respectivamente. (Consulte [utilização com capacidade RDMA ou ativadas para GPU instâncias em conjuntos do Batch](batch-pool-compute-intensive-sizes.md).)

* **Tarefas por nó** -é selecionado ao selecionar um tamanho de nó supondo que uma tarefa seja executada num nó de cada vez. No entanto, poderá ser vantajoso ter várias tarefas (e, portanto, várias instâncias da aplicação) [executadas em paralelo](batch-parallel-node-tasks.md) em nós de computação durante a execução de tarefa. Neste caso, é comum escolher um tamanho de nó com vários núcleos para acomodar a maior necessidade de execução de tarefas paralelas.

* **Níveis para diferentes tarefas de carga** -todos os nós num conjunto têm o mesmo tamanho. Se quiser executar aplicações com requisitos de sistema e/ou níveis de carga diferentes, recomendamos utilizar conjuntos separados.

* **Disponibilidade de região** -série de uma VM ou tamanho poderá não estar disponível nas regiões onde criar as contas do Batch. Para verificar que um tamanho está disponível, consulte [produtos disponíveis por região](https://azure.microsoft.com/regions/services/).

* **Quotas** – a [núcleos quotas](batch-quota-limit.md#resource-quotas) no seu lote conta pode limitar o número de nós de um determinado tamanho, pode adicionar a um conjunto do Batch. Para pedir um aumento de quota, consulte [este artigo](batch-quota-limit.md#increase-a-quota). 

* **Configuração do pool** -em geral, tem mais opções de tamanho VM quando cria um conjunto na configuração da Máquina Virtual, em comparação com a configuração de serviço em nuvem.

## <a name="next-steps"></a>Passos Seguintes

* Para uma descrição geral aprofundada do Batch, consulte [soluções com o Batch de computação paralelas em grande escala de desenvolver](batch-api-basics.md).
* Para obter informações sobre a utilização de tamanhos de VM de computação intensiva, consulte [utilização com capacidade RDMA ou ativadas para GPU instâncias em conjuntos do Batch](batch-pool-compute-intensive-sizes.md).
