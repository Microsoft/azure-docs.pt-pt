---
title: Escolha tamanhos e imagens VM para piscinas
description: Como escolher entre os tamanhos VM disponíveis e versões de SO para nó de computação em piscinas Azure Batch
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: seodec18
ms.openlocfilehash: f108e1347ef6c3c7df45c4b3d807a754f4867097
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800487"
---
# <a name="choose-a-vm-size-and-image-for-compute-nodes-in-an-azure-batch-pool"></a>Escolha um tamanho e imagem VM para nó de computação numa piscina Azure Batch

Ao selecionar um tamanho de nó para uma piscina Azure Batch, pode escolher entre quase todos os tamanhos VM disponíveis em Azure. O Azure oferece uma gama de tamanhos para Os VMs Linux e Windows para diferentes cargas de trabalho.

## <a name="supported-vm-series-and-sizes"></a>Séries e tamanhos VM suportados

Existem algumas exceções e limitações à escolha de um tamanho VM para a sua piscina de Lote:

- Algumas séries VM ou tamanhos VM não são suportados em Batch.
- Alguns tamanhos VM são restritos e precisam de ser especificamente ativados antes de poderem ser atribuídos.

### <a name="pools-in-virtual-machine-configuration"></a>Piscinas na configuração da Máquina Virtual

As piscinas de lote na configuração da Máquina Virtual suportam quase todos os [tamanhos VM](../virtual-machines/sizes.md). Consulte a tabela seguinte para saber mais sobre tamanhos e restrições suportados.

| Série das VMs  | Tamanhos suportados |
|------------|---------|
| Básico A | Todos os *tamanhos, exceto* Basic_A0 (A0) |
| A | Todos os tamanhos, *exceto* Standard_A0, Standard_A8, Standard_A9, Standard_A10, Standard_A11 |
| Av2 | Todos os tamanhos |
| B | Não suportado |
| DC | Não suportado |
| Dv2, DSv2 | Todos os tamanhos |
| Dv3 | Todos os tamanhos |
| Dav4 | Todos os tamanhos |
| Dasv4 | Todos os tamanhos |
| Ddv4 |  Todos os tamanhos |
| Dv4 | Não suportado |
| Ev3 | Todos os tamanhos, exceto E64is_v3 |
| Eav4 | Todos os tamanhos |
| Easv4 | Todos os tamanhos |
| Edv4 |  Todos os tamanhos |
| Ev4 | Não suportado |
| F, Fs | Todos os tamanhos |
| Fsv2 | Todos os tamanhos |
| G, Gs | Todos os tamanhos |
| H | Todos os tamanhos |
| HB | Todos os tamanhos |
| HBv2 | Todos os tamanhos |
| HC | Todos os tamanhos |
| Ls | Todos os tamanhos |
| Lsv2 | Todos os tamanhos |
| M | Todos os tamanhos |
| Mv2<sup>1</sup> | Todos os tamanhos |
| NC | Todos os tamanhos |
| NCv2 | Todos os tamanhos |
| NCv3 | Todos os tamanhos |
| NCasT4_v3 | Nenhum - ainda não disponível |
| ND | Todos os tamanhos |
| NDv2 | Nenhum - ainda não disponível |
| NV | Todos os tamanhos |
| NVv3 | Todos os tamanhos |
| NVv4 | Todos os tamanhos |
| SAP HANA | Não suportado |

<sup>1</sup> Estas séries VM só podem ser utilizadas com imagens VM de geração 2.

### <a name="using-generation-2-vm-images"></a>Usando imagens de geração 2 VM

Algumas séries VM, como [mv2,](../virtual-machines/mv2-series.md)só podem ser usadas com [imagens de geração 2 VM](../virtual-machines/generation-2.md). As imagens de geração 2 VM são especificadas como qualquer imagem VM, utilizando a propriedade 'sku' da configuração ['imageReference';](/rest/api/batchservice/pool/add#imagereference) as cordas 'sku' têm um sufixo como "-g2" ou "-gen2". Para obter uma lista de imagens VM suportadas por Batch, incluindo imagens de geração 2, utilize a ['lista de imagens suportadas'](/rest/api/batchservice/account/listsupportedimages) API, [PowerShell](/powershell/module/az.batch/get-azbatchsupportedimage)ou [Azure CLI](/cli/azure/batch/pool/supported-images).

### <a name="pools-in-cloud-service-configuration"></a>Piscinas na configuração do Serviço de Nuvem

As piscinas de lote na configuração do Serviço de Nuvem suportam todos os [tamanhos VM para serviços em nuvem,](../cloud-services/cloud-services-sizes-specs.md) **exceto** para os seguintes:

| Série das VMs  | Tamanhos não suportados |
|------------|-------------------|
| Série A   | Extra pequeno       |
| Série Av2 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Considerações sobre o tamanho

- **Requisitos de aplicação** - Considere as características e requisitos da aplicação que irá executar nos nós. Alguns aspetos, como se a aplicação tem vários threads e a quantidade de memória que consome, podem ajudar a determinar o tamanho de nó mais adequado e económico. Para [cargas de trabalho de MPI](batch-mpi.md) de várias instâncias ou aplicações CUDA, considere tamanhos de [VM](../virtual-machines/sizes-hpc.md) especializados ou [de GPU,](../virtual-machines/sizes-gpu.md) respectivamente. Para obter mais informações, consulte [a utilização de casos com capacidade de RDMA ou GPU em piscinas de Lote](batch-pool-compute-intensive-sizes.md).

- **Tarefas por nó** - É típico selecionar um tamanho de nó assumindo que uma tarefa é executado num nó de cada vez. No entanto, pode ser vantajoso ter múltiplas tarefas (e, portanto, múltiplas instâncias de aplicação) [executadas em paralelo em](batch-parallel-node-tasks.md) nós computacional durante a execução do trabalho. Neste caso, é comum escolher um tamanho de nó multicore para acomodar a procura crescente de execução paralela de tarefas.

- **Níveis de carga para diferentes tarefas** - Todos os nós numa piscina têm o mesmo tamanho. Se quiser executar aplicações com requisitos de sistema e/ou níveis de carga diferentes, recomendamos utilizar conjuntos separados.

- **Disponibilidade da região** - Uma série ou tamanho VM pode não estar disponível nas regiões onde cria as suas contas Batch. Para verificar se existe um tamanho, consulte [os produtos disponíveis por região.](https://azure.microsoft.com/regions/services/)

- **Quotas** - As [quotas de núcleos](batch-quota-limit.md#resource-quotas) na sua conta Batch podem limitar o número de nós de um dado tamanho que pode adicionar a uma piscina de Lote. Quando necessário, pode [solicitar um aumento de quota.](batch-quota-limit.md#increase-a-quota)

- **Configuração da piscina** - Em geral, tem mais opções de tamanho VM quando cria uma piscina na configuração da Máquina Virtual, em comparação com a configuração do Serviço de Nuvem.

## <a name="supported-vm-images"></a>Imagens VM suportadas

Utilize uma das seguintes APIs para devolver uma lista de imagens VM Windows e Linux atualmente suportadas pelo Batch, incluindo o agente de nó SKU IDs para cada imagem:

- Serviço de lote REST API: [Lista de imagens suportadas](/rest/api/batchservice/account/listsupportedimages)
- PowerShell: [Get-AzBatchSupportedImage](/powershell/module/az.batch/get-azbatchsupportedimage)
- Azure CLI: [az lote pool suportado imagens](/cli/azure/batch/pool/supported-images)

Recomenda-se vivamente evitar imagens com datas iminentes de fim de vida (EOL) de suporte ao lote. Estas datas podem ser descobertas através da [ `ListSupportedImages` API,](https://docs.microsoft.com/rest/api/batchservice/account/listsupportedimages) [PowerShell](https://docs.microsoft.com/powershell/module/az.batch/get-azbatchsupportedimage)ou [Azure CLI](https://docs.microsoft.com/cli/azure/batch/pool/supported-images). Consulte o [guia de boas práticas](best-practices.md) do Lote para obter mais informações sobre a seleção de imagem em Batch pool VM.

## <a name="next-steps"></a>Passos seguintes

- Conheça o fluxo de trabalho do [serviço Batch e os recursos primários,](batch-service-workflow-features.md) tais como piscinas, nós, empregos e tarefas.
- Para obter informações sobre a utilização de tamanhos de VM intensivos em computação, consulte [utilize casos com capacidade de RDMA ou GPU em piscinas de Lote](batch-pool-compute-intensive-sizes.md).
