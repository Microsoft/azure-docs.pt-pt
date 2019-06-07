---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 04/26/2019
ms.author: azcspmt;jonbeck;cynthn;amverma
ms.custom: include file
ms.openlocfilehash: 9b4bdee19c883252e7de140ac7b19babd43d1df8
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755201"
---
Máquinas de virtuais de série H do Azure (VMs) foram concebidas para fornecer desempenho de classe de liderança, escalabilidade MPI e a eficiência para uma variedade de cargas de trabalho do mundo real HPC de custo.

As VMs da série HB estão otimizadas para aplicações orientadas pela largura de banda de memória, como dinâmica fluída, análise de elementos finitos explícitos e modelos de previsão meteorológica. As VMs da série HB apresentam 60 núcleos de processador AMD EPYC 7551, 4 GB de RAM por núcleo de CPU e nenhum hiperthreading. A plataforma AMD EPYC oferece mais de 260 GB/segundo de largura de banda de memória.

As VMs da série HC estão otimizadas para aplicativos orientados por densa computação, como análises implícita de elementos finitos, molecular dynamics e supramolecular computacional. As VMs da série HC apresentam 44 núcleos de processador Intel Xeon Platinum 8168, 8 GB de RAM por núcleo de CPU e nenhum hiperthreading. A plataforma Intel Xeon Platinum oferece suporte a ecossistema diversificado da Intel das ferramentas de software, como a biblioteca de Kernel do Intel matemática.

HB e HC VMs de recursos de 100 Gb/seg Mellanox EDR InfiniBand num fat sem bloqueio de árvore configuração para um desempenho consistente RDMA. HB e HC VMs suportam controladores de Mellanox/OFED padrão, de modo a que todos os tipos MPI e versões, bem como verbos RDMA, também são oferecidos.

VMs de série H são otimizadas para aplicativos orientados por altas frequências de CPU ou memória grande por principais requisitos. Processador de v3 Intel Xeon E5 2667 do série H VMs funcionalidade 8 ou 16 núcleos, 7 ou 14 GB de RAM por núcleo de CPU e não hyperthreading. Série H funcionalidades 56 Gb/seg Mellanox FDR InfiniBand num fat sem bloqueio de árvore configuração para um desempenho consistente RDMA. VMs de série H oferecer suporte a MPI Intel 5.x e MS-MPI.

## <a name="hb-series"></a>Série HB

ACU: 199-216

Armazenamento Premium: Suportadas

Cache de armazenamento Premium: Suportadas

| Tamanho | vCPU | Processador | Memória (GB) | Largura de banda de memória GB/s | Frequência de base da CPU (GHz) | Frequência de todos os núcleos (GHz, pico) | Frequência de núcleo único (GHz, pico) | Desempenho de RDMA (GB/s) | Suporte MPI | Armazenamento temporário (GB) | Discos de dados máximos | NICs de Ethernet máx. |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | Todos | 700 | 4 | 1 |

<br>

## <a name="hc-series"></a>Série HC

ACU: 297-315

Armazenamento Premium: Suportadas

Cache de armazenamento Premium: Suportadas


| Tamanho | vCPU | Processador | Memória (GB) | Largura de banda de memória GB/s | Frequência de base da CPU (GHz) | Frequência de todos os núcleos (GHz, pico) | Frequência de núcleo único (GHz, pico) | Desempenho de RDMA (GB/s) | Suporte MPI | Armazenamento temporário (GB) | Discos de dados máximos | NICs de Ethernet máx. |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Todos | 700 | 4 | 1 |


<br>

## <a name="h-series"></a>Série H

ACU: 290-300

Armazenamento Premium:  Não suportado

Cache de armazenamento Premium:  Não suportado

| Tamanho | vCPU | Processador | Memória (GB) | Largura de banda de memória GB/s | Frequência de base da CPU (GHz) | Frequência de todos os núcleos (GHz, pico) | Frequência de núcleo único (GHz, pico) | Desempenho de RDMA (GB/s) | Suporte MPI | Armazenamento temporário (GB) | Discos de dados máximos | NICs de Ethernet máx. |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 | 8 | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16 | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 |  - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H8m | 8 | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> para aplicativos MPI, a rede de back-end RDMA dedicada é ativada pela rede FDR InfiniBand.

<br>
