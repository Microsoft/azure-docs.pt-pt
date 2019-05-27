---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 05/16/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 40857879826963f9a82cb5864b6980305c522679
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145738"
---
Esta seção fornece informações sobre as gerações anteriores de tamanhos de máquina virtual. Estes tamanhos ainda podem ser utilizados, mas há gerações mais recentes disponíveis. 

## <a name="f-series"></a>Série F

A série F tem por base o processador Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz, o qual pode atingir velocidades de relógio de 3,1 GHz com o Intel Turbo Boost Technology 2.0. Este é o mesmo desempenho de CPU da série Dv2 de VMs.  

As VMs da série F são uma excelente opção para cargas de trabalho que exigem CPUs mais rápidas, mas não precisam de mais memória ou armazenamento temporário por vCPU.  As cargas de trabalho, como análise, servidores de jogos, servidores Web e processamento de lotes, beneficiarão do valor da série F.

ACU: 210 - 250

Armazenamento Premium:  Não suportado

Cache de armazenamento Premium:  Não suportado

| Tamanho         | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Débito do armazenamento temporário máximo: IOPS / MBps de leitura / MBps de escrita | Discos de dados máximos / débito: IOPS | NICs. Máx. / esperado de largura de banda de rede (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 4 / 4x500                         | 2 / 750                 |
| Standard_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1500                     |
| Standard_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000           |

## <a name="fs-series-sup1sup"></a>Série FS <sup>1</sup>

A série Fs oferece todas as vantagens da série F, além do armazenamento Premium.

ACU: 210 - 250

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo de armazenamento temporário e em cache: IOPS / MBps (tamanho da cache em GiB) | Débito máximo de disco não colocado em cache: IOPS / MBps | NICs. Máx. / esperado de largura de banda de rede (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4,000 / 32 (12) |3,200 / 48 |2 / 750 |
| Standard_F2s |2 |4 |8 |8 |8,000 / 64 (24) |6,400 / 96 |2 / 1500 |
| Standard_F4s |4 |8 |16 |16 |16,000 / 128 (48) |12,800 / 192 |4 / 3000 |
| Standard_F8s |8 |16 |32 |32 |32,000 / 256 (96) |25,600 / 384 |8 / 6000 |
| Standard_F16s |16 |32 |64 |64 |64,000 / 512 (192) |51,200 / 768 |8 / 12000 |

MBps = 10^6 bytes por segundo e GiB = 1024^3 bytes.

<sup>1</sup> o débito máximo do disco (IOPS ou MBps) possível com uma VM da série Fs pode estar limitado pelo número, tamanho e repartição dos discos anexados.  Para obter detalhes, consulte [conceber o elevado desempenho](../articles/virtual-machines/windows/premium-storage-performance.md).  

## <a name="ls-series"></a>Série Ls

A série Ls oferece até 32 vCPUs, com o [processador Intel® Xeon® E5 v3 família](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). A série Ls tem o mesmo desempenho de CPU que a série G/GS e dispõe de 8 GiB de memória por vCPU.

A série Ls não suporta a criação de um cache local para aumentar o IOPS alcançável por discos de dados durável. O alto débito e IOPS de disco local torna as VMs da série Ls ideal para arquivos de NoSQL, como o Apache Cassandra e MongoDB que replique dados em várias VMs para alcançar a persistência no caso de falha de uma única VM.

ACU: 180-240

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Não suportado
 
| Tamanho          | vCPU | Memória (GiB) | Armazenamento temporário (GiB) | Discos de dados máximos | Débito máximo do armazenamento temporário (IOPS / MBps) | Max não colocado em cache de débito de disco (IOPS / MBps) | NICs. Máx. / esperado de largura de banda de rede (Mbps) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20,000 / 200 | 5,000 / 125  | 2 / 4,000  | 
| Standard_L8s   | 8  | 64  | 1,388 | 32 | 40,000 / 400 | 10,000 / 250 | 4 / 8,000  | 
| Standard_L16s  | 16 | 128 | 2,807 | 64 | 80,000 / 800 | 20,000 / 500 | 8 / 16,000 | 
| Standard_L32s&nbsp;<sup>1</sup> | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 40,000 / 1,000     | 8 / 20,000 | 

O débito máximo do disco possível com VMs da série Ls pode estar limitado pelo número, tamanho e repartição de quaisquer discos de anexados. Para obter detalhes, consulte [conceber o elevado desempenho](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>1</sup> instância está isolada do hardware dedicado de um único cliente.

## <a name="nvv2-series-preview"></a>Série NVv2 (pré-visualização)

**Recomendação de tamanho mais recente**: [Série NVv3 (pré-visualização)](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nvv2-series-preview)

As máquinas de virtuais de série NVv2 têm a tecnologia [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPUs e NVIDIA GRID tecnologia com Intel Broadwell CPUs. Estas máquinas virtuais são direcionadas para aplicações de gráficos de acelerado de GPU e áreas de trabalho virtuais onde os clientes pretendem visualize os seus dados, simular resultados para ver, funciona em CAD ou conteúdo de composição e o stream. Além disto, estas máquinas virtuais podem executar cargas de trabalho de precisão individuais, como codificação e composição. Máquinas de virtuais de NVv2 suportam o Premium Storage e vêm com duas vezes a memória do sistema (RAM), em comparação com o seu predecessor série NV.  

Cada GPU em instâncias de NVv2 vem com uma licença de GRADE. Esta licença dá-lhe a flexibilidade para utilizar uma instância de NV como uma estação de trabalho virtual para um único utilizador ou 25 utilizadores em simultâneo podem ligar-se para a VM para um cenário de aplicação virtual.

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória GPU: GiB | Discos de dados máximos | NICs máximos | Estações de trabalho virtual | Aplicações virtuais | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6s_v2 |6 |112 |320 | 1 | 8 | 12 | 4 | 1 | 25 |
| Standard_NV12s_v2 |12 |224 |640 | 2 | 16 | 24 | 8 | 2 | 50 |
| Standard_NV24s_v2 |24 |448 |1280 | 4 | 32 | 32 | 8 | 4 | 100 |

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0 a A4 que utiliza a CLI e o PowerShell

No modelo de implementação clássica, alguns nomes de tamanhos de VMs são ligeiramente diferentes, como na CLI e no PowerShell:

* Standard_A0 é ExtraSmall
* Standard_A1 é Small
* Standard_A2 é Medium
* Standard_A3 é Large
* Standard_A4 é ExtraLarge
