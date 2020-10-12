---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/03/2020
ms.author: alkohli
ms.openlocfilehash: 7ce49873b4e59bcf474deaea4420f56a72c9c589
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89085743"
---
O tamanho VM determina a quantidade de recursos computacional como CPU, GPU e memória que são disponibilizados ao VM. As máquinas virtuais devem ser criadas utilizando um tamanho VM adequado para a carga de trabalho. Apesar de todas as máquinas estarem a funcionar no mesmo hardware, os tamanhos das máquinas têm limites diferentes para o acesso ao disco, o que pode ajudá-lo a gerir o acesso geral ao disco através dos seus VMs. Se a carga de trabalho aumentar, uma máquina virtual existente também pode ser redimensionada.

Os VMs da série Standard Dv2 são suportados para criação no dispositivo Azure Stack Edge.

### <a name="dv2-series"></a>Série Dv2
|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Produção de disco Max OS (IOPS) | Produção de armazenamento temporário max (IOPS) | Discos de dados /produção máxima (IOPS) | NICs máximos |
|-------------------|----|----|-----|----|------|------------|---------|
|**Standard_D1_v2** |1   |3.5 |50   |500 |3.000  |4 / 4x500   |2 |
|**Standard_D2_v2** |2   |7   |100  |500 |6000  |8 / 8x500   |2 |
|**Standard_D3_v2** |4   |14  |200  |500 |12000 |16 / 16x500 |4 |
|**Standard_D4_v2** |8   |28  |400  |500 |24000 |32 / 32x500 |8 |
|**Standard_D5_v2** |16  |56  |800  |500 |48000 |64 / 64x500 |8 |

### <a name="dsv2-series"></a>Série DSv2
|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Produção de disco Max OS (IOPS) | Produção de armazenamento temporário max (IOPS) | Discos de dados /produção máxima (IOPS) | NICs máximos |
|--------------------|----|----|----|-----|------|-------------|---------|
|**Standard_DS1_v2** |1   |3.5 |7   |1000 |4000  |4 / 4x2300   |2 |
|**Standard_DS2_v2** |2   |7   |14  |1000 |8000  |8 / 8x2300   |2 |
|**Standard_DS3_v2** |4   |14  |28  |1000 |16000 |16 / 16x2300 |4 |
|**Standard_DS4_v2** |8   |28  |56  |1000 |32000 |32 / 32x2300 |8 |
|**Standard_DS5_v2** |16  |56  |112 |1000 |64000 |64 / 64x2300 |8 |

### <a name="dv2-series"></a>Série Dv2
|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Produção de disco Max OS (IOPS) | Produção de armazenamento temporário max (IOPS) | Discos de dados /produção máxima (IOPS) | NICs máximos |
|--------------------|----|----|-----|----|-------|-------------|---------|
|**Standard_D11_v2** |2   |14  |100  |500 |6000   |8 / 8x500    |2 |
|**Standard_D12_v2** |4   |28  |200  |500 |12000  |16 / 16x500  |4 |
|**Standard_D13_v2** |8   |56  |400  |500 |24000  |32 / 32x500  |8 |
|**Standard_D14_v2** |16  |112 |800  |500 |48000  |64 / 64x500  |8 |


### <a name="dsv2-series"></a>Série DSv2
|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Produção de disco Max OS (IOPS) | Produção de armazenamento temporário max (IOPS) | Discos de dados /produção máxima (IOPS) | NICs máximos |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_DS11_v2** |2   |14  |28   |1000 |8000   |4 / 4x2300    |2 |
|**Standard_DS12_v2** |4   |28  |56   |1000 |16000  |8 / 8x2300    |4 |
|**Standard_DS13_v2** |8   |56  |112  |1000 |32000  |16 / 16x2300  |8 |
|**Standard_DS14_v2** |16  |112 |224  |1000 |64000  |32 / 32x2300  |8 |

Para obter mais informações, aceda à [série Dv2 em tamanhos VM de finalidade geral](../articles/virtual-machines/dv2-dsv2-series.md#dv2-series).
