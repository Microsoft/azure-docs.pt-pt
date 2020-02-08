---
title: Escolher tamanhos de VM para pools-Azure batch | Microsoft Docs
description: Como escolher entre os tamanhos de VM disponíveis para nós de computação em pools do lote do Azure
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: c18190ec5e5d079d51630a976681717a78a46e00
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087041"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Escolher um tamanho de VM para nós de computação em um pool do lote do Azure

Ao selecionar um tamanho de nó para um pool do lote do Azure, você pode escolher entre quase todos os tamanhos de VM disponíveis no Azure. O Azure oferece uma variedade de tamanhos para VMs Linux e Windows para diferentes cargas de trabalho.

Há algumas exceções e limitações para escolher um tamanho de VM:

* Não há suporte para alguns tamanhos de VM ou séries de VM no lote.
* Alguns tamanhos de VM são restritos e precisam ser especificamente habilitados para que possam ser alocados.

## <a name="supported-vm-series-and-sizes"></a>Séries e tamanhos de VM com suporte

### <a name="pools-in-virtual-machine-configuration"></a>Pools na configuração de máquina virtual

As piscinas de lote na configuração da Máquina Virtual suportam quase todos os tamanhos vm[(Linux,](../virtual-machines/linux/sizes.md) [Windows).](../virtual-machines/windows/sizes.md) Consulte a tabela a seguir para saber mais sobre tamanhos e restrições com suporte.

| Série VM  | Tamanhos com suporte |
|------------|---------|
| Básico A | Todos os *tamanhos, exceto* Basic_A0 (A0) |
| A | Todos os tamanhos, *exceto* Standard_A0 |
| Av2 | Todos os tamanhos |
| B | Nenhuma |
| DC | Nenhuma |
| Dv2, DSv2 | Todos os tamanhos |
| Dv3, Dsv3 | Todos os tamanhos |
| Dav4 | Nenhum - ainda não disponível |
| Ev3, Esv3 | Todos os tamanhos, exceto E64is_v3 e E64i_v3 |
| Eav4 | Nenhum - ainda não disponível |
| F, Fs | Todos os tamanhos |
| Fsv2 | Todos os tamanhos |
| G, Gs | Todos os tamanhos |
| H | Todos os tamanhos |
| HB<sup>1</sup> | Todos os tamanhos |
| HBv2<sup>1</sup> | Todos os tamanhos |
| HC<sup>1</sup> | Todos os tamanhos |
| Ls | Todos os tamanhos |
| Lsv2 | Nenhum - ainda não disponível |
| M<sup>1</sup> | Todos os tamanhos, com exceção de M64, M64m, M128, M128m |
| Mv2 | Nenhum - ainda não disponível |
| NC | Todos os tamanhos |
| NCv2<sup>1</sup> | Todos os tamanhos |
| NCv3<sup>1</sup> | Todos os tamanhos |
| ND<sup>1</sup> | Todos os tamanhos |
| NDv2<sup>1</sup> | Nenhum - ainda não disponível |
| NV | Todos os tamanhos |
| NVv3<sup>1</sup> | Todos os tamanhos |
| NVv4 | Nenhuma |
| SAP HANA | Nenhuma |

<sup>1</sup> Estes tamanhos vm podem ser atribuídos em piscinas de lote na configuração da Máquina Virtual, mas você deve criar uma nova conta Batch e solicitar um aumento específico de [quota](batch-quota-limit.md#increase-a-quota). Esta limitação será removida assim que a quota vCPU por série VM for totalmente suportada para as contas do Lote.

### <a name="pools-in-cloud-service-configuration"></a>Pools na configuração do serviço de nuvem

As piscinas de lote na configuração do Serviço Cloud suportam todos os [tamanhos vm para serviços](../cloud-services/cloud-services-sizes-specs.md) de nuvem, **exceto** para o seguinte:

| Série VM  | Tamanhos sem suporte |
|------------|-------------------|
| Série A   | Extra pequeno       |
| Série Av2 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Considerações de tamanho

* **Requisitos de aplicação** - Considere as características e requisitos da aplicação que executará nos nódosos. Alguns aspetos, como se a aplicação tem vários threads e a quantidade de memória que consome, podem ajudar a determinar o tamanho de nó mais adequado e económico. Para cargas de trabalho de MPI em [várias instâncias](batch-mpi.md) ou aplicações CUDA, considere tamanhos de VM sancionados de [HPC](../virtual-machines/linux/sizes-hpc.md) ou [GPU,](../virtual-machines/linux/sizes-gpu.md) respectivamente. (Ver utilização de [instâncias com capacidade rdma ou GPU em piscinas](batch-pool-compute-intensive-sizes.md)de lote .)

* **Tarefas por nó** - É típico selecionar um tamanho de nó assumindo que uma tarefa corre num nó de cada vez. No entanto, pode ser vantajoso ter múltiplas tarefas (e, portanto, múltiplas instâncias de aplicação) [executadas em paralelo](batch-parallel-node-tasks.md) em nós de cálculo durante a execução do emprego. Nesse caso, é comum escolher um tamanho de nó de vários núcleos para acomodar a maior demanda de execução de tarefa paralela.

* Níveis de **carga para diferentes tarefas** - Todos os nós de uma piscina têm o mesmo tamanho. Se quiser executar aplicações com requisitos de sistema e/ou níveis de carga diferentes, recomendamos utilizar conjuntos separados.

* **Disponibilidade da região** - Uma série ou tamanho VM pode não estar disponível nas regiões onde cria as suas contas de Lote. Para verificar se existe um tamanho disponível, consulte [produtos disponíveis por região.](https://azure.microsoft.com/regions/services/)

* **Quotas** - As quotas de [núcleos](batch-quota-limit.md#resource-quotas) na sua conta Batch podem limitar o número de nós de um dado tamanho que pode adicionar a uma piscina de Lote. Para solicitar um aumento de quota, consulte [este artigo.](batch-quota-limit.md#increase-a-quota) 

* **Configuração** da piscina - Em geral, tem mais opções de tamanho VM quando cria uma piscina na configuração da Máquina Virtual, em comparação com a configuração do Cloud Service.

## <a name="next-steps"></a>Passos seguintes

* Para uma visão geral aprofundada do Lote, consulte Desenvolver soluções de [computação paralela em larga escala com lote](batch-api-basics.md).
* Para obter informações sobre a utilização de tamanhos vm intensivos de computação, consulte [Utilize instâncias capazes de RDMA ou gpu-habilitadas em piscinas](batch-pool-compute-intensive-sizes.md)de lote .
