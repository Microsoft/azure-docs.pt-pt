---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 06/11/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 0325fc8cabc43988fb27a307921977b9b487c123
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286167"
---
Os tamanhos de VM otimizadas para GPU são máquinas virtuais especializadas disponíveis com uma ou várias GPUs NVIDIA. Esses tamanhos são projetados para cargas de trabalho com uso intensivo de computação, gráficos e visualização. Este artigo fornece informações sobre o número e tipo de GPUs, vCPUs, discos de dados e NICs. A taxa de transferência de armazenamento e a largura de banda da rede também estão incluídas para cada tamanho neste agrupamento.

* **NC, NCv2,** tamanhos de NCv3 são otimizados para aplicativos e algoritmos com uso intensivo de computação e rede. Alguns exemplos são aplicativos baseados em CUDA e OpenCL e simulações, ia e aprendizado profundo. A série NCv3 está concentrada em cargas de trabalho de computação de alto desempenho com a GPU Tesla V100 da NVIDIA. A série NC usa o processador Intel Xeon E5-2690 v3 2,60 GHz (Haswell) e as VMs da série NCv2 e NCv3 usam o processador Intel Xeon E5-2690 V4 (Broadwell).

* **ND e NDv2** A série ND se concentra em cenários de treinamento e inferência para aprendizado profundo. Ele usa o NVIDIA Tesla P40 GPU e o processador Intel Xeon E5-2690 V4 (Broadwell). A série NDv2 usa o processador Intel Xeon Platinum 8168 (Skylake).

* Os tamanhos de **NV e NVv3** são otimizados e projetados para cenários de visualização remota, streaming, jogos, codificação e VDI usando estruturas como OpenGL e DirectX.  Essas VMs são apoiadas pela GPU NVIDIA Tesla M60.

## <a name="nc-series"></a>Série NC

Armazenamento Premium:  Não suportado

Cache de armazenamento Premium:  Não suportado

As VMs da série NC são alimentadas pela placa [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) e pelo processador Intel Xeon E5-2690 v3 (Haswell). Os usuários podem investigar os dados mais rapidamente aproveitando o CUDA para aplicativos de exploração de energia, simulações de falhas, renderização de Ray Traced, aprendizado profundo e muito mais. A configuração NC24r fornece uma interface de rede de alta taxa de transferência e baixa latência otimizada para cargas de trabalho de computação paralela firmemente acopladas.

| Size | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | NICs máximos |
| --- | --- | --- | --- | --- | --- | --- | ---- |
| Standard_NC6 |6 |56 | 340 | 1 | 12 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 24 | 48 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = metade de uma placa K80.

*Com capacidade RDMA

## <a name="ncv2-series"></a>Série NCv2

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

As VMs da série NCv2 são alimentadas por GPUs [NVIDIA Tesla P100](https://www.nvidia.com/en-us/data-center/tesla-p100/) . Essas GPUs podem fornecer mais de duas vezes o desempenho computacional da série NC. Os clientes podem aproveitar essas GPUs atualizadas para cargas de trabalho de HPC tradicionais, como modelagem de reservatório, sequenciamento de DNA, análise de proteína, simulações de Carlo monte e outros. Além das GPUs, as VMs da série NCv2 também são alimentadas por CPUs Intel Xeon E5-2690 V4 (Broadwell).

A configuração do NC24rs v2 fornece uma interface de rede de alta taxa de transferência e baixa latência otimizada para cargas de trabalho de computação paralela firmemente acopladas.

> [!IMPORTANT]
> Para essa família de tamanho, a cota de vCPU (núcleo) em sua assinatura é inicialmente definida como 0 em cada região. [Solicite um aumento de cota de vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) para essa família em uma [região disponível](https://azure.microsoft.com/regions/services/).
>

| Size | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Débito máximo de disco não colocado em cache: IOPS/MBps | NICs máximos |
| --- | --- | --- | --- | --- | --- | ---  | ---| --- |
| Standard_NC6s_v2 | 6 |112 | 736 | 1 | 16 | 12 | 20000/ 200 | 4 |
| Standard_NC12s_v2 | 12 |224 | 1474 | 2 | 32 | 24 | 40000 / 400 | 8 |
| Standard_NC24s_v2 | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v2* | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = um cartão P100.

*Com capacidade RDMA

## <a name="ncv3-series"></a>Série NCv3

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

As VMs da série NCv3 são alimentadas por GPUs [NVIDIA Tesla V100](https://www.nvidia.com/en-us/data-center/tesla-v100/) . Essas GPUs podem fornecer um desempenho computacional de 1,5 x da série NCv2. Os clientes podem aproveitar essas GPUs atualizadas para cargas de trabalho de HPC tradicionais, como modelagem de reservatório, sequenciamento de DNA, análise de proteína, simulações de Carlo monte e outros. A configuração do NC24rs v3 fornece uma interface de rede de alta taxa de transferência e baixa latência otimizada para cargas de trabalho de computação paralela firmemente acopladas. Além das GPUs, as VMs da série NCv3 também são alimentadas por CPUs Intel Xeon E5-2690 V4 (Broadwell).

> [!IMPORTANT]
> Para essa família de tamanho, a cota de vCPU (núcleo) em sua assinatura é inicialmente definida como 0 em cada região. [Solicite um aumento de cota de vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) para essa família em uma [região disponível](https://azure.microsoft.com/regions/services/).
>

| Size | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Débito máximo de disco não colocado em cache: IOPS/MBps | NICs máximos |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 | 6 |112 | 736 | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v3 | 12 |224 | 1474 | 2 | 32 | 24 | 40000 / 400 | 8 |
| Standard_NC24s_v3 | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = um cartão V100.

*Com capacidade RDMA

## <a name="ndv2-series-preview"></a>Série NDv2 (versão prévia)

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

InfiniBand Não suportado

A máquina virtual NDv2-Series é uma nova adição à família GPU projetada para as necessidades das cargas de trabalho HPC, AI e Machine Learning. Ele é alimentado por oito GPUs NVIDIA Tesla V100 NVLINK interconectadas e 40 núcleos Intel Xeon Platinum 8168 (Skylake) e 672 GiB de memória do sistema. A instância NDv2 fornece um excelente desempenho FP32 e FP64 para cargas de trabalho de HPC e IA através das arquiteturas Cuda, TensorFlow, Pytorch, Caffe e de outro tipo.

[Inscreva-se e obtenha acesso a esses computadores durante a visualização](https://aka.ms/ndv2signup).
<br>

| Size | vCPU | GPU | Memória | NICs (Máx) | Armazenamento temporário (SSD) GiB | Um máximo de discos de dados | Débito máximo de disco não colocado em cache: IOPS/MBps | Largura de banda máxima da rede | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND40s_v2 | 40 | 8 V100 (NVLink) | 672 GiB | 8 | 2948 | 32 | 80000/800 | 24000 Mbps |

## <a name="nd-series"></a>Série ND

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

As máquinas virtuais da série ND são uma nova adição à família de GPU projetada para ia e cargas de trabalho de aprendizado profundo. Eles oferecem um desempenho excelente para treinamento e inferência. As instâncias do ND são alimentadas por [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPUs e CPUs Intel Xeon E5-2690 V4 (Broadwell). Essas instâncias fornecem um desempenho excelente para operações de ponto flutuante de precisão simples, para cargas de trabalho de ia que utilizam Microsoft Cognitive Toolkit, TensorFlow, Caffe e outras estruturas. A série ND também oferece um tamanho de memória GPU muito maior (24 GB), que permite adaptar modelos de rede neuronal muito maiores. Como a série NC, a série ND oferece uma configuração com uma rede secundária de baixa latência e alta taxa de transferência por meio de RDMA e conectividade InfiniBand para que você possa executar trabalhos de treinamento em grande escala abrangendo muitas GPUs.

> [!IMPORTANT]
> Para essa família de tamanho, a cota de vCPU (núcleo) por região em sua assinatura é definida inicialmente como 0. [Solicite um aumento de cota de vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) para essa família em uma [região disponível](https://azure.microsoft.com/regions/services/).
>

| Size | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Débito máximo de disco não colocado em cache: IOPS/MBps | NICs máximos |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s | 6 |112 | 736 | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s | 12 |224 | 1474 | 2 | 48 | 24 | 40000 / 400 | 8 | 
| Standard_ND24s | 24 |448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |
| Standard_ND24rs* | 24 |448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 GPU = um cartão P40.

*Com capacidade RDMA

## <a name="nv-series"></a>Série NV

Armazenamento Premium:  Não suportado

Cache de armazenamento Premium:  Não suportado

As máquinas virtuais da série NV são alimentadas por GPUs [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) e pela tecnologia NVIDIA Grid para aplicativos acelerados para desktop e áreas de trabalho virtuais em que os clientes podem visualizar seus dados ou simulações. Os usuários podem visualizar seus fluxos de trabalho com uso intensivo de gráficos nas instâncias NV para obter recursos gráficos superiores e, além disso, executar cargas de trabalho de precisão única, como codificação e renderização. As VMs da série NV também são alimentadas por CPUs do Intel Xeon E5-2690 v3 (Haswell).

Cada GPU em instâncias NV vem com uma licença de grade. Essa licença oferece a flexibilidade de usar uma instância de NV como uma estação de trabalho virtual para um único usuário, ou 25 usuários simultâneos podem se conectar à VM para um cenário de aplicativo virtual.

| Size | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | NICs máximos | Estações de trabalho virtuais | Aplicativos virtuais |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |340 | 1 | 8 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = metade de uma placa M60.

## <a name="nvv3-series--sup1sup"></a>Série NVv3 <sup>1</sup>

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

As máquinas virtuais da série NVv3 são alimentadas pelas GPUs [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) e pela tecnologia NVIDIA Grid com as CPUs Intel E5-2690 V4 (Broadwell). Essas máquinas virtuais são destinadas a aplicativos gráficos acelerados à GPU e áreas de trabalho virtuais em que os clientes desejam Visualizar seus dados, simular resultados para exibir, trabalhar em CAD ou renderizar e transmitir conteúdo. Além disto, estas máquinas virtuais podem executar cargas de trabalho de precisão individuais, como codificação e composição. As máquinas virtuais NVv3 dão suporte ao armazenamento Premium e vêm com duas vezes a RAM (memória do sistema) quando comparadas com sua série NV do antecessor.  

Cada GPU em instâncias de NVv3 vem com uma licença de grade. Essa licença oferece a flexibilidade de usar uma instância de NV como uma estação de trabalho virtual para um único usuário, ou 25 usuários simultâneos podem se conectar à VM para um cenário de aplicativo virtual.

| Size | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Débito máximo de disco não colocado em cache: IOPS/MBps | NICs máximos | Estações de trabalho virtuais | Aplicativos virtuais | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV12s_v3 |12 |112 |320 | 1 | 8 | 12 | 20000/200 | 4 | 1 | 25 |
| Standard_NV24s_v3 |24 |224 |640 | 2 | 16 | 24 | 40000 / 400 | 8 | 2 | 50 |
| Standard_NV48s_v3 |48 |448 |1280 | 4 | 32 | 32 | 80000/800 | 8 | 4 | 100 |

1 GPU = metade de uma placa M60.

<sup>1</sup> o recurso de VMs NVv3-Series Intel Hyper-Threading Technology
