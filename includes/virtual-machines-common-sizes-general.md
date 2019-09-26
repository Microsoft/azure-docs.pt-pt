---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 08/08/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 5d89feaa5225aea56af86aa7f70d3666994fdaec
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266896"
---
Os tamanhos de VM de uso geral fornecem a proporção balanceada de CPU para memória. Ideal para teste e desenvolvimento, bases de dados pequenas a médias e servidores Web com tráfego baixo a médio. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento para tamanhos neste agrupamento.

- A [série DC](#dc-series) é uma família de máquinas virtuais no Azure que pode ajudar a proteger a confidencialidade e a integridade de seus dados e código enquanto eles são processados na nuvem pública. Estas máquinas assentam na geração mais recente do Processador Intel XEON E-2176G de 3,7 GHz com tecnologia SGX. Com o Intel Turbo Boost Technology, estas máquinas podem chegar aos 4,7 GHz. As instâncias da série DC permitem aos clientes criar aplicações baseadas em enclave seguras para proteger os respetivos dados e código enquanto estão em utilização.

- As VMs da série Av2 podem ser implantadas em uma variedade de tipos de hardware e processadores. As VMs de série A têm as configurações de desempenho e memória da CPU mais adequadas a cargas de trabalho ao nível da entrada como, por exemplo, desenvolvimento e teste. O tamanho é limitado com base no hardware de forma a proporcionar um desempenho de processador consistente para a instância em execução, independentemente do hardware no qual está implementado. Para determinar o hardware físico no qual este tamanho está implementado, consulte o hardware virtual a partir da Máquina Virtual.

  Os casos de uso de exemplo incluem servidores de desenvolvimento e teste, servidores Web de tráfego baixo, bancos de dados pequenos a médios, prova de conceitos e repositórios de código.

- A série Dv2, uma continuação da série D original, apresenta uma CPU mais potente e configuração ideal de CPU para memória, tornando-as adequadas para a maioria das cargas de trabalho de produção. A CPU da série Dv2 é cerca de 35% mais rápida do que a CPU da série D. Ele se baseia na última geração dos processadores Intel Xeon® E5-2673 v3 2,4 GHz (Haswell) ou E5-2673 v4 2,3 GHz (Broadwell) e, com a tecnologia Intel Turbo Boost 2,0, pode chegar a até 3,1 GHz. A série Dv2 tem as mesmas configurações de memória e disco da série D.

- A série Dv3 apresenta o processador Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz ou o processador mais recente de 2,3 GHz Intel XEON® E5-2673 V4 (Broadwell) em uma configuração de hiperthread, fornecendo uma proposta de valor melhor para cargas de trabalho de uso geral.  A memória foi expandida (de ~ 3,5 GiB/vCPU para 4 GiB/vCPU) enquanto os limites de disco e de rede foram ajustados por núcleo para alinhar com a mudança para hyperthreading.  O Dv3 não tem mais os tamanhos de VM de memória alta das famílias D/Dv2, aqueles que foram movidos para a nova família Ev3.

  Exemplos de casos de uso da série D incluem aplicativos de nível empresarial, bancos de dados relacionais, cache na memória e análise.

- As séries a e Dasv3 são novos tamanhos que utilizam o processador 2.35 GHz EPYC<sup>TM</sup> 7452V da AMD em uma configuração multi-threaded com até 256 GB de cache L3, que dedica 8 GB desse cache L3 a cada 8 núcleos aumentando as opções do cliente para executar seu geral cargas de trabalho de finalidade. A série da e Dasv3 têm as mesmas configurações de memória e disco que a série D & Dsv3.
  
## <a name="b-series"></a>Série B

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Não Suportada

As VMs expansível da série B são ideais para cargas de trabalho que não precisam do desempenho total da CPU continuamente, como servidores Web, bancos de dados pequenos e ambientes de desenvolvimento e teste. Essas cargas de trabalho normalmente têm requisitos de desempenho intermitentes. A série B fornece a esses clientes a capacidade de comprar um tamanho de VM com um desempenho de linha de base atento a preços que permite que a instância de VM crie créditos quando a VM estiver utilizando menos do que seu desempenho base. Quando a VM tiver acumulado o crédito, a VM poderá ultrapassar a linha de base da VM usando até 100% da CPU quando seu aplicativo exigir o maior desempenho da CPU.

Os casos de uso de exemplo incluem servidores de desenvolvimento e teste, servidores Web de tráfego baixo, bancos de dados pequenos, micro Services, servidores para prova de conceito, servidores de compilação.


| Size             | vCPU  | Memória: GiB | Armazenamento (SSD) temporário GiB | Desempenho Base da CPU da VM | Desempenho Máximo da CPU da VM | Créditos Iniciais | Créditos obtidos/hora | Máximo de Créditos Obtidos | Discos de dados máximos | Débito máximo de armazenamento temporário e em cache: IOPS/MBps | Débito máximo de disco não colocado em cache: IOPS/MBps | NICs máximos |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5%                   | 100%                   | 30                   | 3                  | 72            | 2                                      | 200 / 10                                  | 160 / 10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10%                   | 100%                   | 30                   | 6                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 100%                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |
| Standard_B12ms | 12           | 48             | 96                         | 202%                  | 1200%                   | 360                   | 121                 | 2909           | 16                                     | 6480 / 75                                 | 4320 / 50                                 | 6  |
| Standard_B16ms | 16           | 64             | 128                         | 270%                  | 1600%                   | 480                   | 162                 | 3888           | 32                                     | 8640 / 100                                 | 4320 / 50                                 | 8  |
| Standard_B20ms | 20           | 80             | 160                         | 337%                  | 2000%                   | 600                   | 203                 | 4860           | 32                                     | 10800/125                                 | 4320 / 50                                 | 8  |

<sup>1</sup> B1ls tem suporte apenas no Linux

## <a name="dsv3-series-sup1sup"></a>Série Dsv3 <sup>1</sup>

ACU: 160-190

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

Os tamanhos da série Dsv3 são baseados no processador Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz ou no processador mais recente de 2,3 GHz Intel XEON® E5-2673 V4 (Broadwell) que pode atingir 3,5 GHz com a tecnologia Intel Turbo Boost 2,0 e usar o armazenamento Premium. Os tamanhos da série Dsv3 oferecem uma combinação de vCPU, memória e armazenamento temporário para a maioria das cargas de trabalho de produção.


| Size             | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo de armazenamento temporário e em cache: IOPS/MBps (tamanho do cache em GiB) | Débito máximo de disco não colocado em cache: IOPS/MBps | Máximo de NICs/largura de banda de rede esperada (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_D2s_v3  | 2      | 8           | 16             | 4              | 4000/32 (50)                                                       | 3200 / 48                                | 2 / 1000                                   |
| Standard_D4s_v3  | 4      | 16          | 32             | 8              | 8000/64 (100)                                                      | 6400 / 96                                | 2 / 2000                                   |
| Standard_D8s_v3  | 8      | 32          | 64             | 16             | 16000 / 128 (200)                                                    | 12800 / 192                              | 4 / 4000                                      |
| Standard_D16s_v3 | 16     | 64          | 128            | 32             | 32000/256 (400)                                                    | 25600 / 384                              | 8 / 8000                                      |
| Standard_D32s_v3 | 32     | 128          | 256            | 32             | 64000 / 512 (800)                                                    | 51200 / 768                              | 8 / 16000                                               |
| Standard_D48s_v3 | 48     | 192          | 384            | 32             | 96000/768 (1200)                                                    | 76800 / 1152                               | 8 / 24000                                               |
| Standard_D64s_v3 | 64     | 256          | 512            | 32             | 128000 / 1024 (1600)                                                    | 80000 / 1200                              | 8 / 30000                                               |

<sup>1</sup> o recurso da VM da série Dsv3 Intel® tecnologia Hyper-Threading

## <a name="dasv3-series-preview"></a>Série Dasv3 (versão prévia)

Armazenamento Premium: Suportadas

Cache de armazenamento Premium: Suportadas

Os tamanhos da série Dasv3 baseiam-se no processador AMD EPYC<sup>TM</sup> 7452 de 2.35 GHz que pode alcançar um Fmax aumentado de 3.35 GHz e usar o armazenamento Premium. Os tamanhos da série Dasv3 oferecem uma combinação de vCPU, memória e armazenamento temporário para a maioria das cargas de trabalho de produção.

[Clique aqui para se inscrever para a versão prévia](http://aka.ms/azureamdpreview).

| Size | vCPU | Memória: GiB | Armazenamento temporário (SSD): GiB |
|---|---|---|---|
| Standard_D2as_v3  | 2  | 8   | 16  |
| Standard_D4as_v3  | 4  | 16  | 32  |
| Standard_D8as_v3  | 8  | 32  | 64  |
| Standard_D16as_v3 | 16 | 64  | 128 |
| Standard_D32as_v3 | 32 | 128 | 256 |
| Standard_D48as_v3 | 48 | 192 | 384 |
| Standard_D64as_v3 | 64 | 256 | 512 |

## <a name="dv3-series-sup1sup"></a>Série Dv3 <sup>1</sup>

ACU: 160-190

Armazenamento Premium:  Não Suportada

Cache de armazenamento Premium:  Não Suportada

Os tamanhos da série Dv3 são baseados no processador Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz ou no processador 2,3 GHz Intel XEON® E5-2673 V4 (Broadwell) que pode atingir 3,5 GHz com a tecnologia Intel Turbo Boost 2,0. Os tamanhos da série Dv3 oferecem uma combinação de vCPU, memória e armazenamento temporário para a maioria das cargas de trabalho de produção.

O armazenamento de discos de dados são cobrados em separado das máquinas virtuais. Para utilizar os discos de armazenamento premium, utilize os tamanhos Dsv3. Os medidores de preços e de faturação dos tamanhos Dsv3 são os mesmos do que os da série Dv3. 


| Tamanho            | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Taxa de transferência máxima do armazenamento temporário: IOPS/MBps de leitura/MBps de gravação | NICs/Largura de banda da rede máximos |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_D2_v3  | 2         | 8           | 50             | 4              | 3000/46/23                                               | 2 / 1000                    |
| Standard_D4_v3  | 4         | 16          | 100            | 8              | 6000/93/46                                               | 2 / 2000                    |
| Standard_D8_v3  | 8         | 32          | 200            | 16             | 12000/187/93                                             | 4 / 4000                    |
| Standard_D16_v3 | 16        | 64          | 400            | 32             | 24000/375/187                                            | 8 / 8000                    |
| Standard_D32_v3 | 32        | 128         | 800            | 32             | 48000/750/375                                            | 8 / 16000                   |
| Standard_D48_v3 | 48        | 192          | 1200            | 32             | 96000/1000/500                                            | 8 / 24000                             |
| Standard_D64_v3 | 64        | 256         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000                   |

<sup>1</sup> o recurso da VM da série Dv3 Intel® tecnologia Hyper-Threading

## <a name="dav3-series-preview"></a>Série Dav3 (versão prévia)

Armazenamento Premium: Não Suportada

Cache de armazenamento Premium: Não Suportada

Os tamanhos da série Dav3 são baseados no processador AMD EPYC<sup>TM</sup> 7452 de 2.35 GHz que pode alcançar um Fmax aumentado de 3.35 GHz. Os tamanhos da série Dav3 oferecem uma combinação de vCPU, memória e armazenamento temporário para a maioria das cargas de trabalho de produção. O armazenamento de discos de dados são cobrados em separado das máquinas virtuais. Para usar discos de armazenamento Premium, use os tamanhos de Dasv3. Os medidores de cobrança e preço para os tamanhos de Dasv3 são os mesmos que os da série Dav3.

[Clique aqui para se inscrever para a versão prévia](http://aka.ms/azureamdpreview).

| Size | vCPU | Memória: GiB | Armazenamento temporário (SSD): GiB |
|---|---|---|---|
| Standard_D2a_v3  | 2  | 8   | 50   |
| Standard_D4a_v3  | 4  | 16  | 100  |
| Standard_D8a_v3  | 8  | 32  | 200  |
| Standard_D16a_v3 | 16 | 64  | 400  |
| Standard_D32a_v3 | 32 | 128 | 800  |
| Standard_D48a_v3 | 48 | 192 | 1200 |
| Standard_D64a_v3 | 64 | 256 | 1600 |

## <a name="dsv2-series"></a>Série DSv2

ACU: 210-250

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

| Size | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo de armazenamento temporário e em cache: IOPS/MBps (tamanho do cache em GiB) | Débito máximo de disco não colocado em cache: IOPS/MBps | Máximo de NICs/largura de banda de rede esperada (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3.5 |7 |4 |4000/32 (43) |3200 / 48 |2 / 750 |
| Standard_DS2_v2 |2 |7 |14 |8 |8000/64 (86) |6400 / 96 |2 / 1500 |
| Standard_DS3_v2 |4 |14 |28 |16 |16000 / 128 (172) |12800 / 192 |4 / 3000 |
| Standard_DS4_v2 |8 |28 |56 |32 |32000/256 (344) |25600 / 384 |8 / 6000 |
| Standard_DS5_v2 |16 |56 |112 |64 |64000 / 512 (688) |51200 / 768 |8 / 12000 |

## <a name="dv2-series"></a>Série Dv2

ACU: 210-250

Armazenamento Premium:  Não Suportada

Cache de armazenamento Premium:  Não Suportada

| Size           | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Taxa de transferência máxima do armazenamento temporário: IOPS/MBps de leitura/MBps de gravação | Discos de dados máximos | Taxa IOPS | Máximo de NICs/largura de banda de rede esperada (Mbps) |
|----------------|------|-------------|------------------------|------------------------------------------------------------|----------------|------------------|----------------------------------------------|
| Standard_D1_v2 | 1    | 3.5         | 50                     | 3000 / 46 / 23                                             | 4              | 4x500            | 2 / 750                                      |
| Standard_D2_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 8              | 8x500            | 2 / 1500                                     |
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16             | 16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32             | 32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64             | 64x500           | 8 / 12000                                    |

## <a name="av2-series"></a>Série Av2

ACU: 100

Armazenamento Premium:  Não Suportada

Cache de armazenamento Premium:  Não Suportada


| Size            | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Taxa de transferência máxima do armazenamento temporário: IOPS/MBps de leitura/MBps de gravação | Máximo de discos de dados/taxa de transferência: IOPS | Máximo de NICs/largura de banda de rede esperada (Mbps) | 
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2x500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4x500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8x500               | 4 / 1000                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8000 / 160 / 80                                          | 16 / 16x500             | 8 / 2000                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2000 / 40 / 20                                           | 4 / 4x500               | 2 / 500                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4000 / 80 / 40                                           | 8 / 8x500               | 4 / 1000                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8000 / 160 / 80                                          | 16 / 16x500             | 8 / 2000                     |

## <a name="dc-series"></a>Série DC

Armazenamento Premium: Suportadas

Cache de armazenamento Premium: Suportadas



| Size          | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo de armazenamento temporário e em cache: IOPS/MBps (tamanho do cache em GiB) | Débito máximo de disco não colocado em cache: IOPS/MBps | Máximo de NICs/largura de banda de rede esperada (Mbps) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                          | 3200 /48                                  | 2 / 1500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                          | 6400 /96                                  | 2 / 3000                                     |
