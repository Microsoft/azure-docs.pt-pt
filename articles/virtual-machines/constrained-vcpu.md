---
title: Tamanhos de vCPU limitados
description: Lista os tamanhos Vm que são capazes de ter uma contagem de vCPU restrita.
author: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 03/09/2018
ms.author: mimckitt
ms.openlocfilehash: 7faeec8494a908b9aab00be9b63904354b5e0994
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557085"
---
# <a name="constrained-vcpu-capable-vm-sizes"></a>Tamanhos VM capazes de VCPU constrangidos

Algumas cargas de trabalho de base de dados como SQL Server ou Oracle requerem alta memória, armazenamento e largura de banda de I/S, mas não uma contagem de núcleo elevado. Muitas cargas de trabalho na base de dados não são intensivas em CPU. O Azure oferece certos tamanhos VM onde pode limitar a contagem VM vCPU para reduzir o custo do licenciamento de software, mantendo a mesma memória, armazenamento e largura de banda de I/O.

A contagem de vCPU pode ser limitada a metade ou um quarto do tamanho original de VM. Estes novos tamanhos VM têm um sufixo que especifica o número de vCPUs ativos para facilitar a sua identificação.

Por exemplo, o tamanho VM atual Standard_GS5 vem com 32 vCPUs, 448 GB de RAM, 64 discos (até 256 TB) e 80.000 IOPs ou 2 GB/s de largura de banda de I/O. Os novos tamanhos VM Standard_GS5-16 e Standard_GS5-8 vêm com vCPUs ativos 16 e 8, respectivamente, mantendo o resto das especificações do Standard_GS5 para memória, armazenamento e largura de banda de I/O.

As taxas de licenciamento cobradas pelo SQL Server ou pela Oracle estão limitadas à nova contagem de vCPU, e outros produtos devem ser cobrados com base na nova contagem de vCPU. Isto resulta num aumento de 50% a 75% no rácio das especificações VM para vCPUs ativos (faturados). Estes novos tamanhos VM permitem que as cargas de trabalho dos clientes utilizem a mesma memória, armazenamento e largura de banda de I/O, ao mesmo tempo que otimizam o custo de licenciamento do software. Neste momento, o custo do cálculo, que inclui o licenciamento de SO, permanece o mesmo que o tamanho original. Para obter mais informações, consulte [os tamanhos Azure VM para obter cargas de trabalho mais rentáveis da base de dados.](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/)


| Name                | vCPU | Especificações           |
|---------------------|------|-----------------|
| Standard_M8-2ms     | 2    | O mesmo que M8ms    |
| Standard_M8-4ms     | 4    | O mesmo que M8ms    |
| Standard_M16-4ms    | 4    | O mesmo que M16ms   |
| Standard_M16-8ms    | 8    | O mesmo que M16ms   |
| Standard_M32-8ms    | 8    | O mesmo que M32ms   |
| Standard_M32-16ms   | 16   | O mesmo que M32ms   |
| Standard_M64-32ms   | 32   | O mesmo que M64ms   |
| Standard_M64-16ms   | 16   | O mesmo que M64ms   |
| Standard_M128-64ms  | 64   | O mesmo que M128ms  |
| Standard_M128-32ms  | 32   | O mesmo que M128ms  |
| Standard_E4 2s_v3   | 2    | O mesmo que E4s_v3  |
| Standard_E8 4s_v3   | 4    | O mesmo que E8s_v3  |
| Standard_E8 2s_v3   | 2    | O mesmo que E8s_v3  |
| Standard_E16 8s_v3  | 8    | O mesmo que E16s_v3 |
| Standard_E16-4s_v3  | 4    | O mesmo que E16s_v3 |
| Standard_E32 16s_v3 | 16   | O mesmo que E32s_v3 |
| Standard_E32 8s_v3  | 8    | O mesmo que E32s_v3 |
| Standard_E64 32s_v3 | 32   | O mesmo que E64s_v3 |
| Standard_E64 16s_v3 | 16   | O mesmo que E64s_v3 |
| Standard_E4-2s_v4   | 2    | O mesmo que E4s_v4  |
| Standard_E8 4s_v4   | 4    | O mesmo que E8s_v4  |
| Standard_E8 2s_v4   | 2    | O mesmo que E8s_v4  |
| Standard_E16-8s_v4  | 8    | O mesmo que E16s_v4 |
| Standard_E16 4s_v4  | 4    | O mesmo que E16s_v4 |
| Standard_E32 16s_v4 | 16   | O mesmo que E32s_v4 |
| Standard_E32-8s_v4  | 8    | O mesmo que E32s_v4 |
| Standard_E64 32s_v4 | 32   | O mesmo que E64s_v4 |
| Standard_E64 16s_v4 | 16   | O mesmo que E64s_v4 |
| Standard_E4 2ds_v4  | 2    | O mesmo que E4ds_v4 |
| Standard_E8 4ds_v4  | 4    | O mesmo que E8ds_v4 |
| Standard_E8 2ds_v4  | 2    | O mesmo que E8ds_v4 |
| Standard_E16 8ds_v4 | 8    | O mesmo que E16ds_v4|
| Standard_E16 4ds_v4 | 4    | O mesmo que E16ds_v4|
| Standard_E32-16ds_v4| 16   | O mesmo que E32ds_v4|
| Standard_E32 8ds_v4 | 8    | O mesmo que E32ds_v4|
| Standard_E64 32ds_v4| 32   | O mesmo que E64ds_v4|
| Standard_E64 16ds_v4| 16   | O mesmo que E64ds_v4|
| Standard_E4-2as_v4  | 2    | O mesmo que E4as_v4 |
| Standard_E8-4as_v4  | 4    | O mesmo que E8as_v4 |
| Standard_E8 2as_v4  | 2    | O mesmo que E8as_v4 |
| Standard_E16 8as_v4 | 8    | O mesmo que E16as_v4|
| Standard_E16-4as_v4 | 4    | O mesmo que E16as_v4|
| Standard_E32 16as_v4| 16   | O mesmo que E32as_v4|
| Standard_E32-8as_v4 | 8    | O mesmo que E32as_v4|
| Standard_E64-32as_v4| 32   | O mesmo que E64as_v4|
| Standard_E64-16as_v4| 16   | O mesmo que E64as_v4|
| Standard_E96-48as_v4| 48   | O mesmo que E96as_v4|
| Standard_E96 24as_v4| 24   | O mesmo que E96as_v4|
| Standard_GS4-8      | 8    | O mesmo que gS4     |
| Standard_GS4-4      | 4    | O mesmo que gS4     |
| Standard_GS5-16     | 16   | O mesmo que gS5     |
| Standard_GS5-8      | 8    | O mesmo que gS5     |
| Standard_DS11 1_v2  | 1    | O mesmo que DS11_v2 |
| Standard_DS12 2_v2  | 2    | O mesmo que DS12_v2 |
| Standard_DS12-1_v2  | 1    | O mesmo que DS12_v2 |
| Standard_DS13 4_v2  | 4    | O mesmo que DS13_v2 |
| Standard_DS13-2_v2  | 2    | O mesmo que DS13_v2 |
| Standard_DS14 8_v2  | 8    | O mesmo que DS14_v2 |
| Standard_DS14 4_v2  | 4    | O mesmo que DS14_v2 |
| Standard_M416 208s_v2 | 208    | O mesmo que M416s_v2|
| Standard_M416 208ms_v2 | 208    | O mesmo que M416ms_v2 |

## <a name="other-sizes"></a>Outros tamanhos
- [Com otimização de computação](./sizes-compute.md)
- [Com otimização de memória](./sizes-memory.md)
- [Com otimização de armazenamento](./sizes-storage.md)
- [GPU](./sizes-gpu.md)
- [Computação de elevado desempenho](./sizes-hpc.md)

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [as unidades de computação Azure (ACU)](./acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
