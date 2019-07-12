---
title: Descrição geral de VM HC series - máquinas virtuais do Azure | Documentos da Microsoft
description: Saiba mais sobre o suporte de pré-visualização para o tamanho VM da série HC no Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 6cdb539846104f70dabf684925685fb062fea8af
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797557"
---
# <a name="hc-series-virtual-machine-overview"></a>Descrição geral de máquina virtual de série HC

Maximizar o desempenho de aplicações de HPC em processadores dimensionável do Intel Xeon requer uma abordagem consciente para colocação de processo sobre essa nova arquitetura. Aqui, descrevemos nossa implementação do mesmo em VMs de série HC do Azure para aplicações de HPC. Utilizamos o termo "pNUMA" para fazer referência a um domínio físico de NUMA e "vNUMA" para fazer referência a um domínio NUMA virtualizado. Da mesma forma, utilizamos o termo "pCore" para fazer referência a núcleos de CPU físicos, e "vCore" para fazer referência a virtualizado núcleos de CPU.

Fisicamente, um servidor HC é 2 * 24-core Intel Xeon Platinum 8168 CPUs para um total de 48 núcleos físicos. Cada CPU é um domínio único pNUMA e unificação de acesso a seis canais de DRAM. Funcionalidade de Intel Xeon Platinum CPUs por 4 x de cache L2 maior do que nas gerações anteriores (1 MB/núcleo-> 256 KB/core), reduzindo a cache de L3 comparado com anterior Intel CPUs (2,5 MB/núcleo-> 1.375 MB/core).

A topologia acima apresenta para a configuração de Hipervisor de HC series também. Para fornecer espaço para o hipervisor do Azure operar sem interferir com a VM, a Microsoft reserva-pCores 0-1 e 24 e 25 (ou seja, os primeiro 2 pCores em cada soquete). Em seguida, Atribuímos pNUMA domínios todos os núcleos restantes para a VM. Portanto, verá a VM:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44` núcleos por VM

A VM não tem conhecimento que pCores 0-1 e 24 e 25 não estavam tendo em conta a ele. Portanto, ele expõe cada vNUMA como se ele tivesse nativamente 22 núcleos.

Intel Xeon Platinum, Gold e Silver CPUs também introduzem uma rede de malha 2D-die para comunicação dentro e externo para o socket de CPU. Recomendamos vivamente a afixação de processo para otimizar o desempenho e consistência. A afixação de processo irá funcionar em VMs de série HC porque silicone subjacente é exposta como-é a VM do convidado. Para obter mais informações, consulte [arquitetura Intel Xeon SP](https://bit.ly/2RCYkiE).

O diagrama seguinte mostra a diferenciação de núcleos reservados para o hipervisor do Azure e a VM de série HC.

![Segregação de núcleos reservados para o hipervisor do Azure e a VM de série HC](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Especificações de hardware

| Especificações de hardware          | VM da série HC                     |
|----------------------------------|----------------------------------|
| Núcleos                            | 40 (HT desativada)                 |
| CPU                              | Intel Xeon Platinum 8168*        |
| Frequência de CPU (não AVX)          | 3,7 GHz (um núcleo), 3.4 2,7 GHz (todos os núcleos) |
| Memória                           | 8 GB/núcleos (total de 352)            |
| Disco Local                       | NVMe de 700 GB                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 * * |
| Rede                          | 50 Gb Ethernet (40 Gb utilizável) do Azure segundo fins SmartNIC *** |

## <a name="software-specifications"></a>Especificações de software

| Especificações de software     | VM da série HC          |
|-----------------------------|-----------------------|
| Tamanho de tarefa de MPI máx.            | 4400 núcleos (100 máquinas virtuais conjuntos de dimensionamento), 8800 núcleos (200 conjuntos de dimensionamento de máquina virtual) |
| Suporte MPI                 | MVAPICH2, OpenMPI, MPICH, plataforma MPI, Intel MPI  |
| Estruturas adicionais       | X de comunicação unificada, libfabric, PGAS |
| Suporte de armazenamento do Azure       | E padrão + Premium (4 discos máx.) |
| Suporte de SO para SRIOV RDMA   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+ |
| Suporte de CycleCloud do Azure    | Sim                         |
| Suporte do Azure Batch         | Sim                         |

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre tamanhos de VM de HPC para [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) e [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) no Azure.

* Saiba mais sobre [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) no Azure.
