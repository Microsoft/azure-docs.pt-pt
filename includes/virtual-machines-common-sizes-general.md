---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 10/08/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 98e0732ece3d046b7c073ac075b7eae89d36c125
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66391416"
---
Tamanhos de VM de fins gerais oferecem rácio de CPU / memória equilibrado. Ideal para teste e desenvolvimento, bases de dados pequenas a médias e servidores Web com tráfego baixo a médio. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como débito de armazenamento para tamanhos neste agrupamento. 

- O [DC série](#dc-series) é uma nova família de máquinas virtuais no Azure que pode ajudar a proteger a confidencialidade e integridade dos seus dados e código enquanto ele é processado na cloud pública. Estas máquinas assentam na geração mais recente do Processador Intel XEON E-2176G de 3,7 GHz com tecnologia SGX. Com o Intel Turbo Boost Technology, estas máquinas podem chegar aos 4,7 GHz. As instâncias da série DC permitem aos clientes criar aplicações baseadas em enclave seguras para proteger os respetivos dados e código enquanto estão em utilização.

- As VMs da série Av2 podem ser implementadas numa variedade de tipos de hardware e de processadores. As VMs de série A têm as configurações de desempenho e memória da CPU mais adequadas a cargas de trabalho ao nível da entrada como, por exemplo, desenvolvimento e teste. O tamanho é limitado com base no hardware de forma a proporcionar um desempenho de processador consistente para a instância em execução, independentemente do hardware no qual está implementado. Para determinar o hardware físico no qual este tamanho está implementado, consulte o hardware virtual a partir da Máquina Virtual.

  Casos de utilização de exemplo incluem o desenvolvimento e teste servidores, servidores web de tráfego reduzido, pequeno a médios bases de dados, uma prova de conceitos e repositórios de código.

- Série Dv2, seguimento da série de D original, apresenta uma CPU mais poderosa e a configuração de CPU / memória ideal, tornando-os adequados para a maioria das cargas de trabalho de produção. A CPU da série Dv2 é cerca de 35% mais rápida do que a CPU da série D. Baseia-se a mais recente Intel Xeon® E5-2673 v3 da geração 2,4 GHz (Haswell) ou E5 2673 v4 (Broadwell) de 2.3 GHz processadores, com o Intel Turbo Boost Technology 2.0, podendo chegar aos 3,1 GHz. A série Dv2 tem as mesmas configurações de memória e disco da série D.

- A série Dv3 funcionalidades de 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) processador ou de mais recente 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell) de processador numa configuração de hyper-thread, fornecendo uma proposta de valor melhor para cargas de trabalho de fins mais gerais.  Memória foi expandida (a partir de ~3.5 GiB/Vcpus a 4 GiB/vCPU), enquanto os limites de disco e rede foram ajustados numa base por núcleo para alinhar com a mudança para o hyperthreading.  O Dv3 já não tem os tamanhos VM de elevada da memória das famílias D/Dv2, aqueles foram movidos para a nova família Ev3.

  Casos de utilização de série D de exemplo incluem aplicações de nível empresarial, bases de dados relacionais, colocação em cache na memória e análise. 
  
## <a name="b-series"></a>Série B

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Não suportado

As VMs burstable de série B são ideais para cargas de trabalho que não necessita de um desempenho total da CPU continuamente, como servidores web, desenvolvimento e pequenas bases de dados e os ambientes de teste. Estas cargas de trabalho normalmente têm requisitos de desempenho burstable. A série B fornece estes clientes a possibilidade de comprar um tamanho de VM com um desempenho de linha de base consciente de preço que permite que a instância VM acumular créditos quando a VM estiver a utilizar menos do que o desempenho de base. Quando a VM acumular crédito, a VM pode ultrapassar os limites acima de linha de base da VM com até 100% da CPU quando seu aplicativo requer o desempenho de CPU superior.

Casos de utilização de exemplo incluem servidores de desenvolvimento e teste, servidores web de tráfego reduzido, bases de dados pequenas, microsserviços, servidores para prova de conceito, servidores de compilação.


| Tamanho             | vCPU  | Memória: GiB | Armazenamento (SSD) temporário GiB | Base de desempenho de CPU da VM | Desempenho de CPU máxima da VM | Créditos iniciais | Créditos banked / hora | Máx. Banked créditos | Discos de dados máximos | Débito máximo de armazenamento temporário e em cache: IOPS / MBps | Débito máximo de disco não colocado em cache: IOPS / MBps | NICs máximos |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5%                   | 100%                   | 30                   | 3                  | 72            | 2                                      | 200 / 10                                  | 160 / 10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10%                   | 100%                   | 30                   | 6                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 100%                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |

<sup>1</sup> B1ls só é suportado no Linux

## <a name="dsv3-series-sup1sup"></a>Dsv3-series <sup>1</sup>

ACU: 160-190

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

Tamanhos da série Dsv3 baseiam-se de 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) processador ou de mais recente 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell) processador que pode chegar aos 3,5 GHz com o Intel Turbo Boost Technology 2.0 e utilizar o armazenamento premium. Os tamanhos da série Dsv3 oferecem uma combinação de vCPU, memória e armazenamento temporário para a maioria das cargas de trabalho de produção.


| Tamanho             | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo de armazenamento temporário e em cache: IOPS / MBps (tamanho da cache em GiB) | Débito máximo de disco não colocado em cache: IOPS / MBps | NICs. Máx. / esperado de largura de banda de rede (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_D2s_v3  | 2      | 8           | 16             | 4              | 4000 / 32 (50)                                                       | 3200 / 48                                | 2 / 1000                                   |
| Standard_D4s_v3  | 4      | 16          | 32             | 8              | 8000 / 64 (100)                                                      | 6400 / 96                                | 2 / 2000                                   |
| Standard_D8s_v3  | 8      | 32          | 64             | 16             | 16000 / 128 (200)                                                    | 12800 / 192                              | 4 / 4000                                      |
| Standard_D16s_v3 | 16     | 64          | 128            | 32             | 32000 / 256 (400)                                                    | 25600 / 384                              | 8 / 8000                                      |
| Standard_D32s_v3 | 32     | 128          | 256            | 32             | 64000 / 512 (800)                                                    | 51200 / 768                              | 8 / 16000                                               |
| Standard_D64s_v3 | 64     | 256          | 512            | 32             | 128000 / 1024 (1600)                                                    | 80000 / 1200                              | 8 / 30000                                               |

<sup>1</sup> as VMS da série Dsv3 funcionalidade Intel® Hyper-Threading Technology

## <a name="dv3-series-sup1sup"></a>Série Dv3 <sup>1</sup>

ACU: 160-190

Armazenamento Premium:  Não suportado

Cache de armazenamento Premium:  Não suportado

Tamanhos da série Dv3 baseiam-se de 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) processador ou 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell) processador que pode chegar aos 3,5 GHz com o Intel Turbo Boost Technology 2.0. Os tamanhos da série Dv3 oferecem uma combinação de vCPU, memória e armazenamento temporário para a maioria das cargas de trabalho de produção.

O armazenamento de discos de dados são cobrados em separado das máquinas virtuais. Para utilizar os discos de armazenamento premium, utilize os tamanhos Dsv3. Os medidores de preços e de faturação dos tamanhos Dsv3 são os mesmos do que os da série Dv3. 


| Tamanho            | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito do armazenamento temporário máximo: IOPS / MBps de leitura / MBps de escrita | NICs/Largura de banda da rede máximos |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_D2_v3  | 2         | 8           | 50             | 4              | 3000/46/23                                               | 2 / 1000                    |
| Standard_D4_v3  | 4         | 16          | 100            | 8              | 6000/93/46                                               | 2 / 2000                    |
| Standard_D8_v3  | 8         | 32          | 200            | 16             | 12000/187/93                                             | 4 / 4000                    |
| Standard_D16_v3 | 16        | 64          | 400            | 32             | 24000/375/187                                            | 8 / 8000                    |
| Standard_D32_v3 | 32        | 128         | 800            | 32             | 48000/750/375                                            | 8 / 16000                   |
| Standard_D64_v3 | 64        | 256         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000                   |

<sup>1</sup> as VMS da série Dv3 funcionalidade Intel® Hyper-Threading Technology

## <a name="dsv2-series"></a>Série DSv2

ACU: 210-250

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo de armazenamento temporário e em cache: IOPS / MBps (tamanho da cache em GiB) | Débito máximo de disco não colocado em cache: IOPS / MBps | NICs. Máx. / esperado de largura de banda de rede (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3.5 |7 |4 |4000 / 32 (43) |3200 / 48 |2 / 750 |
| Standard_DS2_v2 |2 |7 |14 |8 |8000 / 64 (86) |6400 / 96 |2 / 1500 |
| Standard_DS3_v2 |4 |14 |28 |16 |16000 / 128 (172) |12800 / 192 |4 / 3000 |
| Standard_DS4_v2 |8 |28 |56 |32 |32000 / 256 (344) |25600 / 384 |8 / 6000 |
| Standard_DS5_v2 |16 |56 |112 |64 |64000 / 512 (688) |51200 / 768 |8 / 12000 |

## <a name="dv2-series"></a>Série Dv2

ACU: 210-250

Armazenamento Premium:  Não suportado

Cache de armazenamento Premium:  Não suportado

| Tamanho           | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Débito do armazenamento temporário máximo: IOPS / MBps de leitura / MBps de escrita | Discos de dados máximos | Taxa de transferência: IOPS | NICs. Máx. / esperado de largura de banda de rede (Mbps) |
|----------------|------|-------------|------------------------|------------------------------------------------------------|----------------|------------------|----------------------------------------------|
| Standard_D1_v2 | 1    | 3.5         | 50                     | 3000 / 46 / 23                                             | 4              | 4x500            | 2 / 750                                      |
| Standard_D2_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 8              | 8x500            | 2 / 1500                                     |
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16             | 16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32             | 32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64             | 64x500           | 8 / 12000                                    |

## <a name="av2-series"></a>Série Av2

ACU: 100

Armazenamento Premium:  Não suportado

Cache de armazenamento Premium:  Não suportado


| Tamanho            | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Débito do armazenamento temporário máximo: IOPS / MBps de leitura / MBps de escrita | Discos de dados máximos / débito: IOPS | NICs. Máx. / esperado de largura de banda de rede (Mbps) | 
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



| Tamanho          | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo de armazenamento temporário e em cache: IOPS / MBps (tamanho da cache em GiB) | Débito máximo de disco não colocado em cache: IOPS / MBps | NICs. Máx. / esperado de largura de banda de rede (Mbps) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000 / 32 (43)                                                          | 3200 /48                                  | 2 / 1500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400 /96                                  | 2 / 3000                                     |