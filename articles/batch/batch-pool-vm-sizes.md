---
title: Escolher tamanhos de VM para pools-Azure batch | Microsoft Docs
description: Como escolher entre os tamanhos de VM disponíveis para nós de computação em pools do lote do Azure
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/01/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: f894a7438c56c3830eb62c516447195f10fc3b76
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094661"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Escolher um tamanho de VM para nós de computação em um pool do lote do Azure

Ao selecionar um tamanho de nó para um pool do lote do Azure, você pode escolher entre quase todos os tamanhos de VM disponíveis no Azure. O Azure oferece uma variedade de tamanhos para VMs Linux e Windows para diferentes cargas de trabalho.

Há algumas exceções e limitações para escolher um tamanho de VM:

* Não há suporte para alguns tamanhos de VM ou séries de VM no lote.
* Alguns tamanhos de VM são restritos e precisam ser especificamente habilitados para que possam ser alocados.

## <a name="supported-vm-series-and-sizes"></a>Séries e tamanhos de VM com suporte

### <a name="pools-in-virtual-machine-configuration"></a>Pools na configuração de máquina virtual

Os pools do lote na configuração de máquina virtual dão suporte a quase todos os tamanhos de VM ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). Consulte a tabela a seguir para saber mais sobre tamanhos e restrições com suporte.

Os tamanhos de VM promocional ou de visualização não listados não são garantidos para suporte.

| Série de VM  | Tamanhos com suporte | Modo de alocação do pool de contas do lote<sup>1</sup> |
|------------|---------|-----------------|
| Série A básica | Todos os tamanhos *, exceto* Basic_A0 (a0) | Any |
| Série A | Todos os tamanhos *, exceto* Standard_A0 | Any |
| Série Av2 | Todos os tamanhos | Any |
| Série B | Nenhum | Indisponível |
| Série DC | Nenhum | Indisponível |
| Dv2, série Dsv2 | Todos os tamanhos | Any |
| Dv3, série Dsv3 | Todos os tamanhos | Any |
| [Tamanhos com otimização de memória](../virtual-machines/linux/sizes-memory.md) | Nenhum | Indisponível |
| Série Fsv2 | Todos os tamanhos | Any |
| Série H | Todos os tamanhos | Any |
| Série HB<sup>2</sup> | Todos os tamanhos | Any |
| HC-série<sup>2</sup> | Todos os tamanhos | Any |
| Série Ls | Todos os tamanhos | Any |
| Série Lsv2 | Nenhum | Indisponível |
| Série M | Standard_M64ms (somente baixa prioridade), Standard_M128s (somente baixa prioridade) | Any |  
| Série NCv2<sup>2</sup> | Todos os tamanhos | Any |
| Série NCv3<sup>2</sup> | Todos os tamanhos | Any |
| Série ND<sup>2</sup> | Todos os tamanhos | Any |
| Série NDv2 | Todos os tamanhos | Modo de subscrição do utilizador |
| Série NV | Todos os tamanhos | Any |
| Série NVv3 | Nenhum | Indisponível |
| SAP HANA | Nenhum | Indisponível |

<sup>1</sup> há suporte parcial para algumas séries VM mais recentes inicialmente. Essas séries de VMs podem ser alocadas por contas do lote com o **modo de alocação do pool** definido como assinatura do **usuário**. Consulte [gerenciar contas do lote](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode) para obter mais informações sobre a configuração da conta do lote. Consulte [cotas e limites](batch-quota-limit.md) para saber como solicitar cota para essas séries de VMs com suporte parcial para contas do lote de **assinatura do usuário** .  

<sup>2</sup> esses tamanhos de VM podem ser alocados em pools do lote na configuração da máquina virtual, mas você deve solicitar um [aumento de cota](batch-quota-limit.md#increase-a-quota)específico.

### <a name="pools-in-cloud-service-configuration"></a>Pools na configuração do serviço de nuvem

Os pools do lote na configuração do serviço de nuvem dão suporte a todos os [tamanhos de VM para serviços de nuvem](../cloud-services/cloud-services-sizes-specs.md) **, exceto** para o seguinte:

| Série de VM  | Tamanhos sem suporte |
|------------|-------------------|
| Série A   | Extra pequeno       |
| Série Av2 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Considerações de tamanho

* **Requisitos do aplicativo** -considere as características e os requisitos do aplicativo que será executado nos nós. Alguns aspetos, como se a aplicação tem vários threads e a quantidade de memória que consome, podem ajudar a determinar o tamanho de nó mais adequado e económico. Para [cargas de trabalho MPI](batch-mpi.md) de várias instâncias ou aplicativos CUDA, considere tamanhos especializados [de VM habilitados para](../virtual-machines/linux/sizes-gpu.md) o [HPC](../virtual-machines/linux/sizes-hpc.md) ou GPU, respectivamente. (Consulte [usar instâncias compatíveis com RDMA ou habilitadas para GPU em pools do lote](batch-pool-compute-intensive-sizes.md).)

* **Tarefas por nó** – é comum selecionar um tamanho de nó supondo que uma tarefa seja executada em um nó por vez. No entanto, pode ser vantajoso ter várias tarefas (e, portanto, várias instâncias de aplicativo) [executadas em paralelo](batch-parallel-node-tasks.md) em nós de computação durante a execução do trabalho. Nesse caso, é comum escolher um tamanho de nó de vários núcleos para acomodar a maior demanda de execução de tarefa paralela.

* **Níveis de carga para tarefas diferentes** – todos os nós em um pool têm o mesmo tamanho. Se quiser executar aplicações com requisitos de sistema e/ou níveis de carga diferentes, recomendamos utilizar conjuntos separados.

* **Disponibilidade de região** – uma série ou tamanho de VM pode não estar disponível nas regiões em que você cria suas contas do lote. Para verificar se há um tamanho disponível, consulte [produtos disponíveis por região](https://azure.microsoft.com/regions/services/).

* **Cotas** -as [cotas](batch-quota-limit.md#resource-quotas) de núcleos em sua conta do lote podem limitar o número de nós de um determinado tamanho que você pode adicionar a um pool do lote. Para solicitar um aumento de cota, consulte [Este artigo](batch-quota-limit.md#increase-a-quota). 

* **Configuração de pool** -em geral, você tem mais opções de tamanho de VM ao criar um pool na configuração de máquina virtual, em comparação com a configuração do serviço de nuvem.

## <a name="next-steps"></a>Passos seguintes

* Para obter uma visão geral detalhada do lote, consulte [desenvolver soluções de computação paralela em larga escala com o lote](batch-api-basics.md).
* Para obter informações sobre como usar tamanhos de VM com uso intensivo de computação, consulte [usar instâncias compatíveis com RDMA ou habilitadas para GPU em pools do lote](batch-pool-compute-intensive-sizes.md).
