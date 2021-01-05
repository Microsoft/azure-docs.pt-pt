---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/09/2020
ms.author: alkohli
ms.openlocfilehash: 9ea5fb26a52c967c5296f1a83976e748c86c9e18
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763881"
---
O tamanho VM determina a quantidade de recursos computacional como CPU, GPU e memória que são disponibilizados ao VM. As máquinas virtuais devem ser criadas utilizando um tamanho VM adequado para a carga de trabalho. Apesar de todas as máquinas estarem a funcionar no mesmo hardware, os tamanhos das máquinas têm limites diferentes para o acesso ao disco, o que pode ajudá-lo a gerir o acesso geral ao disco através dos seus VMs. Se a carga de trabalho aumentar, uma máquina virtual existente também pode ser redimensionada.

Os VMs seguintes são suportados para criação no dispositivo Azure Stack Edge.

### <a name="dv2-series"></a>Série Dv2
|Tamanho     |vCPU     |Memória (GiB) | Tamanho do disco de recurso (GiB)  | Tamanho do disco de SO (GiB) | Discos de dados máximos | NICs máximos |
|-------------------|----|----|-----|----|------|------------|
|**Standard_D1_v2** |1   |3.5 |50   |1000| 4    |2 |
|**Standard_D2_v2** |2   |7   |100  |1000| 8    |4 |
|**Standard_D3_v2** |4   |14  |200  |1000| 16  |4 |
|**Standard_D4_v2** |8   |28  |400  |1000| 32  |8 |
|**Standard_D5_v2** |16  |56  |800  |1000| 64  |8 |
|**Standard_D11_v2** |2   |14  |100  |1000| 8     |2 |
|**Standard_D12_v2** |4   |28  |200  |1000| 16   |4 |
|**Standard_D13_v2** |8   |56  |400  |1000| 32  |8 |

### <a name="dsv2-series"></a>Série DSv2
|Tamanho     |vCPU     |Memória (GiB) |  Tamanho do disco de recurso (GiB)  | Tamanho do disco de SO (GiB) | Discos de dados máximos| NICs máximos |
|--------------------|----|----|----|-----|------|-------------|
|**Standard_DS1_v2** |1   |3.5 |7  |4000  |1000 |4  |2 |
|**Standard_DS2_v2** |2   |7   |14 |8000  |1000 |8  |4 |
|**Standard_DS3_v2** |4   |14  |28 |16000 |1000 |16 |4 |
|**Standard_DS4_v2** |8   |28  |56 |32000 |1000 |32 |8 |
|**Standard_DS5_v2** |16  |56  |112|64000 |1000 |64 |8 |
|**Standard_DS11_v2**|2   |14  |28 |8000  |1000 |4  |2 |
|**Standard_DS12_v2**|4   |28  |56 |16000 |1000 |8  |4 |
|**Standard_DS13_v2**|8   |56  |112|32000 |1000 |16 |8 |


Para obter mais informações, aceda à [série Dv2 em tamanhos VM de finalidade geral](../articles/virtual-machines/dv2-dsv2-series.md#dv2-series).

### <a name="ncast4_v3-series-preview"></a>NCasT4_v3 série (Pré-visualização)

Estes tamanhos são suportados para VMs GPU no seu dispositivo e são otimizados para aplicações aceleradas por GPU com intensidade computacional. Esta série está focada em cargas de trabalho de inferência com Tesla T4 GPU da Nvidia. 

|Tamanho     |vCPU     |Memória (GiB) | Tamanho do disco de recurso (GiB)  |Tamanho do disco de SO (GiB)| GPU | Memória gpu (GiB) | NICs máximos |
|---------------------|----|----|-----|-----|-------|--------------|
|**Standard_NC4as_T4_v3** |4   |28  |180   |1000|1 |16   |4 |
|**Standard_NC8as_T4_v3** |8   |56  |360   |1000|1 |16  |8 |

Para obter mais informações, aceda a [NCasT4_v3 séries em tamanhos de VM otimizados da GPU.](../articles/virtual-machines/nct4-v3-series.md)

### <a name="f-series"></a>Série F

Estas séries são otimizadas para cargas de trabalho computacionais e executadas em processadores Intel Xeon. 

| Tamanho | vCPU | Memória: GiB |Tamanho do disco de recurso (GiB) |Tamanho do disco de SO (GiB)|  Discos de dados máximos | NICs máximos |
|---|---|---|---|---|---|---|---|---|
| Standard_F1  | 1  | 2   |16      |1000| 4  |  2 |
| Standard_F2 | 2  | 4 |32      |1000| 8  |  4 |
| Standard_F4  | 4  | 8 |64   |1000| 16 |  4 |
| Standard_F8 | 8 | 16  |132    |1000| 32 |  8 |
| Standard_F16 | 16 | 32  |262   |1000| 64 |  8 |
| Standard_F1s | 1 | 2  | 4  |1000| 4 | 1 |
| Standard_F2s | 2 | 4 |8   |1000| 8 | 4 |
| Standard_F4s | 4 | 8 |16 |1000| 16 |  4 |
| Standard_F8s | 8 | 16 |32 |1000| 32 |  8 |
| Standard_F16s | 16 | 32 |64 |1000| 64 |  8 |

Para obter mais informações, aceda à [série Fsv2 em tamanhos de VM otimizados compute](../articles/virtual-machines/fsv2-series.md).

