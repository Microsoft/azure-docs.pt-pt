---
title: Visão geral da série VM da série HBv3, arquitetura, topologia - Azure Virtual Machines | Microsoft Docs
description: Saiba mais sobre o tamanho VM da série HBv3 em Azure.
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f78420a65cd9c2402266eb9ba973eabe758d7ee5
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608289"
---
# <a name="hbv3-series-virtual-machine-overview"></a>Visão geral da máquina virtual da série HBv3 

Um servidor [da série HBv3](../../hbv3-series.md) apresenta CPUs EPYC 7V13 de 64 núcleos de 64 núcleos para um total de 128 núcleos "Zen3" físicos. A multi-leitura simultânea (SMT) é desativada em HBv3. Estes 128 núcleos são divididos em 16 secções (8 por tomada), cada secção contendo 8 núcleos de processador com acesso uniforme a uma cache L3 de 32 MB. Os servidores Azure HBv3 também executam as seguintes definições DE BIOS AMD:

```bash
Nodes per Socket (NPS) = 2
L3 as NUMA = Disabled
NUMA domains within VM OS = 4
C-states = Enabled
```

Como resultado, as botas do servidor com 4 domínios NUMA (2 por tomada) cada 32 núcleos de tamanho. Cada NUMA tem acesso direto a 4 canais de DRAM físico operando a 3200 MT/s.

Para dar espaço ao hipervisor Azure para operar sem interferir com o VM, reservamos 8 núcleos físicos por servidor.

## <a name="vm-topology"></a>Topologia VM

O diagrama seguinte mostra a topologia do servidor. Reservamos estes 8 núcleos hospedeiros hipervisores (amarelos) simetricamente em ambas as tomadas do CPU, tomando os primeiros 2 núcleos de Núcleo Complexo Específico Dies (CCDs) em cada domínio NUMA, com os restantes núcleos para o VM da série HBv3 (verde).

![Topologia do servidor da série HBv3](./media/architecture/hbv3/hbv3-topology-server.png)

Note que o limite CCD não é equivalente a um limite NUMA. Em HBv3, um grupo de quatro CCDs consecutivos (4) é configurado como um domínio NUMA, tanto no nível de sever do anfitrião como dentro de um VM convidado. Assim, todos os tamanhos HBv3 VM expõem 4 domínios NUMA que aparecerão a um SISTEMA e aplicação como mostrado abaixo, 4 domínios NUMA uniformes, cada um com um número diferente de núcleos dependendo do tamanho específico [de HBv3 VM](../../hbv3-series.md).

![Topologia da série HBv3 VM](./media/architecture/hbv3/hbv3-topology-vm.png)

Cada tamanho HBv3 VM é semelhante em layout físico, características e desempenho de um CPU diferente da série AMD EPYC 7003, da seguinte forma:

| Tamanho VM da série HBv3             | Domínios NUMA | Núcleos por domínio NUMA  | Semelhança com AMD EPYC         |
|---------------------------------|--------------|------------------------|----------------------------------|
Standard_HB120rs_v3               | 4            | 30                     | Tomada dupla EPYC 7713            |
Standard_HB120r 96s_v3            | 4            | 24                     | Tomada dupla EPYC 7643            |
Standard_HB120r 64s_v3            | 4            | 16                     | Tomada dupla EPYC 7543            |
Standard_HB120r-32s_v3            | 4            | 8                      | Tomada dupla EPYC 7313            |
Standard_HB120r 16s_v3            | 4            | 4                      | Tomada dupla EPYC 72F3            |

> [!NOTE]
> Os núcleos constrangidos tamanhos VM apenas reduzem o número de núcleos físicos expostos ao VM. Todos os ativos globais partilhados (RAM, largura de banda de memória, cache L3, conectividade GMI e xGMI, InfiniBand, rede Azure Ethernet, SSD local) permanecem constantes. Isto permite que um cliente escolha um tamanho VM melhor adaptado a um determinado conjunto de necessidades de carga de trabalho ou licenciamento de software.

O mapeamento virtual de UMA de cada tamanho HBv3 VM é mapeado para a topologia física de NUMA subjacente. Não há uma abstração potencialmente enganosa da topologia do hardware. 

A topologia exata para o tamanho de VM de vários [HBv3](../../hbv3-series.md) aparece da seguinte forma utilizando a saída do [istopo:](https://linux.die.net/man/1/lstopo)
```bash
lstopo-no-graphics --no-io --no-legend --of txt
```
<br>
<details>
<summary>Clique para ver a saída do Istopo para Standard_HB120rs_v3</summary>

![Saída istopo para HBv3-120 VM](./media/architecture/hbv3/hbv3-120-lstopo.png)
</details>

<details>
<summary>Clique para ver a saída do Istopo para Standard_HB120rs-96_v3</summary>

![Saída istopo para HBv3-96 VM](./media/architecture/hbv3/hbv3-96-lstopo.png)
</details>

<details>
<summary>Clique para ver a saída do Istopo para Standard_HB120rs 64_v3</summary>

![Saída istopo para HBv3-64 VM](./media/architecture/hbv3/hbv3-64-lstopo.png)
</details>

<details>
<summary>Clique para ver a saída do Istopo para Standard_HB120rs-32_v3</summary>

![Saída istopo para HBv3-32 VM](./media/architecture/hbv3/hbv3-32-lstopo.png)
</details>

<details>
<summary>Clique para ver a saída do Istopo para Standard_HB120rs 16_v3</summary>

![Saída istopo para HBv3-16 VM](./media/architecture/hbv3/hbv3-16-lstopo.png)
</details>

## <a name="infiniband-networking"></a>Rede InfiniBand
Os VMs HBv3 também contam com adaptadores de rede Nvidia Mellanox HDR InfiniBand (ConnectX-6) que operam a até 200 Gigabits/seg. O NIC é passado para o VM via SRIOV, permitindo que o tráfego de rede contorne o hipervisor. Como resultado, os clientes carregam os condutores padrão da Mellanox OFED em VMs HBv3, uma vez que seriam um ambiente metálico nu.

Os VMS HBv3 suportam o Adaptive Routing, o Transporte Conectado Dinâmico (DCT, adicional aos transportes RC e UD padrão) e a descarga baseada em hardware de coletivos MPI para o processador de bordo do adaptador ConnectX-6. Estas funcionalidades aumentam o desempenho da aplicação, escalabilidade e consistência, e a sua utilização é fortemente recomendada.

## <a name="temporary-storage"></a>Armazenamento temporário
Os VMS HBv3 dispõem de 3 dispositivos SSD fisicamente locais. Um dispositivo é pré-testado para servir como um ficheiro de página e aparecerá dentro do seu VM como um dispositivo genérico "SSD".

Dois outros SSDs maiores são fornecidos como dispositivos NVMe de bloco não equipados via NVMeDirect. À medida que o dispositivo NVMe de bloco contornar o hipervisor, terá maior largura de banda, IOPS mais alto e menor latência por PIO.

Quando emparelhado numa matriz às riscas, o SSD NVMe fornece até 7 leituras de 7 GB/s e 3 GB/s, e até 186.000 IOPS (leituras) e 201.000 IOPS (escreve) para profundidades profundas de fila.

## <a name="hardware-specifications"></a>Especificações de hardware 

| Especificações de hardware          | VMs da série HBv3              |
|----------------------------------|----------------------------------|
| Núcleos                            | 120, 96, 64, 32 ou 16 (SMT desativado)               | 
| CPU                              | AMD EPYC 7V13                   | 
| Frequência CPU (não-AVX)          | 3.1 GHz (todos os núcleos), 3.675 GHz (até 10 núcleos)    | 
| Memória                           | 448 GB (RAM por núcleo depende do tamanho de VM)         | 
| Disco Local                       | 2 * 960 GB NVMe (bloco), 480 GB SSD (ficheiro de página) | 
| Infiniband                       | 200 Gb/s Mellanox ConnectX-6 HDR InfiniBand | 
| Rede                          | 50 Gb/s Ethernet (40 Gb/s utilizável) Azure segundo Gen SmartNIC | 

## <a name="software-specifications"></a>Especificações de software 

| Especificações de software        | VMs da série HBv3                                            | 
|--------------------------------|-----------------------------------------------------------|
| Tamanho do trabalho de MPI Max               | 36.000 núcleos (300 VMs numa única escala de máquina virtual definida com singlePlacementGroup=verdadeiro) |
| Suporte MPI                    | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH  |
| Quadros Adicionais          | UCX, libfabric, PGAS                  |
| Suporte de armazenamento Azure          | Discos Standard e Premium (máximo 32 discos)              |
| Apoio ao SO para SRIOV RDMA      | CentOS/RHEL 7.6+, Ubuntu 18.04+, SLES 12 SP4+, WinServer 2016+           |
| SoA recomendado para desempenho | CentOS 8.1, Windows Server 2019+
| Apoio orquestrador           | Azure CycleCloud, Azure Batch, AKS; [opções de configuração de cluster](../../sizes-hpc.md#cluster-configuration-options)                      | 

> [!NOTE] 
> O Windows Server 2012 R2 não é suportado em HBv3 e outros VMs com mais de 64 núcleos (virtuais ou físicos). Aceda [aqui](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) para obter mais detalhes.

## <a name="next-steps"></a>Passos seguintes

- Leia sobre os últimos anúncios, exemplos de carga de trabalho do HPC e resultados de desempenho nos [Blogs comunitários Azure Compute Tech.](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)
- Para uma visão arquitetónica de nível mais elevado da execução das cargas de trabalho do HPC, consulte [a High Performance Computing (HPC) em Azure](/azure/architecture/topics/high-performance-computing/).
