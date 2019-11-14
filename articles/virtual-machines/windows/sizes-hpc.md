---
title: Tamanhos de VM Windows do Azure-HPC
description: Lista os diferentes tamanhos disponíveis para máquinas virtuais de computação de alto desempenho do Windows no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como taxa de transferência de armazenamento e largura de banda de rede para tamanhos desta série.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: e96c74eb4b9b96459bb5de7db2faeb47ed99a82e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74065442"
---
# <a name="high-performance-compute-vm-sizes"></a>Tamanhos de VM de computação de alto desempenho

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **Sistema operacional** -Windows Server 2016 em todas as VMs da série HPC acima. O Windows Server 2012 R2, o Windows Server 2012 também tem suporte em VMs não habilitadas para SR-IOV (portanto, excluindo HB e HC).

* **MPI** -os tamanhos de VM habilitados para Sr-IOV no Azure (HB, HC) permitem que quase qualquer tipo de MPI seja usado com o Mellanox ofed.
Em VMs não habilitadas para SR-IOV, as implementações de MPI com suporte usam a interface do Microsoft Network Direct (ND) para se comunicar entre instâncias. Portanto, somente as versões do Microsoft MPI (MS-MPI) 2012 R2 ou posterior e do Intel MPI 5. x têm suporte. Versões posteriores (2017, 2018) da biblioteca de tempo de execução do Intel MPI podem ou não ser compatíveis com os drivers RDMA do Azure.

* **Extensão de VM InfiniBandDriverWindows** -em VMs compatíveis com RDMA, adicione a extensão InfiniBandDriverWindows para habilitar a InfiniBand. Essa extensão de VM do Windows instala drivers diretos de rede do Windows (em VMs que não são de SR-IOV) ou drivers Mellanox OFED (em VMs SR-IOV) para conectividade RDMA.
Em determinadas implantações de instâncias A8 e A9, a extensão HpcVmDrivers é adicionada automaticamente. Observe que a extensão de VM HpcVmDrivers está sendo preterida; Ele não será atualizado. Para adicionar a extensão de VM a uma VM, você pode usar cmdlets [Azure PowerShell](/powershell/azure/overview) . 

  O comando a seguir instala a extensão mais recente da versão 1,0 do InfiniBandDriverWindows em uma VM compatível com RDMA existente chamada *myVM* implantada no grupo de recursos chamado *MyResource* Group na região *oeste dos EUA* :

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```
  Como alternativa, as extensões de VM podem ser incluídas em modelos de Azure Resource Manager para fácil implantação, com o seguinte elemento JSON:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  O comando a seguir instala a extensão mais recente da versão 1,0 do InfiniBandDriverWindows em todas as VMs compatíveis com RDMA em um conjunto de dimensionamento de VM existente chamado *myVMSS* implantado no grupo de recursos chamado *MyResource*Group:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  Para obter mais informações, consulte [recursos e extensões de máquina virtual](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Você também pode trabalhar com extensões para VMs implantadas no [modelo de implantação clássico](classic/manage-extensions.md).

* **Espaço de endereço de rede RDMA** -a rede RDMA no Azure reserva o espaço de endereço 172.16.0.0/16. Para executar aplicativos MPI em instâncias implantadas em uma rede virtual do Azure, certifique-se de que o espaço de endereço de rede virtual não se sobreponha à rede RDMA.


### <a name="cluster-configuration-options"></a>Opções de configuração de cluster

O Azure fornece várias opções para criar clusters de VMs do Windows HPC que podem se comunicar usando a rede RDMA, incluindo: 

* **Máquinas virtuais** – implante as VMs HPC compatíveis com RDMA no mesmo conjunto de disponibilidade (ao usar o modelo de implantação Azure Resource Manager). Se você usar o modelo de implantação clássico, implante as VMs no mesmo serviço de nuvem. 

* **Conjuntos de dimensionamento de máquinas virtuais** – em um conjunto de dimensionamento de máquinas virtuais, certifique-se de limitar a implantação a um único grupo de posicionamento. Por exemplo, em um modelo do Resource Manager, defina a propriedade `singlePlacementGroup` como `true`. 

* **MPI entre máquinas virtuais** – se a comunicação MPI for necessária entre máquinas virtuais (VMS), verifique se as VMs estão no mesmo conjunto de disponibilidade ou na mesma configuração de conjunto de dimensionamento da máquina virtual.

* **Azure CycleCloud** -crie um cluster HPC no [Azure CycleCloud](/azure/cyclecloud/) para executar trabalhos MPI em nós do Windows.

* **Lote do Azure** -crie um pool [do lote do Azure](/azure/batch/) para executar cargas de trabalho MPI em nós de computação do Windows Server. Para obter mais informações, consulte [usar instâncias compatíveis com RDMA ou habilitadas para GPU em pools do lote](../../batch/batch-pool-compute-intensive-sizes.md). Consulte também o projeto [Shipyard do lote](https://github.com/Azure/batch-shipyard) para executar cargas de trabalho baseadas em contêiner no lote.

* O **Microsoft HPC pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) inclui um ambiente de tempo de execução para MS-MPI que usa a rede RDMA do Azure quando implantado em VMs do Windows compatíveis com RDMA. Por exemplo, para implantações, consulte [configurar um cluster de RDMA do Windows com o HPC Pack para executar aplicativos MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Outros tamanhos
- [Fins gerais](sizes-general.md)
- [Com otimização de computação](sizes-compute.md)
- [Com otimização de memória](../virtual-machines-windows-sizes-memory.md)
- [Com otimização de armazenamento](../virtual-machines-windows-sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

- Para que as listas de verificação usem as instâncias de computação intensiva com o HPC Pack no Windows Server, consulte [configurar um cluster de RDMA do Windows com o HPC Pack para executar aplicativos MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

- Para usar instâncias de computação intensiva ao executar aplicativos MPI com o lote do Azure, consulte [usar tarefas de várias instâncias para executar aplicativos MPI (interface de transmissão de mensagens) no lote do Azure](../../batch/batch-mpi.md).

- Saiba mais sobre como as [unidades de computação do Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho de computação entre SKUs do Azure.
