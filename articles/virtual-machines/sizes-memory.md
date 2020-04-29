---
title: Tamanhos De VM Azure - Memória / Microsoft Docs
description: Lista os diferentes tamanhos otimizados de memória disponíveis para máquinas virtuais em Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a entrada de armazenamento e largura de banda da rede para tamanhos nesta série.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
keywords: Isolamento VM,VM isolado,isolamento,isolado
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: 5a20e9c64b6ef948167333b54b16b34e84dc0e32
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81273584"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>Tamanhos otimizados da memória

Os tamanhos de VM otimizados pela memória oferecem uma elevada relação memória-CPU que é ótimo para servidores de base de dados relacionais, caches médios a grandes e análise sin-memory. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como a produção de armazenamento e largura de banda da rede para cada tamanho neste agrupamento.

- [A série Dv2 e DSv2,](dv2-dsv2-series-memory.md)uma continuação da série D original, apresenta um CPU mais poderoso. A série Dv2 é cerca de 35% mais rápida que a série D. Funciona nos processadores&reg; Intel&reg; Xeon 8171M 2.1 GHz&reg; (Skylake) ou Intel Xeon&reg; E5-2673 v4&reg; 2.3 GHz (Broadwell) ou na Intel Xeon&reg; E5-2673 v3 2.4 GHz (Haswell) e com a Intel Turbo Boost Technology 2.0. A série Dv2 tem as mesmas configurações de memória e disco da série D.

    As séries Dv2 e DSv2 são ideais para aplicações que exigem vCPUs mais rápidos, melhor desempenho temporário de armazenamento ou têm maiores exigências de memória. Proporcionam uma combinação poderosa para inúmeras aplicações empresariais.

- As [séries Eav4 e Easv4](eav4-easv4-series.md) utilizam o processador EPYC<sup>TM</sup> 7452 da AMD em uma configuração multi-roscada com até 256MB de cache L3, aumentando as opções para executar a maioria das cargas de trabalho otimizadas pela memória. As séries Eav4 e Easv4 têm as mesmas configurações de memória e disco que o Ev3 & série Esv3.

- O processador [Intel](ev3-esv3-series.md) &reg; Xeon&reg; 8171M 2.1 GHz (Skylake) ou&reg; o processador&reg; Intel Xeon E5-2673 v4 2.3 GHz (Broadwell) numa configuração hiper-roscada, proporcionando uma proposta de melhor valor para a maioria das cargas de trabalho para fins gerais, e colocando o Ev3 em alinhamento com os VMs de finalidade geral da maioria das outras nuvens. A memória foi expandida (de 7 GiB/vCPU para 8 GiB/vCPU) enquanto os limites do disco e da rede foram ajustados numa base de núcleo para alinhar com a mudança para hiper-rosca. O Ev3 é o seguimento dos tamanhos vm de alta memória das famílias D/Dv2.

- A [série M](m-series.md) oferece uma alta contagem de vCPU (até 128 vCPUs) e uma grande quantidade de memória (até 3,8 TiB). Também é ideal para bases de dados extremamente grandes ou outras aplicações que beneficiam de altas contagens de vCPU e grandes quantidades de memória.

- A [série Mv2](mv2-series.md) oferece a contagem de VCPU mais alta (até 416 vCPUs) e a maior memória (até 11,4 TiB) de qualquer VM na nuvem. É ideal para bases de dados extremamente grandes ou outras aplicações que beneficiam de altas contagens de vCPU e grandes quantidades de memória.

A Azure Compute oferece tamanhos de máquinas virtuais que estão isolados a um tipo de hardware específico e dedicados a um único cliente. Estes tamanhos de máquinas virtuais são mais adequados para cargas de trabalho que requerem um elevado grau de isolamento de outros clientes para cargas de trabalho envolvendo elementos como conformidade e requisitos regulamentares. Os clientes também podem optar por subdividir ainda mais os recursos destas máquinas virtuais isoladas utilizando suporte [Azure para máquinas virtuais aninhadas.](https://azure.microsoft.com/blog/nested-virtualization-in-azure/) Consulte as páginas para famílias de máquinas virtuais abaixo para obter as suas opções de VM isoladas.

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de computação](sizes-compute.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como as unidades de [computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho da computação em Azure SKUs.
