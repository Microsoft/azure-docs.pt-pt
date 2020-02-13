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
ms.openlocfilehash: 8f256749c363e2900fe62bda557f7cb4caa72e3e
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77179124"
---
Os tamanhos de VM otimizados pela GPU são máquinas virtuais especializadas disponíveis com GPUs únicaou múltipla seleções da NVIDIA. Estes tamanhos são projetados para cargas de trabalho intensivas em cálculo, gráficos-intensivos e visualização. Este artigo fornece informações sobre o número e o tipo de GPUs, vCPUs, discos de dados e NICs. A produção de armazenamento e a largura de banda da rede também estão incluídas para cada tamanho neste agrupamento.

* Os tamanhos **NC, NCv2, NCv3** são otimizados para aplicações e algoritmos intensivos de computação e de rede intensiva. Alguns exemplos são aplicações e simulações baseadas em CUDA e OpenCL, IA e Deep Learning. A série NCv3 está focada em cargas de trabalho de computação de alto desempenho com o GPU Tesla V100 da NVIDIA. A série NC utiliza o processador Intel Xeon E5-2690 v3 2.60GHz v3 (Haswell) e o processador NCv2 série e NCv3 da série VMs usam o processador Intel Xeon E5-2690 v4 (Broadwell).

* **ND, e NDv2** A série ND está focada em cenários de treino e inferência para a aprendizagem profunda. Utiliza o GPU NVIDIA Tesla P40 e o processador Intel Xeon E5-2690 v4 (Broadwell). A série NDv2 utiliza o processador Intel Xeon Platinum 8168 (Skylake).

* Os tamanhos **NV e NVv3** são otimizados e projetados para visualização remota, streaming, jogos, codificação e cenários VDI usando estruturas como OpenGL e DirectX.  Estes VMs são apoiados pela NVIDIA Tesla M60 GPU.

* Os tamanhos **NVv4** são otimizados e projetados para VDI e visualização remota. Com GPUs partioned, nVv4 oferece o tamanho certo para cargas de trabalho que requerem recursos GPU menores.  Estes VMs são apoiados pela AMD Radeon Instinct MI25 GPU.


## <a name="nc-series"></a>Série NC

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

Os VMs da série NC são alimentados pelo cartão [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) e pelo processador Intel Xeon E5-2690 v3 (Haswell). Os utilizadores podem analisar os dados mais rapidamente, aproveitando a CUDA para aplicações de exploração de energia, simulações de acidentes, renderização por raios, aprendizagem profunda e muito mais. A configuração NC24r proporciona uma interface de rede de rede de baixa latência e alta potência otimizada para trabalhos de computação paralela sacados.

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória DE GPU: GiB | Discos de dados máximos | NICs máximos |
| --- | --- | --- | --- | --- | --- | --- | ---- |
| Standard_NC6 |6 |56 | 340 | 1 | 12 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 24 | 48 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = metade de uma placa K80.

*Com capacidade RDMA

## <a name="ncv2-series"></a>Série NCv2

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

Os VMs da série NCv2 são alimentados por [GPUs NVIDIA Tesla P100.](https://www.nvidia.com/en-us/data-center/tesla-p100/) Estas GPUs podem fornecer mais de 2x o desempenho computacional da série NC. Os clientes podem tirar partido destas GPUs atualizadas para cargas de trabalho tradicionais de HPC, tais como modelação de reservatórios, sequenciação de ADN, análise de proteínas, simulações de Monte Carlo, entre outros. Além das GPUs, os VMs da série NCv2 também são alimentados por CpUs Intel Xeon E5-2690 v4 (Broadwell).

A configuração NC24rs v2 proporciona uma interface de rede de baixa latência e alta potência otimizada para trabalhos de computação paralela salutares.

> [!IMPORTANT]
> Para esta família de tamanhos, a quota vCPU (núcleo) na sua subscrição está inicialmente definida para 0 em cada região. [Solicite um aumento de quota vCPU](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md) para esta família numa [região disponível.](https://azure.microsoft.com/regions/services/)
>

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD): GiB | GPU | Memória DE GPU: GiB | Discos de dados máximos | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs máximos |
| --- | --- | --- | --- | --- | --- | ---  | ---| --- |
| Standard_NC6s_v2 | 6 |112 | 736 | 1 | 16 | 12 | 20000/ 200 | 4 |
| Standard_NC12s_v2 | 12 |224 | 1474 | 2 | 32 | 24 | 40000 / 400 | 8 |
| Standard_NC24s_v2 | 24 |448 | 2948 | 4 | 64 | 32 | 80000 / 800 | 8 |
| Standard_NC24rs_v2* | 24 |448 | 2948 | 4 | 64 | 32 | 80000 / 800 | 8 |

1 GPU = um cartão P100.

*Com capacidade RDMA

## <a name="ncv3-series"></a>Série NCv3

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

Os VMs da série NCv3 são alimentados por [GPUs NVIDIA Tesla V100.](https://www.nvidia.com/en-us/data-center/tesla-v100/) Estes GPUs podem fornecer 1,5x o desempenho computacional da série NCv2. Os clientes podem tirar partido destas GPUs atualizadas para cargas de trabalho tradicionais de HPC, tais como modelação de reservatórios, sequenciação de ADN, análise de proteínas, simulações de Monte Carlo, entre outros. A configuração NC24rs v3 proporciona uma interface de rede de baixa latência e alta potência otimizada para trabalhos de computação paralela salutares. Além das GPUs, os VMs da série NCv3 também são alimentados por CpUs Intel Xeon E5-2690 v4 (Broadwell).

> [!IMPORTANT]
> Para esta família de tamanhos, a quota vCPU (núcleo) na sua subscrição está inicialmente definida para 0 em cada região. [Solicite um aumento de quota vCPU](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md) para esta família numa [região disponível.](https://azure.microsoft.com/regions/services/)
>

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD): GiB | GPU | Memória DE GPU: GiB | Discos de dados máximos | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs máximos |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 | 6 |112 | 736 | 1 | 16 | 12 | 20000 / 200 | 4 |
| Standard_NC12s_v3 | 12 |224 | 1474 | 2 | 32 | 24 | 40000 / 400 | 8 |
| Standard_NC24s_v3 | 24 |448 | 2948 | 4 | 64 | 32 | 80000 / 800 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 64 | 32 | 80000 / 800 | 8 |

1 GPU = um cartão V100.

*Com capacidade RDMA

## <a name="updated-ndv2-series-preview"></a>Série NDv2 atualizada (Pré-visualização)

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

InfiniBand: Suportado

A máquina virtual da série NDv2 é uma nova adição à família GPU projetada para as necessidades da IA acelerada por GPU, machine learning, simulação e hPC. 

O NDv2 é alimentado por 8 GPUs ligados a NVIDIA Tesla V100 NVLINK, cada um com 32 GB de memória GPU. Cada NDv2 VM também tem 40 núcleos Intel Xeon Platinum 8168 (Skylake) e 672 GiB de memória do sistema. 

Os casos NDv2 proporcionam um excelente desempenho para as cargas de trabalho de HPC e IA utilizando os núcleos de computação otimizados pela CUDA GPU, e as muitas ferramentas de IA, ML e analytics que suportam a aceleração do GPU 'out-of-box', tais como TensorFlow, Pytorch, Caffe, RAPIDS, entre outros quadros. 

Criticamente, o NDv2 é construído para cargas de trabalho computacionalmente intensas (aproveitando 8 GPUs por VM) e scale-out (aproveitando vários VMs que trabalham em conjunto). A série NDv2 suporta agora 100 Gigabit InfiniBand EDR backend networking, semelhante ao disponível na série HB de HPC VM, para permitir o agrupamento de alto desempenho para cenários paralelos, incluindo formação distribuída para IA e ML. Esta rede de backend suporta todos os principais protocolos InfiniBand, incluindo os empregados pelas bibliotecas NCCL2 da NVIDIA, permitindo um agrupamento sem emenda de GPUs.

> Ao [ativar a InfiniBand](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband) no ND40rs_v2 VM, utilize o condutor 4.7-1.1.0.0.1 Mellanox OFED.

> Devido ao aumento da memória gpu, o novo ND40rs_v2 VM requer a utilização de [VMs](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) de Geração 2 e imagens de mercado. 

> [Inscreva-se para solicitar acesso antecipado à pré-visualização da máquina virtual NDv2.](https://aka.ms/AzureNDrv2Preview)

> Nota: O ND40s_v2 com 16 GB de memória por GPU já não está disponível para pré-visualização e foi substituído pela ND40rs_v2 atualizada.
<br>

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD): GiB | GPU | Memória GPU: GiB | Discos de dados máximos | Débito máximo do disco não colocado em cache: IOPS/MBps | Largura de banda de rede max | NICs máximos |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 16 | 32 | 80000 / 800 | 24000 Mbps | 8 |

## <a name="nd-series"></a>Série ND

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

As máquinas virtuais da série ND são uma nova adição à família GPU projetada para cargas de trabalho de IA e Deep Learning. Oferecem excelente desempenho para treino e inferência. As instâncias ND são alimentadas por [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPUs e Intel Xeon E5-2690 v4 (Broadwell) CPUs. Estes casos proporcionam um excelente desempenho para operações de ponto flutuante de precisão única, para cargas de trabalho de IA utilizando o Microsoft Cognitive Toolkit, TensorFlow, Caffe e outras estruturas. A série ND também oferece um tamanho de memória GPU muito maior (24 GB), permitindo encaixar modelos de rede neural muito maiores. Tal como a série NC, a série ND oferece uma configuração com uma rede secundária de baixa latência, de alta frequência através de RDMA, e conectividade InfiniBand para que possa executar trabalhos de formação em larga escala abrangendo muitas GPUs.

> [!IMPORTANT]
> Para esta família de tamanhos, a quota vCPU (núcleo) por região na sua subscrição está inicialmente definida para 0. [Solicite um aumento de quota vCPU](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md) para esta família numa [região disponível.](https://azure.microsoft.com/regions/services/)
>

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória DE GPU: GiB | Discos de dados máximos | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs máximos |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s | 6 |112 | 736 | 1 | 24 | 12 | 20000 / 200 | 4 |
| Standard_ND12s | 12 |224 | 1474 | 2 | 48 | 24 | 40000 / 400 | 8 | 
| Standard_ND24s | 24 |448 | 2948 | 4 | 96 | 32 | 80000 / 800 | 8 |
| Standard_ND24rs* | 24 |448 | 2948 | 4 | 96 | 32 | 80000 / 800 | 8 |

1 GPU = um cartão P40.

*Com capacidade RDMA

## <a name="nv-series"></a>Série NV

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

As máquinas virtuais da série NV são alimentadas pela tecnologia [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPUs e NVIDIA GRID para aplicações aceleradas de desktop e desktops virtuais onde os clientes são capazes de visualizar os seus dados ou simulações. Os utilizadores são capazes de visualizar os seus fluxos de trabalho intensivos gráficos nas instâncias de NV para obter uma capacidade gráfica superior e, adicionalmente, executar cargas de trabalho de precisão única, como codificação e renderização. Os VMs da série NV também são alimentados por Intel Xeon E5-2690 v3 (Haswell) CPUs.

Cada GPU em instâncias de NV vem com uma licença GRID. Esta licença dá-lhe a flexibilidade para usar uma instância de NV como uma estação de trabalho virtual para um único utilizador, ou 25 utilizadores simultâneos podem ligar-se ao VM para um cenário de aplicação virtual.

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória DE GPU: GiB | Discos de dados máximos | NICs máximos | Estações de Trabalho Virtuais | Aplicações Virtuais |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 | 8 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = metade de uma placa M60.

## <a name="nvv3-series--sup1sup"></a>NVv3 série <sup>1</sup>

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

As máquinas virtuais da série NVv3 são alimentadas pela tecnologia [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPUs e NVIDIA GRID com CPUs Intel E5-2690 v4 (Broadwell). Estas máquinas virtuais são direcionadas para aplicações gráficas aceleradas de GPU e desktops virtuais onde os clientes querem visualizar os seus dados, simular resultados para visualizar, trabalhar em CAD ou renderizar e transmitir conteúdo. Além disso, estas máquinas virtuais podem executar cargas de trabalho de precisão única, tais como codificação e renderização. As máquinas virtuais NVv3 suportam o Armazenamento Premium e vêm com o dobro da memória do sistema (RAM) quando comparadas com a sua série NV antecessora.  

Cada GPU em instâncias NVv3 vem com uma licença GRID. Esta licença dá-lhe a flexibilidade para usar uma instância de NV como uma estação de trabalho virtual para um único utilizador, ou 25 utilizadores simultâneos podem ligar-se ao VM para um cenário de aplicação virtual.

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória DE GPU: GiB | Discos de dados máximos | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs máximos | Estações de Trabalho Virtuais | Aplicações Virtuais | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV12s_v3 |12 |112 |736 | 1 | 8 | 12 | 20000 / 200 | 4 | 1 | 25 |
| Standard_NV24s_v3 |24 |224 |1474 | 2 | 16 | 24 | 40000 / 400 | 8 | 2 | 50 |
| Standard_NV48s_v3 |48 |448 |2948 | 4 | 32 | 32 | 80000 / 800 | 8 | 4 | 100 |

1 GPU = metade de uma placa M60.

<sup>1</sup> VMs série NVv3 apresentam tecnologia Intel Hyper-Threading

## <a name="nvv4-series-preview--sup1sup"></a>Série NVv4 (Pré-visualização) <sup>1</sup>

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

As máquinas virtuais da série NVv4 são alimentadas por [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPUs e AMD EPYC 7V12 (Roma) CPUs. Com a série NVv4, o Azure está a introduzir máquinas virtuais com GPUs parciais. Escolha a máquina virtual de tamanho certo para aplicações gráficas aceleradas de GPU e ambientes de trabalho virtuais a partir de 1/8 de um GPU com 2 tampão de moldura GiB para um GPU completo com 16 tampão de fotogramas GiB. Atualmente, as máquinas virtuais NVv4 suportam apenas o sistema operativo windows.

[Inscreva-se e obtenha acesso a estas máquinas durante a pré-visualização](https://aka.ms/nvv4signup).
<br>

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória DE GPU: GiB | Discos de dados máximos | NICs máximos |
| --- | --- | --- | --- | --- | --- | --- | --- | 
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 | 
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 | 



<sup>1</sup> VMs série NVv4 apresentam tecnologia multithreading amd simultânea

