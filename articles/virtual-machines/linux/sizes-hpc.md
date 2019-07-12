---
title: Tamanhos de VM do Linux do Azure – HPC | Documentos da Microsoft
description: Lista os tamanhos diferentes disponíveis para máquinas virtuais no Azure de computação de Linux ao alto desempenho. Lista as informações sobre o número de vCPUs, discos de dados e NICs, bem como armazenamento e débito de rede largura de banda para tamanhos nesta série.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 847f25d9be1a8654bbc0435d7874acb0ff793304
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695590"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Tamanhos de máquinas virtuais de computação de elevado desempenho

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

O SR-IOV ativados tamanhos de VM no Azure permitem que praticamente qualquer tipo de MPI a ser utilizado.
Em VMs de ativado não SR-IOV, são suportadas apenas versões de 5.x do Intel MPI. Versões posteriores (2017, 2018) de tempo de execução do Intel MPI biblioteca pode ou não ser compatível com os controladores de RDMA do Linux do Azure.


### <a name="supported-os-images"></a>Imagens de SO suportadas
 
O Azure Marketplace tem muitas distribuições de Linux que suportam a conetividade RDMA:
  
* **Baseada em centOS HPC** – para não-SR-IOV ativado VMs, a versão baseada em CentOS 6.5 HPC ou uma versão posterior, até 7.5 são adequadas. Para VMs de série H, recomenda-se as versões 7.1 para 7.5. Drivers RDMA e Intel MPI 5.1 são instalados na VM.
  Para VMs de SR-IOV, CentOS HPC a 7,6 vem otimizado e pré-carregados com os controladores RDMA e vários pacotes de MPI instalados.
  Outras imagens de VM RHEL/CentOS, adicione a extensão de InfiniBandLinux para ativar InfiniBand. Esta extensão de VM do Linux instala controladores de Mellanox OFED (em VMs de SR-IOV) para conetividade RDMA. O cmdlet PowerShell seguinte instala a versão mais recente (versão 1.0) da extensão InfiniBandDriverLinux numa VM existente com capacidade RDMA. A VM com capacidade RDMA *myVM* e é implementada no grupo de recursos com o nome *myResourceGroup* no *E.U.A. oeste* região da seguinte forma:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  Em alternativa, as extensões de VM podem ser incluídas nos modelos do Azure Resource Manager para facilitar a implementação com o seguinte elemento JSON:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  O seguinte comando instala a extensão de InfiniBandDriverLinux mais recente versão 1.0 em todas as VMs com capacidade RDMA num conjunto nomeado de dimensionamento VM existente *myVMSS* implementados no grupo de recursos com o nome *myResourceGroup*:
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > Nas imagens de HPC baseada em CentOS, as atualizações de kernel estão desativadas no **yum** ficheiro de configuração. Isso ocorre porque os drivers de RDMA do Linux são distribuídos como um pacote RPM e atualizações de controladores poderão não funcionar se o kernel é atualizado.
  >
  

* **SUSE Linux Enterprise Server** -SLES 12 SP3 para HPC, SLES 12 SP3 para HPC (Premium), SLES 12 SP1 para HPC, SLES 12 SP1 para HPC (Premium), SLES 12 SP4 e SLES 15. Controladores de RDMA são instalados e os pacotes de Intel MPI são distribuídos na VM. Instale o MPI executando o seguinte comando:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** - Ubuntu Server 16.04 LTS, 18.04 LTS. Configurar controladores RDMA na VM e registar com o Intel para transferir a MPI Intel:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Para obter mais detalhes sobre como ativar o InfiniBand, configuração de MPI, consulte [InfiniBand ativar](../workloads/hpc/enable-infiniband.md).


### <a name="cluster-configuration-options"></a>Opções de configuração de cluster

O Azure fornece várias opções para criar clusters HPC de VMs do Linux que podem se comunicar com a rede RDMA, incluindo: 

* **Máquinas virtuais** -implementar as VMs de HPC com capacidade RDMA no mesmo conjunto (ao utilizar o modelo de implementação Azure Resource Manager) de disponibilidade. Se utilizar o modelo de implementação clássica, implemente as VMs no mesmo serviço cloud. 

* **Os conjuntos de dimensionamento de máquinas virtuais** - In de dimensionamento de máquinas virtuais do conjunto, certifique-se de que limite a implementação para um único grupo de colocação. Por exemplo, num modelo do Resource Manager, defina o `singlePlacementGroup` propriedade `true`. 

* **MPI entre máquinas virtuais** - se a comunicação de MPI se for necessário entre máquinas virtuais (VMs), certifique-se de que as VMs estão no mesmo conjunto de disponibilidade ou a virtual máquina mesmo conjunto de dimensionamento.

* **Azure CycleCloud** -crie um cluster HPC no [Azure CycleCloud](/azure/cyclecloud/) para executar trabalhos de MPI em nós do Linux.

* **O Azure Batch** -crie uma [do Azure Batch](/azure/batch/) nós de computação do conjunto para executar cargas de trabalho MPI no Linux. Para obter mais informações, consulte [utilização com capacidade RDMA ou ativadas para GPU instâncias em conjuntos do Batch](../../batch/batch-pool-compute-intensive-sizes.md). Consulte também os [Batch Shipyard](https://github.com/Azure/batch-shipyard) projeto, para executar cargas de trabalho baseadas em contentores no Batch.

* **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) suporta várias distribuições do Linux para serem executadas em nós de computação implementados em VMs do Azure com capacidade RDMA, gerido por um nó principal do Windows Server. Para um exemplo de implementação, consulte [criar HPC Pack RDMA Cluster do Linux no Azure](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).


### <a name="network-considerations"></a>Considerações de rede
* Não-SR-iov, com RDMA ativado VMs do Linux no Azure, eth1 está reservada para o tráfego de rede RDMA. Não altere as definições de eth1 ou todas as informações no ficheiro de configuração que faça referência a esta rede.
* No SR-IOV ativado VMs (HB e HC série), ib0 está reservado para o tráfego de rede RDMA.
* A rede RDMA no Azure reserva-se o endereço espaço 172.16.0.0/16. Para executar aplicações MPI em instâncias implementadas numa rede virtual do Azure, certifique-se de que o espaço de endereços de rede virtual não se sobreponha a rede RDMA.
* Dependendo de sua escolha de ferramenta de gerenciamento de cluster, a configuração de sistema adicionais pode ser necessários para executar trabalhos de MPI. Por exemplo, num cluster de VMs, se pretender estabelecer confiança entre os nós de cluster por gerar chaves SSH ou através do estabelecimento de passe inícios de sessão SSH.


## <a name="other-sizes"></a>Outros tamanhos
- [Fins gerais](sizes-general.md)
- [Com otimização de computação](sizes-compute.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como configurar, otimizar e dimensionar [cargas de trabalho HPC](../workloads/hpc/configure.md) no Azure.
- Saiba mais sobre como [computação do Azure (ACU) de unidades](acu.md) pode ajudá-lo a comparar o desempenho de computação nos SKUs do Azure.
