---
title: Série H - Azure Virtual Machines
description: Especificações para os VMs da série H.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 08/01/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 797a036b9cf2e77dfbcdf8dc7596179c4673e6a6
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513745"
---
# <a name="h-series"></a>Série H

Os VMs da série H são otimizados para aplicações impulsionadas por altas frequências de CPU ou por grandes necessidades de memória por requisitos fundamentais. Os VMs da série H apresentam núcleos de processador Intel Xeon E5 2667 v3 da série H, até 14 GB de RAM por núcleo cpu, e sem hiperfinaling. A série H apresenta 56 Gb/seg Mellanox FDR InfiniBand numa configuração de árvore de gordura não bloqueada para um desempenho RDMA consistente. Os VMs da série H suportam Intel MPI 5.x e MS-MPI.

ACU: 290-300

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

Migração ao vivo: Não suportado

Atualizações de preservação da memória: Não suportadas

| Tamanho | vCPU | Processador | Memória (GB) | Largura de banda de memória GB/s | Frequência base cpu (GHz) | Frequência all-cores (GHz, pico) | Frequência monomússária (GHz, pico) | Desempenho de RDMA (Gb/s) | Suporte mpi | Armazenamento temporário (GB) | Discos de dados máximos | Débito máximo do disco: IOPS | Max Ethernet NICs |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |

<sup>1</sup> Para aplicações MPI, a rede de backend dedicada RDMA é ativada pela rede FDR InfiniBand.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


## <a name="supported-os-images-linux"></a>Imagens de SO suportadas (Linux)
 
O Azure Marketplace tem muitas distribuições Linux que suportam a conectividade RDMA:
  
* **HPC baseado em CentOS** - Para VMs não-SR-IOV habilitados, versão centos 6.5 HPC ou uma versão posterior, até 7.5 são adequados. Para vMs da série H, recomenda-se as versões 7.1 a 7.5. Os condutores de RDMA e Intel MPI 5.1 estão instalados no VM.
  Para os VMs SR-IOV, o CentOS-HPC 7.6 vem otimizado e pré-carregado com os controladores RDMA e vários pacotes MPI instalados.
  Para outras imagens RHEL/CentOS VM, adicione a extensão InfiniBandLinux para ativar a InfiniBand. Esta extensão Linux VM instala controladores Mellanox OFED (em VMs SR-IOV) para conectividade RDMA. O cmdlet PowerShell seguinte instala a versão mais recente (versão 1.0) da extensão InfiniBandDriverLinux num VM com capacidade RDMA existente. O VM com capacidade rdma é nomeado *myVM* e é implantado no grupo de recursos chamado *myResourceGroup* na região oeste *dos EUA* da seguinte forma:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  Alternativamente, as extensões VM podem ser incluídas nos modelos Azure Resource Manager para uma fácil implementação com o seguinte elemento JSON:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  O seguinte comando instala a mais recente extensão infiniBandDriverLinux da versão 1.0 em todos os VMs capazes de RDMA num conjunto de escala de máquina virtual existente chamado *myVMSS* implantado no grupo de recursos chamado *myResourceGroup*:
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > Nas imagens HPC baseadas no CentOS, as atualizações de kernel são desativadas no ficheiro de configuração **yum.** Isto porque os controladores RdMA Linux são distribuídos como um pacote RPM, e as atualizações do condutor podem não funcionar se o núcleo for atualizado.
  >
  

* **SUSE Linux Enterprise Server** - SLES 12 SP3 para HPC, SLES 12 SP3 para HPC (Premium), SLES 12 SP1 para HPC, SLES 12 SP1 para HPC (Premium), SLES 12 SP4 e SLES 15. Os controladores RDMA estão instalados e os pacotes DE MPI da Intel são distribuídos no VM. Instale o MPI executando o seguinte comando:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** - Ubuntu Server 16.04 LTS, 18.04 LTS. Configure os controladores RDMA no VM e registe-se com a Intel para descarregar o MPI da Intel:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Para obter mais detalhes sobre a ativação da InfiniBand, configuração de MPI, consulte [Enable InfiniBand](./workloads/hpc/enable-infiniband.md).

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a otimização das suas aplicações HPC para Azure e alguns exemplos na [HPC Workloads](./workloads/hpc/overview.md).
- Leia sobre os últimos anúncios e alguns exemplos e resultados do HPC no [Azure Compute Tech Community Blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para uma visão arquitetónica de nível mais elevado da execução das cargas de trabalho do HPC, consulte [a High Performance Computing (HPC) em Azure](/azure/architecture/topics/high-performance-computing/).
- Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
