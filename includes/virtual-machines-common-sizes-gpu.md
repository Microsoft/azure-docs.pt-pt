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
ms.openlocfilehash: 26a5baf07ee31bdf155629139e12ef1977ddca1d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67457491"
---
VM com otimização de GPU são de tamanhos de máquinas virtuais especializadas disponíveis com GPUs NVIDIA únicas ou múltiplas. Estes tamanhos foram concebidos para cargas de trabalho de computação intensiva, grande intensidade de gráficos e visualização. Este artigo fornece informações sobre o número e tipo de GPUs, vCPUs, discos de dados e NICs. Largura de banda de armazenamento, débito e de rede também estão incluídos para cada tamanho neste agrupamento.

* **NC, a NCv2, NCv3** tamanhos estão otimizados para aplicações de computação e rede intensivas e algoritmos. Alguns exemplos são aplicações baseadas em CUDA e OpenCL e simulações, IA e aprendizagem aprofundada. A série NCv3 se concentra em alto desempenho cargas de trabalho computacionais do placa GPU da NVIDIA Tesla V100. A série NC utiliza a v3 de 2.60GHz Intel Xeon E5-2690 v3 (Haswell) de processador e a série NCv2 e a VMs da série NCv3 utilizam o Intel Xeon E5-2690 v4 (Broadwell) de processador.

* **ND e NDv2** a série ND se concentra em cenários de formação e inferência para aprendizagem profunda. Ele usa a NVIDIA Tesla P40 GPU e a Intel Xeon E5-2690 v4 (Broadwell) de processador. A série NDv2 utiliza o processador Intel Xeon Platinum 8168 (Skylake).

* **NV e NVv3** tamanhos são otimizados e concebidos para visualização remota, transmissão em fluxo, jogos, codificação e cenários VDI com arquiteturas, como OpenGL e DirectX.  Estas VMs são apoiadas por NVIDIA Tesla M60 GPU.

## <a name="nc-series"></a>Série NC

Armazenamento Premium:  Não suportado

Cache de armazenamento Premium:  Não suportado

As VMs da série NC têm tecnologia a [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) cartão e a Intel Xeon E5-2690 v3 (Haswell) de processador. Os utilizadores podem processar os dados mais rapidamente ao tirar partido das CUDA para aplicações de exploração de energia, simulações de falhas, ray composição rastreada, aprendizagem aprofundada e muito mais. A configuração NC24r fornece uma baixa latência, otimizado para cargas de trabalho de computação paralela fortemente interligadas de interface de rede de alto débito.

| Size | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória GPU: GiB | Discos de dados máximos | NICs máximos |
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

As VMs da série NCv2 têm a tecnologia [NVIDIA Tesla P100](https://www.nvidia.com/en-us/data-center/tesla-p100/) GPUs. Essas GPUs podem proporcionar 2x mais do que o desempenho computacional da série NC. Os clientes podem tirar partido destas GPUs atualizadas para cargas de trabalho HPC tradicionais, tais como modelação de reservatórios, DNA sequenciamento, análise de proteínas, simulações Monte Carlo e outras pessoas. Para além das GPUs, as VMs da série NCv2 são também com tecnologia Intel Xeon E5-2690 v4 (Broadwell) de CPUs.

A configuração de v2 NC24rs fornece uma baixa latência, otimizado para cargas de trabalho de computação paralela fortemente interligadas de interface de rede de alto débito.

> [!IMPORTANT]
> Para esta família de tamanho, a quota de vCPU (núcleo) na sua subscrição inicialmente é definida como 0 em cada região. [Pedir um aumento de quota de vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) para esta família num [região disponível](https://azure.microsoft.com/regions/services/).
>

| Size | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória GPU: GiB | Discos de dados máximos | Débito máximo de disco não colocado em cache: IOPS / MBps | NICs máximos |
| --- | --- | --- | --- | --- | --- | ---  | ---| --- |
| Standard_NC6s_v2 | 6 |112 | 736 | 1 | 16 | 12 | 20000/ 200 | 4 |
| Standard_NC12s_v2 | 12 |224 | 1474 | 2 | 32 | 24 | 40000 / 400 | 8 |
| Standard_NC24s_v2 | 24 |448 | 2948 | 4 | 64 | 32 | 80000 / 800 | 8 |
| Standard_NC24rs_v2* | 24 |448 | 2948 | 4 | 64 | 32 | 80000 / 800 | 8 |

1 GPU = um cartão de P100.

*Com capacidade RDMA

## <a name="ncv3-series"></a>Série NCv3

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

As VMs da série NCv3 têm a tecnologia [NVIDIA Tesla V100](https://www.nvidia.com/en-us/data-center/tesla-v100/) GPUs. Essas GPUs podem proporcionar 1,5 vezes o desempenho computacional da série NCv2. Os clientes podem tirar partido destas GPUs atualizadas para cargas de trabalho HPC tradicionais, tais como modelação de reservatórios, DNA sequenciamento, análise de proteínas, simulações Monte Carlo e outras pessoas. A configuração de v3 NC24rs fornece uma baixa latência, otimizado para cargas de trabalho de computação paralela fortemente interligadas de interface de rede de alto débito. Para além das GPUs, as VMs de série NCv3 são também com tecnologia Intel Xeon E5-2690 v4 (Broadwell) de CPUs.

> [!IMPORTANT]
> Para esta família de tamanho, a quota de vCPU (núcleo) na sua subscrição inicialmente é definida como 0 em cada região. [Pedir um aumento de quota de vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) para esta família num [região disponível](https://azure.microsoft.com/regions/services/).
>

| Size | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória GPU: GiB | Discos de dados máximos | Débito máximo de disco não colocado em cache: IOPS / MBps | NICs máximos |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 | 6 |112 | 736 | 1 | 16 | 12 | 20000 / 200 | 4 |
| Standard_NC12s_v3 | 12 |224 | 1474 | 2 | 32 | 24 | 40000 / 400 | 8 |
| Standard_NC24s_v3 | 24 |448 | 2948 | 4 | 64 | 32 | 80000 / 800 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 64 | 32 | 80000 / 800 | 8 |

1 GPU = um cartão de V100.

*Com capacidade RDMA

## <a name="ndv2-series-preview"></a>Série NDv2 (pré-visualização)

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

Infiniband: Não suportado

Máquinas de virtuais de série NDv2 é uma nova adição à família de GPU concebida para satisfazer as necessidades do HPC, IA e aprendizagem automática cargas de trabalho. Possui tecnologia 8 NVIDIA Tesla V100 NVLINK interligados GPUs e 40 Intel Xeon Platinum 8168 (Skylake) núcleos e 672 GiB de memória do sistema. A instância NDv2 fornece um excelente desempenho FP32 e FP64 para cargas de trabalho de HPC e IA através das arquiteturas Cuda, TensorFlow, Pytorch, Caffe e de outro tipo.

[Inscreva-se e obtenha acesso a essas máquinas durante a pré-visualização](https://aka.ms/ndv2signup).
<br>

| Size | vCPU | GPU | Memória | NICs (Máx) | (SSD) de armazenamento temporário GiB | Um máximo de Discos de dados | Débito máximo de disco não colocado em cache: IOPS / MBps | Largura de banda de rede de máx. | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND40s_v2 | 40 | 8 V100 (NVLink) | 672 GiB | 8 | 2948 | 32 | 80000 / 800 | 24000 Mbps |

## <a name="nd-series"></a>Série ND

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

As máquinas de virtuais de série ND são que uma novidade na família de GPU concebida para IA e aprendizagem profunda, cargas de trabalho. Eles oferecem excelente desempenho para formação e inferência. Instâncias de ND têm a tecnologia [NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPUs e Intel Xeon E5-2690 v4 (Broadwell) de CPUs. Estas instâncias oferecem excelente desempenho para precisão simples flutuante operações, para cargas de trabalho de IA que o Microsoft Cognitive Toolkit, TensorFlow, Caffe e outras estruturas. A série ND também oferece um tamanho de memória GPU muito maior (24 GB), que permite adaptar modelos de rede neuronal muito maiores. Como a série NC, a série ND oferece uma configuração com uma rede de baixa latência e alto débito secundária através de RDMA e conectividade InfiniBand para que possa executar tarefas de formação de larga escala que abrangem muitas GPUs.

> [!IMPORTANT]
> Para esta família de tamanho, a quota de vCPU (núcleo) por região na sua subscrição é inicialmente definida como 0. [Pedir um aumento de quota de vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) para esta família num [região disponível](https://azure.microsoft.com/regions/services/).
>

| Size | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória GPU: GiB | Discos de dados máximos | Débito máximo de disco não colocado em cache: IOPS / MBps | NICs máximos |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s | 6 |112 | 736 | 1 | 24 | 12 | 20000 / 200 | 4 |
| Standard_ND12s | 12 |224 | 1474 | 2 | 48 | 24 | 40000 / 400 | 8 | 
| Standard_ND24s | 24 |448 | 2948 | 4 | 96 | 32 | 80000 / 800 | 8 |
| Standard_ND24rs* | 24 |448 | 2948 | 4 | 96 | 32 | 80000 / 800 | 8 |

1 GPU = um cartão de P40.

*Com capacidade RDMA

## <a name="nv-series"></a>Série NV

Armazenamento Premium:  Não suportado

Cache de armazenamento Premium:  Não suportado

As máquinas de virtuais de série NV têm a tecnologia [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPUs e NVIDIA GRID tecnologia para o ambiente de trabalho aplicações aceleradas e áreas de trabalho virtuais onde os clientes podem visualizar os seus dados ou simulações. Os utilizadores são podem visualizar os seus fluxos de trabalho intensivas de gráficos nas instâncias NV para obter capacidades gráficas superiores e executar adicionalmente cargas de trabalho de precisão única, como codificação e composição. As VMs da série NV também têm a tecnologia Intel Xeon E5-2690 v3 (Haswell) de CPUs.

Cada GPU em instâncias de NV vem com uma licença de GRADE. Esta licença dá-lhe a flexibilidade para utilizar uma instância de NV como uma estação de trabalho virtual para um único utilizador ou 25 utilizadores em simultâneo podem ligar-se para a VM para um cenário de aplicação virtual.

| Size | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória GPU: GiB | Discos de dados máximos | NICs máximos | Estações de trabalho virtual | Aplicações virtuais |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |340 | 1 | 8 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = metade de uma placa M60.

## <a name="nvv3-series-preview-sup1sup"></a>Série NVv3 (pré-visualização) <sup>1</sup>

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

As máquinas de virtuais de série NVv3 têm a tecnologia [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPUs e NVIDIA GRID tecnologia com Intel E5-2690 v4 (Broadwell) de CPUs. Estas máquinas virtuais são direcionadas para aplicações de gráficos de acelerado de GPU e áreas de trabalho virtuais onde os clientes pretendem visualize os seus dados, simular resultados para ver, funciona em CAD ou conteúdo de composição e o stream. Além disto, estas máquinas virtuais podem executar cargas de trabalho de precisão individuais, como codificação e composição. Máquinas de virtuais de NVv3 suportam o Premium Storage e vêm com duas vezes a memória do sistema (RAM), em comparação com o seu predecessor série NV.  

Cada GPU em instâncias de NVv3 vem com uma licença de GRADE. Esta licença dá-lhe a flexibilidade para utilizar uma instância de NV como uma estação de trabalho virtual para um único utilizador ou 25 utilizadores em simultâneo podem ligar-se para a VM para um cenário de aplicação virtual.

| Size | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória GPU: GiB | Discos de dados máximos | Débito máximo de disco não colocado em cache: IOPS / MBps | NICs máximos | Estações de trabalho virtual | Aplicações virtuais | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV12s_v3 |12 |112 |320 | 1 | 8 | 12 | 20000 / 200 | 4 | 1 | 25 |
| Standard_NV24s_v3 |24 |224 |640 | 2 | 16 | 24 | 40000 / 400 | 8 | 2 | 50 |
| Standard_NV48s_v3 |48 |448 |1280 | 4 | 32 | 32 | 80000 / 800 | 8 | 4 | 100 |

1 GPU = metade de uma placa M60.

<sup>1</sup> VMs da série NVv3 funcionalidade Intel Hyper-Threading Technology
