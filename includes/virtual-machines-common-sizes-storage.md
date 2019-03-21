---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 07/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: e05281b2279f5d40f8a3ba4ed3f49a38e5abf0ee
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58261596"
---
Armazenamento de tamanhos de VM otimizados oferecem débito de disco elevados e e/s e são ideais para macrodados, SQL, NoSQL bases de dados, armazenamento de dados e grandes bancos de dados transacionais.  Os exemplos incluem Cassandra, MongoDB, Cloudera e Redis. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como armazenamento local débito e de rede da largura de banda para cada tamanho otimizada.

O série Lsv2 funcionalidades alto débito, baixa latência, diretamente mapeado armazenamento local de NVMe em execução no [AMD EPYC &trade; processador 7551](https://www.amd.com/en/products/epyc-7000-series) com um todos os núcleos propulsão 2.55 GHz e um aumento máximo de 3.0 GHz. As VMs da série Lsv2 existem em tamanhos que variam entre as 8 e as 80 vCPUs numa configuração de multithreading simultâneo.  Inclui memória de 8 GiB por vCPU e um dispositivo NVMe SSD M.2 de 1,92 TB por 8 vCPUs, com um valor máximo de 19,2 TB (10 x 1,92 TB) disponível na versão L80s v2.

A série Ls oferece até 32 vCPUs, com o [processador Intel® Xeon® E5 v3 família](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). A série Ls tem o mesmo desempenho de CPU que a série G/GS e dispõe de 8 GiB de memória por vCPU.

> [!NOTE]
> As VMs da série Lsv2 são otimizadas para utilizar o disco local no nó ligados diretamente à VM, em vez de utilizar discos de dados durável.  Isso possibilita maior IOPs / débito das cargas de trabalho.  A série Ls e Lsv2 não suportam a criação de um cache local para aumentar o IOPS alcançável por discos de dados durável. O alto débito e IOPS de disco local torna as VMs de série Ls e Lsv2 ideal para arquivos de NoSQL, como o Apache Cassandra e MongoDB que replique dados em várias VMs para alcançar a persistência no caso de falha de uma única VM. 

## <a name="lsv2-series"></a>Série Lsv2
ACU: 150-175

Armazenamento Premium: Suportadas

Cache de armazenamento Premium: Não suportado

| Tamanho          | vCPU | Memória (GiB) | Disco temporário<sup>1</sup> (GiB) | Discos NVMe<sup>2</sup> | Débito de disco de NVMe<sup>3</sup> (IOPS de leitura / MBps) | Max não colocado em cache o débito de disco de dados (IOPs/MBps)<sup>4</sup> | Discos de dados de máx. | NICs. Máx. / esperado de largura de banda de rede (Mbps) | 
|---------------|-----------|-------------|--------------------------|----------------|---------------------------------------------------|-------------------------------------------|------------------------------|------------------------------| 
| Standard_L8s_v2   |  8 |  64 |  80 |  1x1.92 TB  | 400,000 / 2,000 | 8,000/160 | 16 | 2 / 3,200  | 
| Standard_L16s_v2  | 16 | 128 | 160 |  2x1.92 TB  | 800,000 / 4,000 | 16,000/320 | 32 | 4 / 6,400  | 
| Standard_L32s_v2  | 32 | 256 | 320 |  4x1.92 TB  | 1,5 M / 8.000    | 32,000/640 | 32 | 8 / 12,800 | 
| Standard_L64s_v2  | 64 | 512 | 640 |  8x1.92 TB  | 2.9 M / 16.000   | 64,000/1,280 | 32 | 8 / 25,600 |
| Standard_L80s_v2  | 80 | 640 | 800 | 10x1.92TB   | 3,8 M / 20.000   | 80,000/1,400 | 32 | 8 / 32,000 |
 
<sup>1</sup> VMs da série Lsv2 tem um disco de recursos temporário com base de SCSI padrão para utilização do ficheiro de paginação/troca de SO (d no Windows, /dev/sdb no Linux). Este disco fornece 80 GiB de armazenamento, de 4.000 IOPS e velocidade para cada 8 vCPUs (por exemplo, Standard_L80s_v2 fornece GiB 800 40.000 IOPS e 800 MBPS) de transferência de 80 MBps. Isto garante que as unidades de nvme, algo podem ser completamente dedicadas para a utilização de aplicações. Este disco é Efêmera e todos os dados serão perdidos em Parar/desalocar.

<sup>2</sup> discos de NVMe locais são efémeros, dados serão perdidos nestes discos, se parar/desalocar a VM.

<sup>3</sup> a tecnologia Hyper-V NVMe Direct fornece acesso otimizado para unidades de NVMe locais em segurança mapeado para o espaço VM do convidado.  Obter o máximo de desempenho, é necessário usar a compilação mais recente do WS2019 ou Ubuntu 18.04 ou 16.04 no Azure Marketplace.  Desempenho de gravação varia com base no tamanho de e/s, a carga de unidade e a utilização de capacidade.

<sup>4</sup> VMs da série Lsv2 não oferecem cache do anfitrião para o disco de dados como não se beneficiem as cargas de trabalho Lsv2.  No entanto, as VMs de Lsv2 pode acomodar a opção de disco de SO efémero de VM do Azure (até 30 GiB). 



## <a name="ls-series"></a>Série Ls
ACU: 180-240

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Não suportado
 
| Tamanho          | vCPU | Memória (GiB) | Armazenamento temporário (GiB) | Discos de dados máximos | Débito máximo do armazenamento temporário (IOPS / MBps) | Max não colocado em cache de débito de disco (IOPS / MBps) | NICs. Máx. / esperado de largura de banda de rede (Mbps) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20,000 / 200 | 5,000 / 125  | 2 / 4,000  | 
| Standard_L8s   | 8  | 64  | 1,388 | 32 | 40,000 / 400 | 10,000 / 250 | 4 / 8,000  | 
| Standard_L16s  | 16 | 128 | 2,807 | 64 | 80,000 / 800 | 20,000 / 500 | 8 / 16,000 | 
| Standard_L32s <sup>1</sup> | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 40,000 / 1,000     | 8 / 20,000 | 
 

O débito máximo do disco possível com VMs da série Ls pode estar limitado pelo número, tamanho e repartição de quaisquer discos de anexados. Para obter detalhes, consulte [conceber o elevado desempenho](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>1</sup> instância está isolada do hardware dedicado de um único cliente.

## <a name="size-table-definitions"></a>Definições da tabela de dimensionamento

- A capacidade de armazenamento é apresentada em unidades de GiB ou 1024^3 bytes. Quando comparar discos medidos em GB (1000^3 bytes) com discos medidos em GiB (1024^3), não se esqueça de que os números de capacidade especificados em GiB podem aparecer mais pequenos. Por exemplo, 1023 GiB = 1098,4 GB
- O débito do disco é medido em operações de entrada/saída por segundo (IOPS) e MBps, em que MBps = 10^6 bytes/seg.
- Se quiser obter o melhor desempenho para as suas VMs, deve limitar o número de discos de dados de 2 discos por vCPU.
- **Esperado de largura de banda de rede** é o máximo agregado [largura de banda alocada por tipo de VM](../articles/virtual-network/virtual-machine-network-throughput.md) em todas as NICs, para todos os destinos. Os limites superiores não são garantidos, mas foram concebidos para fornecer orientações para selecionar o tipo de VM correto para a aplicação pretendida. O desempenho de rede real irá depender de vários fatores, incluindo congestionamento, cargas e definições da rede. Para obter mais informações sobre a otimização do débito de rede, veja [Otimizar o débito de rede para Windows e Linux](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Para alcançar o desempenho de rede esperado no Linux ou no Windows, poderá ser necessário selecionar uma versão específica ou otimizar a VM. Para obter mais informações, veja [Como fazer um teste fiável de um débito de máquina virtual](../articles/virtual-network/virtual-network-bandwidth-testing.md).
