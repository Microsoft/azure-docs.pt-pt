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
ms.openlocfilehash: c3b9422ebdb0a5d93f92644d9fea5d9c5ba9fa29
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47185765"
---
Com otimização de memória oferta de tamanhos VM uma alta taxa de memória de CPU que são ótimos para servidores de base de dados relacionais, caches médias a grandes e análise dentro da memória. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como armazenamento e débito de rede da largura de banda para cada tamanho neste agrupamento. 

* A série M oferece a contagem de vCPU mais alta (até 128 vCPUs) e a maior capacidade de memória (até 3,8 TiB) de qualquer VM na cloud.  É ideal para bases de dados muito grandes ou outras aplicações que tiram partido de contagens altas de vCPU e grandes quantidades de memória.

* Série Dv2, série G e as DSv2/GS são ideais para aplicações que exigem vCPUs mais rápidas, melhor desempenho de armazenamento temporário ou memórias.  Proporcionam uma combinação poderosa para inúmeras aplicações empresariais.


* A série DV2, o seguimento da série D original, apresenta uma CPU mais poderosa. A CPU da série Dv2 é cerca de 35% mais rápida do que a CPU da série D. Baseia-se a última geração 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz ou E5 2673 v4 (Broadwell) de 2.3 GHz processadores, com o Intel Turbo Boost Technology 2.0, podendo chegar aos 3,1 GHz. A série Dv2 tem as mesmas configurações de memória e disco da série D.

* O série Ev3 funcionalidades E5-2673 v4 (Broadwell) de 2.3 GHz processador numa configuração com hyper-threading, fornecendo uma proposta de valor melhor para cargas de trabalho de fins mais gerais e colocar a Ev3 em alinhamento com as VMs para fins gerais da maioria das outras clouds.  Memória foi expandida (de 7 GiB/vCPU de 8 GiB/Vcpus) enquanto os limites de disco e rede foram ajustados numa base por núcleo para alinhar com a mudança para o hyperthreading.  A Ev3 é a siga até os tamanhos VM de elevada da memória das famílias D/Dv2.

* A Computação do Azure oferece tamanhos de máquinas virtuais que são Isolados para um tipo de hardware específico e dedicados a um único cliente.  Estes tamanhos de máquinas virtuais são mais adequados para cargas de trabalho que exigem um elevado grau de isolamento de outros clientes para cargas de trabalho que envolvem elementos como os requisitos de conformidade e regulamentares.  Os clientes também podem optar por subdividir os recursos dessas máquinas de virtuais isoladas usando [suporte do Azure para máquinas virtuais aninhadas](https://azure.microsoft.com/en-us/blog/nested-virtualization-in-azure/).  Consulte as tabelas de famílias de máquinas virtuais, abaixo, para as opções de VM isoladas.

## <a name="esv3-series"></a>Série Esv3 

ACU: 160-190 <sup>1</sup>

O armazenamento Premium: suportado

A cache de armazenamento Premium: suportado

As instâncias da série ESv3 baseiam-se no processador 2.3 GHz Intel XEON ® E5-2673 v4 (Broadwell) e podem chegar aos 3.5GHz com o Intel Turbo Boost Technology 2.0 e utilizar o armazenamento premium. As instâncias da série Ev3 são ideais para aplicações empresariais com utilização intensiva da memória.


| Tamanho             | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo do armazenamento temporário e em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs. Máx. / esperado de largura de banda de rede (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4,000 / 32 (50)                                                       | 3,200 / 48                                | 2 / 1,000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8,000 / 64 (100)                                                      | 6,400 / 96                                | 2 / 2,000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16,000 / 128 (200)                                                    | 12,800 / 192                              | 4 / 4,000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32,000 / 256 (400)                                                    | 25,600 / 384                              | 8 / 8,000                                       |
| Standard_E20s_v3&nbsp;<sup>2</sup> | 20     | 160         | 320            | 32             | 40 000 / 320 (400)                                                    | 32.000 / 480                              | 8 / 10 000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64,000 / 512 (800)                                                    | 51,200 / 768                              | 8 / 16,000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128,000/1024 (1600)                                                   | 80,000 / 1200                             | 8 / 30,000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128,000/1024 (1600)                                                   | 80,000 / 1200                             | 8 / 30,000                             |


<sup>1</sup> as VMS da série Esv3 funcionalidade tecnologia Intel® Hyper-Threading.

<sup>2</sup> restrita tamanhos de núcleos disponíveis.

<sup>3</sup> instância está isolada do hardware dedicado de um único cliente.


## <a name="ev3-series"></a>Série Ev3 

ACU: 160-190 <sup>1</sup>

Armazenamento Premium: Não suportado

Cache de armazenamento Premium: Não suportado

As instâncias da série Ev3 são baseadas no processador de 2.3 GHz Intel XEON ® E5-2673 v4 (Broadwell) e pode chegar aos 3.5 GHz com o Intel Turbo Boost Technology 2.0. As instâncias da série Ev3 são ideais para aplicações empresariais com utilização intensiva da memória.

O armazenamento de discos de dados são cobrados em separado das máquinas virtuais. Para utilizar os discos de armazenamento premium, utilize os tamanhos ESv3. Os medidores de preços e de faturação dos tamanhos ESv3 são os mesmos do que os da série Ev3. 


| Tamanho            | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito do armazenamento temporário máximo: IOPS/MBps de Leitura/MBps de Escrita | NICs/Largura de banda da rede máximos |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3000/46/23                                               | 2 / 1,000                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6000/93/46                                               | 2 / 2,000                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12000/187/93                                             | 4 / 4,000                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24000/375/187                                            | 8 / 8,000                     |
| Standard_E20_v3 | 20        | 160         | 500            | 32             | 30000/469/234                                            | 8 / 10 000                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48000/750/375                                            | 8 / 16,000                 |
| Standard_E64_v3 | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30,000           |
| Standard_E64i_v3&nbsp;<sup>2,&nbsp;3</sup> | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30,000           |

<sup>1</sup> tecnologia Intel® Hyper-Threading de recursos das VMS da série Ev3.

<sup>2</sup> restrita tamanhos de núcleos disponíveis.

<sup>3</sup> instância está isolada do hardware dedicado de um único cliente.


## <a name="m-series"></a>Série M 

ACU: 160-180 <sup>1</sup>

O armazenamento Premium: suportado

A cache de armazenamento Premium: suportado

Acelerador de escrita: [suportados](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Tamanho            | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo do armazenamento temporário e em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs. Máx. / esperado de largura de banda de rede (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M8ms&nbsp;<sup>3</sup>    | 8  | 218.75 | 256  | 8  | 10 000 / 100 (793)  | 5.000 / 125 | 4 / 2.000 |
| Standard_M16ms&nbsp;<sup>3</sup>   | 16 | 437.5  | 512  | 16 | 20.000 / 200 (1,587) | 10,000 / 250 | 8 / 4000 |
| Standard_M32ts | 32 | 192    | 1,024 | 32 | 40 000 / 400 (3,174) | 20,000 / 500 | 8 / 8,000 |
| Standard_M32ls | 32 | 256    | 1,024 | 32 | 40 000 / 400 (3,174) | 20,000 / 500 | 8 / 8,000 |
| Standard_M32ms&nbsp;<sup>3</sup>   | 32 | 875    | 1,024 | 32 | 40 000 / 400 (3,174) | 20,000 / 500 | 8 / 8,000 |
| Standard_M64s  | 64 | 1,024   | 2.048 | 64 | 80 000 / 800 (6,348)| 40,000 / 1,000 | 8 / 16,000          |
| Standard_M64ls  | 64 | 512    | 2.048 | 64 | 80 000 / 800 (6,348) | 40,000 / 1,000 | 8 / 16,000 |
| Standard_M64ms&nbsp;<sup>3</sup>  | 64   | 1,792 | 2.048 | 64 | 80 000 / 800 (6,348)| 40,000 / 1,000 | 8 / 16,000          |
| Standard_M128s&nbsp;<sup>2,&nbsp;3</sup> | 128  | 2.048        | 4.096  | 64 | 160,000 / 1,600 (12,696) | 80,000 / 2,000                            | 8 / 30,000          |
| Standard_M128ms&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3,892  | 4.096 | 64 | 160,000 / 1,600 (12,696) | 80,000 / 2,000                            | 8 / 30,000          |
| Standard_M64   | 64  | 1,024 | 7,168  | 64 | 80 000 / 800 (1,228) | 40,000 / 1,000 | 8 / 16,000 |
| Standard_M64m  | 64  | 1,792 | 7,168  | 64 | 80 000 / 800 (1,228) | 40,000 / 1,000 | 8 / 16,000 |
| Standard_M128&nbsp;<sup>2  | 128 | 2.048 | 14,336 | 64 | 250.000 / 1,600 (2,456) | 80,000 / 2,000 | 8 / 32.000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3,892 | 14,336 | 64 | 250.000 / 1,600 (2,456) | 80,000 / 2,000 | 8 / 32.000 |



<sup>1</sup> funcionalidade das VMS da série M Intel® Hyper-Threading Technology

<sup>2</sup> mais do que 64 Vcpus exigem um destes SO convidados suportados: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 e Red Hat Enterprise Linux, CentOS 7.3 ou Oracle Linux 7.3 com LIS 4.2.1.

<sup>3</sup> restrita tamanhos de núcleos disponíveis.

<sup>4</sup> instância está isolada do hardware dedicado de um único cliente.
<br>

## <a name="gs-series"></a>Séries GS 

ACU: 180-240 <sup>1</sup>

O armazenamento Premium: suportado

A cache de armazenamento Premium: suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo do armazenamento temporário e em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs. Máx. / esperado de largura de banda de rede (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 |2 |28 |56 |8 |10,000 / 100 (264) |5,000 / 125 |2 / 2000 |
| Standard_GS2 |4 |56 |112 |16 |20,000 / 200 (528) |10,000 / 250 |2 / 4000 |
| Standard_GS3 |8 |112 |224 |32 |40,000 / 400 (1,056) |20,000 / 500 |4 / 8000 |
| Standard_GS4&nbsp;<sup>3</sup> |16 |224 |448 |64 |80,000 / 800 (2,112) |40,000 / 1,000 |8 / 16000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> |32 |448 |896 |64 |160,000 / 1,600 (4,224) |80,000 / 2,000 |8 / 20000 |

<sup>1</sup> o débito máximo do disco (IOPS ou MBps) possível com uma VM da série GS pode estar limitado pelo número, tamanho e repartição dos discos anexados. Para obter mais detalhes, veja [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho de máquinas virtuais do Azure](../articles/virtual-machines/windows/premium-storage.md). 

<sup>2</sup> instância está isolada do hardware dedicado de um único cliente.

<sup>3</sup> restrita tamanhos de núcleos disponíveis.

<br>

## <a name="g-series"></a>Série G

ACU: 180 - 240

Armazenamento Premium: Não suportado

Cache de armazenamento Premium: Não suportado

| Tamanho         | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Débito do armazenamento temporário máximo: IOPS/MBps de Leitura/MBps de Escrita | Máximo do disco de dados/débito: IOPS | NICs. Máx. / esperado de largura de banda de rede (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 8 / 8 x 500                       | 2 / 2000                     |
| Standard_G2  | 4         | 56          | 768            | 12000 / 187 / 93                                         | 16 / 16 x 500                       | 2 / 4000                     |
| Standard_G3  | 8         | 112         | 1,536          | 24000 / 375 / 187                                        | 32 / 32 x 500                     | 4 / 8000                |
| Standard_G4  | 16        | 224         | 3,072          | 48000 / 750 / 375                                        | 64 / 64 x 500                     | 8 / 16000          |
| Standard_G5&nbsp;<sup>1</sup> | 32        | 448         | 6,144          | 96000 / 1500 / 750                                       | 64 / 64 x 500                     | 8 / 20000           |

<sup>1</sup> instância está isolada do hardware dedicado de um único cliente.
<br>


## <a name="dsv2-series-11-15"></a>11-15 de série DSv2

ACU: 210-250 <sup>1</sup>

O armazenamento Premium: suportado

A cache de armazenamento Premium: suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo do armazenamento temporário e em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs. Máx. / esperado de largura de banda de rede (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8,000 / 64 (72) |6,400 / 96 |2 / 1500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16,000 / 128 (144) |12,800 / 192 |4 / 3000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32,000 / 256 (288) |25,600 / 384 |8 / 6000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64,000 / 512 (576) |51,200 / 768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80,000 / 640 (720) |64,000 / 960 |8 / 25000&nbsp;<sup>4</sup>


<sup>1</sup> o débito máximo do disco (IOPS ou MBps) possível com uma VM da série DSv2 pode estar limitado pelo número, tamanho e repartição dos discos anexados.  Para obter mais detalhes, veja [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho de máquinas virtuais do Azure](../articles/virtual-machines/windows/premium-storage.md).

<sup>2</sup> instância está isolada do hardware dedicado de um único cliente.

<sup>3</sup> restrita tamanhos de núcleos disponíveis.

<sup>4</sup> 25000 Mbps com redes aceleradas.

<br>

## <a name="dv2-series-11-15"></a>11-15 de série Dv2

ACU: 210 - 250

Armazenamento Premium: Não suportado

Cache de armazenamento Premium: Não suportado

| Tamanho              | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Débito do armazenamento temporário máximo: IOPS/MBps de Leitura/MBps de Escrita | Máximo do disco de dados/débito: IOPS | NICs. Máx. / esperado de largura de banda de rede (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1,000          | 60000 / 937 / 468                                        | 64 / 64x500                       | 8 / 25000&nbsp;<sup>2</sup> |

<sup>1</sup> instância está isolada do hardware dedicado de um único cliente. 

<sup>2</sup> 25000 Mbps com redes aceleradas.



<br>



