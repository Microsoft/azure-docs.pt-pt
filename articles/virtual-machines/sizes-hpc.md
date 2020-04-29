---
title: Tamanhos Azure VM - HPC / Microsoft Docs
description: Lista os diferentes tamanhos disponíveis para máquinas virtuais de computação de alto desempenho em Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a entrada de armazenamento e largura de banda da rede para tamanhos nesta série.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: df22c857571e51bb886ff1d25db185a306999540
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80420860"
---
# <a name="high-performance-computing-vm-sizes"></a>Tamanhos vM de computação de alto desempenho

As máquinas virtuais da série Azure H (VMs) são projetadas para oferecer desempenho de classe de liderança, escalabilidade de MPI e eficiência de custos para uma variedade de cargas de trabalho hpc do mundo real.

[Série HBv2](hbv2-series.md) Os VMs apresentam 200 Gb/seg Mellanox HDR InfiniBand, enquanto os VMs hb e HC-series apresentam 100 Gb/seg Mellanox EDR InfiniBand. Cada um destes tipos de VM está ligado numa árvore de gordura não bloqueada para um desempenho DERDMA otimizado e consistente. Os VMs HBv2 suportam o encaminhamento adaptativo e o transporte dinâmico conectado (DCT, em adicional aos transportes RC e UD padrão). Estas funcionalidades aumentam o desempenho da aplicação, escalabilidade e consistência, e o seu uso é fortemente recomendado.

[HB-series](hb-series.md) Os VMs são otimizados para aplicações impulsionadas pela largura de banda da memória, tais como dinâmica de fluidos, análise explícita de elementos finitos e modelação meteorológica. Os VMs HB apresentam 60 núcleos de processador AMD EPYC 7551, 4 GB de RAM por núcleo CPU e sem hiperthreading. A plataforma AMD EPYC fornece mais de 260 GB/seg de largura de banda de memória.

[Série HC](hc-series.md) Os VMs são otimizados para aplicações impulsionadas por uma computação densa, tais como análise implícita de elementos finitos, dinâmica molecular e química computacional. Os HC VMs apresentam 44 núcleos de processador Intel Xeon Platinum 8168, 8 GB de RAM por núcleo CPU, e sem hiperthreading. A plataforma Intel Xeon Platinum suporta o rico ecossistema de ferramentas de software da Intel, como a Intel Math Kernel Library.

[Série H](h-series.md) Os VMs são otimizados para aplicações impulsionadas por altas frequências de CPU ou grandes memórias por requisitos fundamentais. Os VMs da série H apresentam 8 ou 16 núcleos de processador Intel Xeon E5 2667 v3, 7 ou 14 GB de RAM por núcleo CPU, e sem hiperthreading. A série H apresenta 56 Gb/seg Mellanox FDR InfiniBand numa configuração de árvore gorda não bloqueada para um desempenho RDMA consistente. As VMs da série H suportam Intel MPI 5.x e MS-MPI.

> [!NOTE]
> Os A8 – A11 VMs estão previstos para a reforma em 3/2021. Para mais informações, consulte o [Guia de Migração do HPC.](https://azure.microsoft.com/resources/hpc-migration-guide/)

## <a name="rdma-capable-instances"></a>Instâncias com capacidade de RDMA

A maioria dos tamanhos de VM HPC (HBv2, HB, HC, H16r, H16mr, A8 e A9) dispõem de uma interface de rede para acesso remoto à memória direta (RDMA). Selecionados [N-series] (https://docs.microsoft.com/azure/virtual-machines/nc-series) tamanhos designados com 'r' tais como as configurações NC24rs (NC24rs_v3, NC24rs_v2 e NC24r) também são capazes de RDMA. Esta interface é além da interface de rede Padrão Azure disponível nos outros tamanhos VM.

Esta interface permite que as instâncias capazes de RDMA se comuniquem através de uma rede InfiniBand (IB), operando a taxas HDR para HBv2, taxas EDR para HB, HC, taxas DeDr para H16r, H16mr e máquinas virtuais de série N capazes de RDMA, e taxas qDR para VMs A8 e A9. Estas capacidades de RDMA podem aumentar a escalabilidade e desempenho de determinadas aplicações de Interface de Passagem de Mensagem (MPI). Para mais informações sobre a velocidade, consulte os detalhes nas tabelas desta página.

> [!NOTE]
> No Azure HPC, existem duas classes de VMs dependendo se são SR-IOV habilitadas para a InfiniBand. Atualmente, os VMs ativados pela SR-IOV para InfiniBand são: HBv2, HB, HC e NCv3. Os restantes VMs ativados pela InfiniBand não estão ativados pelo SR-IOV.
> RDMA over IB é suportado para todos os VMs capazes de RDMA.
> O IP sobre o IB só é suportado nos VMs ativados pela SR-IOV.

- **Sistema operativo** - O Linux é muito bem suportado para VMs HPC, distros como CentOS, RHEL, Ubuntu, SUSE são comuns. No que diz respeito ao suporte do Windows, o Windows Server 2016 é suportado em todos os VMs da série HPC. Windows Server 2012 R2, Windows Server 2012 também são suportados nos VMs não-SR-IOV habilitados.

- **MPI** - O SR-IOV habilitado tamanhos vm em Azure (HBv2, HB, HC, NCv3) permitem que quase qualquer sabor de MPI seja usado com Mellanox OFED.
Em VMs ativados não-SR-IOV, implementações de MPI suportadas utilizam a interface Microsoft Network Direct (ND) para comunicar entre VMs. Assim, apenas são suportadas as versões MPI (MS-MPI) 2012 R2 ou posteriormente e as versões Intel MPI 5.x. Versões posteriores (2017, 2018) da biblioteca de tempo de execução intel MPI podem ou não ser compatíveis com os condutores do Azure RDMA.

- **InfiniBandDriver<Linux. Windows> extensão VM** - Em VMs com capacidade RDMA, adicione o InfiniBandDriver<Linux. O Windows> extensão para ativar a InfiniBand. No Linux, a extensão VM InfiniBandDriverLinux instala os controladores Mellanox OFED (em VMs SR-IOV) para conectividade RDMA. No Windows, a extensão InfiniBandDriverWindows VM instala controladores Windows Network Direct (em VMs não SR-IOV) ou mellanox DEED (em VMs SR-IOV) para conectividade RDMA.
Em determinadas implementações de instâncias A8 e A9, a extensão HpcVmDrivers é adicionada automaticamente. Note que a extensão VM HpcVmDrivers está a ser depreciada; não será atualizado.
Para adicionar a extensão VM a um VM, pode utilizar cmdlets [Azure PowerShell.](/powershell/azure/overview) 

  O seguinte comando instala a mais recente extensão do InfiniBandDriverWindows da versão 1.0 num VM com capacidade rdma existente, denominado *myVM* implantado no grupo de recursos chamado *myResourceGroup* na região *dos EUA Ocidentais:*

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```

  Alternativamente, as extensões VM podem ser incluídas nos modelos do Gestor de Recursos Azure para uma fácil implantação, com o seguinte elemento JSON:

  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  O comando seguinte instala a mais recente extensão do InfiniBandDriverWindows em todos os VMs capazes de RDMA num conjunto de escala virtual existente chamado *myVMSS* implantado no grupo de recursos chamado *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  Para mais informações, consulte [extensões e funcionalidades da máquina virtual.](./extensions/overview.md) Também pode trabalhar com extensões para VMs implantadas no [modelo de implementação clássico](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

- Espaço de endereço de **rede RDMA** - A rede RDMA em Azure reserva o espaço de endereço172.16.0.0.0.0/16. Para executar aplicações de MPI em casos implantados numa rede virtual Azure, certifique-se de que o espaço de endereço seletiva da rede virtual não se sobrepõe à rede RDMA.

## <a name="cluster-configuration-options"></a>Opções de configuração de cluster

O Azure oferece várias opções para criar clusters de VMs Windows HPC que podem comunicar através da rede RDMA, incluindo: 

- **Máquinas virtuais** - Implemente os VMs HPC capazes de RDMA no mesmo conjunto de escala ou conjunto de disponibilidade (quando utilizar o modelo de implementação do Gestor de Recursos Azure). Se utilizar o modelo de implantação clássico, desloque os VMs no mesmo serviço de nuvem.

- **Conjuntos** de escala de máquina virtual - Num conjunto de escala de máquina virtual (VMSS), certifique-se de que limita a implementação a um único grupo de colocação. Por exemplo, num modelo de `singlePlacementGroup` Gestor `true`de Recursos, defino a propriedade para . Note que o tamanho máximo de VMSS `singlePlacementGroup` que `true` pode ser fiado com propriedade para ser limitado a 100 VMs por padrão. Se as suas necessidades de escala de trabalho HPC forem superiores a 100 VMs num único inquilino VMSS, poderá solicitar um aumento, [abrindo um pedido](../azure-supportability/how-to-create-azure-support-request.md) de apoio ao cliente on-line gratuitamente.

- **MPI entre máquinas virtuais** - Se o RDMA (por exemplo, utilizando a comunicação MPI) for necessário entre máquinas virtuais (VMs), certifique-se de que os VMs estão no mesmo conjunto de escala de máquina virtual ou conjunto de disponibilidade.

- **Azure CycleCloud** - Crie um cluster HPC no [Azure CycleCloud](/azure/cyclecloud/) para executar trabalhos de MPI.

- **Azure Batch** - Crie uma piscina [de lote azure](/azure/batch/) para executar cargas de trabalho DEMPI. Para utilizar instâncias intensivas em cálculo ao executar aplicações de MPI com o Lote Azure, consulte Utilize tarefas de várias instâncias para executar aplicações de interface de passagem de [mensagens (MPI) no Lote Azure](../batch/batch-mpi.md).

- **O Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) inclui um ambiente de tempo de execução para MS-MPI que utiliza a rede Azure RDMA quando implantado em VMs Linux capazes de RDMA. Por exemplo, implementações, consulte [Configurar um cluster Linux RDMA com HPC Pack para executar aplicações MPI](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="deployment-considerations"></a>Considerações sobre implementação

- **Subscrição Azure** – Para implementar mais do que algumas instâncias intensivas em cálculos, considere uma subscrição pay-as-you-go ou outras opções de compra. Se estiver a utilizar uma [conta gratuita do Azure](https://azure.microsoft.com/free/), pode utilizar apenas um número limitado de núcleos de computação do Azure.

- **Preços e disponibilidade** - Estes tamanhos vm são oferecidos apenas no nível de preços Standard. Consulte [os produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para obter disponibilidade nas regiões de Azure.

- **Quota de cores** – Poderá ser necessário aumentar a quota de núcleos na sua subscrição Azure a partir do valor predefinido. A sua subscrição também pode limitar o número de núcleos que pode implantar em certas famílias de tamanho VM, incluindo a série H. Para solicitar um aumento de quota, [abra um pedido de apoio](../azure-supportability/how-to-create-azure-support-request.md) ao cliente online gratuitamente. (Os limites predefinidos podem variar dependendo da sua categoria de subscrição.)

  > [!NOTE]
  > Contacte o Suporte Azure se tiver necessidades de capacidade em larga escala. As quotas azure são limites de crédito, não garantias de capacidade. Independentemente da sua quota, só é cobrado por núcleos que usa.
  
- **Rede virtual** – Uma [rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) Azure não é necessária para utilizar as instâncias intensivas em computação. No entanto, para muitas implementações, você precisa de pelo menos uma rede virtual Azure baseada em nuvem, ou uma ligação site-to-site se você precisar de aceder aos recursos no local. Quando necessário, crie uma nova rede virtual para implementar as ocorrências. A adição de VMs intensivos de computação a uma rede virtual num grupo de afinidade não é suportada.

- **Redimensionamento** – Devido ao hardware especializado, só é possível redimensionar instâncias intensivas em computação dentro da mesma família de tamanhos (série H ou série A intensiva de cálculo). Por exemplo, só é possível redimensionar um VM da série H de um tamanho da série H para outro. Além disso, a redimensionamento de um tamanho não-intensivo para um tamanho intensivo de computação não é suportada.  


## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de computação](sizes-compute.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre otimizar a sua aplicação HPC para o Azure e alguns exemplos na [HPC Workloads] ( Trabalhos HPC] ( Trabalhos HPC] (https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/overview) 

- Saiba mais sobre como as unidades de [computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho da computação em Azure SKUs.
