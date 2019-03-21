---
title: Tamanhos de máquinas virtuais suportados no Azure Stack | Documentos da Microsoft
description: Referência para os tamanhos VM suportadas no Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 0188de6d3a29034ec00999b2e07cab6ddb911631
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176264"
---
# <a name="virtual-machine-sizes-supported-in-azure-stack"></a>Tamanhos de máquinas virtuais suportados no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Este artigo lista os tamanhos de máquina virtual (VM) que estão disponíveis no Azure Stack.

O disco IOPS (entrada/saída operações por segundo) no Azure Stack é uma função do tamanho da VM, em vez do tipo de disco. Isso significa que, para uma série de Standard_Fs VM, independentemente de se escolher o SSD ou HDD para o tipo de disco, o limite de IOPS para um disco de dados adicionais único é 2300 IOPS. Os limites IOPS impostos é um limite (máximo possível) para impedir que os vizinhos ruidosos. Não é uma garantia de IOPS que obterá um tamanho de VM específicos.

## <a name="general-purpose"></a>Fins gerais

Tamanhos de VM para fins gerais oferecem um rácio de CPU / memória equilibrado. São utilizados para teste e desenvolvimento, pequenas a médias bases de dados e baixa para servidores web com tráfego médio. Cada disco de dados é 2300 IOPS para tamanhos de VM premium, exceto para a série de básico A. Para A básica, o tamanho do disco de dados é de 500 IOPS.

### <a name="basic-a"></a>Básico A

> [!NOTE]
> *Básico A* tamanhos de máquinas virtuais foram extinguidos para [criar conjuntos de dimensionamento de máquinas virtuais](../azure-stack-compute-add-scalesets.md) (VMSS) através do portal. Para criar um VMSS com este tamanho, utilize o PowerShell ou um modelo.

|Tamanho – tamanho\nome |vCPU     |Memória | Tamanho máximo do disco temporário | Débito de disco do SO máxima: (IOPS) | Débito máximo do armazenamento temporário (IOPS) | Débito de disco de dados máximo (IOPS) | NICs máximos |    
|-----------------|-----|---------|---------|-----|------|-----------|----|
|**A0\Basic_A0**  |1    |768 MB   | 20 GB   |300  | 300  |1 / 1x300  |1   |
|**A1\Basic_A1**  |1    |1,75 GB  | 40 GB   |300  | 300  |2 / 2x300  |1   |
|**A2\Basic_A2**  |2    |3,5 GB   | 60 GB   |300  | 300  |4 / 4x300  |1   |
|**A3\Basic_A3**  |4    |7 GB     | 120 GB  |300  | 300  |8 / 8x300  |1   |
|**A4\Basic_A4**  |8    |14 GB    | 240 GB  |300  | 300  |16 / 16X300 |1   |

### <a name="standard-a"></a>Standard A 
|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Débito de disco (IOPS) do Mac OS | Débito máximo do armazenamento temporário (IOPS) | Discos de dados máximos / débito (IOPS) | NICs máximos |    
|----------------|--|------|----|----|----|-------|---------|
|**Standard_A0** |1 |0.768 |20  |500 |500 |1x500  |1 |
|**Standard_A1** |1 |1.75  |70  |500 |500 |2x500  |1 |
|**Standard_A2** |2 |3.5   |135 |500 |500 |4x500  |1 |
|**Standard_A3** |4 |7     |285 |500 |500 |8x500  |2 |
|**Standard_A4** |8 |14    |605 |500 |500 |16x500 |4 |
|**Standard_A5** |2 |14    |135 |500 |500 |4x500  |2 |
|**Standard_A6** |4 |28    |285 |500 |500 |8x500  |2 |
|**Standard_A7** |8 |56    |605 |500 |500 |16x500 |4 |

### <a name="av2-series"></a>Série Av2
*Requer versão 1804 ou posterior do Azure Stack*

|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Débito de disco (IOPS) do Mac OS | Débito máximo do armazenamento temporário (IOPS) | Discos de dados máximos / débito (IOPS) | NICs máximos |
|-----------------|----|----|-----|-----|------|--------------|---------|
|**Standard_A1_v2**  |1   |2   |10   |500 |1000  |2 / 2x500   |2 |
|**Standard_A2_v2**  |2   |4   |20   |500 |2000  |4 / 4x500   |2 |
|**Standard_A4v2**   |4   |8   |40   |500 |4000  |8 / 8x500   |4 |
|**Standard_A8_v2**  |8   |16  |80   |500 |8000  |16 / 16x500 |8 |
|**Standard_A2m_v2** |2   |16  |20   |500 |2000  |4 / 4x500   |2 |
|**Standard_A4m_v2** |4   |32  |40   |500 |4000  |8 / 8x500   |4 |
|**Standard_A8m_v2** |8   |64  |80   |500 |8000  |16 / 16x500 |8 |

### <a name="d-series"></a>Série D
|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Débito de disco (IOPS) do Mac OS | Débito máximo do armazenamento temporário (IOPS) | Discos de dados máximos / débito (IOPS) | NICs máximos |
|----------------|----|----|-----|----|------|------------|---------|
|**Standard_D1** |1   |3.5 |50   |500 |3000  |4 / 4x500   |1 |
|**Standard_D2** |2   |7   |100  |500 |6000  |8 / 8x500   |2 |
|**Standard_D3** |4   |14  |200  |500 |12000 |16 / 16x500 |4 |
|**Standard_D4** |8   |28  |400  |500 |24000 |32 / 32x500 |8 |


### <a name="ds-series"></a>Série DS
|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Débito de disco (IOPS) do Mac OS | Débito máximo do armazenamento temporário (IOPS) | Discos de dados máximos / débito (IOPS) | NICs máximos |
|-----------------|----|----|-----|-----|------|-------------|---------|
|**Standard_DS1** |1   |3.5 |7    |1000 |4000  |4 / 4x2300   |1 |
|**Standard_DS2** |2   |7   |14   |1000 |8000  |8 / 8x2300   |2 |
|**Standard_DS3** |4   |14  |28   |1000 |16000 |16 / 16x2300 |4 |
|**Standard_DS4** |8   |28  |56   |1000 |32000 |32 / 32x2300 |8 |

### <a name="dv2-series"></a>Série Dv2
|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Débito de disco (IOPS) do Mac OS | Débito máximo do armazenamento temporário (IOPS) | Discos de dados máximos / débito (IOPS) | NICs máximos |
|-------------------|----|----|-----|----|------|------------|---------|
|**Standard_D1_v2** |1   |3.5 |50   |500 |3000  |4 / 4x500   |1 |
|**Standard_D2_v2** |2   |7   |100  |500 |6000  |8 / 8x500   |2 |
|**Standard_D3_v2** |4   |14  |200  |500 |12000 |16 / 16x500 |4 |
|**Standard_D4_v2** |8   |28  |400  |500 |24000 |32 / 32x500 |8 |
|**Standard_D5_v2** |16  |56  |800  |500 |48000 |64 / 64x500 |8 |

### <a name="dsv2-series"></a>Série DSv2
|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Débito de disco (IOPS) do Mac OS | Débito máximo do armazenamento temporário (IOPS) | Discos de dados máximos / débito (IOPS) | NICs máximos |
|--------------------|----|----|----|-----|------|-------------|---------|
|**Standard_DS1_v2** |1   |3.5 |7   |1000 |4000  |4 / 4x2300   |1 |
|**Standard_DS2_v2** |2   |7   |14  |1000 |8000  |8 / 8x2300   |2 |
|**Standard_DS3_v2** |4   |14  |28  |1000 |16000 |16 / 16x2300 |4 |
|**Standard_DS4_v2** |8   |28  |56  |1000 |32000 |32 / 32x2300 |8 |
|**Standard_DS5_v2** |16  |56  |112 |1000 |64000 |64 / 64x2300 |8 |


## <a name="compute-optimized"></a>Com otimização de computação
### <a name="f-series"></a>Série F
*Requer versão 1804 ou posterior do Azure Stack*

|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Débito de disco (IOPS) do Mac OS | Débito máximo do armazenamento temporário (IOPS) | Discos de dados máximos / débito (IOPS) | NICs máximos |
|-----------------|----|----|-----|----|------|------------|---------|
|**Standard_F1**  |1   |2   |16   |500 |3000  |4 / 4x500   |2 |
|**Standard_F2**  |2   |4   |32   |500 |6000  |8 / 8x500   |2 |
|**Standard_F4**  |4   |8   |64   |500 |12000 |16 / 16x500 |4 |
|**Standard_F8**  |8   |16  |128  |500 |24000 |32 / 32x500 |8 |
|**Standard_F16** |16  |32  |256  |500 |48000 |64 / 64x500 |8 |


### <a name="fs-series"></a>Série Fs
*Requer versão 1804 ou posterior do Azure Stack*  

|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Débito de disco (IOPS) do Mac OS | Débito máximo do armazenamento temporário (IOPS) | Discos de dados máximos / débito (IOPS) | NICs máximos |
|------------------|----|----|----|-----|------|-------------|---------|
|**Standard_F1s**  |1   |2   |4   |1000 |4000  |4 / 4x2300   |2 |
|**Standard_F2s**  |2   |4   |8   |1000 |8000  |8 / 8x2300   |2 |
|**Standard_F4s**  |4   |8   |16  |1000 |16000 |16 / 16x2300 |4 |
|**Standard_F8s**  |8   |16  |32  |1000 |32000 |32 / 32x2300 |8 |
|**Standard_F16s** |16  |32  |64  |1000 |64000 |64 / 64x2300 |8 |


### <a name="fsv2-series"></a>Série Fsv2
*Requer versão 1804 ou posterior do Azure Stack* 

|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Débito de disco (IOPS) do Mac OS | Débito máximo do armazenamento temporário (IOPS) | Discos de dados máximos / débito (IOPS) | NICs máximos |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_F2s_v2**  |2   |4   |16   |1000 |4000   |4 / 4x2300    |2 |
|**Standard_F4s_v2**  |4   |8   |32   |1000 |8000   |8 / 8x2300    |4 |
|**Standard_F8s_v2**  |8   |16  |64   |1000 |16000  |16 / 16x2300  |8 |
|**Standard_F16s_v2** |16  |32  |128  |1000 |32000  |32 / 32x2300  |8 |
|**Standard_F32s_v2** |32  |64  |256  |1000 |64000  |32 / 32x2300  |8 |
|**Standard_F64s_v2** |64  |128 |512  |1000 |128000 |32 / 32x2300  |8 |


## <a name="memory-optimized"></a>Com otimização de memória

Memória otimizados tamanhos de VM oferecem uma alta taxa de memória de CPU, que foi concebida para servidores de base de dados relacionais, caches médias a grandes e análise dentro da memória.

### <a name="mo-d"></a>Série D
|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Débito de disco (IOPS) do Mac OS | Débito máximo do armazenamento temporário (IOPS) | Discos de dados máximos / débito (IOPS) | NICs máximos |
|------------------|---|----|----|--------|------|------------|---------|
|**Standard_D11**  |2  |14  |100 |500     |6000  |8 / 8x500   |2 |
|**Standard_D12**  |4  |28  |200 |500     |12000 |16 / 16x500 |4 |
|**Standard_D13**  |8  |56  |400 |500     |24000 |32 / 32x500 |8 |
|**Standard_D14**  |16 |112 |800 |500     |48000 |64 / 64x500 |8 |

### <a name="mo-ds"></a>Série DS
|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Débito de disco (IOPS) do Mac OS | Débito máximo do armazenamento temporário (IOPS) | Discos de dados máximos / débito (IOPS) | NICs máximos |
|-------------------|---|----|----|--------|------|-------------|---------|
|**Standard_DS11**  |2  |14  |28  |1000    |8000  |8 / 8x2300   |2 |
|**Standard_DS12**  |4  |28  |56  |1000    |12000 |16 / 16x2300 |4 |
|**Standard_DS13**  |8  |56  |112 |1000    |32000 |32 / 32x2300 |8 |
|**Standard_DS14**  |16 |112 |224 |1000    |64000 |64 / 64x2300 |8 |

### <a name="mo-dv2"></a>Série Dv2
|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Débito de disco (IOPS) do Mac OS | Débito máximo do armazenamento temporário (IOPS) | Discos de dados máximos / débito (IOPS) | NICs máximos |
|--------------------|----|----|-----|----|-------|-------------|---------|
|**Standard_D11_v2** |2   |14  |100  |500 |6000   |8 / 8x500    |2 |
|**Standard_D12_v2** |4   |28  |200  |500 |12000  |16 / 16x500  |4 |
|**Standard_D13_v2** |8   |56  |400  |500 |24000  |32 / 32x500  |8 |
|**Standard_D14_v2** |16  |112 |800  |500 |48000  |64 / 64x500  |8 |


### <a name="mo-dsv2"></a>DSv2-series
|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Débito de disco (IOPS) do Mac OS | Débito máximo do armazenamento temporário (IOPS) | Discos de dados máximos / débito (IOPS) | NICs máximos |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_DS11_v2** |2   |14  |28   |1000 |8000   |4 / 4x2300    |2 |
|**Standard_DS12_v2** |4   |28  |56   |1000 |16000  |8 / 8x2300    |4 |
|**Standard_DS13_v2** |8   |56  |112  |1000 |32000  |16 / 16x2300  |8 |
|**Standard_DS14_v2** |16  |112 |224  |1000 |64000  |32 / 32x2300  |8 |


## <a name="next-steps"></a>Passos Seguintes

[Considerações para máquinas virtuais no Azure Stack](azure-stack-vm-considerations.md)
