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
ms.openlocfilehash: 8d90d71b8d29d26f09ef617ddd56ce91eb4e5e2e
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69541524"
---
Esta seção fornece informações sobre gerações anteriores de tamanhos de máquinas virtuais. Esses tamanhos ainda podem ser usados, mas há gerações mais recentes disponíveis. 

## <a name="f-series"></a>Série F

A série F tem por base o processador Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz, o qual pode atingir velocidades de relógio de 3,1 GHz com o Intel Turbo Boost Technology 2.0. Este é o mesmo desempenho de CPU da série Dv2 de VMs.  

As VMs da série F são uma excelente opção para cargas de trabalho que exigem CPUs mais rápidas, mas não precisam de mais memória ou armazenamento temporário por vCPU.  As cargas de trabalho, como análise, servidores de jogos, servidores Web e processamento de lotes, beneficiarão do valor da série F.

ACU: 210-250

Armazenamento Premium:  Não Suportada

Cache de armazenamento Premium:  Não Suportada

| Size         | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Taxa de transferência máxima do armazenamento temporário: IOPS/MBps de leitura/MBps de gravação | Máximo de discos de dados/taxa de transferência: IOPS | Máximo de NICs/largura de banda de rede esperada (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 4 / 4x500                         | 2 / 750                 |
| Standard_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1500                     |
| Standard_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000           |

## <a name="fs-series-sup1sup"></a>Série FS <sup>1</sup>

A série Fs oferece todas as vantagens da série F, além do armazenamento Premium.

ACU: 210-250

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

| Size | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo de armazenamento temporário e em cache: IOPS/MBps (tamanho do cache em GiB) | Débito máximo de disco não colocado em cache: IOPS/MBps | Máximo de NICs/largura de banda de rede esperada (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4000/32 (12) |3200 / 48 |2 / 750 |
| Standard_F2s |2 |4 |8 |8 |8000/64 (24) |6400 / 96 |2 / 1500 |
| Standard_F4s |4 |8 |16 |16 |16000 / 128 (48) |12800 / 192 |4 / 3000 |
| Standard_F8s |8 |16 |32 |32 |32000/256 (96) |25600 / 384 |8 / 6000 |
| Standard_F16s |16 |32 |64 |64 |64000 / 512 (192) |51200 / 768 |8 / 12000 |

MBps = 10^6 bytes por segundo e GiB = 1024^3 bytes.

<sup>1</sup> a taxa de transferência máxima possível do disco (IOPS ou Mbps) com uma VM da série FS pode ser limitada pelo número, tamanho e distribuição dos discos anexados.  Para obter detalhes, consulte [projetando para alto desempenho](../articles/virtual-machines/windows/premium-storage-performance.md).  

## <a name="nvv2-series"></a>Série NVv2

**Recomendação de tamanho mais recente**: [Série NVv3](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nvv3-series-preview-1)

As máquinas virtuais da série NVv2 são alimentadas pelas GPUs [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) e pela tecnologia NVIDIA Grid com CPUs Intel Broadwell. Essas máquinas virtuais são destinadas a aplicativos gráficos acelerados à GPU e áreas de trabalho virtuais em que os clientes desejam Visualizar seus dados, simular resultados para exibir, trabalhar em CAD ou renderizar e transmitir conteúdo. Além disto, estas máquinas virtuais podem executar cargas de trabalho de precisão individuais, como codificação e composição. As máquinas virtuais NVv2 dão suporte ao armazenamento Premium e vêm com duas vezes a RAM (memória do sistema) quando comparadas com sua série NV do antecessor.  

Cada GPU em instâncias de NVv2 vem com uma licença de grade. Essa licença oferece a flexibilidade de usar uma instância de NV como uma estação de trabalho virtual para um único usuário, ou 25 usuários simultâneos podem se conectar à VM para um cenário de aplicativo virtual.

| Size | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | NICs máximos | Estações de trabalho virtuais | Aplicativos virtuais | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6s_v2 |6 |112 |320 | 1 | 8 | 12 | 4 | 1 | 25 |
| Standard_NV12s_v2 |12 |224 |640 | 2 | 16 | 24 | 8 | 2 | 50 |
| Standard_NV24s_v2 |24 |448 |1280 | 4 | 32 | 32 | 8 | 4 | 100 |
