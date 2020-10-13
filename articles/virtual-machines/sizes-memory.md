---
title: Tamanhos Azure VM - Memória / Microsoft Docs
description: Lista os diferentes tamanhos otimizados de memória disponíveis para máquinas virtuais em Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a produção de armazenamento e largura de banda de rede para tamanhos nesta série.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
keywords: VM isolamento,VM isolado,isolamento,isolado
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: ea58eeac5a77f2e1eaf2a7756de6d36a3fc0626b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87825502"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>Tamanhos de máquina virtual otimizados de memória

Os tamanhos de VM otimizados pela memória oferecem uma relação memória-CPU alta que é ótima para servidores de base de dados relacionais, caches médios a grandes e análises na memória. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como a produção de armazenamento e largura de banda de rede para cada tamanho neste agrupamento.

- [Dv2 e série DSv2](dv2-dsv2-series-memory.md), uma continuação da série D original, apresenta um CPU mais poderoso. A série Dv2 é cerca de 35% mais rápida que a série D. Funciona no Intel &reg; Xeon &reg; 8171M 2.1 GHz (Skylake) ou nos processadores Intel &reg; Xeon &reg; E5-2673 v4 2.3 GHz (Broadwell) ou nos processadores Intel &reg; Xeon &reg; E5-2673 v3 2.4 GHz (Haswell) e com o Intel Turbo Boost Technology 2.0. A série Dv2 tem as mesmas configurações de memória e disco da série D.

    As séries Dv2 e DSv2 são ideais para aplicações que exigem vCPUs mais rápidos, melhor desempenho temporário de armazenamento ou têm exigências de memória mais elevadas. Proporcionam uma combinação poderosa para inúmeras aplicações empresariais.

- A [série Eav4 e Easv4](eav4-easv4-series.md) utilizam o processador EPYC<sup>TM</sup> 7452 da AMD 2.35Ghz numa configuração multi-roscada com até 256MB de cache L3, aumentando as opções para executar a maioria das cargas de trabalho otimizadas pela memória. As séries Eav4 e Easv4 têm as mesmas configurações de memória e disco que a série Ev3 & Esv3.

- O processador [Intel](ev3-esv3-series.md) &reg; Xeon &reg; 8171M 2.1 GHz (Skylake) ou o processador Intel &reg; Xeon &reg; E5-2673 v4 2.3 GHz (Broadwell) numa configuração hiper-roscada, proporcionando uma melhor proposta de valor para a maioria das cargas de trabalho para fins gerais, e colocando o Ev3 em alinhamento com o propósito geral VMs da maioria das outras nuvens. A memória foi expandida (de 7 GiB/vCPU para 8 GiB/vCPU) enquanto os limites de disco e rede foram ajustados numa base por núcleo para alinhar com a mudança para o hiper-rosco. O Ev3 é o seguimento dos tamanhos VM de alta memória das famílias D/Dv2.

- A [série Ev4 e Esv4](ev4-esv4-series.md) é executado em processadores Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) de 2ª Geração numa configuração hiper roscada, são ideais para várias aplicações empresariais intensivas de memória e apresentam até 504 GiB de RAM. Conta com a [Intel Turbo Boost Technology &reg; 2.0,](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html) [Intel Hyper-Threading &reg; Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) e [Intel Advanced Vetor &reg; Extensions 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). As séries Ev4 e Esv4 não incluem um disco temporário local. Para obter mais informações, consulte os [tamanhos Azure VM sem disco temporário local.](azure-vms-no-temp-disk.md)

- A [série Edv4 e Edsv4](edv4-edsv4-series.md) funciona em processadores Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) de 2ª Geração, ideais para bases de dados extremamente grandes ou outras aplicações que beneficiam de altas contagens de VCPU e grandes quantidades de memória. Além disso, estes tamanhos VM incluem armazenamento SSD local rápido e maior para aplicações que beneficiam de baixa latência, armazenamento local de alta velocidade. Apresenta uma velocidade do relógio Turbo de 3,4 GHz, [Intel Turbo Boost Technology &reg; 2.0,](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html) [Intel Hyper-Threading &reg; Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) e [Extensões de Vetores Avançados Intel &reg; 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html).

- A [série M](m-series.md) oferece uma alta contagem de VCPU (até 128 vCPUs) e uma grande quantidade de memória (até 3,8 TiB). Também é ideal para bases de dados extremamente grandes ou outras aplicações que beneficiam de altas contagens de vCPU e grandes quantidades de memória.

- A [série Mv2](mv2-series.md) oferece a maior contagem de VCPU (até 416 vCPUs) e a maior memória (até 11,4 TiB) de qualquer VM na nuvem. É ideal para bases de dados extremamente grandes ou outras aplicações que beneficiam de altas contagens de vCPU e grandes quantidades de memória.

O Azure Compute oferece tamanhos de máquinas virtuais que são isolados a um tipo de hardware específico e dedicados a um único cliente. Estes tamanhos de máquina virtuais são mais adequados para cargas de trabalho que requerem um alto grau de isolamento de outros clientes para cargas de trabalho envolvendo elementos como conformidade e requisitos regulamentares. Os clientes também podem optar por subdividir ainda mais os recursos destas máquinas virtuais isoladas utilizando [o suporte Azure para máquinas virtuais aninhadas.](https://azure.microsoft.com/blog/nested-virtualization-in-azure/) Veja as páginas para famílias de máquinas virtuais abaixo para as suas opções de VM isoladas.

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de computação](sizes-compute.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.

Para obter mais informações sobre como a Azure dá nome aos seus VMs, consulte [o tamanho da máquina virtual Azure que nomeia convenções.](./vm-naming-conventions.md)