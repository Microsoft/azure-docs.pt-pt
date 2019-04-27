---
title: Tamanhos de VM do Linux do Azure – HPC | Documentos da Microsoft
description: Lista os tamanhos diferentes disponíveis para máquinas virtuais no Azure de computação de Linux ao alto desempenho. Lista as informações sobre o número de vCPUs, discos de dados e NICs, bem como armazenamento e débito de rede largura de banda para tamanhos nesta série.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
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
ms.openlocfilehash: 44b965bd60d976d4d28dc5e31d78a1c838d4ee02
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542361"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Tamanhos de máquinas virtuais de computação de elevado desempenho

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Apenas o Intel MPI 5.x versões são suportadas.

> [!NOTE]
> Versões posteriores (2017, 2018) de tempo de execução do Intel MPI biblioteca pode ou não ser compatível com os controladores de RDMA do Linux do Azure.

### <a name="distributions"></a>Distribuições
 
Implemente uma VM de computação intensiva de uma das imagens no Azure Marketplace que suporta a conetividade RDMA:
  
* **Ubuntu** - Ubuntu Server 16.04 LTS. Configurar controladores RDMA na VM e registar com o Intel para transferir a MPI Intel:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **SUSE Linux Enterprise Server** -SLES 12 SP3 para HPC, SLES 12 SP3 para HPC (Premium), SLES 12 SP1 para HPC, SLES 12 SP1 para HPC (Premium). Controladores de RDMA são instalados e os pacotes de Intel MPI são distribuídos na VM. Instale o MPI executando o seguinte comando:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **Baseada em centOS HPC** -baseada em CentOS 6.5 HPC ou uma versão posterior (para a série H, versão 7.1 ou posterior é recomendado). Drivers RDMA e Intel MPI 5.1 são instalados na VM.  
 
  > [!NOTE]
  > Nas imagens de HPC baseada em CentOS, as atualizações de kernel estão desativadas no **yum** ficheiro de configuração. Isso ocorre porque os drivers de RDMA do Linux são distribuídos como um pacote RPM e atualizações de controladores poderão não funcionar se o kernel é atualizado.
  > 
 
### <a name="cluster-configuration-options"></a>Opções de configuração de cluster

O Azure fornece várias opções para criar clusters HPC de VMs do Linux que podem se comunicar com a rede RDMA, incluindo: 

* **Máquinas virtuais** -implementar as VMs de HPC com capacidade RDMA no mesmo conjunto (ao utilizar o modelo de implementação Azure Resource Manager) de disponibilidade. Se utilizar o modelo de implementação clássica, implemente as VMs no mesmo serviço cloud. 

* **Conjuntos de dimensionamento de máquinas virtuais** - numa escala VM definir, certifique-se de que limite a implementação para um único grupo de colocação. Por exemplo, num modelo do Resource Manager, defina o `singlePlacementGroup` propriedade `true`. 

* **Azure CycleCloud** -crie um cluster HPC no [Azure CycleCloud](/azure/cyclecloud/) para executar trabalhos de MPI em nós do Linux.

* **O Azure Batch** -crie uma [do Azure Batch](/azure/batch/) nós de computação do conjunto para executar cargas de trabalho MPI no Linux. Para obter mais informações, consulte [utilização com capacidade RDMA ou ativadas para GPU instâncias em conjuntos do Batch](../../batch/batch-pool-compute-intensive-sizes.md). Consulte também os [Batch Shipyard](https://github.com/Azure/batch-shipyard) projeto, para executar cargas de trabalho baseadas em contentores no Batch.

* **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) suporta várias distribuições do Linux para serem executadas em nós de computação implementados em VMs do Azure com capacidade RDMA, gerido por um nó principal do Windows Server. Para um exemplo de implementação, consulte [criar HPC Pack RDMA Cluster do Linux no Azure](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

Dependendo de sua escolha de ferramenta de gerenciamento de cluster, a configuração de sistema adicionais pode ser necessários para executar trabalhos de MPI. Por exemplo, num cluster de VMs, se pretender estabelecer confiança entre os nós de cluster por gerar chaves SSH ou através do estabelecimento de confiança passe de SSH.

### <a name="network-topology-considerations"></a>Considerações de topologia de rede
* Em VMs capacidade RDMA do Linux no Azure, Eth1 está reservado para o tráfego de rede RDMA. Não altere as definições de Eth1 ou todas as informações no ficheiro de configuração que faça referência a esta rede. Eth0 está reservado para regular tráfego de rede do Azure.

* A rede RDMA no Azure reserva-se o endereço espaço 172.16.0.0/16. 




## <a name="other-sizes"></a>Outros tamanhos
- [Fins gerais](sizes-general.md)
- [Com otimização de computação](sizes-compute.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre como [computação do Azure (ACU) de unidades](acu.md) pode ajudá-lo a comparar o desempenho de computação nos SKUs do Azure.




