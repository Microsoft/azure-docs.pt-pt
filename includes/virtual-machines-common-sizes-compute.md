---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: f1f02f8b5488609a0c69a6d335c96d3cc9266c71
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170349"
---
<!-- F-series, Fs-series* -->

Computação otimizados tamanhos de VM de ter um rácio de CPU / memória elevado e são ideais para servidores web com tráfego médio, aplicações de rede, processos em lote e servidores de aplicações. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como armazenamento e débito de rede da largura de banda para cada tamanho neste agrupamento.

Série Fsv2 baseia-se no processador Intel® Xeon® Platinum 8168, apresentando uma constante todos os principais Turbo velocidade de relógio de 3.4GHz e uma frequência máxima turbo de núcleo único de 3,7 GHz. Instruções do Intel® AVX-512, que são novas nos processadores Intel de dimensionável, irão fornecer até um 2x no aumento do desempenho para cargas de trabalho de processamento de vetor em único e de dupla precisão operações de vírgula flutuante. Em outras palavras, elas são realmente rápidas para qualquer carga de trabalho computacional. 

Por um preço de lista por hora mais baixo, a série Fsv2 é o melhor valor de desempenho por preço no portefólio do Azure com base na unidade do Azure de computação (ACU) por vCPU.

## <a name="fsv2-series-sup1sup"></a>Série Fsv2 <sup>1</sup>

ACU: 195 - 210

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

| Tamanho             | da vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo de armazenamento temporário e em cache: IOPS / MBps (tamanho da cache em GiB) | Débito máximo de disco não colocado em cache: IOPS / MBps | NICs. Máx. / esperado de largura de banda de rede (Mbps) |
|------------------|--------|-------------|----------------|----------------|--------------------------|--------------------------|-------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000 / 31 (32)           | 3200 / 47                | 2 / 875                 |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000 / 63 (64)           | 6400 / 95                | 2 / 1,750               |
| Standard_F8s_v2  | 8      | 16          | 64             | 16             | 16000 / 127 (128)        | 12800 / 190              | 4 / 3,500               |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000 / 255 (256)        | 25600 / 380              | 4 / 7,000               |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000 / 512 (512)        | 51200 / 750              | 8 / 14,000              |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000 / 1024 (1024)     | 80000 / 1100             | 8 / 28,000              |
| Standard_F72s_v2<sup>2, 3</sup> | 72 | 144 | 576         | 32             | 144000 / 1152 (1520)     | 80000 / 1100             | 8 / 30,000              |


<sup>1</sup> as VMS da série Fsv2 funcionalidade Intel® Hyper-Threading Technology

<sup>2</sup> mais do que 64 Vcpus exigem um destes SO convidados suportados: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 e Red Hat Enterprise Linux, CentOS 7.3 ou Oracle Linux 7.3 com LIS 4.2.1

<sup>3</sup> instância está isolada do hardware dedicado de um único cliente.