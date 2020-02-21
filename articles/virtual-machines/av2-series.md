---
title: Série Av2 - Máquinas Virtuais Azure
description: Especificações para os VMs da série Av2.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 1e14c633ed11bb628a68756e72e78073603643a1
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493855"
---
# <a name="av2-series"></a>Série Av2

Os VMs da série Av2 podem ser implantados numa variedade de tipos de hardware e processadores. Os VMs da série Av2 têm configurações de desempenho cpu e memória mais adequadas para cargas de trabalho de nível de entrada como desenvolvimento e teste. O tamanho é acelerado para oferecer um desempenho consistente do processador para a instância de execução, independentemente do hardware em que é implantado. Para determinar o hardware físico no qual este tamanho está implementado, consulte o hardware virtual a partir da Máquina Virtual. Alguns casos de uso de exemplo incluem servidores de desenvolvimento e teste, servidores web de tráfego baixo, bases de dados pequenas a médias, prova de conceitos e repositórios de código.

ACU: 100

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Entrada de armazenamento de temperatura máxima: IOPS/Ler MBps/Write MBps | Disquetes/entradas de dados max: IOPS | Largura de banda de rede Max NICs/Esperado (Mbps) |
|---|---|---|---|---|---|---|
| Standard_A1_v2  | 1 | 2  | 10 | 1000/20/10  | 2/2x500   | 2/250  |
| Standard_A2_v2  | 2 | 4  | 20 | 2000/40/20  | 4/4x500   | 2/500  |
| Standard_A4_v2  | 4 | 8  | 40 | 4000/80/40  | 8/8x500   | 4/1000 |
| Standard_A8_v2  | 8 | 16 | 80 | 8000/160/80 | 16/16x500 | 8/2000 |
| Standard_A2m_v2 | 2 | 16 | 20 | 2000/40/20  | 4/4x500   | 2/500  |
| Standard_A4m_v2 | 4 | 32 | 40 | 4000/80/40  | 8/8x500   | 4/1000 |
| Standard_A8m_v2 | 8 | 64 | 80 | 8000/160/80 | 16/16x500 | 8/2000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre como as unidades de [computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho da computação em Azure SKUs.