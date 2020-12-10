---
title: Tamanhos Azure VM - HPC / Microsoft Docs
description: Lista os diferentes tamanhos disponíveis para máquinas virtuais de computação de alto desempenho em Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como o armazenamento e largura de banda de rede para tamanhos nesta série.
author: vermagit
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/09/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 5559e491be15f8f848329405736fc977267c70cc
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938601"
---
# <a name="high-performance-computing-vm-sizes"></a>Tamanhos de VM de computação de alto desempenho

As máquinas virtuais da série Azure H (VMs) são projetadas para oferecer desempenho de classe de liderança, escalabilidade e eficiência de custos para uma variedade de cargas de trabalho de HPC no mundo real.

[Série HBv2](hbv2-series.md) Os VMs são otimizados para aplicações impulsionadas pela largura de banda da memória, tais como dinâmica de fluidos, análise de elementos finitos e simulação do reservatório. Os VMs HBv2 dispõem de 120 núcleos de processador AMD EPYC 7742, 4 GB de RAM por núcleo CPU e sem multi-leitura simultânea. Cada HBv2 VM fornece até 340 GB/seg de largura de banda de memória, e até 4 teraFLOPS de FP64 compute.

Os VMs HBv2 apresentam 200 Gb/sec Mellanox HDR InfiniBand, enquanto os VMs da série HB e HC apresentam 100 Gb/sec Mellanox EDR InfiniBand. Cada um destes tipos de VM está ligado numa árvore de gordura não bloqueada para um desempenho RDMA otimizado e consistente. Os HBv2 VMs suportam o Encaminhamento Adaptativo e o Transporte Conectado Dinâmico (DCT, em adicional aos transportes RC e UD padrão). Estas funcionalidades aumentam o desempenho da aplicação, escalabilidade e consistência, e a sua utilização é fortemente recomendada.

[Série HB](hb-series.md) Os VMs são otimizados para aplicações impulsionadas pela largura de banda da memória, tais como dinâmica de fluidos, análise explícita de elementos finitos e modelação meteorológica. Os VMs HB dispõem de 60 núcleos de processador AMD EPYC 7551, 4 GB de RAM por núcleo CPU e sem hiperligação. A plataforma AMD EPYC fornece mais de 260 GB/seg de largura de banda de memória.

[Série HC](hc-series.md) Os VMs são otimizados para aplicações impulsionadas por computação densa, tais como análise implícita de elementos finitos, dinâmica molecular e química computacional. Os VMS HC dispõem de 44 núcleos de processador Intel Xeon Platinum 8168, 8 GB de RAM por núcleo CPU e sem hiperligação. A plataforma Intel Xeon Platinum suporta o rico ecossistema de ferramentas de software da Intel, como a Biblioteca Intel Math Kernel.

[Série H](h-series.md) Os VMs são otimizados para aplicações impulsionadas por altas frequências de CPU ou por grandes necessidades de memória por requisitos fundamentais. Os VMs da série H apresentam núcleos de processador Intel Xeon E5 2667 v3 da série H, 7 ou 14 GB de RAM por núcleo cpu e sem hiperfinaling. A série H apresenta 56 Gb/seg Mellanox FDR InfiniBand numa configuração de árvore de gordura não bloqueada para um desempenho RDMA consistente. Os VMs da série H suportam Intel MPI 5.x e MS-MPI.

> [!NOTE]
> Todos os VMs da série HBv2, HB e HC têm acesso exclusivo aos servidores físicos. Existe apenas 1 VM por servidor físico e não existe multi-arrendamento partilhado com quaisquer outros VMs para estes tamanhos VM.

> [!NOTE]
> A [A8 – A11 VMs](./sizes-previous-gen.md#a-series---compute-intensive-instances) está prevista para a reforma em 3/2021. Para mais informações, consulte [o Guia de Migração do HPC.](https://azure.microsoft.com/resources/hpc-migration-guide/)

## <a name="rdma-capable-instances"></a>Instâncias com capacidade de RDMA

A maioria dos tamanhos HPC VM (HBv2, HB, HC, H16r, H16mr, A8 e A9) apresentam uma interface de rede para acesso remoto à memória direta (RDMA). Os tamanhos de [série N](./nc-series.md) selecionados designados com 'r' (ND40rs_v2, ND24rs, NC24rs_v3, NC24rs_v2 e NC24r) também são capazes de RDMA. Esta interface é além da interface de rede padrão Azure Ethernet disponível nos outros tamanhos VM.

Esta interface permite que as instâncias com capacidade RDMA se comuniquem através de uma rede InfiniBand (IB), operando a taxas HDR para HBv2, taxas EDR para hb, HC, NDv2, FDR para H16r, H16mr e outras máquinas virtuais de série N capazes de RDMA, e taxas QDR para A8 e A9 VMs. Estas capacidades de RDMA podem aumentar a escalabilidade e desempenho de certas aplicações de Interface de Passagem de Mensagens (MPI).

> [!NOTE]
> Em Azure HPC, existem duas classes de VMs dependendo se são SR-IOV habilitados para InfiniBand. Atualmente, quase toda a nova geração, capaz de RDMA ou InfiniBand habilitados VMs em Azure são SR-IOV habilitados com exceção de H16r, H16mr, NC24r, A8, A9.
> O RDMA só está ativado sobre a rede InfiniBand (IB) e é suportado para todos os VMs capazes de RDMA.
> O IP sobre o IB só é suportado nos VMs ativados pelo SR-IOV.
> O RDMA não está ativado na rede Ethernet.

- **Sistema Operativo** - Linux é muito bem suportado para VMs HPC; distros como CentOS, RHEL, Ubuntu, SUSE são comumente usados. No que diz respeito ao suporte ao Windows, o Windows Server 2016 e as versões mais recentes são suportadas em todos os VMs da série HPC. Windows Server 2012 R2, Windows Server 2012 também são suportados nos VMs não-SR-IOV ativados (H16r, H16mr, A8 e A9). Note que [o Windows Server 2012 R2 não é suportado em HBv2 e outros VMs com mais de 64 núcleos (virtuais ou físicos).](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) Consulte [as Imagens VM](./workloads/hpc/configure.md) para obter uma lista de Imagens VM suportadas no Mercado e como podem ser configuradas adequadamente.

- **InfiniBand e Drivers** - Em VMs ativados pela InfiniBand, os controladores adequados são obrigados a ativar o RDMA. No Linux, tanto para os VM sr-IOV como não-SR-IOV habilitados, as imagens CentOS-HPC VM no Mercado vêm pré-configuradas com os controladores apropriados. As imagens Ubuntu VM podem ser configuradas com os controladores certos usando as [instruções aqui](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351). Consulte [Configure e Otimize VMs para Linux OS](./workloads/hpc/configure.md) para obter mais detalhes sobre imagens VM Linux OS prontas a usar.

   No Linux, a [extensão VM InfiniBandDriverLinux](./extensions/hpc-compute-infiniband-linux.md) pode ser utilizada para instalar os controladores Mellanox OFED e ativar a InfiniBand nos VMs ativados pela SÉRIE H e N ativadas pela SR-IOV. Saiba mais sobre a ativação da InfiniBand em VMs com capacidade rdma na [HPC Workloads](./workloads/hpc/enable-infiniband.md).

   No Windows, a [extensão VM InfiniBandDriverWindows](./extensions/hpc-compute-infiniband-windows.md) instala controladores Windows Network Direct (em VMs não-SR-IOV) ou condutores Mellanox OFED (em VMs SR-IOV) para conectividade RDMA. Em certas implementações de instâncias A8 e A9, a extensão HpcVmDrivers é adicionada automaticamente. Note que a extensão VM HpcVmDrivers está a ser depreciada; não será atualizado.

   Para adicionar a extensão VM a um VM, pode utilizar cmdlets [Azure PowerShell.](/powershell/azure/) Para obter mais informações, consulte [extensões e funcionalidades da máquina virtual.](./extensions/overview.md) Também pode trabalhar com extensões para VMs implantados no [modelo clássico de implantação.](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic)

- **MPI** - O SR-IOV permitiu que os tamanhos de VM no Azure permitissem que quase qualquer sabor de MPI fosse utilizado com Mellanox OFED. Em VMs não-SR-IOV habilitados, implementações de MPI suportadas utilizam a interface Microsoft Network Direct (ND) para comunicar entre VMs. Assim, apenas as versões Microsoft MPI (MS-MPI) 2012 R2 ou posterior e Intel MPI 5.x são suportadas. Versões posteriores (2017, 2018) da biblioteca de tempo de execução do INTEL MPI podem ou não ser compatíveis com os controladores Azure RDMA. Consulte [o MPI de configuração para hpc](./workloads/hpc/setup-mpi.md) para obter mais detalhes sobre a configuração de MPI em VMs HPC em Azure.

- **Espaço de endereço de rede RDMA** - A rede RDMA em Azure reserva o espaço de endereço 172.16.0.0/16. Para executar aplicações MPI em casos implantados numa rede virtual Azure, certifique-se de que o espaço de endereço de rede virtual não se sobrepõe à rede RDMA.

## <a name="cluster-configuration-options"></a>Opções de configuração do cluster

O Azure oferece várias opções para criar clusters de VMs Windows HPC que podem comunicar através da rede RDMA, incluindo: 

- **Máquinas virtuais**  - Implementar os VMS HPC com capacidade RDMA no mesmo conjunto de escala ou disponibilidade (quando utilizar o modelo de implementação do Gestor de Recursos Azure). Se utilizar o modelo de implementação clássico, inserte os VMs no mesmo serviço de nuvem.

- **Conjuntos de escala de máquina virtual** - Num conjunto de escala de máquina virtual, certifique-se de que limita a colocação a um único grupo de colocação para a comunicação InfiniBand dentro do conjunto de escala. Por exemplo, num modelo de Gestor de Recursos, desaprote a `singlePlacementGroup` propriedade para `true` . Note que o tamanho máximo definido que pode ser fiado com `singlePlacementGroup` propriedade é limitado a `true` 100 VMs por padrão. Se as suas necessidades de escala de emprego HPC forem superiores a 100 VMs num único inquilino, poderá solicitar um aumento, [abrir um pedido de apoio](../azure-portal/supportability/how-to-create-azure-support-request.md) ao cliente online gratuitamente. O limite do número de VMs num conjunto de escala única pode ser aumentado para 300. Note que ao implementar VMs usando Conjuntos de Disponibilidade, o limite máximo é de 200 VMs por Conjunto de Disponibilidade.

- **MPI entre máquinas virtuais** - Se o RDMA (por exemplo, utilizando a comunicação MPI) for necessário entre máquinas virtuais (VMs), certifique-se de que os VMs estão no mesmo conjunto de escala de máquina virtual ou conjunto de disponibilidade.

- **Azure CycleCloud** - Crie um cluster HPC em [Azure CycleCloud](/azure/cyclecloud/) para executar trabalhos de MPI.

- **Azure Batch** - Crie uma piscina [Azure Batch](../batch/index.yml) para executar cargas de carga de MPI. Para utilizar casos intensivos de cálculo ao executar aplicações MPI com Azure Batch, consulte [utilizar tarefas multi-instâncias para executar aplicações de Interface de Passagem de Mensagens (MPI) em Azure Batch](../batch/batch-mpi.md).

- **Microsoft HPC Pack**  -  [O HPC Pack](/powershell/high-performance-computing/overview) inclui um ambiente de tempo de funcionamento para o MS-MPI que utiliza a rede Azure RDMA quando implantado em VMs Linux com capacidade RDMA. Por exemplo, consulte [Configurar um cluster Linux RDMA com o HPC Pack para executar aplicações MPI](/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="deployment-considerations"></a>Considerações sobre implementação

- **Subscrição do Azure** – Para implementar mais do que alguns casos intensivos de computação, considere uma subscrição pay-as-you-go ou outras opções de compra. Se estiver a utilizar uma [conta gratuita do Azure](https://azure.microsoft.com/free/), pode utilizar apenas um número limitado de núcleos de computação do Azure.

- **Preços e disponibilidade** - Estes tamanhos VM são oferecidos apenas no nível de preços Standard. Consulte [os produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para obter disponibilidade nas regiões de Azure.

- **Cota de núcleos** – Poderá ter de aumentar a quota de núcleos na sua subscrição Azure a partir do valor predefinido. A sua subscrição também pode limitar o número de núcleos que pode implementar em certas famílias de tamanho VM, incluindo a série H. Para solicitar um aumento de quota, [abra gratuitamente um pedido de apoio](../azure-portal/supportability/how-to-create-azure-support-request.md) ao cliente online. (Os limites predefinidos podem variar dependendo da sua categoria de subscrição.)

  > [!NOTE]
  > Contacte o Suporte Azure se tiver necessidades de capacidade em larga escala. As quotas azure são limites de crédito, não garantias de capacidade. Independentemente da sua quota, só é cobrado pelos núcleos que usa.
  
- **Rede virtual** – Uma [rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) Azure não é necessária para utilizar os casos intensivos de computação. No entanto, para muitas implementações, você precisa de pelo menos uma rede virtual Azure baseada na nuvem, ou uma ligação site-to-site se você precisar de aceder a recursos no local. Quando necessário, crie uma nova rede virtual para implementar os casos. A adição de VMs intensivos a uma rede virtual num grupo de afinidade não é suportada.

- **Redimensionamento** – Devido ao seu hardware especializado, só é possível redimensionar casos intensivos de computação dentro da mesma família (série H ou série N). Por exemplo, só é possível redimensionar um VM da série H de um tamanho da série H para outro. Considerações adicionais em torno do suporte do condutor InfiniBand e dos discos NVMe podem ter de ser consideradas para certos VMs.


## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de computação](sizes-compute.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a configuração dos seus VMs,](./workloads/hpc/configure.md) [permitindo a InfiniBand,](./workloads/hpc/enable-infiniband.md) [configurando MPI](./workloads/hpc/setup-mpi.md) e otimizando as aplicações HPC para Azure na [HPC Workloads](./workloads/hpc/overview.md).
- Leia sobre os últimos anúncios e alguns exemplos e resultados do HPC no [Azure Compute Tech Community Blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para uma visão arquitetónica de nível mais elevado da execução das cargas de trabalho do HPC, consulte [a High Performance Computing (HPC) em Azure](/azure/architecture/topics/high-performance-computing/).
