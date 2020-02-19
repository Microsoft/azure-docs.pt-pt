---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 08/08/2019
ms.author: azcspmt;jonbeck;cynthn;joelpell
ms.custom: include file
ms.openlocfilehash: 3fc20288f4ec80c85bd0109799d5ed45b504d359
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77430147"
---
Os tamanhos vm de propósito geral fornecem uma relação CPU-memória equilibrada. Ideal para testes e desenvolvimento, bases de dados pequenas a médias e servidores web de tráfego baixo a médio. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como o armazenamento de tamanhos neste agrupamento.

- A [série DC](#dc-series) é uma família de máquinas virtuais em Azure que pode ajudar a proteger a confidencialidade e integridade dos seus dados e código enquanto é processado na nuvem pública. Estas máquinas são apoiadas pela mais recente geração de processador Intel XEON E-2176G de 3.7GHz com tecnologia SGX. Com a Tecnologia Intel Turbo Boost estas máquinas podem ir até 4.7GHz. As instâncias da série DC permitem que os clientes construam aplicações seguras baseadas no enclave para proteger o seu código e dados enquanto estão em uso.

- Os VMs da série Av2 podem ser implantados numa variedade de tipos de hardware e processadores. Os VMs da série A têm configurações de desempenho de CPU e memória mais adequadas para cargas de trabalho de nível de entrada como desenvolvimento e teste. O tamanho é limitado com base no hardware de forma a proporcionar um desempenho de processador consistente para a instância em execução, independentemente do hardware no qual está implementado. Para determinar o hardware físico no qual este tamanho está implementado, consulte o hardware virtual a partir da Máquina Virtual.

  Os casos de utilização por exemplo incluem servidores de desenvolvimento e teste, servidores web de tráfego baixo, bases de dados pequenas a médias, provas de conceito e repositórios de código.

- A série Dv2, uma continuação da série D original, apresenta um CPU mais potente e uma configuração ideal cpu-to-memória tornando-as adequadas para a maioria das cargas de trabalho de produção. A série Dv2 é cerca de 35% mais rápida que a série D. As séries Dv2 correm nos intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), ou os processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) com os processadores Intel Turbo Boost Technology 2.0. A série Dv2 tem as mesmas configurações de memória e disco da série D.

- A série Dv3 funciona na Intel® Xeon® processadores 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), ou os processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) numa configuração hiper-roscada, proporcionando uma proposta de melhor valor para a maioria dos processadores gerais cargas de trabalho propositadas.  A memória foi expandida (de ~3.5 GiB/vCPU para 4 GiB/vCPU) enquanto os limites do disco e da rede foram ajustados numa base de núcleo para alinhar com o movimento para o hiperthreading.  A série Dv3 já não tem os tamanhos VM de alta memória da série D/Dv2, estes foram movidos para a memória otimizada série Ev3 para [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#ev3-series) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#ev3-series).

  Exemplo, os casos de utilização da série D incluem aplicações de nível empresarial, bases de dados relacionais, caches na memória e análise.

- As séries Dav4 e dasséries Dasv4 são novos tamanhos utilizando o processador 2.35Ghz EPYC<sup>TM</sup> 7452 da AMD numa configuração multi-roscada com até 256 MB L3 cache dedicando 8 MB dessa cache L3 a cada 8 núcleos, aumentando as opções do cliente para executar as suas cargas de trabalho para fins gerais. As séries Dav4 e Dasv4 têm as mesmas configurações de memória e disco que a série D & Dsv3.
  
## <a name="b-series"></a>Série B

Armazenamento Premium: Suportado

Caching de armazenamento premium: Não suportado

Os VMs de série B são ideais para cargas de trabalho que não necessitam de todo o desempenho do CPU continuamente, como servidores web, pequenas bases de dados e ambientes de desenvolvimento e teste. Estas cargas de trabalho normalmente têm requisitos de desempenho insuportáveis. A Série B oferece a estes clientes a capacidade de adquirir um tamanho VM com um desempenho de base consciente do preço que permite que a instância VM aumente os créditos quando o VM está a utilizar menos do que o seu desempenho base. Quando o VM tiver crédito acumulado, o VM pode rebentar acima da linha de base do VM utilizando até 100% do CPU quando a sua aplicação requer o desempenho mais elevado do CPU.

Casos de uso por exemplo incluem servidores de desenvolvimento e teste, servidores web de baixo tráfego, pequenas bases de dados, micro serviços, servidores para a prova de conceitos, servidores de construção.


| Tamanho             | vCPU  | Memória: GiB | Armazenamento (SSD) temporário GiB | Base CPU Perf da VM | Max CPU Perf da VM | Créditos Iniciais | Créditos bancários/hora | Créditos Bancários Max | Discos de dados máximos | Entrada de armazenamento em cache e temperatura máxima: IOPS / MBps | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs máximos |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| <sup>Standard_B1ls 1</sup>  | 1           | 0,5              | 4                          | 5%                   | 100%                   | 30                   | 3                  | 72            | 2                                      | 200 / 10                                  | 160 / 10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10%                   | 100%                   | 30                   | 6                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 100%                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |
| Standard_B12ms | 12           | 48             | 96                         | 202%                  | 1200%                   | 360                   | 121                 | 2909           | 16                                     | 6480 / 75                                 | 4320 / 50                                 | 6  |
| Standard_B16ms | 16           | 64             | 128                         | 270%                  | 1600%                   | 480                   | 162                 | 3888           | 32                                     | 8640 / 100                                 | 4320 / 50                                 | 8  |
| Standard_B20ms | 20           | 80             | 160                         | 337%                  | 2000%                   | 600                   | 203                 | 4860           | 32                                     | 10800 / 125                                 | 4320 / 50                                 | 8  |

<sup>1</sup> B1ls é suportado apenas em Linux

## <a name="dsv3-series-sup1sup"></a>Série Dsv3 <sup>1</sup>

ACU: 160-190

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

Os tamanhos da série Dsv3 funcionam nos processadores Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), ou os processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) com intel Turbo Boost Technology 2.0 e usam armazenamento premium. Os tamanhos da série Dsv3 oferecem uma combinação de vCPU, memória e armazenamento temporário para a maioria das cargas de trabalho de produção.


| Tamanho             | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo do armazenamento temporário e em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | Max NICs / Largura de banda de rede esperada (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_D2s_v3  | 2      | 8           | 16             | 4              | 4000 / 32 (50)                                                       | 3200 / 48                                | 2 / 1000                                   |
| Standard_D4s_v3  | 4      | 16          | 32             | 8              | 8000 / 64 (100)                                                      | 6400 / 96                                | 2 / 2000                                   |
| Standard_D8s_v3  | 8      | 32          | 64             | 16             | 16000 / 128 (200)                                                    | 12800 / 192                              | 4 / 4000                                      |
| Standard_D16s_v3 | 16     | 64          | 128            | 32             | 32000 / 256 (400)                                                    | 25600 / 384                              | 8 / 8000                                      |
| Standard_D32s_v3 | 32     | 128          | 256            | 32             | 64000 / 512 (800)                                                    | 51200 / 768                              | 8 / 16000                                               |
| Standard_D48s_v3 | 48     | 192          | 384            | 32             | 96000 / 768 (1200)                                                    | 76800 / 1152                               | 8 / 24000                                               |
| Standard_D64s_v3 | 64     | 256          | 512            | 32             | 128000 / 1024 (1600)                                                    | 80000 / 1200                              | 8 / 30000                                               |

<sup>1</sup> dsv3 série VM recurso Intel® Hiper-Threading Technology

## <a name="dasv4-series"></a>Série Dasv4

ACU: 230-260

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

Os tamanhos da série Dasv4 baseiam-se no processador 2.35Ghz AMD EPYC<sup>TM</sup> 7452 que pode alcançar uma frequência máxima aumentada de 3,35GHz e utilizar sSD premium. Os tamanhos da série Dasv4 oferecem uma combinação de vCPU, memória e armazenamento temporário para a maioria das cargas de trabalho de produção.

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo do armazenamento temporário e em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | Max NICs / Largura de banda de rede esperada (MBps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4|2|8|16|4|4000 / 32 (50)|3200 / 48|2 / 1000 |
| Standard_D4as_v4|4|16|32|8|8000 / 64 (100)|6400 / 96|2 / 2000 |
| Standard_D8as_v4|8|32|64|16|16000 / 128 (200)|12800 / 192|4 / 4000 |
| Standard_D16as_v4|16|64|128|32|32000 / 255 (400)|25600 / 384|8 / 8000 |
| Standard_D32as_v4|32|128|256|32|64000 / 510 (800)|51200 / 768|8 / 16000 |
| Standard_D48as_v4 <sup>**</sup>|48|192|384|32| | | 
| Standard_D64as_v4 <sup>**</sup>|64|256|512|32| | | 
| <sup>Standard_D96as_v4**</sup>|96|384|768|32| | | 

<sup>**</sup> Estes tamanhos estão em Pré-visualização.  Se estiver interessado em experimentar estes tamanhos maiores, inscreva-se em [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="dv3-series-sup1sup"></a>Série Dv3 <sup>1</sup>

ACU: 160-190

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

Os tamanhos da série Dv3 funcionam nos processadores Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), ou os processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) com a Intel Boost Technology 2.0. Os tamanhos da série Dv3 oferecem uma combinação de vCPU, memória e armazenamento temporário para a maioria das cargas de trabalho de produção.

O armazenamento de discos de dados são cobrados em separado das máquinas virtuais. Para utilizar os discos de armazenamento premium, utilize os tamanhos Dsv3. Os medidores de preços e de faturação dos tamanhos Dsv3 são os mesmos do que os da série Dv3. 


| Tamanho            | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito do armazenamento temporário máximo: IOPS/MBps de Leitura/MBps de Escrita | Max NICs / Largura de banda da rede (Mbps) |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_D2_v3  | 2         | 8           | 50             | 4              | 3000/46/23                                               | 2 / 1000                    |
| Standard_D4_v3  | 4         | 16          | 100            | 8              | 6000/93/46                                               | 2 / 2000                    |
| Standard_D8_v3  | 8         | 32          | 200            | 16             | 12000/187/93                                             | 4 / 4000                    |
| Standard_D16_v3 | 16        | 64          | 400            | 32             | 24000/375/187                                            | 8 / 8000                    |
| Standard_D32_v3 | 32        | 128         | 800            | 32             | 48000/750/375                                            | 8 / 16000                   |
| Standard_D48_v3 | 48        | 192          | 1200            | 32             | 96000/1000/500                                            | 8 / 24000                             |
| Standard_D64_v3 | 64        | 256         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000                   |

<sup>1</sup> Funcionalidade da Série VM da Série Dv3 Intel® Tecnologia De Hiper-Threading

## <a name="dav4-series"></a>Série Dav4

ACU: 230-260

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

Os tamanhos da série Dav4 baseiam-se no processador 2.35Ghz AMD EPYC<sup>TM</sup> 7452 que pode alcançar uma frequência máxima aumentada de 3.35GHz. Os tamanhos da série Dav4 oferecem uma combinação de vCPU, memória e armazenamento temporário para a maioria das cargas de trabalho de produção. O armazenamento de discos de dados são cobrados em separado das máquinas virtuais. Para utilizar sSD premium, utilize os tamanhos Dasv4. Os contadores de preços e faturação para tamanhos Dasv4 são os mesmos que a série Dav4.

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito do armazenamento temporário máximo: IOPS/MBps de Leitura/MBps de Escrita | Max NICs / Largura de banda de rede esperada (MBps) |
|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4 |  2  | 8  | 50  | 4  | 3000 / 46 / 23   | 2 / 1000 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000 / 93 / 46   | 2 / 2000 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12000 / 187 / 93 | 4 / 4000 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24000 / 375 / 187 |8 / 8000 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48000 / 750 / 375 |8 / 16000 |
| <sup>Standard_D48a_v4**</sup> | 48 | 192| 1200 | 32 | | |
| Standard_D64a_v4 <sup>**</sup> | 64 | 256 | 1600 | 32 | | |
| <sup>Standard_D96a_v4**</sup> | 96 | 384 | 2400 | 32 | | |

<sup>**</sup> Estes tamanhos estão em Pré-visualização.  Se estiver interessado em experimentar estes tamanhos maiores, inscreva-se em [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="dsv2-series"></a>Série DSv2

ACU: 210-250

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

Os tamanhos da série DSv2 funcionam nos processadores Intel® Xeon® 8171M 2.1GHz (Skylake) ou na Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) ou na Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) com a Intel Turbo Boost Technology 2.0 e utilizam o armazenamento premium.

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo do armazenamento temporário e em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | Max NICs / Largura de banda de rede esperada (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3.5 |7 |4 |4000 / 32 (43) |3200 / 48 |2 / 750 |
| Standard_DS2_v2 |2 |7 |14 |8 |8000 / 64 (86) |6400 / 96 |2 / 1500 |
| Standard_DS3_v2 |4 |14 |28 |16 |16000 / 128 (172) |12800 / 192 |4 / 3000 |
| Standard_DS4_v2 |8 |28 |56 |32 |32000 / 256 (344) |25600 / 384 |8 / 6000 |
| Standard_DS5_v2 |16 |56 |112 |64 |64000 / 512 (688) |51200 / 768 |8 / 12000 |

## <a name="dv2-series"></a>Série Dv2

ACU: 210-250

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

Os tamanhos da série DSv2 funcionam nos processadores Intel® Xeon® 8171M 2.1GHz (Skylake) ou nos processadores Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) ou na Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) com intel Turbo Boost Technology 2.0.

| Tamanho           | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Débito do armazenamento temporário máximo: IOPS/MBps de Leitura/MBps de Escrita | Discos de dados máximos | Entrada: IOPS | Max NICs / Largura de banda de rede esperada (Mbps) |
|----------------|------|-------------|------------------------|------------------------------------------------------------|----------------|------------------|----------------------------------------------|
| Standard_D1_v2 | 1    | 3.5         | 50                     | 3000 / 46 / 23                                             | 4              | 4x500            | 2 / 750                                      |
| Standard_D2_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 8              | 8x500            | 2 / 1500                                     |
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16             | 16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32             | 32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64             | 64x500           | 8 / 12000                                    |

## <a name="av2-series"></a>Série Av2

ACU: 100

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

| Tamanho            | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Débito do armazenamento temporário máximo: IOPS/MBps de Leitura/MBps de Escrita | Máximo do disco de dados/débito: IOPS | Max NICs / Largura de banda de rede esperada (Mbps) | 
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2x500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4x500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8x500               | 4 / 1000                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8000 / 160 / 80                                          | 16 / 16x500             | 8 / 2000                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2000 / 40 / 20                                           | 4 / 4x500               | 2 / 500                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4000 / 80 / 40                                           | 8 / 8x500               | 4 / 1000                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8000 / 160 / 80                                          | 16 / 16x500             | 8 / 2000                     |

## <a name="dc-series"></a>SÉRIE DC

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado



| Tamanho          | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo do armazenamento temporário e em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | Max NICs / Largura de banda de rede esperada (Mbps) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000 / 32 (43)                                                          | 3200 /48                                  | 2 / 1500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400 /96                                  | 2 / 3000                                     |
