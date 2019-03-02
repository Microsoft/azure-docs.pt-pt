---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 11/14/2018
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 8967e4b2cce052044abf2e2b0925f162478428d1
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57203911"
---
VM com otimização de GPU são de tamanhos de máquinas virtuais especializadas disponíveis com GPUs NVIDIA únicas ou múltiplas. Estes tamanhos foram concebidos para cargas de trabalho de computação intensiva, grande intensidade de gráficos e visualização. Este artigo fornece informações sobre o número e tipo de GPUs, vCPUs, discos de dados e NICs. Largura de banda de armazenamento, débito e de rede também estão incluídos para cada tamanho neste agrupamento. 

* **NC, NCv2, NCv3, ND e NDv2** tamanhos estão otimizados para aplicações de computação e rede intensivas e algoritmos. Alguns exemplos são aplicações baseadas em CUDA e OpenCL e simulações, IA e aprendizagem aprofundada. A série NCv3 se concentra em alto desempenho cargas de trabalho computacionais do placa GPU da NVIDIA Tesla V100.  A série ND destina-se a cenários de formação e inferência para aprendizagem aprofundada. Utiliza o GPU NVIDIA Tesla P40.

* O **série NC** apresenta um processador de 2.60 GHz Intel Xeon® E5-2690 v3.

* O **NCSv3**, **NCSv2**, e **ND** tamanhos apresentam um processador de 2.60 GHz Intel Xeon® E5-2690 v4.
                      
* **NV e NVv2** tamanhos são otimizados e concebidos para visualização remota, transmissão em fluxo, jogos, codificação e cenários VDI com arquiteturas, como OpenGL e DirectX.  Estas VMs são apoiadas por NVIDIA Tesla M60 GPU.


## <a name="nc-series"></a>Série NC

Armazenamento Premium:  Não suportado

Cache de armazenamento Premium:  Não suportado

As VMs da série NC têm tecnologia a [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) cartão. Os utilizadores podem processar os dados mais rapidamente ao tirar partido das CUDA para aplicações de exploração de energia, simulações de falhas, ray composição rastreada, aprendizagem aprofundada e muito mais. A configuração NC24r fornece uma baixa latência, otimizado para cargas de trabalho de computação paralela fortemente interligadas de interface de rede de alto débito.


| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória GPU: GiB | Discos de dados máximos | NICs máximos |
| --- | --- | --- | --- | --- | --- | --- | ---- |
| Standard_NC6 |6 |56 | 340 | 1 | 8 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 16 | 48 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 | 32 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 32 | 64 | 4 |

1 GPU = metade de uma placa K80.

*Com capacidade RDMA

## <a name="ncv2-series"></a>Série NCv2

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

As VMs da série NCv2 têm a tecnologia [NVIDIA Tesla P100](http://images.nvidia.com/content/tesla/pdf/nvidia-tesla-p100-datasheet.pdf) GPUs. Essas GPUs podem proporcionar 2x mais do que o desempenho computacional da série NC. Os clientes podem tirar partido destas GPUs atualizadas para cargas de trabalho HPC tradicionais, tais como modelação de reservatórios, DNA sequenciamento, análise de proteínas, simulações Monte Carlo e outras pessoas. A configuração de v2 NC24rs fornece uma baixa latência, otimizado para cargas de trabalho de computação paralela fortemente interligadas de interface de rede de alto débito.

> [!IMPORTANT]
> Para esta família de tamanho, a quota de vCPU (núcleo) na sua subscrição inicialmente é definida como 0 em cada região. [Pedir um aumento de quota de vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) para esta família num [região disponível](https://azure.microsoft.com/regions/services/).
>

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória GPU: GiB | Discos de dados máximos | NICs máximos |
| --- | --- | --- | --- | --- | --- | ---  | --- |
| Standard_NC6s_v2 |6 |112 | 736 | 1 | 16 | 12 | 4 |
| Standard_NC12s_v2 |12 |224 | 1474 | 2 | 32 | 24 | 8 |
| Standard_NC24s_v2 |24 |448 | 2948 | 4 | 64 | 32 | 8 |
| Standard_NC24rs_v2* |24 |448 | 2948 | 4 | 64 | 32 | 8 |

1 GPU = um cartão de P100.

*Com capacidade RDMA

## <a name="ncv3-series"></a>Série NCv3

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

As VMs da série NCv3 têm a tecnologia [NVIDIA Tesla V100](http://www.nvidia.com/content/PDF/Volta-Datasheet.pdf) GPUs. Essas GPUs podem proporcionar 1,5 vezes o desempenho computacional da série NCv2. Os clientes podem tirar partido destas GPUs atualizadas para cargas de trabalho HPC tradicionais, tais como modelação de reservatórios, DNA sequenciamento, análise de proteínas, simulações Monte Carlo e outras pessoas. A configuração de v3 NC24rs fornece uma baixa latência, otimizado para cargas de trabalho de computação paralela fortemente interligadas de interface de rede de alto débito.

> [!IMPORTANT]
> Para esta família de tamanho, a quota de vCPU (núcleo) na sua subscrição inicialmente é definida como 0 em cada região. [Pedir um aumento de quota de vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) para esta família num [região disponível](https://azure.microsoft.com/regions/services/).
>

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória GPU: GiB | Discos de dados máximos | NICs máximos |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 |6 |112 | 736 | 1 | 16 | 12 | 4 |
| Standard_NC12s_v3 |12 |224 | 1474 | 2 | 32 | 24 | 8 |
| Standard_NC24s_v3 |24 |448 | 2948 | 4 | 64 | 32 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 64 | 32 | 8 |

1 GPU = um cartão de V100.

*Com capacidade RDMA

## <a name="ndv2-series-preview"></a>Série NDv2 (pré-visualização)


Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

Infiniband: Não suportado


Máquinas de virtuais de série NDv2 é uma nova adição à família de GPU concebida para satisfazer as necessidades do HPC, IA e aprendizagem automática cargas de trabalho. Tem tecnologia de 8 GPUs NVIDIA Tesla V100 NVLINK interligadas, 40 núcleos Intel Skylake e 672 GiB de memória do sistema. A instância NDv2 fornece um excelente desempenho FP32 e FP64 para cargas de trabalho de HPC e IA através das arquiteturas Cuda, TensorFlow, Pytorch, Caffe e de outro tipo.

[Inscreva-se e obtenha acesso a essas máquinas durante a pré-visualização](https://aka.ms/ndv2signup).
<br>


| Tamanho              | vCPU | GPU              | Memória  | NICs (Máx) | Um máximo de Tamanho do disco           | Um máximo de discos de dados (1023 GB cada) | Largura de banda de rede de máx. | 
|-------------------|------|------------------|---------|------------|--------------------------|--------------------------------|-----------------------|
| Standard_ND40s_v2 | 40   | 8 V100 (NVLink) | 672 GiB | 8          | Temporary 1344 / 2948XIO | 32                             | 24.000 Mbps           |

## <a name="nd-series"></a>Série ND

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

As máquinas de virtuais de série ND são que uma novidade na família de GPU concebida para IA e aprendizagem profunda, cargas de trabalho. Eles oferecem excelente desempenho para formação e inferência. Instâncias de ND têm a tecnologia [NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPUs. Estas instâncias oferecem excelente desempenho para precisão simples flutuante operações, para cargas de trabalho de IA que o Microsoft Cognitive Toolkit, TensorFlow, Caffe e outras estruturas. A série ND também oferece um tamanho de memória GPU muito maior (24 GB), que permite adaptar modelos de rede neuronal muito maiores. Como a série NC, a série ND oferece uma configuração com uma rede de baixa latência e alto débito secundária através de RDMA e conectividade InfiniBand para que possa executar tarefas de formação de larga escala que abrangem muitas GPUs.

> [!IMPORTANT]
> Para esta família de tamanho, a quota de vCPU (núcleo) por região na sua subscrição é inicialmente definida como 0. [Pedir um aumento de quota de vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) para esta família num [região disponível](https://azure.microsoft.com/regions/services/).
>

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória GPU: GiB | Discos de dados máximos | NICs máximos |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s |6 |112 | 736 | 1 | 24 | 12 | 4 |
| Standard_ND12s |12 |224 | 1474 | 2 | 48 | 24 | 8 | 
| Standard_ND24s |24 |448 | 2948 | 4 | 96 | 32 | 8 |
| Standard_ND24rs* |24 |448 | 2948 | 4 | 96 | 32 | 8 |

1 GPU = um cartão de P40.

*Com capacidade RDMA

## <a name="nv-series"></a>Série NV

Armazenamento Premium:  Não suportado

Cache de armazenamento Premium:  Não suportado

As máquinas de virtuais de série NV têm a tecnologia [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPUs e NVIDIA GRID tecnologia para o ambiente de trabalho aplicações aceleradas e áreas de trabalho virtuais onde os clientes podem visualizar os seus dados ou simulações. Os utilizadores são podem visualizar os seus fluxos de trabalho intensivas de gráficos nas instâncias NV para obter capacidades gráficas superiores e executar adicionalmente cargas de trabalho de precisão única, como codificação e composição. 

Cada GPU em instâncias de NV vem com uma licença de GRADE. Esta licença dá-lhe a flexibilidade para utilizar uma instância de NV como uma estação de trabalho virtual para um único utilizador ou 25 utilizadores em simultâneo podem ligar-se para a VM para um cenário de aplicação virtual.

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória GPU: GiB | Discos de dados máximos | NICs máximos | Estações de trabalho virtual | Aplicações virtuais | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| Standard_NV6 |6 |56 |340 | 1 | 8 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = metade de uma placa M60.

## <a name="nvv2-series-preview"></a>Série NVv2 (pré-visualização)

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

As máquinas de virtuais de série NVv2 têm a tecnologia [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPUs e NVIDIA GRID tecnologia com Intel Broadwell CPUs. Estas máquinas virtuais são direcionadas para aplicações de gráficos de acelerado de GPU e áreas de trabalho virtuais onde os clientes pretendem visualize os seus dados, simular resultados para ver, funciona em CAD ou conteúdo de composição e o stream. Além disto, estas máquinas virtuais podem executar cargas de trabalho de precisão individuais, como codificação e composição. Máquinas de virtuais de NVv2 suportam o Premium Storage e vêm com duas vezes a memória do sistema (RAM), em comparação com o seu predecessor série NV.  

Cada GPU em instâncias de NVv2 vem com uma licença de GRADE. Esta licença dá-lhe a flexibilidade para utilizar uma instância de NV como uma estação de trabalho virtual para um único utilizador ou 25 utilizadores em simultâneo podem ligar-se para a VM para um cenário de aplicação virtual.

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória GPU: GiB | Discos de dados máximos | NICs máximos | Estações de trabalho virtual | Aplicações virtuais | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6s_v2 |6 |112 |320 | 1 | 8 | 12 | 4 | 1 | 25 |
| Standard_NV12s_v2 |12 |224 |640 | 2 | 16 | 24 | 8 | 2 | 50 |
| Standard_NV24s_v2 |24 |448 |1280 | 4 | 32 | 32 | 8 | 4 | 100 |

1 GPU = metade de uma placa M60.

 
