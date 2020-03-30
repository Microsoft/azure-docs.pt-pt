---
title: Série H - Máquinas Virtuais Azure
description: Especificações para os VMs da série H.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 03/10/2020
ms.author: lahugh
ms.openlocfilehash: cb05a8c42c5710182f81d4bc1c76b78abe59c882
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79470508"
---
# <a name="h-series"></a>Série H

Os VMs da série H são otimizados para aplicações impulsionadas por altas frequências de CPU ou grandes memórias por requisitos fundamentais. Os VMs da série H apresentam 8 ou 16 núcleos de processador Intel Xeon E5 2667 v3, até 14 GB de RAM por núcleo CPU, e sem hiperthreading. A série H apresenta 56 Gb/seg Mellanox FDR InfiniBand numa configuração de árvore gorda não bloqueada para um desempenho RDMA consistente. As VMs da série H suportam Intel MPI 5.x e MS-MPI.

ACU: 290-300

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: não suportado

Migração Ao Vivo: Não Suportado

Atualizações de preservação da memória: não suportadas

| Tamanho | vCPU | Processador | Memória (GB) | Largura de banda de memória GB/s | Frequência de CPU base (GHz) | Frequência de todos os núcleos (GHz, pico) | Frequência de núcleo único (GHz, pico) | Desempenho RDMA (Gb/s) | Suporte do MPI | Armazenamento temporário (GB) | Discos de dados máximos | Max Ethernet NICs |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| <sup>Standard_H16r 1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3,2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3,2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> Para aplicações DEMPI, a rede de backend dedicada RDMA é ativada pela rede FDR InfiniBand.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


## <a name="supported-os-images-linux"></a>Imagens de Os Suportadas (Linux)
 
O Azure Marketplace tem muitas distribuições Linux que suportam a conectividade RDMA:
  
* **HPC baseado em CentOS** - Para VMs não-SR-IOV habilitados, versão baseada em CentOS 6.5 HPC ou uma versão posterior, até 7.5 são adequados. Para VMs da série H, as versões 7.1 a 7.5 são recomendadas. Os controladores RDMA e intel MPI 5.1 estão instalados no VM.
  Para vMs SR-IOV, o CentOS-HPC 7.6 vem otimizado e pré-carregado com os controladores RDMA e vários pacotes DEMPI instalados.
  Para outras imagens RHEL/CentOS VM, adicione a extensão InfiniBandLinux para ativar a InfiniBand. Esta extensão Linux VM instala os controladores Mellanox OFED (em VMs SR-IOV) para conectividade RDMA. O seguinte cmdlet PowerShell instala a versão mais recente (versão 1.0) da extensão InfiniBandDriverLinux num VM com capacidade para RDMA existente. O VM com capacidade rdma é nomeado *myVM* e é implantado no grupo de recursos chamado *myResourceGroup* na região *dos EUA Ocidentais* da seguinte forma:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  Alternativamente, as extensões VM podem ser incluídas nos modelos do Gestor de Recursos Azure para uma fácil implantação com o seguinte elemento JSON:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  O comando seguinte instala a mais recente extensão infiniBandDriverLinux em todos os VMs capazes de RDMA num conjunto de escala virtual existente chamado *myVMSS* implantado no grupo de recursos chamado *myResourceGroup*:
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > Nas imagens HPC baseadas no CentOS, as atualizações de kernel são desativadas no ficheiro de configuração **yum.** Isto porque os condutores de RDMA linux são distribuídos como um pacote RPM, e as atualizações do condutor podem não funcionar se o núcleo for atualizado.
  >
  

* **SUSE Linux Enterprise Server** - SLES 12 SP3 para HPC, SLES 12 SP3 para HPC (Premium), SLES 12 SP1 para HPC, SLES 12 SP1 para HPC (Premium), SLES 12 SP4 e SLES 15. Os controladores RDMA são instalados e os pacotes de MPI intel são distribuídos no VM. Instale MPI executando o seguinte comando:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** - Ubuntu Server 16.04 LTS, 18.04 LTS. Configure os condutores de RDMA no VM e registe-se com a Intel para descarregar mpi intel:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Para obter mais detalhes sobre a habilitação da InfiniBand, configuração de MPI, consulte [Enable InfiniBand](./workloads/hpc/enable-infiniband.md).

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como as unidades de [computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho da computação em Azure SKUs.
