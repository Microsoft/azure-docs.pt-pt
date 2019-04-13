---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines-windows, virtual-machines-linux
author: laurenhughes
ms.service: multiple
ms.topic: include
ms.date: 04/11/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: d89a9c4c4498e249dbfbd453ef9772d18ffd213f
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59550159"
---
Esta seção fornece informações sobre suporte a gerações anteriores de tamanhos de máquina virtual. Estes tamanhos ainda são suportados mas não receberão a capacidade adicional. Existem mais recente ou alternativos tamanhos que estão disponíveis em geral. Consulte a [máquinas de virtuais de tamanhos para Windows no Azure](../articles/virtual-machines/windows/sizes.md) ou [tamanhos de máquinas de virtuais do Linux no Azure](../articles/virtual-machines/linux/sizes.md) para escolher a VM tamanhos que serão melhor acordo com as suas necessidades.  

Para obter mais informações sobre o redimensionamento de uma VM do Linux, consulte [redimensionar a máquina virtual do Linux com a CLI do Azure](../articles/virtual-machines/linux/change-vm-size.md). Se estiver a utilizar VMs do Windows e preferir utilizar o PowerShell, veja [redimensionar uma VM do Windows](../articles/virtual-machines/windows/resize-vm.md).  

<br>

### <a name="basic-a"></a>Básico A  

**Recomendação de tamanho mais recente**: [Série Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

Armazenamento Premium:  Não suportado

Cache de armazenamento Premium:  Não suportado

Os tamanhos do escalão básico destinam-se, principalmente, ao desenvolvimento de cargas de trabalho e outras aplicações que não requerem balanceamento de carga, dimensionamento automático ou máquinas virtuais que consomem muita memória.

|Tamanho – Tamanho\Nome | vCPU |Memória|NICs (Máx)|Tamanho máximo do disco temporário |Um máximo de discos de dados (1023 GB cada)|Um máximo de IOPS (300 por disco)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2| 20 GB|1|1 x 300|
|A1\Basic_A1|1|1,75 GB|2| 40 GB |2|2 x 300|
|A2\Basic_A2|2|3,5 GB|2| 60 GB|4|4 x 300|
|A3\Basic_A3|4|7 GB|2| 120 GB |8|8 x 300|
|A4\Basic_A4|8|14 GB|2| 240 GB |16|16 x 300|

<br>

### <a name="a-series"></a>Série A  

**Recomendação de tamanho mais recente**: [Série Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 50-100

Armazenamento Premium:  Não suportado

Cache de armazenamento Premium:  Não suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (HDD): GiB | Discos de dados máximos | Débito de disco de dados máximo: IOPS | NICs. Máx. / esperado de largura de banda de rede (Mbps)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1</sup> |1 |0.768 |20 |1 |1x500 |2 / 100 |
| Standard_A1 |1 |1.75 |70 |2 |2x500 |2 / 500  |
| Standard_A2 |2 |3.5 |135 |4 |4x500 |2 / 500 |
| Standard_A3 |4 |7 |285 |8 |8x500 |2 / 1000 |
| Standard_A4 |8 |14 |605 |16 |16x500 |4 / 2000 |
| Standard_A5 |2 |14 |135 |4 |4x500 |2 / 500 |
| Standard_A6 |4 |28 |285 |8 |8x500 |2 / 1000 |
| Standard_A7 |8 |56 |605 |16 |16x500 |4 / 2000 |

<sup>1</sup> tamanho o A0 está sobre-subscrito no hardware físico. Apenas para este tamanho específico, outras implementações de cliente podem afetar o desempenho da carga de trabalho em execução. O desempenho relativo é indicado abaixo como a linha de base esperada, sujeito a uma variabilidade aproximada de 15%.

<br>

### <a name="a-series---compute-intensive-instances"></a>Série A – Instâncias de computação intensiva  

**Recomendação de tamanho mais recente**: [Série Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 225

Armazenamento Premium:  Não suportado

Cache de armazenamento Premium:  Não suportado

Os tamanhos das séries A8-A11 e H também são conhecidos como *instâncias de computação intensiva*. O hardware que executa estes tamanhos foi concebido e otimizado para aplicações de computação e rede intensivas, incluindo aplicações, modelação e simulações de clusters de computação de alto desempenho (HPC). As séries A8-A11 utilizam o Intel Xeon E5-2670 @ 2,6 GHZ e a série H utiliza o Intel Xeon E5-2667 v3 @ 3,2 GHz.  

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (HDD): GiB | Discos de dados máximos | Débito de disco de dados máximo: IOPS | NICs máximos|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8&nbsp;<sup>1</sup> |8 |56 |382 |32 |32x500 |2 |
| Standard_A9&nbsp;<sup>1</sup> |16 |112 |382 |64 |64x500 |4 |
| Standard_A10 |8 |56 |382 |32 |32x500 |2  |
| Standard_A11 |16 |112 |382 |64 |64x500 |4 |

<sup>1</sup>para aplicações de MPI, rede de back-end RDMA dedicada é ativada pela rede FDR InfiniBand, que proporciona o ultra baixa latência e alta largura de banda.  

<br>

### <a name="d-series"></a>Série D  

**Recomendação de tamanho mais recente**: [Série Dv3](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160-250 <sup>1</sup>

Armazenamento Premium:  Não suportado

Cache de armazenamento Premium:  Não suportado

| Tamanho         | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Débito do armazenamento temporário máximo: IOPS / MBps de leitura / MBps de escrita | Discos de dados máximos / débito: IOPS | NICs. Máx. / esperado de largura de banda de rede (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3.5         | 50             | 3000 / 46 / 23                                           | 4 / 4x500                         | 2 / 500                 |
| Standard_D2  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1000                     |
| Standard_D3  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 2000                     |
| Standard_D4  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 4000                     |

<sup>1</sup> família de VM pode ser executado em um dos seguinte da CPU: 2.2 GHz Intel Xeon® E5 2660 v2, 2,4 GHz Intel Xeon® E5 2673 v3 (Haswell) ou 2.3 GHz Intel XEON® E5 2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>Série D - com otimização de memória  

**Recomendação de tamanho mais recente**: [Série Dv3](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160-250 <sup>1</sup>

Armazenamento Premium:  Não suportado

Cache de armazenamento Premium:  Não suportado

| Tamanho         | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Débito do armazenamento temporário máximo: IOPS / MBps de leitura / MBps de escrita | Discos de dados máximos / débito: IOPS | NICs. Máx. / esperado de largura de banda de rede (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1000                     |
| Standard_D12 | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 2000                     |
| Standard_D13 | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 4000                     |
| Standard_D14 | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 8000                |

<sup>1</sup> família de VM pode ser executado em um dos seguinte da CPU: 2.2 GHz Intel Xeon® E5 2660 v2, 2,4 GHz Intel Xeon® E5 2673 v3 (Haswell) ou 2.3 GHz Intel XEON® E5 2673 v4 (Broadwell)  

<br>

### <a name="ds-series"></a>Série DS  

**Recomendação de tamanho mais recente**: [Série DSv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160-250 <sup>1</sup>

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo de armazenamento temporário e em cache: IOPS / MBps (tamanho da cache em GiB) | Débito máximo de disco não colocado em cache: IOPS / MBps | NICs. Máx. / esperado de largura de banda de rede (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3.5 |7 |4 |4,000 / 32 (43) |3,200 / 32 |2 / 500 |
| Standard_DS2 |2 |7 |14 |8 |8,000 / 64 (86) |6,400 / 64 |2 / 1000 |
| Standard_DS3 |4 |14 |28 |16 |16,000 / 128 (172) |12,800 / 128 |4 / 2000 |
| Standard_DS4 |8 |28 |56 |32 |32,000 / 256 (344) |25,600 / 256 |8 / 4000 |

<sup>1</sup> família de VM pode ser executado em um dos seguinte da CPU: 2.2 GHz Intel Xeon® E5 2660 v2, 2,4 GHz Intel Xeon® E5 2673 v3 (Haswell) ou 2.3 GHz Intel XEON® E5 2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>Série DS - com otimização de memória  

**Recomendação de tamanho mais recente**: [Série DSv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160-250 <sup>1,2</sup>

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo de armazenamento temporário e em cache: IOPS / MBps (tamanho da cache em GiB) | Débito máximo de disco não colocado em cache: IOPS / MBps | NICs. Máx. / esperado de largura de banda de rede (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8,000 / 64 (72) |6,400 / 64 |2 / 1000 |
| Standard_DS12 |4 |28 |56 |16 |16,000 / 128 (144) |12,800 / 128 |4 / 2000 |
| Standard_DS13 |8 |56 |112 |32 |32,000 / 256 (288) |25,600 / 256 |8 / 4000 |
| Standard_DS14 |16 |112 |224 |64 |64,000 / 512 (576) |51,200 / 512 |8 / 8000 |

<sup>1</sup> o débito máximo do disco (IOPS ou MBps) possível com uma VM da série DS pode estar limitado pelo número, tamanho e repartição dos discos anexados.  Para obter detalhes, consulte [conceber o elevado desempenho](../articles/virtual-machines/windows/premium-storage-performance.md).   
<sup>2</sup> família de VM pode ser executado em um dos seguinte da CPU: 2.2 GHz Intel Xeon® E5 2660 v2, 2,4 GHz Intel Xeon® E5 2673 v3 (Haswell) ou 2.3 GHz Intel XEON® E5 2673 v4 (Broadwell)  

<br>
