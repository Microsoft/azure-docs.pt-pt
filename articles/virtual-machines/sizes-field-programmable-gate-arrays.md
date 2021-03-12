---
title: Tamanhos de máquina virtual azul para matrizes de portão programáveis em campo (FPGA)
description: Lista os diferentes tamanhos otimizados da FPGA disponíveis para máquinas virtuais em Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como o armazenamento e largura de banda de rede para tamanhos nesta série.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-fpga
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: vikancha
ms.openlocfilehash: d9eb0d5bc93cbe9c2a7cbae56c336115bb227b04
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557680"
---
# <a name="fpga-optimized-virtual-machine-sizes"></a>FPGA otimizado tamanhos de máquinas virtuais

Os tamanhos VM otimizados da FPGA são máquinas virtuais especializadas disponíveis com FPGAs individuais ou múltiplas. Estes tamanhos são projetados para cargas de trabalho intensivos de computação. Este artigo fornece informações sobre o número e tipo de FPGAs, vCPUs, discos de dados e NICs. A produção de armazenamento e a largura de banda da rede também estão incluídas para cada tamanho neste agrupamento.

- Os tamanhos [da série NP](np-series.md) são otimizados para cargas de trabalho, incluindo inferência de aprendizagem automática, transcoding de vídeo e pesquisa de bases de dados & análise. A série NP é alimentada por aceleradores Xilinx U250.


## <a name="deployment-considerations"></a>Considerações sobre implementação

- Para obter disponibilidade de VMs da série N, consulte [produtos disponíveis por região.](https://azure.microsoft.com/regions/services/)

- Os VMs da série N só podem ser implantados no modelo de implementação do Gestor de Recursos.

- Se quiser implementar mais do que alguns VMs da série N, considere uma subscrição pay-as-you-go ou outras opções de compra. Se estiver a utilizar uma [conta gratuita do Azure](https://azure.microsoft.com/free/), pode utilizar apenas um número limitado de núcleos de computação do Azure.

- Você pode precisar aumentar a quota de núcleos (por região) na sua assinatura Azure, e aumentar a quota separada para núcleos NP. Para solicitar um aumento de quota, [abra gratuitamente um pedido de apoio](../azure-portal/supportability/how-to-create-azure-support-request.md) ao cliente online. Os limites predefinidos podem variar dependendo da sua categoria de subscrição.

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de computação](sizes-compute.md)
- [GPU acelerou computação](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
