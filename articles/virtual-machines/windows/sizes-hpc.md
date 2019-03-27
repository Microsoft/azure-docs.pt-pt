---
title: Tamanhos de VM do Windows Azure – HPC | Documentos da Microsoft
description: Lista os tamanhos diferentes disponíveis para Windows computação de alto desempenho máquinas virtuais no Azure. Lista as informações sobre o número de vCPUs, discos de dados e NICs, bem como armazenamento e débito de rede largura de banda para tamanhos nesta série.
services: virtual-machines-windows
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 58d4ced041b6f5cf767b45191e28a4b395f584b6
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58480265"
---
# <a name="high-performance-compute-vm-sizes"></a>Tamanhos de VM de computação de elevado desempenho

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **Sistema operativo** -Windows Server 2016, Windows Server 2012 R2, Windows Server 2012

* **MPI** -Microsoft MPI (MS-MPI) 2012 R2 ou posterior, Intel MPI biblioteca 5.x

  Implementações de MPI suportadas utilizam a interface direta de rede do Microsoft para comunicar entre instâncias. 

* **Espaço de endereços de rede RDMA** -rede o RDMA no Azure reserva-se o endereço espaço 172.16.0.0/16. Para executar aplicações MPI em instâncias implementadas numa rede virtual do Azure, certifique-se de que o espaço de endereços de rede virtual não se sobreponha a rede RDMA.

* **Extensão de HpcVmDrivers VM** - em VMs com capacidade RDMA, adicione a extensão de HpcVmDrivers para instalar controladores de dispositivo de rede Windows para a conectividade RDMA. (Em determinadas implementações das instâncias A8 e A9, a extensão de HpcVmDrivers é adicionada automaticamente.) Para adicionar a extensão de VM a uma VM, pode utilizar [do Azure PowerShell](/powershell/azure/overview) cmdlets. 

  
  O seguinte comando instala a extensão de HpcVMDrivers versão 1.1 mais recente numa VM com capacidade RDMA existente com o nome *myVM* implementados no grupo de recursos com o nome *myResourceGroup* no  *Oeste dos E.U.A.* região:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  
  Para obter mais informações, consulte [extensões de Máquina Virtual e funcionalidades](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Também pode trabalhar com as extensões para as VMs implementadas no [modelo de implementação clássica](classic/manage-extensions.md).

### <a name="cluster-configuration-options"></a>Opções de configuração de cluster

O Azure fornece várias opções para criação de clusters de VMs do Windows HPC que possa comunicar com a rede RDMA, incluindo: 

* **Máquinas virtuais** -implementar as VMs de HPC com capacidade RDMA no mesmo conjunto (ao utilizar o modelo de implementação Azure Resource Manager) de disponibilidade. Se utilizar o modelo de implementação clássica, implemente as VMs no mesmo serviço cloud. 

* **Conjuntos de dimensionamento de máquinas virtuais** - numa escala VM definir, certifique-se de que limite a implementação para um único grupo de colocação. Por exemplo, num modelo do Resource Manager, defina o `singlePlacementGroup` propriedade `true`. 

* **Azure CycleCloud** -crie um cluster HPC no [Azure CycleCloud](/azure/cyclecloud/) para executar trabalhos de MPI em nós do Windows.

* **O Azure Batch** -crie uma [do Azure Batch](/azure/batch/) nós de computação do conjunto para executar cargas de trabalho MPI no Windows Server. Para obter mais informações, consulte [utilização com capacidade RDMA ou ativadas para GPU instâncias em conjuntos do Batch](../../batch/batch-pool-compute-intensive-sizes.md). Consulte também os [Batch Shipyard](https://github.com/Azure/batch-shipyard) projeto, para executar cargas de trabalho baseadas em contentores no Batch.

* **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) inclui um ambiente de tempo de execução para MS-MPI, que utiliza a rede de RDMA do Azure quando implementadas em VMs do Windows com capacidade RDMA. Por exemplo implementações, consulte [configurar um cluster Windows RDMA com HPC Pack para executar aplicações MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Outros tamanhos
- [Fins gerais](sizes-general.md)
- [Com otimização de computação](sizes-compute.md)
- [Com otimização de memória](../virtual-machines-windows-sizes-memory.md)
- [Com otimização de armazenamento](../virtual-machines-windows-sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos Seguintes

- Para listas de verificação utilizar as instâncias de computação intensiva, com o HPC Pack no Windows Server, consulte [configurar um cluster Windows RDMA com HPC Pack para executar aplicações MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

- Para utilizar instâncias de computação intensiva ao executar aplicações MPI com o Azure Batch, veja [utilizar tarefas de várias instâncias para executar aplicações Message Passing Interface (MPI) no Azure Batch](../../batch/batch-mpi.md).

- Saiba mais sobre como [computação do Azure (ACU) de unidades](acu.md) pode ajudá-lo a comparar o desempenho de computação nos SKUs do Azure.




