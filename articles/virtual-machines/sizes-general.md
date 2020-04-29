---
title: Tamanhos De VM Azure - Propósito geral Microsoft Docs
description: Lista os diferentes tamanhos de fim geral disponíveis para máquinas virtuais em Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a entrada de armazenamento e largura de banda da rede para tamanhos nesta série.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: mimckitt
ms.openlocfilehash: fc263eb6fbe6c6402aaf529229bb7025f070b8d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81269674"
---
# <a name="general-purpose-virtual-machine-sizes"></a>Tamanhos de máquina virtual de propósito geral

Os tamanhos vm de propósito geral fornecem uma relação CPU-memória equilibrada. Ideais para testes e programação, bases de dados pequenas a médias e servidores Web de tráfego baixo a médio. Este artigo fornece informações sobre as ofertas para a computação geral.

- Os VMs [da série Av2](av2-series.md) podem ser implantados numa variedade de tipos de hardware e processadores. Os VMs da série A têm configurações de desempenho de CPU e memória mais adequadas para cargas de trabalho de nível de entrada como desenvolvimento e teste. O tamanho é limitado com base no hardware de forma a proporcionar um desempenho de processador consistente para a instância em execução, independentemente do hardware no qual está implementado. Para determinar o hardware físico no qual este tamanho está implementado, consulte o hardware virtual a partir da Máquina Virtual. Os casos de utilização por exemplo incluem servidores de desenvolvimento e teste, servidores web de tráfego baixo, bases de dados pequenas a médias, provas de conceitos e repositórios de código.

  > [!NOTE]
  > Os A8 – A11 VMs estão previstos para a reforma em 3/2021. Para mais informações, consulte o [Guia de Migração do HPC.](https://azure.microsoft.com/resources/hpc-migration-guide/)

- [Série B rebenta](sizes-b-series-burstable.md) Os VMs são ideais para cargas de trabalho que não necessitam de todo o desempenho do CPU continuamente, como servidores web, pequenas bases de dados e ambientes de desenvolvimento e teste. Estas cargas de trabalho normalmente têm requisitos de desempenho insuportáveis. A Série B oferece a estes clientes a capacidade de adquirir um tamanho VM com um desempenho de base consciente do preço que permite que a instância VM aumente os créditos quando o VM está a utilizar menos do que o seu desempenho base. Quando o VM tiver crédito acumulado, o VM pode rebentar acima da linha de base do VM utilizando até 100% do CPU quando a sua aplicação requer o desempenho mais elevado do CPU.

- [As séries Dav4 e Dasv4](dav4-dasv4-series.md) são novos tamanhos utilizando o processador 2.35Ghz EPYC<sup>TM</sup> 7452 da AMD numa configuração multi-roscada com até 256 MB L3 cache dedicando 8 MB dessa cache L3 a cada 8 núcleos aumentando as opções dos clientes para executar as suas cargas de trabalho para fins gerais. As séries Dav4 e Dasv4 têm as mesmas configurações de memória e disco que a série D & Dsv3.

- A [série DCv2](dcv2-series.md) pode ajudar a proteger a confidencialidade e integridade dos seus dados e código enquanto é processado na nuvem pública. Estas máquinas são apoiadas pela mais recente geração do processador Intel XEON E-2288G com tecnologia SGX. Com a Tecnologia Intel Turbo Boost estas máquinas podem ir até 5.0GHz. As instâncias da série DCv2 permitem que os clientes construam aplicações seguras baseadas no enclave para proteger o seu código e dados enquanto estão em uso.

- [Série Dv2 e Dsv2](dv2-dsv2-series.md) Os VMs, um seguimento à série D original, apresenta um CPU mais potente e uma configuração ideal cpu-to-memória tornando-os adequados para a maioria das cargas de trabalho de produção. A série Dv2 é cerca de 35% mais rápida que a série D. As séries Dv2 correm nos intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), ou os processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) com os processadores Intel Turbo Boost Technology 2.0. A série Dv2 tem as mesmas configurações de memória e disco da série D.

- [Série Dv3 e Dsv3](dv3-dsv3-series.md) Os VMs funcionam nos processadores Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), ou nos processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) numa configuração hiper-roscada, proporcionando um melhor valor para a maioria das cargas de trabalho gerais. A memória foi expandida (de ~3.5 GiB/vCPU para 4 GiB/vCPU) enquanto os limites do disco e da rede foram ajustados numa base de núcleo para alinhar com o movimento para o hiperthreading. A série Dv3 já não tem os tamanhos VM de alta memória da série D/Dv2, estes foram movidos para a memória otimizada [ev3 e série Esv3.](ev3-esv3-series.md)

Exemplo, os casos de utilização da série D incluem aplicações de nível empresarial, bases de dados relacionais, caches na memória e análise.

## <a name="other-sizes"></a>Outros tamanhos

- [Com otimização de computação](sizes-compute.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como as unidades de [computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho da computação em Azure SKUs.
