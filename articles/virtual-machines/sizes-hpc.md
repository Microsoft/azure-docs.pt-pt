---
title: Tamanhos Azure VM - HPC | Microsoft Docs
description: Lista os diferentes tamanhos disponíveis para máquinas virtuais de computação de alto desempenho em Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como o armazenamento e largura de banda de rede para tamanhos nesta série.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/19/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: a41dce28427db40dfd19879e4ada95add64009c3
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772438"
---
# <a name="high-performance-computing-vm-sizes"></a>Tamanhos de VM de computação de alto desempenho

As máquinas virtuais da série Azure H (VMs) são projetadas para oferecer desempenho de classe de liderança, escalabilidade e eficiência de custos para várias cargas de trabalho de HPC no mundo real.

[Série HBv3](hbv3-series.md) Os VMs são otimizados para aplicações de HPC tais como dinâmica de fluidos, análise explícita e implícita de elementos finitos, modelação meteorológica, processamento sísmico, simulação de reservatório e simulação de RTL. Os HBv3 VMs apresentam até 120 AMD EPYC™ núcleos cpU de série 7003 (Milão), 448 GB de RAM e nenhuma hiperfinal. Os VMs da série HBv3 também fornecem 350 GB/seg de largura de banda de memória, até 32 MB de cache L3 por núcleo, até 7 GB/s de desempenho SSD do dispositivo de bloco, e frequências do relógio até 3,675 GHz. 

Todos os VMs da série HBv3 apresentam 200 Gb/sec HDR InfiniBand da NVIDIA Networking para permitir cargas de cargas de MPI em escala de supercomputadores. Estes VMs estão ligados numa árvore de gordura não bloqueada para um desempenho RDMA otimizado e consistente. O tecido HDR InfiniBand também suporta o Encaminhamento Adaptativo e o Transporte Conectado Dinâmico (DCT, além dos transportes padrão de RC e UD). Estas funcionalidades aumentam o desempenho da aplicação, escalabilidade e consistência, e a sua utilização é fortemente recomendada.

[Série HBv2](hbv2-series.md) Os VMs são otimizados para aplicações impulsionadas pela largura de banda da memória, tais como dinâmica de fluidos, análise de elementos finitos e simulação do reservatório. Os VMs HBv2 dispõem de 120 núcleos de processador AMD EPYC 7742, 4 GB de RAM por núcleo CPU e sem multi-leitura simultânea. Cada HBv2 VM fornece até 340 GB/seg de largura de banda de memória, e até 4 teraFLOPS de FP64 compute.

Os VMs HBv2 apresentam 200 Gb/sec Mellanox HDR InfiniBand, enquanto os VMs da série HB e HC apresentam 100 Gb/sec Mellanox EDR InfiniBand. Cada um destes tipos de VM está ligado numa árvore de gordura não bloqueada para um desempenho RDMA otimizado e consistente. Os VMS HBv2 suportam o Encaminhamento Adaptativo e o Transporte Conectado Dinâmico (DCT, além dos transportes RC e UD padrão). Estas funcionalidades aumentam o desempenho da aplicação, escalabilidade e consistência, e a sua utilização é fortemente recomendada.

[Série HB](hb-series.md) Os VMs são otimizados para aplicações impulsionadas pela largura de banda da memória, tais como dinâmica de fluidos, análise explícita de elementos finitos e modelação meteorológica. Os VMs HB dispõem de 60 núcleos de processador AMD EPYC 7551, 4 GB de RAM por núcleo CPU e sem hiperligação. A plataforma AMD EPYC fornece mais de 260 GB/seg de largura de banda de memória.

[Série HC](hc-series.md) Os VMs são otimizados para aplicações impulsionadas por computação densa, tais como análise implícita de elementos finitos, dinâmica molecular e química computacional. Os VMS HC dispõem de 44 núcleos de processador Intel Xeon Platinum 8168, 8 GB de RAM por núcleo CPU e sem hiperligação. A plataforma Intel Xeon Platinum suporta o rico ecossistema de ferramentas de software da Intel, como a Biblioteca Intel Math Kernel.

[Série H](h-series.md) Os VMs são otimizados para aplicações impulsionadas por altas frequências de CPU ou por grandes necessidades de memória por requisitos fundamentais. Os VMs da série H apresentam núcleos de processador Intel Xeon E5 2667 v3 da série H, 7 ou 14 GB de RAM por núcleo cpu e sem hiperfinaling. A série H apresenta 56 Gb/seg Mellanox FDR InfiniBand numa configuração de árvore de gordura não bloqueada para um desempenho RDMA consistente. Os VMs da série H suportam Intel MPI 5.x e MS-MPI.

> [!NOTE]
> Todos os VMs da série HBv3, HBv2, HB e HC têm acesso exclusivo aos servidores físicos. Existe apenas 1 VM por servidor físico e não existe multi-arrendamento partilhado com quaisquer outros VMs para estes tamanhos VM.

> [!NOTE]
> Os [A8 – A11 VMs](./sizes-previous-gen.md#a-series---compute-intensive-instances) estão reformados a partir de 3/2021. Não são possíveis novas implantações em VM destes tamanhos. Se tiver VMs existentes, consulte notificações enviadas por e-mail para os próximos passos, incluindo migração para outros tamanhos VM no [Guia de Migração do HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

## <a name="rdma-capable-instances"></a>Instâncias com capacidade de RDMA

A maioria dos tamanhos HPC VM possui uma interface de rede para acesso remoto de memória direta (RDMA) conectividade. Os tamanhos de [série N](./nc-series.md) selecionados designados com 'r' também são capazes de RDMA. Esta interface é além da interface de rede padrão Azure Ethernet disponível nos outros tamanhos VM.

Esta interface secundária permite que as instâncias com capacidade RDMA se comuniquem através de uma rede InfiniBand (IB), operando a taxas HDR para HBv3, HBv2, taxas EDR para hb, HC, NDv2 e FDR para h16r, H16mr e outras máquinas virtuais da série N capazes de RDMA. Estas capacidades de RDMA podem aumentar a escalabilidade e desempenho das aplicações baseadas em Interface de Passagem de Mensagens (MPI).

> [!NOTE]
> **Suporte SR-IOV**: Em Azure HPC, existem atualmente duas classes de VMs dependendo se são SR-IOV habilitados para InfiniBand. Atualmente, quase toda a nova geração, capaz de RDMA ou InfiniBand habilitados VMs em Azure são SR-IOV habilitados com exceção de H16r, H16mr e NC24r.
> O RDMA só está ativado sobre a rede InfiniBand (IB) e é suportado para todos os VMs capazes de RDMA.
> O IP sobre o IB só é suportado nos VMs ativados pelo SR-IOV.
> O RDMA não está ativado na rede Ethernet.

- **Sistema Operativo** - As distribuições de Linux tais como CentOS, RHEL, Ubuntu, SUSE são comumente usadas. O Windows Server 2016 e as versões mais recentes são suportadas em todos os VMs da série HPC. O Windows Server 2012 R2 e o Windows Server 2012 também são suportados nos VMs não ativados por SR-IOV. Note que [o Windows Server 2012 R2 não é suportado em HBv2 em diante como tamanhos VM com mais de 64 núcleos (virtuais ou físicos).](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) Consulte [as Imagens VM](./workloads/hpc/configure.md) para obter uma lista de Imagens VM suportadas no Mercado e como podem ser configuradas adequadamente. As respetivas páginas de tamanho VM também listam o suporte da pilha de software.

- **InfiniBand e Drivers** - Em VMs ativados pela InfiniBand, os controladores adequados são obrigados a ativar o RDMA. Consulte [as Imagens VM](./workloads/hpc/configure.md) para obter uma lista de Imagens VM suportadas no Mercado e como podem ser configuradas adequadamente. Consulte também [a InfiniBand](./workloads/hpc/enable-infiniband.md) para saber sobre extensões VM ou instalação manual de controladores InfiniBand.

- **MPI** - O SR-IOV permitiu que os tamanhos de VM no Azure permitissem que quase qualquer sabor de MPI fosse utilizado com Mellanox OFED. Em VMs não-SR-IOV habilitados, implementações de MPI suportadas utilizam a interface Microsoft Network Direct (ND) para comunicar entre VMs. Assim, apenas intel MPI 5.x e Microsoft MPI (MS-MPI) 2012 R2 ou versões posteriores são suportadas. Versões posteriores da biblioteca de tempo de execução Intel MPI podem ou não ser compatíveis com os controladores Azure RDMA. Consulte [o MPI de configuração para hpc](./workloads/hpc/setup-mpi.md) para obter mais detalhes sobre a configuração de MPI em VMs HPC em Azure.

  > [!NOTE]
  > **Espaço de endereço de rede RDMA**: A rede RDMA em Azure reserva o espaço de endereço 172.16.0.0/16. Para executar aplicações MPI em casos implantados numa rede virtual Azure, certifique-se de que o espaço de endereço de rede virtual não se sobrepõe à rede RDMA.

## <a name="cluster-configuration-options"></a>Opções de configuração do cluster

O Azure oferece várias opções para criar clusters de VMs HPC que podem comunicar através da rede RDMA, incluindo: 

- **Máquinas virtuais**  - Implementar os VMS HPC com capacidade RDMA no mesmo conjunto de escala ou disponibilidade (quando utilizar o modelo de implementação do Gestor de Recursos Azure). Se utilizar o modelo de implementação clássico, inserte os VMs no mesmo serviço de nuvem.

- **Conjuntos de escala de máquina virtual** - Num conjunto de escala de máquina virtual, certifique-se de que limita a colocação a um único grupo de colocação para a comunicação InfiniBand dentro do conjunto de escala. Por exemplo, num modelo de Gestor de Recursos, desaprote a `singlePlacementGroup` propriedade para `true` . Note que o tamanho máximo de escala que pode ser girado é `singlePlacementGroup=true` limitado a 100 VMs por padrão. Se as suas necessidades de escala de emprego HPC forem superiores a 100 VMs num único inquilino, poderá solicitar um aumento, [abrir um pedido de apoio](../azure-portal/supportability/how-to-create-azure-support-request.md) ao cliente online gratuitamente. O limite do número de VMs num conjunto de escala única pode ser aumentado para 300. Note que ao implementar VMs usando Conjuntos de Disponibilidade, o limite máximo é de 200 VMs por Conjunto de Disponibilidade.

  > [!NOTE]
  > **MPI entre máquinas virtuais**: Se rdma (por exemplo, utilização de comunicação MPI) for necessário entre máquinas virtuais (VMs), certifique-se de que os VMs estão no mesmo conjunto de escala de máquina virtual ou conjunto de disponibilidade.

- **Azure CycleCloud** - Criar um cluster HPC usando [Azure CycleCloud](/azure/cyclecloud/) para executar trabalhos de MPI.

- **Azure Batch** - Crie uma piscina [Azure Batch](../batch/index.yml) para executar cargas de carga de MPI. Para utilizar casos intensivos de cálculo ao executar aplicações MPI com Azure Batch, consulte [utilizar tarefas multi-instâncias para executar aplicações de Interface de Passagem de Mensagens (MPI) em Azure Batch](../batch/batch-mpi.md).

- **Microsoft HPC Pack**  -  [O HPC Pack](/powershell/high-performance-computing/overview) inclui um ambiente de tempo de funcionamento para o MS-MPI que utiliza a rede Azure RDMA quando implantado em VMs Linux com capacidade RDMA. Por exemplo, consulte [Configurar um cluster Linux RDMA com o HPC Pack para executar aplicações MPI](/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="deployment-considerations"></a>Considerações sobre implementação

- **Subscrição do Azure** – Para implementar mais do que alguns casos intensivos de computação, considere uma subscrição pay-as-you-go ou outras opções de compra. Se estiver a utilizar uma [conta gratuita do Azure](https://azure.microsoft.com/free/), pode utilizar apenas um número limitado de núcleos de computação do Azure.

- **Preços e disponibilidade** - Verifique os preços e [disponibilidade](https://azure.microsoft.com/global-infrastructure/services/) [de VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) pelas regiões de Azure.

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
- [Otimizada para GPU](sizes-gpu.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a configuração dos seus VMs,](./workloads/hpc/configure.md) [permitindo a InfiniBand,](./workloads/hpc/enable-infiniband.md) [configurando MPI](./workloads/hpc/setup-mpi.md) e otimizando as aplicações HPC para Azure na [HPC Workloads](./workloads/hpc/overview.md).
- Reveja a visão geral da [série HBv3](./workloads/hpc/hbv3-series-overview.md) e [a visão geral da série HC](./workloads/hpc/hc-series-overview.md).
- Leia sobre os últimos anúncios, exemplos de carga de trabalho do HPC e resultados de desempenho nos [Blogs comunitários Azure Compute Tech.](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)
- Para uma visão arquitetónica de nível mais elevado da execução das cargas de trabalho do HPC, consulte [a High Performance Computing (HPC) em Azure](/azure/architecture/topics/high-performance-computing/).
