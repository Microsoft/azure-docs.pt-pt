---
title: Tamanhos Azure VM - | de finalidade geral Microsoft Docs
description: Lista os diferentes tamanhos de finalidade geral disponíveis para máquinas virtuais em Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a produção de armazenamento e largura de banda de rede para tamanhos nesta série.
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.devlang: na
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: mimckitt
ms.openlocfilehash: 46a9b7e630001161fbf2790cfb9be474379864b6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557629"
---
# <a name="general-purpose-virtual-machine-sizes"></a>Tamanhos de máquina virtual de propósito geral

Os tamanhos VM de finalidade geral proporcionam uma relação CPU-memória equilibrada. Ideais para testes e desenvolvimento, bases de dados pequenas a médias e servidores Web de tráfego baixo a médio. Este artigo fornece informações sobre as ofertas para a computação para fins gerais.

- Os VMs [da série Av2](av2-series.md) podem ser implantados numa variedade de tipos e processadores de hardware. Os VMs da série A têm configurações de desempenho e memória de CPU mais adequadas para cargas de trabalho de nível de entrada como desenvolvimento e teste. O tamanho é limitado com base no hardware de forma a proporcionar um desempenho de processador consistente para a instância em execução, independentemente do hardware no qual está implementado. Para determinar o hardware físico no qual este tamanho está implementado, consulte o hardware virtual a partir da Máquina Virtual. Os casos de uso de exemplo incluem servidores de desenvolvimento e teste, servidores web de baixo tráfego, bases de dados pequenas a médias, prova de conceitos e repositórios de código.

  > [!NOTE]
  > A A8, A9, A10 A11 VMs estão previstas para a reforma em 3/2021. Para mais informações, consulte [o Guia de Migração do HPC.](https://azure.microsoft.com/resources/hpc-migration-guide/) Estes tamanhos VM estão na série original "A_v1", NÃO "v2".

- [Rebentamento da série B](sizes-b-series-burstable.md) Os VMs são ideais para cargas de trabalho que não necessitam de um desempenho completo do CPU continuamente, como servidores web, pequenas bases de dados e ambientes de desenvolvimento e teste. Estas cargas de trabalho normalmente têm requisitos de desempenho rebeníveis. A Série B proporciona a estes clientes a capacidade de adquirir um tamanho VM com um desempenho de base consciente do preço que permite à instância VM acumular créditos quando o VM está a utilizar menos do que o seu desempenho base. Quando o VM tiver crédito acumulado, o VM pode rebentar acima da linha de base do VM utilizando até 100% do CPU quando a sua aplicação requer um desempenho de CPU mais elevado.

- [Dav4 e Dasv4-series](dav4-dasv4-series.md) são novos tamanhos que usando o processador EPYC TM 7452 da AMD 2.35Ghz<sup>EPYC TM</sup> 7452 numa configuração multi-roscada com até 256 cache L3 MB dedicando 8 MB dessa cache L3 a cada 8 núcleos aumentando as opções de clientes para executar as suas cargas de trabalho para fins gerais. As séries Dav4 e Dasv4 têm as mesmas configurações de memória e disco que a série D & Dsv3.

- [Série Dv4 e Dsv4](dv4-dsv4-series.md) A série Dv4 e Dsv4 funciona nos processadores Intel® Xeon® Platinum 8272CL (Cascade Lake) numa configuração hiper roscada, proporcionando uma melhor proposta de valor para a maioria das cargas de trabalho de uso geral. Apresenta uma velocidade do relógio Turbo de 3,4 GHz.

- [Série Ddv4 e Ddsv4](ddv4-ddsv4-series.md) A série Ddv4 e Ddsv4 funciona nos processadores Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) numa configuração hiper roscada, proporcionando uma proposta de melhor valor para a maioria das cargas de trabalho de uso geral. Apresenta uma velocidade máxima do relógio Turbo de 3,4 GHz, [Intel Turbo Boost Technology &reg; 2.0,](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html) [Intel Hyper-Threading &reg; Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) e [Extensões de Vetores Avançados Intel &reg; 512 (Intel &reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Também apoiam [o Intel Deep Learning &reg; Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Estes novos tamanhos VM terão 50% maior armazenamento local, bem como melhores IOPS de disco local para leitura e escrita em comparação com os [tamanhos Dv3/Dsv3](./dv3-dsv3-series.md) com [VMs Gen2.](./generation-2.md)

- [Série Dv3 e Dsv3](dv3-dsv3-series.md) Os VMs funcionam na 2ª Geração Intel® Xeon® Platinum 8272CL (Lago cascade), Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2 673 v4 2.3 GHz (Broadwell), ou os processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) numa configuração hiper roscada, proporcionando uma melhor proposta de valor para a maioria das cargas de trabalho para fins gerais. A memória foi expandida (de ~3.5 GiB/vCPU para 4 GiB/vCPU) enquanto os limites de disco e rede foram ajustados numa base por núcleo para alinhar com a mudança para a hipertonagem. A série Dv3 já não tem os tamanhos VM de alta memória da série D/Dv2, estas foram movidas para a memória otimizada [série Ev3 e Esv3](ev3-esv3-series.md).

- [Série Dv2 e Dsv2](dv2-dsv2-series.md) Os VMs, um seguimento à série D original, apresenta um CPU mais potente e uma configuração ótima de CPU-para-memória, tornando-os adequados para a maioria das cargas de trabalho de produção. A série Dv2 é cerca de 35% mais rápida que a série D. Série dv2 executada na 2ª Geração Intel® Xeon® Platinum 8272CL (Lago de Cascade), Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), ou os processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) com os processadores Intel Turbo Boost Technology 2.0. A série Dv2 tem as mesmas configurações de memória e disco da série D.

- A [série DCv2](dcv2-series.md) pode ajudar a proteger a confidencialidade e integridade dos seus dados e código enquanto é processado na nuvem pública. Estas máquinas são apoiadas pela última geração do Processador Intel XEON E-2288G com tecnologia SGX. Com a Tecnologia Intel Turbo Boost estas máquinas podem ir até 5.0GHz. As instâncias da série DCv2 permitem que os clientes construam aplicações seguras baseadas em enclaves para proteger o seu código e dados enquanto estão em uso.

## <a name="other-sizes"></a>Outros tamanhos

- [Com otimização de computação](sizes-compute.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Otimizada para GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.

Para obter mais informações sobre como a Azure dá nome aos seus VMs, consulte [o tamanho da máquina virtual Azure que nomeia convenções.](./vm-naming-conventions.md)
