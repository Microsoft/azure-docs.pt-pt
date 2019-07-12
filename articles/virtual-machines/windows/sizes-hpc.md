---
title: Tamanhos de VM do Windows Azure – HPC | Documentos da Microsoft
description: Lista os tamanhos diferentes disponíveis para Windows computação de alto desempenho máquinas virtuais no Azure. Lista as informações sobre o número de vCPUs, discos de dados e NICs, bem como armazenamento e débito de rede largura de banda para tamanhos nesta série.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck;amverma
ms.openlocfilehash: 62c6bb906d9c9935be2da148f24d5285cbf0ed67
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710344"
---
# <a name="high-performance-compute-vm-sizes"></a>Tamanhos de VM de computação de elevado desempenho

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **Sistema operativo** -Windows Server 2016 em todos os acima HPC série de VMs. Windows Server 2012 R2, Windows Server 2012 também são suportados nas VMs não SR-IOV ativadas (, por conseguinte, excluindo HB e HC).

* **MPI** -o SR-IOV ativados tamanhos de VM no Azure (HB, HC) permitem que praticamente qualquer tipo de MPI para ser utilizado com Mellanox OFED.
Em VMs de ativado não SR-IOV, implementações de MPI suportadas utilizam a interface de Microsoft rede direto (ND) para comunicar entre instâncias. Por conseguinte, apenas Microsoft MPI (MS-MPI) 2012 R2 ou posterior e Intel MPI 5.x versões são suportadas. Versões posteriores (2017, 2018) do runtime de Intel MPI biblioteca pode ou não ser compatível com os controladores de RDMA do Azure.

* **Extensão de InfiniBandDriverWindows VM** - em VMs com capacidade RDMA, adicione a extensão de InfiniBandDriverWindows para ativar InfiniBand. Esta extensão de VM do Windows instala controladores direto de rede do Windows (nas VMs de não-SR-IOV) ou controladores de Mellanox OFED (em VMs de SR-IOV) para conetividade RDMA.
Em determinadas implementações das instâncias A8 e A9, a extensão de HpcVmDrivers é adicionada automaticamente. Tenha em atenção que a extensão de HpcVmDrivers VM está a ser preterida; não será atualizado. Para adicionar a extensão de VM a uma VM, pode utilizar [do Azure PowerShell](/powershell/azure/overview) cmdlets. 

  O seguinte comando instala a extensão de InfiniBandDriverWindows mais recente versão 1.0 numa VM com capacidade RDMA existente com o nome *myVM* implementados no grupo de recursos com o nome *myResourceGroup* no  *Oeste dos E.U.A.* região:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```
  Em alternativa, as extensões de VM podem ser incluídas nos modelos do Azure Resource Manager para facilitar a implementação, com o seguinte elemento JSON:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  O seguinte comando instala a extensão de InfiniBandDriverWindows mais recente versão 1.0 em todas as VMs com capacidade RDMA num conjunto nomeado de dimensionamento VM existente *myVMSS* implementados no grupo de recursos com o nome *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  Para obter mais informações, consulte [extensões de Máquina Virtual e funcionalidades](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Também pode trabalhar com as extensões para as VMs implementadas no [modelo de implementação clássica](classic/manage-extensions.md).

* **Espaço de endereços de rede RDMA** -rede o RDMA no Azure reserva-se o endereço espaço 172.16.0.0/16. Para executar aplicações MPI em instâncias implementadas numa rede virtual do Azure, certifique-se de que o espaço de endereços de rede virtual não se sobreponha a rede RDMA.


### <a name="cluster-configuration-options"></a>Opções de configuração de cluster

O Azure fornece várias opções para criação de clusters de VMs do Windows HPC que possa comunicar com a rede RDMA, incluindo: 

* **Máquinas virtuais** -implementar as VMs de HPC com capacidade RDMA no mesmo conjunto (ao utilizar o modelo de implementação Azure Resource Manager) de disponibilidade. Se utilizar o modelo de implementação clássica, implemente as VMs no mesmo serviço cloud. 

* **Os conjuntos de dimensionamento de máquinas virtuais** - In de dimensionamento de máquinas virtuais do conjunto, certifique-se de que limite a implementação para um único grupo de colocação. Por exemplo, num modelo do Resource Manager, defina o `singlePlacementGroup` propriedade `true`. 

* **MPI entre máquinas virtuais** - se a comunicação de MPI se for necessário entre máquinas virtuais (VMs), certifique-se de que as VMs estão no mesmo conjunto de disponibilidade ou a virtual máquina mesmo conjunto de dimensionamento.

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
