---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 04/17/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 765ee3c737adbe1da89b9e908d0e22e44d0f29ba
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748967"
---
Os tamanhos de VM otimizados de armazenamento oferecem alta entrada de disco e IO, e são ideais para big data, bases de dados SQL, NoSQL, armazenamento de dados e grandes bases de dados transacionais.  Exemplos incluem Cassandra, MongoDB, Cloudera e Redis. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como a entrada local de armazenamento e largura de banda da rede para cada tamanho otimizado.

A série Lsv2 apresenta alta potência, baixa latência, mapeado diretamente o armazenamento local NVMe em execução no [processador AMD EPYC &trade; 7551](https://www.amd.com/en/products/epyc-7000-series) com um impulso de núcleo de 2,55GHz e um aumento máximo de 3,0GHz. Os VMs da série Lsv2 vêm em tamanhos de 8 a 80 vCPU numa configuração multi-rosca simultânea.  Existe 8 GiB de memória por vCPU, e um dispositivo 1.92TB NVMe SSD M.2 por 8 vCPUs, com até 19.2TB (10x1.92TB) disponíveis no L80s v2.

> [!NOTE]
> Os VMs da série Lsv2 são otimizados para usar o disco local no nó ligado diretamente ao VM em vez de utilizar discos de dados duráveis. Isto permite um maior iOPs / entrada para as suas cargas de trabalho. As séries Lsv2 e Ls não suportam a criação de uma cache local para aumentar os IOPs alcançáveis por discos de dados duráveis.
>
> A alta produção e IOPs do disco local tornam os VMs lsv2 e Ls-series ideais para lojas NoSQL como Apache Cassandra e MongoDB que replicam dados em vários VMs para alcançar persistência em caso de falha de um único VM.
>
> Para saber mais, consulte [o desempenho do Otimize nas máquinas virtuais da série Lsv2.](../articles/virtual-machines/linux/storage-performance.md)  


## <a name="lsv2-series"></a>Série Lsv2

ACU: 150-175

Armazenamento Premium: com suporte

Armazenamento em cache Premium: sem suporte

| Size          | vCPU | Memória (GiB) | Disco temporário<sup>1</sup> (GiB) | Discos NVMe<sup>2</sup> | Entrada de disco NVMe<sup>3</sup> (Ler IOPS / MBps) | Entrada de disco de dados max uncached (IOPs/MBps)<sup>4</sup> | Discos de dados Max | Máximo de NICs/largura de banda de rede esperada (Mbps) |
|---------------|-----------|-------------|--------------------------|----------------|---------------------------------------------------|-------------------------------------------|------------------------------|------------------------------| 
| Standard_L8s_v2   |  8 |  64 |  80 |  1x1.92 TB  | 400000 / 2000  | 8000/160   | 16 | 2 / 3200  |
| Standard_L16s_v2  | 16 | 128 | 160 |  2x1.92 TB  | 800000 / 4000  | 16000/320  | 32 | 4 / 6400  |
| Standard_L32s_v2  | 32 | 256 | 320 |  4x1.92 TB  | 1,5M / 8000    | 32000/640  | 32 | 8 / 12800 |
| Standard_L48s_v2  | 48 | 384 | 480 |  6x1.92 TB  | 2.2M / 14000   | 48000/960  | 32 | 8 / 16000+ |
| Standard_L64s_v2  | 64 | 512 | 640 |  8x1.92 TB  | 2.9M / 16000   | 64000/1280 | 32 | 8 / 16000+ |
| Standard_L80s_v2<sup>5</sup> | 80 | 640 | 800 | 10x1.92TB   | 3.8M / 20000   | 80000/1400 | 32 | 8 / 16000+ |

<sup>1</sup> Os VMs da série Lsv2 têm um disco de recursos temporários baseado em SCSI padrão para a utilização de ficheiros de paging/swap (D: no Windows, /dev/sdb no Linux). Este disco fornece 80 GiB de armazenamento, 4.000 IOPS e 80 MBps taxa de transferência por cada 8 vCPUs (por exemplo, Standard_L80s_v2 fornece 800 GiB a 40.000 IOPS e 800 MBPS). Isto garante que as unidades NVMe podem ser totalmente dedicadas ao uso da aplicação. Este disco é efémero, e todos os dados serão perdidos em stop/deallocate.

<sup>2</sup> Os Discos NVMe locais são efémeros, os dados serão perdidos nestes discos se parar/desalojar o seu VM.

<sup>3</sup> A tecnologia Hyper-V NVMe Direct fornece acesso não throttled às unidades locais da NVMe mapeadas de forma segura no espaço VM do hóspede.  Alcançar o máximo desempenho requer a utilização da mais recente construção WS2019 ou Ubuntu 18.04 ou 16.04 do Azure Marketplace.  O desempenho da escrita varia em função do tamanho da IO, da carga de acionamento e da utilização da capacidade.

<sup>4</sup> VMs série Lsv2 não fornecem cache hospedeiro para disco de dados, uma vez que não beneficia as cargas de trabalho Lsv2.  No entanto, os VMs Lsv2 podem acomodar a opção de disco Ephemeral VM OS da Azure (até 30 GiB).

<sup>5</sup> VMs com mais de 64 vCPUs requerem um destes sistemas operativos de hóspedes suportados:
- Windows Server 2016 ou posterior
- Ubuntu 16, 4 LTS ou posterior, com kernel ajustado do Azure (kernel 4,15 ou posterior)
- SLES 12 SP2 ou posterior
- RHEL ou CentOS versão 6.7 a 6.10, com o pacote LIS fornecido pela Microsoft 4.3.1 (ou mais tarde) instalado
- RHEL ou CentOS versão 7,3, com o pacote LIS fornecido pela Microsoft 4.2.1 (ou posterior) instalado
- RHEL ou CentOS versão 7,6 ou posterior
- Oracle Linux com UEK4 ou posterior
- Debian 9 com o kernel de backports, Debian 10 ou posterior
- CoreOS com um kernel 4,14 ou posterior


## <a name="size-table-definitions"></a>Definições da tabela de dimensionamento

- A capacidade de armazenamento é apresentada em unidades de GiB ou 1024^3 bytes. Quando comparar discos medidos em GB (1000^3 bytes) com discos medidos em GiB (1024^3), não se esqueça de que os números de capacidade especificados em GiB podem aparecer mais pequenos. Por exemplo, 1023 GiB = 1098,4 GB
- O débito do disco é medido em operações de entrada/saída por segundo (IOPS) e MBps, em que MBps = 10^6 bytes/seg.
- Se quiser obter o melhor desempenho para os seus VMs, deve limitar o número de discos de dados a 2 discos por vCPU.
- **A largura de banda esperada** da rede é a largura de banda agregada máxima [atribuída por tipo VM](../articles/virtual-network/virtual-machine-network-throughput.md) em todos os NICs, para todos os destinos. Os limites superiores não são garantidos, mas foram concebidos para fornecer orientações para selecionar o tipo de VM correto para a aplicação pretendida. O desempenho de rede real irá depender de vários fatores, incluindo congestionamento, cargas e definições da rede. Para obter mais informações sobre a otimização do débito de rede, veja [Otimizar o débito de rede para Windows e Linux](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Para alcançar o desempenho de rede esperado no Linux ou no Windows, poderá ser necessário selecionar uma versão específica ou otimizar a VM. Para obter mais informações, veja [Como fazer um teste fiável de um débito de máquina virtual](../articles/virtual-network/virtual-network-bandwidth-testing.md).
