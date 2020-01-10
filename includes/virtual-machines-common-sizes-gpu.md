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
ms.openlocfilehash: 9b08dd60020dad6f747167f35e8d172fdc24a59e
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75752188"
---
Os tamanhos de VM otimizadas para GPU são máquinas virtuais especializadas disponíveis com uma ou várias GPUs NVIDIA. Esses tamanhos são projetados para cargas de trabalho com uso intensivo de computação, gráficos e visualização. Este artigo fornece informações sobre o número e tipo de GPUs, vCPUs, discos de dados e NICs. A taxa de transferência de armazenamento e a largura de banda da rede também estão incluídas para cada tamanho neste agrupamento.

* **NC, NCv2,** tamanhos de NCv3 são otimizados para aplicativos e algoritmos com uso intensivo de computação e rede. Alguns exemplos são aplicativos baseados em CUDA e OpenCL e simulações, ia e aprendizado profundo. A série NCv3 está concentrada em cargas de trabalho de computação de alto desempenho com a GPU Tesla V100 da NVIDIA. A série NC usa o processador Intel Xeon E5-2690 v3 2,60 GHz (Haswell) e as VMs da série NCv2 e NCv3 usam o processador Intel Xeon E5-2690 V4 (Broadwell).

* **ND e NDv2** A série ND se concentra em cenários de treinamento e inferência para aprendizado profundo. Ele usa o NVIDIA Tesla P40 GPU e o processador Intel Xeon E5-2690 V4 (Broadwell). A série NDv2 usa o processador Intel Xeon Platinum 8168 (Skylake).

* Os tamanhos de **NV e NVv3** são otimizados e projetados para cenários de visualização remota, streaming, jogos, codificação e VDI usando estruturas como OpenGL e DirectX.  Essas VMs são apoiadas pela GPU NVIDIA Tesla M60.

* Os tamanhos de **NVv4** são otimizados e projetados para VDI e visualização remota. Com GPUs particionadas, o NVv4 oferece o tamanho certo para cargas de trabalho que exigem recursos menores de GPU.  Essas VMs são apoiadas pela GPU AMD Radeon instinto MI25.


## <a name="nc-series"></a>Série NC

Armazenamento Premium: sem suporte

Armazenamento em cache Premium: sem suporte

As VMs da série NC são alimentadas pela placa [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) e pelo processador Intel Xeon E5-2690 v3 (Haswell). Os usuários podem investigar os dados mais rapidamente aproveitando o CUDA para aplicativos de exploração de energia, simulações de falhas, renderização de Ray Traced, aprendizado profundo e muito mais. A configuração NC24r fornece uma interface de rede de alta taxa de transferência e baixa latência otimizada para cargas de trabalho de computação paralela firmemente acopladas.

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário em GiB | GPU | Memória da GPU: GiB | Máximo de discos de dados | NICs máximos |
| --- | --- | --- | --- | --- | --- | --- | ---- |
| Standard_NC6 |6 |56 | 340 | 1 | 12 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 24 | 48 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = metade de uma placa K80.

*Com capacidade RDMA

## <a name="ncv2-series"></a>Série NCv2

Armazenamento Premium: com suporte

Cache de armazenamento Premium: com suporte

As VMs da série NCv2 são alimentadas por GPUs [NVIDIA Tesla P100](https://www.nvidia.com/en-us/data-center/tesla-p100/) . Essas GPUs podem fornecer mais de duas vezes o desempenho computacional da série NC. Os clientes podem aproveitar essas GPUs atualizadas para cargas de trabalho de HPC tradicionais, como modelagem de reservatório, sequenciamento de DNA, análise de proteína, simulações de Carlo monte e outros. Além das GPUs, as VMs da série NCv2 também são alimentadas por CPUs Intel Xeon E5-2690 V4 (Broadwell).

A configuração do NC24rs v2 fornece uma interface de rede de alta taxa de transferência e baixa latência otimizada para cargas de trabalho de computação paralela firmemente acopladas.

> [!IMPORTANT]
> Para essa família de tamanho, a cota de vCPU (núcleo) em sua assinatura é inicialmente definida como 0 em cada região. [Solicite um aumento de cota de vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) para essa família em uma [região disponível](https://azure.microsoft.com/regions/services/).
>

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD): GiB | GPU | Memória da GPU: GiB | Máximo de discos de dados | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs máximos |
| --- | --- | --- | --- | --- | --- | ---  | ---| --- |
| Standard_NC6s_v2 | 6 |112 | 736 | 1 | 16 | 12 | 20000/ 200 | 4 |
| Standard_NC12s_v2 | 12 |224 | 1474 | 2 | 32 | 24 | 40000 / 400 | 8 |
| Standard_NC24s_v2 | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v2* | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = um cartão P100.

*Com capacidade RDMA

## <a name="ncv3-series"></a>Série NCv3

Armazenamento Premium: com suporte

Cache de armazenamento Premium: com suporte

As VMs da série NCv3 são alimentadas por GPUs [NVIDIA Tesla V100](https://www.nvidia.com/en-us/data-center/tesla-v100/) . Essas GPUs podem fornecer um desempenho computacional de 1,5 x da série NCv2. Os clientes podem aproveitar essas GPUs atualizadas para cargas de trabalho de HPC tradicionais, como modelagem de reservatório, sequenciamento de DNA, análise de proteína, simulações de Carlo monte e outros. A configuração do NC24rs v3 fornece uma interface de rede de alta taxa de transferência e baixa latência otimizada para cargas de trabalho de computação paralela firmemente acopladas. Além das GPUs, as VMs da série NCv3 também são alimentadas por CPUs Intel Xeon E5-2690 V4 (Broadwell).

> [!IMPORTANT]
> Para essa família de tamanho, a cota de vCPU (núcleo) em sua assinatura é inicialmente definida como 0 em cada região. [Solicite um aumento de cota de vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) para essa família em uma [região disponível](https://azure.microsoft.com/regions/services/).
>

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD): GiB | GPU | Memória da GPU: GiB | Máximo de discos de dados | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs máximos |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 | 6 |112 | 736 | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v3 | 12 |224 | 1474 | 2 | 32 | 24 | 40000 / 400 | 8 |
| Standard_NC24s_v3 | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = um cartão V100.

*Com capacidade RDMA

## <a name="updated-ndv2-series-preview"></a>Série NDv2 atualizada (versão prévia)

Armazenamento Premium: com suporte

Cache de armazenamento Premium: com suporte

InfiniBand: com suporte

A máquina virtual da série NDv2 é uma nova adição à família de GPU projetada para as necessidades das cargas de trabalho mais exigentes do ia, do aprendizado de máquina, da simulação e do HPC com maior rapidez. 

A NDv2 é alimentada por oito GPUs NVIDIA Tesla V100 NVLINK conectadas, cada uma com 32 GB de memória de GPU. Cada VM NDv2 também tem 40 núcleos não hiperthreads Intel Xeon Platinum 8168 (Skylake) e 672 GiB de memória do sistema. 

As instâncias de NDv2 fornecem um desempenho excelente para cargas de trabalho do HPC e do AI utilizando kernels de computação com otimização de GPU CUDA e as muitas ferramentas de ia, ML e análises que dão suporte à aceleração de GPU "pronta para uso", como TensorFlow, Pytorch, Caffe, RAPIDS e outras estruturas. 

De forma crucial, o NDv2 é criado para expansão computacionalmente intensa (aproveitando 8 GPUs por VM) e escalar horizontalmente (aproveitando várias VMs trabalhando juntas) cargas de trabalho. A série NDv2 agora dá suporte à rede de back-end Gigabit InfiniBand EDR de 100, semelhante àquela disponível na série HB da VM HPC, para permitir o clustering de alto desempenho para cenários paralelos, incluindo treinamento distribuído para ia e ML. Essa rede de back-end dá suporte a todos os principais protocolos InfiniBand, incluindo aqueles empregados pelas bibliotecas NCCL2 da NVIDIA, permitindo um clustering contínuo de GPUs.

> Ao [habilitar a InfiniBand](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband) na VM ND40rs_v2, use o driver 4.7-1.0.0.1 Mellanox ofed.

> Devido à maior memória da GPU, o novo ND40rs_v2 VM requer o uso de [VMs de geração 2](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) e imagens do Marketplace. 

> [Inscreva-se para solicitar acesso antecipado à visualização da máquina virtual NDv2.](https://aka.ms/AzureNDrv2Preview)

> Observação: o ND40s_v2 incluindo 16 GB de memória por GPU não está mais disponível para visualização e foi substituído pelo ND40rs_v2 atualizado.
<br>

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD): GiB | GPU | Memória da GPU: GiB | Máximo de discos de dados | Débito máximo do disco não colocado em cache: IOPS/MBps | Largura de banda máxima da rede | NICs máximos |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 16 | 32 | 80000/800 | 24000 Mbps | 8 |

## <a name="nd-series"></a>Série ND

Armazenamento Premium: com suporte

Cache de armazenamento Premium: com suporte

As máquinas virtuais da série ND são uma nova adição à família de GPU projetada para ia e cargas de trabalho de aprendizado profundo. Eles oferecem um desempenho excelente para treinamento e inferência. As instâncias do ND são alimentadas por [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPUs e CPUs Intel Xeon E5-2690 V4 (Broadwell). Essas instâncias fornecem um desempenho excelente para operações de ponto flutuante de precisão simples, para cargas de trabalho de ia que utilizam Microsoft Cognitive Toolkit, TensorFlow, Caffe e outras estruturas. A série ND também oferece um tamanho de memória GPU muito maior (24 GB), que permite adaptar modelos de rede neuronal muito maiores. Como a série NC, a série ND oferece uma configuração com uma rede secundária de baixa latência e alta taxa de transferência por meio de RDMA e conectividade InfiniBand para que você possa executar trabalhos de treinamento em grande escala abrangendo muitas GPUs.

> [!IMPORTANT]
> Para essa família de tamanho, a cota de vCPU (núcleo) por região em sua assinatura é definida inicialmente como 0. [Solicite um aumento de cota de vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) para essa família em uma [região disponível](https://azure.microsoft.com/regions/services/).
>

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário em GiB | GPU | Memória da GPU: GiB | Máximo de discos de dados | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs máximos |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s | 6 |112 | 736 | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s | 12 |224 | 1474 | 2 | 48 | 24 | 40000 / 400 | 8 | 
| Standard_ND24s | 24 |448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |
| Standard_ND24rs* | 24 |448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 GPU = um cartão P40.

*Com capacidade RDMA

## <a name="nv-series"></a>Série NV

Armazenamento Premium: sem suporte

Armazenamento em cache Premium: sem suporte

As máquinas virtuais da série NV são alimentadas por GPUs [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) e pela tecnologia NVIDIA Grid para aplicativos acelerados para desktop e áreas de trabalho virtuais em que os clientes podem visualizar seus dados ou simulações. Os usuários podem visualizar seus fluxos de trabalho com uso intensivo de gráficos nas instâncias NV para obter recursos gráficos superiores e, além disso, executar cargas de trabalho de precisão única, como codificação e renderização. As VMs da série NV também são alimentadas por CPUs do Intel Xeon E5-2690 v3 (Haswell).

Cada GPU em instâncias NV vem com uma licença de grade. Essa licença oferece a flexibilidade de usar uma instância de NV como uma estação de trabalho virtual para um único usuário, ou 25 usuários simultâneos podem se conectar à VM para um cenário de aplicativo virtual.

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário em GiB | GPU | Memória da GPU: GiB | Máximo de discos de dados | NICs máximos | Estações de trabalho virtuais | Aplicações Virtuais |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |340 | 1 | 8 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = metade de uma placa M60.

## <a name="nvv3-series--sup1sup"></a>Série NVv3 <sup>1</sup>

Armazenamento Premium: com suporte

Cache de armazenamento Premium: com suporte

As máquinas virtuais da série NVv3 são alimentadas pelas GPUs [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) e pela tecnologia NVIDIA Grid com as CPUs Intel E5-2690 V4 (Broadwell). Essas máquinas virtuais são destinadas a aplicativos gráficos acelerados à GPU e áreas de trabalho virtuais em que os clientes desejam Visualizar seus dados, simular resultados para exibir, trabalhar em CAD ou renderizar e transmitir conteúdo. Além disto, estas máquinas virtuais podem executar cargas de trabalho de precisão individuais, como codificação e composição. As máquinas virtuais NVv3 dão suporte ao armazenamento Premium e vêm com duas vezes a RAM (memória do sistema) quando comparadas com sua série NV do antecessor.  

Cada GPU em instâncias de NVv3 vem com uma licença de grade. Essa licença oferece a flexibilidade de usar uma instância de NV como uma estação de trabalho virtual para um único usuário, ou 25 usuários simultâneos podem se conectar à VM para um cenário de aplicativo virtual.

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário em GiB | GPU | Memória da GPU: GiB | Máximo de discos de dados | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs máximos | Estações de trabalho virtuais | Aplicações Virtuais | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV12s_v3 |12 |112 |320 | 1 | 8 | 12 | 20000/200 | 4 | 1 | 25 |
| Standard_NV24s_v3 |24 |224 |640 | 2 | 16 | 24 | 40000 / 400 | 8 | 2 | 50 |
| Standard_NV48s_v3 |48 |448 |1280 | 4 | 32 | 32 | 80000/800 | 8 | 4 | 100 |

1 GPU = metade de uma placa M60.

<sup>1</sup> o recurso de VMs NVv3-Series Intel Hyper-Threading Technology

## <a name="nvv4-series-preview--sup1sup"></a>Série NVv4 (visualização) <sup>1</sup>

Armazenamento Premium: com suporte

Cache de armazenamento Premium: com suporte

As máquinas virtuais da série NVv4 são alimentadas por GPUs [AMD Radeon instinto MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) e CPUs AMD EPYC 7V12 (Roma). Com o NVv4-Series, o Azure está introduzindo máquinas virtuais com GPUs parciais. Escolha a máquina virtual de tamanho certo para aplicativos gráficos acelerados de GPU e áreas de trabalho virtuais começando em 1/8 de uma GPU com 2 buffer de quadros GiB para uma GPU completa com 16 buffer de quadros GiB. Atualmente, as máquinas virtuais NVv4 dão suporte apenas ao sistema operacional convidado do Windows.

[Inscreva-se e obtenha acesso a esses computadores durante a visualização](https://aka.ms/nvv4signup).
<br>

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário em GiB | GPU | Memória da GPU: GiB | Máximo de discos de dados | NICs máximos |
| --- | --- | --- | --- | --- | --- | --- | --- | 
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 | 
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 | 



<sup>1</sup> as VMs da série NVv4 apresentam a tecnologia de vários threads simultâneos da AMD

