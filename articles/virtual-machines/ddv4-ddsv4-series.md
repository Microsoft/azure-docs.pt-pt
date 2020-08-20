---
title: Séries Ddv4 e Ddsv4
description: Especificações para os VMs da série Dv4, Ddv4, Dsv4 e Ddsv4.
author: brbell
ms.author: brbell
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 3c51a00e5760a2dac1648cc938416625bd52ddb7
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88649912"
---
# <a name="ddv4-and-ddsv4-series"></a>Séries Ddv4 e Ddsv4

A série Ddv4 e Ddsv4 funciona nos processadores Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) numa configuração hiper roscada, proporcionando uma proposta de melhor valor para a maioria das cargas de trabalho de uso geral. Apresenta uma velocidade do relógio Turbo de 3,4 GHz, [Intel Turbo Boost Technology &reg; 2.0,](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html) [Intel &reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) e Intel Advanced Vetor [ &reg; Extensions 512 (Intel &reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Também apoiam [o Intel Deep Learning &reg; Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Estes novos tamanhos VM terão 50% maior armazenamento local, bem como melhores IOPS de disco local para leitura e escrita em comparação com os [tamanhos Dv3/Dsv3](./dv3-dsv3-series.md) com [VMs Gen2.](./linux/generation-2.md)

Os casos de utilização em série D incluem aplicações de nível empresarial, bases de dados relacionais, caching na memória e análise.

## <a name="ddv4-series"></a>Série Ddv4

Os tamanhos da série Ddv4 são executados no Intel &reg; Xeon &reg; Platinum 8272CL (Lago cascata). A série Ddv4 oferece uma combinação de vCPU, memória e disco temporário para a maioria das cargas de produção.

Os novos tamanhos Ddv4 VM incluem armazenamento SSD local rápido e maior (até 2.400 GiB) e são projetados para aplicações que beneficiam de baixa latência, armazenamento local de alta velocidade, como aplicações que requerem leituras rápidas/escritas para armazenamento temporário ou que precisam de armazenamento temporário para caches ou arquivos temporários. Pode anexar SSDs Standard e armazenamento standard de HDDs aos Ddv4 VMs. O armazenamento remoto do disco de dados é faturado separadamente das máquinas virtuais.

ACU: 195-210

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

Migração ao Vivo: Suportado

Atualizações de preservação da memória: Suportadas

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Produção de armazenamento em cache máximo e temporário: IOPS/MBps | NICs máximos|Largura de banda esperada da rede (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2d_v4 | 2 | 8 | 75 | 4 | 19000/120 | 2|1000 |
| Standard_D4d_v4 | 4 | 16 | 150 | 8 | 38500/242 | 2|2000 |
| Standard_D8d_v4 | 8 | 32 | 300 | 16 | 77000/485 | 4|4000 |
| Standard_D16d_v4 | 16 | 64 | 600 | 32 | 154000/968 | 8|8000 |
| Standard_D32d_v4 | 32 | 128 | 1200 | 32 | 308000/1936 | 8|16000 |
| Standard_D48d_v4 | 48 | 192 | 1800 | 32 | 462000/2904 | 8|24000 |
| Standard_D64d_v4 | 64 | 256 | 2400 | 32 | 615000/3872 | 8|30000 |

## <a name="ddsv4-series"></a>Série Ddsv4

Série Ddsv4 corre no Intel &reg; Xeon &reg; Platinum 8272CL (Lago cascata). A série Ddsv4 oferece uma combinação de vCPU, memória e disco temporário para a maioria das cargas de produção.

Os novos tamanhos Ddsv4 VM incluem armazenamento SSD local rápido e maior (até 2.400 GiB) e são projetados para aplicações que beneficiam de baixa latência, armazenamento local de alta velocidade, como aplicações que requerem leituras rápidas/escritas para armazenamento temporário ou que precisam de armazenamento temporário para caches ou arquivos temporários. 

 > [!NOTE]
 >Os preços e os contadores de faturação para tamanhos Ddsv4 são os mesmos que a série Ddv4.

ACU: 195-210

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

Migração ao Vivo: Suportado

Atualizações de preservação da memória: Suportadas

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Produção de armazenamento em cache máximo e temporário: IOPS/MBps (tamanho da cache em GiB) | Produção de disco não-abacatado por maxilar: IOPS/MBps | NICs máximos|Largura de banda esperada da rede (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2ds_v4 | 2 | 8 | 75 | 4 | 19000/120(50) | 3200/48 | 2|1000 |
| Standard_D4ds_v4 | 4 | 16 | 150 | 8 | 38500/242(100) | 6400/96 | 2|2000 |
| Standard_D8ds_v4 | 8 | 32 | 300 | 16 | 77000/485(200) | 12800/192 | 4|4000 |
| Standard_D16ds_v4 | 16 | 64 | 600 | 32 | 154000/968(400) | 25600/384 | 8|8000 |
| Standard_D32ds_v4 | 32 | 128 | 1200 | 32 | 308000/1936(800) | 51200/768 | 8|16000 |
| Standard_D48ds_v4 | 48 | 192 | 1800 | 32 | 462000/2904(1200) | 76800/1152 | 8|24000 |
| Standard_D64ds_v4 | 64 | 256 | 2400 | 32 | 615000/3872(1600) | 80000/1200 | 8|30000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Outros tamanhos e informações

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

Calculadora de preços: [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/)

Mais informações sobre tipos de discos : [tipos de discos](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
