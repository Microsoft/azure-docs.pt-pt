---
title: Escolha tamanhos VM para piscinas
description: Como escolher entre os tamanhos vm disponíveis para os nódosos de computação em piscinas de Lote Azure
ms.topic: how-to
ms.date: 09/12/2019
ms.custom: seodec18
ms.openlocfilehash: a299ce05caa02432f96dfbb5eb675bc2d6f82ab5
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83723719"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Escolha um tamanho VM para comum de cafés em uma piscina de lote azure

Ao selecionar um tamanho de nó para uma piscina de Lote Azure, pode escolher entre quase todos os tamanhos vm disponíveis em Azure. O Azure oferece uma gama de tamanhos para VMs Linux e Windows para diferentes cargas de trabalho.

Existem algumas exceções e limitações para escolher um tamanho VM:

* Algumas séries VM ou tamanhos VM não são suportadas em Batch.
* Alguns tamanhos vm são restritos e precisam de ser especificamente ativados antes de poderem ser atribuídos.

## <a name="supported-vm-series-and-sizes"></a>Séries e tamanhos VM suportados

### <a name="pools-in-virtual-machine-configuration"></a>Piscinas na configuração da Máquina Virtual

As piscinas de lote na configuração da Máquina Virtual suportam quase todos os tamanhos vm[(Linux,](../virtual-machines/linux/sizes.md) [Windows).](../virtual-machines/windows/sizes.md) Consulte a tabela seguinte para saber mais sobre tamanhos e restrições suportadas.

| Série das VMs  | Tamanhos suportados |
|------------|---------|
| Básico A | Todos os *tamanhos, exceto* Basic_A0 (A0) |
| A | Todos os tamanhos, *exceto* Standard_A0 |
| Av2 | Todos os tamanhos |
| B | Nenhum |
| DC | Nenhum |
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
| Rio Lsv2 | Nenhum - ainda não disponível |
| M<sup>1</sup> | Todos os tamanhos, com exceção de M64, M64m, M128, M128m |
| Mv2 | Nenhum - ainda não disponível |
| NC | Todos os tamanhos |
| NCv2<sup>1</sup> | Todos os tamanhos |
| NCv3<sup>1</sup> | Todos os tamanhos |
| ND<sup>1</sup> | Todos os tamanhos |
| NDv2<sup>1</sup> | Nenhum - ainda não disponível |
| NV | Todos os tamanhos |
| NVv3<sup>1</sup> | Todos os tamanhos |
| NVv4 | Nenhum |
| SAP HANA | Nenhum |

<sup>1</sup> Estes tamanhos vm podem ser atribuídos em piscinas de lote na configuração da Máquina Virtual, mas você deve criar uma nova conta Batch e solicitar um aumento específico de [quota](batch-quota-limit.md#increase-a-quota). Esta limitação será removida assim que a quota vCPU por série VM for totalmente suportada para as contas do Lote.

### <a name="pools-in-cloud-service-configuration"></a>Piscinas na configuração do Serviço cloud

As piscinas de lote na configuração do Serviço Cloud suportam todos os [tamanhos vm para serviços](../cloud-services/cloud-services-sizes-specs.md) de nuvem, **exceto** para o seguinte:

| Série das VMs  | Tamanhos não suportados |
|------------|-------------------|
| Série A   | Extra pequeno       |
| Série Av2 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Considerações sobre o tamanho

* **Requisitos de aplicação** - Considere as características e requisitos da aplicação que executará nos nódosos. Alguns aspetos, como se a aplicação tem vários threads e a quantidade de memória que consome, podem ajudar a determinar o tamanho de nó mais adequado e económico. Para cargas de trabalho de MPI em [várias instâncias](batch-mpi.md) ou aplicações CUDA, considere tamanhos de VM sancionados de [HPC](../virtual-machines/linux/sizes-hpc.md) ou [GPU,](../virtual-machines/linux/sizes-gpu.md) respectivamente. (Ver utilização de [instâncias com capacidade rdma ou GPU em piscinas](batch-pool-compute-intensive-sizes.md)de lote .)

* **Tarefas por nó** - É típico selecionar um tamanho de nó assumindo que uma tarefa corre num nó de cada vez. No entanto, pode ser vantajoso ter múltiplas tarefas (e, portanto, múltiplas instâncias de aplicação) [executadas em paralelo](batch-parallel-node-tasks.md) em nós de cálculo durante a execução do emprego. Neste caso, é comum escolher um tamanho de nó multicore para acomodar a procura crescente de execução de tarefas paralelas.

* Níveis de **carga para diferentes tarefas** - Todos os nós de uma piscina têm o mesmo tamanho. Se quiser executar aplicações com requisitos de sistema e/ou níveis de carga diferentes, recomendamos utilizar conjuntos separados.

* **Disponibilidade da região** - Uma série ou tamanho VM pode não estar disponível nas regiões onde cria as suas contas de Lote. Para verificar se existe um tamanho disponível, consulte [produtos disponíveis por região.](https://azure.microsoft.com/regions/services/)

* **Quotas** - As quotas de [núcleos](batch-quota-limit.md#resource-quotas) na sua conta Batch podem limitar o número de nós de um dado tamanho que pode adicionar a uma piscina de Lote. Para solicitar um aumento de quota, consulte [este artigo.](batch-quota-limit.md#increase-a-quota) 

* **Configuração** da piscina - Em geral, tem mais opções de tamanho VM quando cria uma piscina na configuração da Máquina Virtual, em comparação com a configuração do Cloud Service.

## <a name="next-steps"></a>Próximos passos

* Para uma visão geral aprofundada do Lote, consulte Desenvolver soluções de [computação paralela em larga escala com lote](batch-api-basics.md).
* Para obter informações sobre a utilização de tamanhos vm intensivos de computação, consulte [Utilize instâncias capazes de RDMA ou gpu-habilitadas em piscinas](batch-pool-compute-intensive-sizes.md)de lote .
