---
title: Tamanhos Azure VM - gerações anteriores Microsoft Docs
description: Lista as gerações anteriores de tamanhos disponíveis para máquinas virtuais em Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como o armazenamento e largura de banda de rede para tamanhos nesta série.
services: virtual-machines
ms.subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/02/2020
ms.author: jushiman
ms.openlocfilehash: afad68be53637cf258223eafab0a13f525a92f4c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91666624"
---
# <a name="previous-generations-of-virtual-machine-sizes"></a>Gerações anteriores de tamanhos de máquinas virtuais

Esta secção fornece informações sobre gerações anteriores de tamanhos de máquinas virtuais. Estes tamanhos ainda podem ser usados, mas há gerações mais novas disponíveis.

## <a name="f-series"></a>Série F

A série F tem por base o processador Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz, o qual pode atingir velocidades de relógio de 3,1 GHz com o Intel Turbo Boost Technology 2.0. Este é o mesmo desempenho de CPU da série Dv2 de VMs.  

As VMs da série F são uma excelente opção para cargas de trabalho que exigem CPUs mais rápidas, mas não precisam de mais memória ou armazenamento temporário por vCPU.  As cargas de trabalho, como análise, servidores de jogos, servidores Web e processamento de lotes, beneficiarão do valor da série F.

ACU: 210 - 250

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Produção de armazenamento temporário máximo: IOPS/Read MBps/Write MBps | Discos de dados/produção de dados máximos: IOPS | Largura de banda de rede Max NICs/Expect (Mbps) |
|---|---|---|---|---|---|---|
| Standard_F1  | 1  | 2  | 16  | 3000/46/23    | 4/4x500   | 2/750   |
| Standard_F2  | 2  | 4  | 32  | 6000/93/46    | 8/8x500   | 2/1500  |
| Standard_F4  | 4  | 8  | 64  | 12000/187/93  | 16/16x500 | 4/3000  |
| Standard_F8  | 8  | 16 | 128 | 24000/375/187 | 32/32x500 | 8/6000  |
| Standard_F16 | 16 | 32 | 256 | 48000/750/375 | 64/64x500 | 8/12000 |

## <a name="fs-series-sup1sup"></a>Série F <sup>1</sup>

A série Fs oferece todas as vantagens da série F, além do armazenamento Premium.

ACU: 210 - 250

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Produção de armazenamento em cache máximo e temporário: IOPS/MBps (tamanho da cache em GiB) | Produção de disco não-abacatado por maxilar: IOPS/MBps | Largura de banda de rede Max NICs/Expect (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_F1s  | 1  | 2  | 4  | 4  | 4000/32 (12)    | 3200/48   | 2/750   |
| Standard_F2s  | 2  | 4  | 8  | 8  | 8000/64 (24)    | 6400/96   | 2/1500  |
| Standard_F4s  | 4  | 8  | 16 | 16 | 16000/128 (48)  | 12800/192 | 4/3000  |
| Standard_F8s  | 8  | 16 | 32 | 32 | 32000/256 (96)  | 25600/384 | 8/6000  |
| Standard_F16s | 16 | 32 | 64 | 64 | 64000/512 (192) | 51200/768 | 8/12000 |

MBps = 10^6 bytes por segundo e GiB = 1024^3 bytes.

<sup>1</sup> A produção máxima de disco (IOPS ou MBps) possível com um VM da série Fs pode ser limitada pelo número, tamanho e desmontagem do ou dos discos anexos.  Para mais detalhes, consulte [Design para obter um desempenho elevado.](premium-storage-performance.md)


## <a name="nvv2-series"></a>Série NVv2

**Recomendação de tamanho mais recente**: Série [NVv3](nvv3-series.md)

As máquinas virtuais da série NVv2 são alimentadas pela [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPUs e pela tecnologia NVIDIA GRID com CPUs Intel Broadwell. Estas máquinas virtuais são direcionadas para aplicações gráficas aceleradas da GPU e desktops virtuais onde os clientes querem visualizar os seus dados, simular resultados para ver, trabalhar no CAD ou renderizar e transmitir conteúdo. Além disso, estas máquinas virtuais podem executar cargas de trabalho de precisão única, tais como codificação e renderização. As máquinas virtuais NVv2 suportam o Armazenamento Premium e vêm com o dobro da memória do sistema (RAM) quando comparadas com as suas séries NV antecessoras.  

Cada GPU em instâncias NVv2 vem com uma licença GRID. Esta licença dá-lhe a flexibilidade para usar uma instância NV como uma estação de trabalho virtual para um único utilizador, ou 25 utilizadores simultâneos podem ligar-se ao VM para um cenário de aplicação virtual.

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória gpu: GiB | Discos de dados máximos | NICs máximos | Estações de trabalho virtuais | Aplicações Virtuais |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6s_v2  | 6  | 112 | 320  | 1 | 8  | 12 | 4 | 1 | 25  |
| Standard_NV12s_v2 | 12 | 224 | 640  | 2 | 16 | 24 | 8 | 2 | 50  |
| Standard_NV24s_v2 | 24 | 448 | 1280 | 4 | 32 | 32 | 8 | 4 | 100 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="older-generations-of-virtual-machine-sizes"></a>Gerações mais velhas de tamanhos de máquinas virtuais

Esta secção fornece informações sobre gerações mais antigas de tamanhos de máquinas virtuais. Estes tamanhos ainda são suportados, mas não receberão capacidade adicional. Existem tamanhos mais recentes ou alternativos que geralmente estão disponíveis. Consulte os [Tamanhos das máquinas virtuais Linux em Azure](./sizes.md) para escolher os tamanhos VM que melhor se adaptem à sua necessidade.  

Para obter mais informações sobre o redimensionamento de um Linux VM, consulte [Resize um Linux VM](linux/change-vm-size.md).  

<br>

### <a name="basic-a"></a>Básico A  

**Recomendação de tamanho mais recente**: Série [Av2](av2-series.md)

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

Os tamanhos do escalão básico destinam-se, principalmente, ao desenvolvimento de cargas de trabalho e outras aplicações que não requerem balanceamento de carga, dimensionamento automático ou máquinas virtuais que consomem muita memória.

| Tamanho – Tamanho\Nome | vCPU | Memória|NICs (Máx)| Tamanho máximo do disco temporário | Um máximo de discos de dados (1023 GB cada)| Um máximo de IOPS (300 por disco) |
|---|---|---|---|---|---|---|
| A0\Basic_A0 | 1 | 768 MB  | 2 | 20 GB  | 1  | 1 x 300  |
| A1\Basic_A1 | 1 | 1,75 GB | 2 | 40 GB  | 2  | 2 x 300  |
| A2\Basic_A2 | 2 | 3,5 GB  | 2 | 60 GB  | 4  | 4 x 300  |
| A3\Basic_A3 | 4 | 7 GB    | 2 | 120 GB | 8  | 8 x 300  |
| A4\Basic_A4 | 8 | 14 GB   | 2 | 240 GB | 16 | 16 x 300 |

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0 a A4 que utiliza a CLI e o PowerShell

No modelo de implementação clássica, alguns nomes de tamanhos de VMs são ligeiramente diferentes, como na CLI e no PowerShell:

* Standard_A0 é ExtraSmall
* Standard_A1 é Small
* Standard_A2 é Medium
* Standard_A3 é Large
* Standard_A4 é ExtraLarge

### <a name="a-series"></a>Série A  

**Recomendação de tamanho mais recente**: Série [Av2](av2-series.md)

ACU: 50-100

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento (HDD) temporário: GiB | Discos de dados máximos | Débito máximo do disco de dados: IOPS | Largura de banda de rede Max NICs/Expect (Mbps) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 &nbsp; <sup>1</sup> | 1 | 0.768 | 20 | 1 | 1x500 | 2/100 |
| Standard_A1 | 1 | 1,75 | 70  | 2  | 2x500  | 2/500  |
| Standard_A2 | 2 | 3.5  | 135 | 4  | 4x500  | 2/500  |
| Standard_A3 | 4 | 7    | 285 | 8  | 8x500  | 2/1000 |
| Standard_A4 | 8 | 14   | 605 | 16 | 16x500 | 4/2000 |
| Standard_A5 | 2 | 14   | 135 | 4  | 4x500  | 2/500  |
| Standard_A6 | 4 | 28   | 285 | 8  | 8x500  | 2/1000 |
| Standard_A7 | 8 | 56   | 605 | 16 | 16x500 | 4/2000 |

<sup>1</sup> O tamanho A0 está sobre-subscrito no hardware físico. Apenas para este tamanho específico, outras implementações de cliente podem afetar o desempenho da carga de trabalho em execução. O desempenho relativo é indicado abaixo como a linha de base esperada, sujeito a uma variabilidade aproximada de 15%.

<br>

### <a name="a-series---compute-intensive-instances"></a>Série A – Instâncias de computação intensiva  

**Recomendação de tamanho mais recente**: Série [Av2](av2-series.md)

ACU: 225

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

Os tamanhos das séries A8-A11 e H também são conhecidos como *instâncias de computação intensiva*. O hardware que executa estes tamanhos foi concebido e otimizado para aplicações de computação e rede intensivas, incluindo aplicações, modelação e simulações de clusters de computação de alto desempenho (HPC). As séries A8-A11 utilizam o Intel Xeon E5-2670 @ 2,6 GHZ e a série H utiliza o Intel Xeon E5-2667 v3 @ 3,2 GHz.  

| Tamanho | vCPU | Memória: GiB | Armazenamento (HDD) temporário: GiB | Discos de dados máximos | Débito máximo do disco de dados: IOPS | NICs máximos|
|---|---|---|---|---|---|---|
| Standard_A8 &nbsp; <sup>1</sup> | 8  | 56  | 382 | 32 | 32x500 | 2 |
| Standard_A9 &nbsp; <sup>1</sup> | 16 | 112 | 382 | 64 | 64x500 | 4 |
| Standard_A10 | 8  | 56  | 382 | 32 | 32x500 | 2 |
| Standard_A11 | 16 | 112 | 382 | 64 | 64x500 | 4 |

<sup>1</sup> Para aplicações MPI, a rede de backend dedicada RDMA é ativada pela rede FDR InfiniBand, que fornece uma latência ultra-baixa e alta largura de banda.  

> [!NOTE]
> A A8 – A11 VMs está prevista para a reforma em 3/2021. Recomendamos vivamente não criar novos A8 – A11 VMs. Por favor, emigre todos os A8 – VMs existentes para tamanhos VM de computação de alto desempenho mais recentes e poderosos, tais como H, HB, HC, HBv2, bem como tamanhos VM de computação de propósito geral, tais como D, E e F para um melhor desempenho de preço. Para mais informações, consulte [o Guia de Migração do HPC.](https://azure.microsoft.com/resources/hpc-migration-guide/)

<br>

### <a name="d-series"></a>Série D  

**Recomendação de tamanho mais recente**: Série [Dav4,](dav4-dasv4-series.md) [Série Dv4](dv4-dsv4-series.md) e [Série Ddv4](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1</sup>

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Produção de armazenamento temporário máximo: IOPS/Read MBps/Write MBps | Discos de dados/produção de dados máximos: IOPS | Largura de banda de rede Max NICs/Expect (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D1  | 1 | 3.5 | 50  | 3000/46/23    | 4/4x500   | 2/500  |
| Standard_D2  | 2 | 7   | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standard_D3  | 4 | 14  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standard_D4  | 8 | 28  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |

<sup>1</sup> VM Family pode funcionar em um dos seguintes CPU's: 2.2 GHz Intel Xeon® E5-2660 v2, 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) ou 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>Série D - memória otimizada  

**Recomendação de tamanho mais recente**: Série [Dav4,](dav4-dasv4-series.md) [Série Dv4](dv4-dsv4-series.md) e [Série Ddv4](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1</sup>

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Produção de armazenamento temporário máximo: IOPS/Read MBps/Write MBps | Discos de dados/produção de dados máximos: IOPS | Largura de banda de rede Max NICs/Expect (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D11 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standard_D12 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standard_D13 | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |
| Standard_D14 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8/8000 |

<sup>1</sup> VM Family pode funcionar em um dos seguintes CPU's: 2.2 GHz Intel Xeon® E5-2660 v2, 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) ou 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

## <a name="preview-dc-series"></a>Pré-visualização: Série DC

**Recomendação de tamanho mais recente**: Série [DCsv2](dcv2-series.md)

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

A série DC utiliza a última geração de processador Intel XEON E-2176G de 3.7GHz com tecnologia SGX, e com a Tecnologia Intel Turbo Boost pode ir até 4.7GHz. 

| Tamanho          | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo do armazenamento temporário e em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | Max NICs / Largura de banda esperada (Mbps) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000 / 32 (43)                                                          | 3200 /48                                  | 2 / 1500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400 /96                                  | 2 / 3000                                     |

> [!IMPORTANT]
>
> Os VMs da série DC são [de geração 2 VMs](./linux/generation-2.md#creating-a-generation-2-vm) e `Gen2` apenas imagens de suporte.


### <a name="ds-series"></a>Série DS  

**Recomendação de tamanho mais recente**: [Séries Dasv4,](dav4-dasv4-series.md) [séries Dsv4](dv4-dsv4-series.md) e [série Ddsv4](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1</sup>

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Produção de armazenamento em cache máximo e temporário: IOPS/MBps (tamanho da cache em GiB) | Produção de disco não-abacatado por maxilar: IOPS/MBps | Largura de banda de rede Max NICs/Expect (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS1 | 1 | 3.5 | 7  | 4  | 4000/32 (43)    | 3200/32   | 2/500  |
| Standard_DS2 | 2 | 7   | 14 | 8  | 8000/64 (86)    | 6400/64   | 2/1000 |
| Standard_DS3 | 4 | 14  | 28 | 16 | 16000/128 (172) | 12800/128 | 4/2000 |
| Standard_DS4 | 8 | 28  | 56 | 32 | 32000/256 (344) | 25600/256 | 8/4000 |

<sup>1</sup> VM Family pode funcionar em um dos seguintes CPU's: 2.2 GHz Intel Xeon® E5-2660 v2, 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) ou 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>Série DS - memória otimizada  

**Recomendação de tamanho mais recente**: [Séries Dasv4,](dav4-dasv4-series.md) [séries Dsv4](dv4-dsv4-series.md) e [série Ddsv4](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1,2</sup>

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Produção de armazenamento em cache máximo e temporário: IOPS/MBps (tamanho da cache em GiB) | Produção de disco não-abacatado por maxilar: IOPS/MBps | Largura de banda de rede Max NICs/Expect (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS11 | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/64   | 2/1000 |
| Standard_DS12 | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/128 | 4/2000 |
| Standard_DS13 | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/256 | 8/4000 |
| Standard_DS14 | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/512 | 8/8000 |

<sup>1</sup> A produção máxima de disco (IOPS ou MBps) possível com um VM série DS pode ser limitada pelo número, tamanho e desmontagem do ou dos discos anexos.  Para mais detalhes, consulte [Design para obter um desempenho elevado.](premium-storage-performance.md)
<sup>2</sup> VM Family pode funcionar em um dos seguintes CPU's: 2.2 GHz Intel Xeon® E5-2660 v2, 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) ou 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ls-series"></a>Série Ls

**Recomendação de tamanho mais recente**: Série [Lsv2](lsv2-series.md)

A série Ls oferece até 32 vCPUs, com o [processador Intel® Xeon® E5 v3 família](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). A série Ls tem o mesmo desempenho de CPU que a série G/GS e dispõe de 8 GiB de memória por vCPU.

A série L não suporta a criação de uma cache local para aumentar o IOPS alcançável por discos de dados duráveis. A elevada produção e iops do disco local torna os VMs da série LS ideais para lojas NoSQL, como a Apache Cassandra e a MongoDB, que replicam dados em vários VMs para alcançar a persistência em caso de falha de um único VM.

ACU: 180-240

Armazenamento Premium: Suportado

Caching de armazenamento premium: Não suportado

| Tamanho | vCPU | Memória (GiB) | Armazenamento temporário (GiB) | Discos de dados máximos | Produção de armazenamento temporário max (IOPS/MBps) | Produção de disco não encaqueado por gelo (IOPS/MBps) | Largura de banda de rede Max NICs/Expect (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_L4s   | 4  | 32  | 678  | 16 | 20000/200 | 5000/125  | 2/4000  |
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4/8000  |
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8/16000 |
| Standard_L32s &nbsp; <sup>1</sup> | 32 | 256 | 5630 | 64 | 160000/1600 | 40000/1000 | 8/20000 |

A produção máxima de disco possível com VMs da série L pode ser limitada pelo número, tamanho e despimento de quaisquer discos anexos. Para mais detalhes, consulte [Design para obter um desempenho elevado.](premium-storage-performance.md)

<sup>1</sup> A instância é isolada para hardware dedicado a um único cliente.

### <a name="gs-series"></a>Série GS

**Recomendação de tamanho mais recente**: série [Easv4,](eav4-easv4-series.md) [série Esv4,](ev4-esv4-series.md) [série Edsv4](edv4-edsv4-series.md) e [sérieS M](m-series.md)

ACU: 180 - 240 <sup>1</sup>

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo do armazenamento temporário e em cache: IOPS/MBps (tamanho da cache em GiB) | Produção de disco não-abacatado por maxilar: IOPS/MBps | Largura de banda de rede Max NICs/Expect (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 | 2 | 28  | 56  | 8  | 10000/100 (264)  | 5000/ 125  | 2/2000 |
| Standard_GS2 | 4 | 56  | 112 | 16 | 20000/200 (528)  | 10000/ 250 | 2/4000 |
| Standard_GS3 | 8 | 112 | 224 | 32 | 40000/400 (1056) | 20000/ 500 | 4/8000 |
| Standard_GS4 &nbsp; <sup>3</sup> | 16 | 224 | 448 | 64 | 80000/800 (2112) | 40000/1000 | 8/16000 |
| Standard_GS5 &nbsp; <sup>2, &nbsp; 3</sup> | 32 | 448 |896 | 64 |160000/1600 (4224) | 80000/2000 | 8/20000 |

<sup>1</sup> A produção máxima de disco (IOPS ou MBps) possível com um VM da série GS pode ser limitada pelo número, tamanho e desmontagem do ou dos discos anexos. Para mais detalhes, consulte [Design para obter um desempenho elevado.](premium-storage-performance.md)

<sup>2</sup> A instância é isolada para hardware dedicado a um único cliente.

<sup>3</sup> tamanhos de núcleo limitados disponíveis.

<br>

### <a name="g-series"></a>Série G

**Recomendação de tamanho mais recente**: Série [Eav4,](eav4-easv4-series.md) [série Ev4](ev4-esv4-series.md) e [série Edv4](edv4-edsv4-series.md) e [sérieS M](m-series.md)

ACU: 180 - 240

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Produção de armazenamento temporário máximo: IOPS/Read MBps/Write MBps | Discos de dados/produção de dados máximos: IOPS | Largura de banda de rede Max NICs/Expect (Mbps) |
|---|---|---|---|---|---|---|
| Standard_G1  | 2  | 28  | 384  | 6000/93/46    | 8/8x500   | 2/2000  |
| Standard_G2  | 4  | 56  | 768  | 12000/187/93  | 16/16x500 | 2/4000  |
| Standard_G3  | 8  | 112 | 1536 | 24000/375/187 | 32/32x500 | 4/8000  |
| Standard_G4  | 16 | 224 | 3072 | 48000/750/375 | 64/64x500 | 8/16000 |
| Standard_G5 &nbsp; <sup>1</sup> | 32 | 448 | 6144 | 96000/1500/750| 64/64x500 | 8/20000 |

<sup>1</sup> A instância é isolada para hardware dedicado a um único cliente.
<br>

## <a name="nv-series"></a>Série NV
**Recomendação de tamanho mais recente**: Série [NVv3](nvv3-series.md) e [série NVv4](nvv4-series.md)

As máquinas virtuais da série NV são alimentadas pela [tecnologia NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPUs e NVIDIA GRID para aplicações aceleradas no ambiente de trabalho e desktops virtuais onde os clientes são capazes de visualizar os seus dados ou simulações. Os utilizadores são capazes de visualizar os seus fluxos de trabalho intensivos gráficos nas instâncias NV para obter uma capacidade gráfica superior e, adicionalmente, executar cargas de trabalho de precisão única, tais como codificação e renderização. Os VMs da série NV também são alimentados por CPUs Intel Xeon E5-2690 v3 (Haswell).

Cada GPU em instâncias NV vem com uma licença GRID. Esta licença dá-lhe a flexibilidade para usar uma instância NV como uma estação de trabalho virtual para um único utilizador, ou 25 utilizadores simultâneos podem ligar-se ao VM para um cenário de aplicação virtual.

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

Migração ao vivo: Não suportado

Atualizações de preservação da memória: Não suportadas

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória gpu: GiB | Discos de dados máximos | NICs máximos | Estações de trabalho virtuais | Aplicações Virtuais |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standard_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standard_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = metade de uma placa M60.
<br>

## <a name="other-sizes"></a>Outros tamanhos

* [Fins gerais](sizes-general.md)
* [Com otimização de computação](sizes-compute.md)
* [Com otimização de memória](sizes-memory.md)
* [Com otimização de armazenamento](sizes-storage.md)
* [GPU](sizes-gpu.md)
* [Computação de elevado desempenho](sizes-hpc.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
