---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines-windows, virtual-machines-linux
author: laurenhughes
ms.service: multiple
ms.topic: include
ms.date: 08/15/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: a4746a945f1a89c34308a3bd968f6341e0e25ac5
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69541523"
---
Esta seção fornece informações sobre gerações mais antigas de tamanhos de máquina virtual. Esses tamanhos ainda têm suporte, mas não receberão capacidade adicional. Há tamanhos mais novos ou alternativos que estão geralmente disponíveis. Consulte [tamanhos de máquinas virtuais do Windows no Azure](../articles/virtual-machines/windows/sizes.md) ou [tamanhos para máquinas virtuais do Linux no Azure](../articles/virtual-machines/linux/sizes.md) para escolher os tamanhos de VM que melhor se ajustam à sua necessidade.  

Para obter mais informações sobre o redimensionamento de uma VM do Linux, consulte [redimensionar uma máquina virtual do Linux usando CLI do Azure](../articles/virtual-machines/linux/change-vm-size.md). Se você estiver usando VMs do Windows e preferir usar o PowerShell, consulte [redimensionar uma VM do Windows](../articles/virtual-machines/windows/resize-vm.md).  

<br>

### <a name="basic-a"></a>Básico A  

**Recomendação de tamanho mais recente**: [Série Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

Armazenamento Premium:  Não Suportada

Cache de armazenamento Premium:  Não Suportada

Os tamanhos do escalão básico destinam-se, principalmente, ao desenvolvimento de cargas de trabalho e outras aplicações que não requerem balanceamento de carga, dimensionamento automático ou máquinas virtuais que consomem muita memória.

|Tamanho – Tamanho\Nome | vCPU |Memória|NICs (Máx)|Tamanho máximo do disco temporário |Um máximo de discos de dados (1023 GB cada)|Um máximo de IOPS (300 por disco)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2| 20 GB|1|1 x 300|
|A1\Basic_A1|1|1,75 GB|2| 40 GB |2|2 x 300|
|A2\Basic_A2|2|3,5 GB|2| 60 GB|4|4 x 300|
|A3\Basic_A3|4|7 GB|2| 120 GB |8|8 x 300|
|A4\Basic_A4|8|14 GB|2| 240 GB |16|16 x 300|

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0 a A4 que utiliza a CLI e o PowerShell

No modelo de implementação clássica, alguns nomes de tamanhos de VMs são ligeiramente diferentes, como na CLI e no PowerShell:

* Standard_A0 é ExtraSmall
* Standard_A1 é Small
* Standard_A2 é Medium
* Standard_A3 é Large
* Standard_A4 é ExtraLarge

### <a name="a-series"></a>Série A  

**Recomendação de tamanho mais recente**: [Série Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 50-100

Armazenamento Premium:  Não Suportada

Cache de armazenamento Premium:  Não Suportada

| Size | vCPU | Memória: GiB | Armazenamento temporário (HDD): GiB | Discos de dados máximos | Taxa de transferência máxima do disco de dados: IOPS | Máximo de NICs/largura de banda de rede esperada (Mbps)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1</sup> |1 |0.768 |20 |1 |1x500 |2 / 100 |
| Standard_A1 |1 |1.75 |70 |2 |2x500 |2 / 500  |
| Standard_A2 |2 |3.5 |135 |4 |4x500 |2 / 500 |
| Standard_A3 |4 |7 |285 |8 |8x500 |2 / 1000 |
| Standard_A4 |8 |14 |605 |16 |16x500 |4 / 2000 |
| Standard_A5 |2 |14 |135 |4 |4x500 |2 / 500 |
| Standard_A6 |4 |28 |285 |8 |8x500 |2 / 1000 |
| Standard_A7 |8 |56 |605 |16 |16x500 |4 / 2000 |

<sup>1</sup> o tamanho a0 tem assinatura excessiva no hardware físico. Apenas para este tamanho específico, outras implementações de cliente podem afetar o desempenho da carga de trabalho em execução. O desempenho relativo é indicado abaixo como a linha de base esperada, sujeito a uma variabilidade aproximada de 15%.

<br>

### <a name="a-series---compute-intensive-instances"></a>Série A – Instâncias de computação intensiva  

**Recomendação de tamanho mais recente**: [Série Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 225

Armazenamento Premium:  Não Suportada

Cache de armazenamento Premium:  Não Suportada

Os tamanhos das séries A8-A11 e H também são conhecidos como *instâncias de computação intensiva*. O hardware que executa estes tamanhos foi concebido e otimizado para aplicações de computação e rede intensivas, incluindo aplicações, modelação e simulações de clusters de computação de alto desempenho (HPC). As séries A8-A11 utilizam o Intel Xeon E5-2670 @ 2,6 GHZ e a série H utiliza o Intel Xeon E5-2667 v3 @ 3,2 GHz.  

| Size | vCPU | Memória: GiB | Armazenamento temporário (HDD): GiB | Discos de dados máximos | Taxa de transferência máxima do disco de dados: IOPS | NICs máximos|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8&nbsp;<sup>1</sup> |8 |56 |382 |32 |32x500 |2 |
| Standard_A9&nbsp;<sup>1</sup> |16 |112 |382 |64 |64x500 |4 |
| Standard_A10 |8 |56 |382 |32 |32x500 |2  |
| Standard_A11 |16 |112 |382 |64 |64x500 |4 |

<sup>1</sup> Para aplicativos MPI, a rede de back-end RDMA dedicada é habilitada pela rede InfiniBand FDR, que fornece baixa latência e alta largura de banda.  

<br>

### <a name="d-series"></a>Série D  

**Recomendação de tamanho mais recente**: [Série Dv3](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160-250 <sup>1</sup>

Armazenamento Premium:  Não Suportada

Cache de armazenamento Premium:  Não Suportada

| Size         | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Taxa de transferência máxima do armazenamento temporário: IOPS/MBps de leitura/MBps de gravação | Máximo de discos de dados/taxa de transferência: IOPS | Máximo de NICs/largura de banda de rede esperada (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3.5         | 50             | 3000 / 46 / 23                                           | 4 / 4x500                         | 2 / 500                 |
| Standard_D2  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1000                     |
| Standard_D3  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 2000                     |
| Standard_D4  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 4000                     |

<sup></sup> uma família de VMs pode ser executada em uma das seguintes CPUs: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) ou 2,3 GHz Intel XEON® E5-2673 V4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>Série D-otimizado para memória  

**Recomendação de tamanho mais recente**: [Série Dv3](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160-250 <sup>1</sup>

Armazenamento Premium:  Não Suportada

Cache de armazenamento Premium:  Não Suportada

| Size         | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Taxa de transferência máxima do armazenamento temporário: IOPS/MBps de leitura/MBps de gravação | Máximo de discos de dados/taxa de transferência: IOPS | Máximo de NICs/largura de banda de rede esperada (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1000                     |
| Standard_D12 | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 2000                     |
| Standard_D13 | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 4000                     |
| Standard_D14 | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 8000                |

<sup></sup> uma família de VMs pode ser executada em uma das seguintes CPUs: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) ou 2,3 GHz Intel XEON® E5-2673 V4 (Broadwell)  

<br>

### <a name="ds-series"></a>Série DS  

**Recomendação de tamanho mais recente**: [Série DSv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160-250 <sup>1</sup>

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

| Size | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo de armazenamento temporário e em cache: IOPS/MBps (tamanho do cache em GiB) | Débito máximo de disco não colocado em cache: IOPS/MBps | Máximo de NICs/largura de banda de rede esperada (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3.5 |7 |4 |4,000 / 32 (43) |3,200 / 32 |2 / 500 |
| Standard_DS2 |2 |7 |14 |8 |8,000 / 64 (86) |6,400 / 64 |2 / 1000 |
| Standard_DS3 |4 |14 |28 |16 |16,000 / 128 (172) |12,800 / 128 |4 / 2000 |
| Standard_DS4 |8 |28 |56 |32 |32,000 / 256 (344) |25,600 / 256 |8 / 4000 |

<sup></sup> uma família de VMs pode ser executada em uma das seguintes CPUs: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) ou 2,3 GHz Intel XEON® E5-2673 V4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>Série DS-otimização de memória  

**Recomendação de tamanho mais recente**: [Série DSv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160-250 <sup>1, 2</sup>

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

| Size | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo de armazenamento temporário e em cache: IOPS/MBps (tamanho do cache em GiB) | Débito máximo de disco não colocado em cache: IOPS/MBps | Máximo de NICs/largura de banda de rede esperada (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8,000 / 64 (72) |6,400 / 64 |2 / 1000 |
| Standard_DS12 |4 |28 |56 |16 |16,000 / 128 (144) |12,800 / 128 |4 / 2000 |
| Standard_DS13 |8 |56 |112 |32 |32,000 / 256 (288) |25,600 / 256 |8 / 4000 |
| Standard_DS14 |16 |112 |224 |64 |64,000 / 512 (576) |51,200 / 512 |8 / 8000 |

<sup>1</sup> a taxa de transferência máxima possível do disco (IOPS ou Mbps) com uma VM da série DS pode ser limitada pelo número, tamanho e distribuição dos discos anexados.  Para obter detalhes, consulte [projetando para alto desempenho](../articles/virtual-machines/windows/premium-storage-performance.md).   
<sup>2</sup> a família de VMs pode ser executada em uma das seguintes CPUs: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) ou 2,3 GHz Intel XEON® E5-2673 V4 (Broadwell)  

<br>

### <a name="ls-series"></a>Série Ls

A série Ls oferece até 32 vCPUs, com o [processador Intel® Xeon® E5 v3 família](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). A série Ls tem o mesmo desempenho de CPU que a série G/GS e dispõe de 8 GiB de memória por vCPU.

A série ls não oferece suporte à criação de um cache local para aumentar o IOPS atingível por discos de dados duráveis. A alta taxa de transferência e o IOPS do disco local tornam as VMs da série ls ideais para repositórios NoSQL, como o Apache Cassandra e o MongoDB, que replicam dados em várias VMs para alcançar a persistência no caso de falha de uma única VM.

ACU: 180-240

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Não Suportada
 
| Size          | vCPU | Memória (GiB) | Armazenamento temporário (GiB) | Discos de dados máximos | Taxa de transferência máxima do armazenamento temporário (IOPS/MBps) | Taxa de transferência máxima do disco não armazenado em cache (IOPS/MBps) | Máximo de NICs/largura de banda de rede esperada (Mbps) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20000/200 | 5000 / 125  | 2 / 4000  | 
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000 / 400 | 10000/250 | 4 / 8000  | 
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000 / 500 | 8 / 16000 | 
| Standard_L32s&nbsp;<sup>1</sup> | 32   | 256  | 5630 | 64   | 160000 / 1600   | 40000 / 1000     | 8 / 20000 | 

A taxa de transferência máxima possível de disco com VMs da série ls pode ser limitada pelo número, tamanho e distribuição de quaisquer discos anexados. Para obter detalhes, consulte [projetando para alto desempenho](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>1</sup> a instância é isolada em hardware dedicado a um único cliente.

### <a name="gs-series"></a>Séries GS 

ACU: 180-240 <sup>1</sup>

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

| Size | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo de armazenamento temporário e em cache: IOPS/MBps (tamanho do cache em GiB) | Débito máximo de disco não colocado em cache: IOPS/MBps | Máximo de NICs/largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 |2 |28 |56 |8 |10,000 / 100 (264) |5,000 / 125 |2 / 2000 |
| Standard_GS2 |4 |56 |112 |16 |20,000 / 200 (528) |10,000 / 250 |2 / 4000 |
| Standard_GS3 |8 |112 |224 |32 |40,000 / 400 (1,056) |20,000 / 500 |4 / 8000 |
| Standard_GS4&nbsp;<sup>3</sup> |16 |224 |448 |64 |80,000 / 800 (2,112) |40,000 / 1,000 |8 / 16000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> |32 |448 |896 |64 |160,000 / 1,600 (4,224) |80,000 / 2,000 |8 / 20000 |

<sup>1</sup> a taxa de transferência máxima possível do disco (IOPS ou Mbps) com uma VM da série GS pode ser limitada pelo número, tamanho e distribuição dos discos anexados. Para obter detalhes, consulte [projetando para alto desempenho](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>2</sup> a instância é isolada em hardware dedicado a um único cliente.

<sup>3</sup> tamanhos de núcleos restritos disponíveis.

<br>

### <a name="g-series"></a>Série G

ACU: 180-240

Armazenamento Premium:  Não Suportada

Cache de armazenamento Premium:  Não Suportada

| Size         | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Taxa de transferência máxima do armazenamento temporário: IOPS/MBps de leitura/MBps de gravação | Máximo de discos de dados/taxa de transferência: IOPS | Máximo de NICs/largura de banda de rede esperada (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 8 / 8 x 500                       | 2 / 2000                     |
| Standard_G2  | 4         | 56          | 768            | 12000 / 187 / 93                                         | 16 / 16 x 500                       | 2 / 4000                     |
| Standard_G3  | 8         | 112         | 1,536          | 24000 / 375 / 187                                        | 32 / 32 x 500                     | 4 / 8000                |
| Standard_G4  | 16        | 224         | 3,072          | 48000 / 750 / 375                                        | 64 / 64 x 500                     | 8 / 16000          |
| Standard_G5&nbsp;<sup>1</sup> | 32        | 448         | 6,144          | 96000 / 1500 / 750                                       | 64 / 64 x 500                     | 8 / 20000           |

<sup>1</sup> a instância é isolada em hardware dedicado a um único cliente.
<br>
