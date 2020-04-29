---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 24fbab2bb637edf6c7e31215cdc7ac82bdb0ebab
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "67184254"
---
Algumas cargas de trabalho de base de dados como o SQL Server ou o Oracle requerem alta memória, armazenamento e largura de banda em I/S, mas não uma contagem de núcleo elevada. Muitas cargas de trabalho na base de dados não são intensivas em CPU. O Azure oferece certos tamanhos vM onde pode limitar a contagem vCPU VM para reduzir o custo de licenciamento de software, mantendo ao mesmo tempo a mesma memória, armazenamento e largura de banda I/S.

A contagem de VCPU pode ser limitada a metade ou um quarto do tamanho original do VM. Estes novos tamanhos vm têm um sufixo que especifica o número de vCPUs ativos para facilitar a sua identificação.

Por exemplo, o tamanho atual do VM Standard_GS5 vem com 32 vCPUs, 448 GB DE RAM, 64 discos (até 256 TB) e 80.000 IOPs ou 2 GB/s de largura de banda I/O. Os novos tamanhos de VM Standard_GS5-16 e Standard_GS5-8, respectivamente, com 16 e 8 vCPUs ativos, mantendo o resto das especificações do Standard_GS5 para memória, armazenamento e largura de banda Em/S.

As taxas de licenciamento cobradas para o SQL Server ou oracle estão limitadas à nova contagem de vCPU, e outros produtos devem ser cobrados com base na nova contagem de vCPU. Isto resulta num aumento de 50% a 75% no rácio das especificações vm para vCPUs ativos (faturados). Estes novos tamanhos vm permitem que as cargas de trabalho dos clientes utilizem a mesma memória, armazenamento e largura de banda Em/S, ao mesmo tempo que otimizam o seu custo de licenciamento de software. Neste momento, o custo do cálculo, que inclui o licenciamento de OS, permanece o mesmo que o tamanho original. Para obter mais informações, consulte os tamanhos de [VM Do Azure para obter cargas de trabalho de base de dados mais rentáveis.](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/)


| Nome                | vCPU | Especificações           |
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
| Standard_E4-2s_v3   | 2    | O mesmo que E4s_v3  |
| Standard_E8-4s_v3   | 4    | O mesmo que E8s_v3  |
| Standard_E8-2s_v3   | 2    | O mesmo que E8s_v3  |
| Standard_E16-8s_v3  | 8    | O mesmo que E16s_v3 |
| Standard_E16-4s_v3  | 4    | O mesmo que E16s_v3 |
| Standard_E32-16s_v3 | 16   | O mesmo que E32s_v3 |
| Standard_E32-8s_v3  | 8    | O mesmo que E32s_v3 |
| Standard_E64-32s_v3 | 32   | O mesmo que E64s_v3 |
| Standard_E64-16s_v3 | 16   | O mesmo que E64s_v3 |
| Standard_GS4-8      | 8    | O mesmo que o GS4     |
| Standard_GS4-4      | 4    | O mesmo que o GS4     |
| Standard_GS5-16     | 16   | O mesmo que o GS5     |
| Standard_GS5-8      | 8    | O mesmo que o GS5     |
| Standard_DS11-1_v2  | 1    | O mesmo que DS11_v2 |
| Standard_DS12-2_v2  | 2    | O mesmo que DS12_v2 |
| Standard_DS12-1_v2  | 1    | O mesmo que DS12_v2 |
| Standard_DS13-4_v2  | 4    | O mesmo que DS13_v2 |
| Standard_DS13-2_v2  | 2    | O mesmo que DS13_v2 |
| Standard_DS14-8_v2  | 8    | O mesmo que DS14_v2 |
| Standard_DS14-4_v2  | 4    | O mesmo que DS14_v2 |
