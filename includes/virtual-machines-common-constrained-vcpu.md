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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66158492"
---
Algumas cargas de trabalho de base de dados, como o SQL Server ou Oracle requerem elevada da memória, armazenamento e largura de banda de e/s, mas não é um número de núcleos elevada. Muitas cargas de trabalho de base de dados não são intensiva da CPU. O Azure oferece determinados tamanhos de VM em que pode restringir a contagem de vCPU VM para reduzir o custo de licenciamento de software, mantendo a mesma memória, armazenamento e largura de banda de e/s.

Pode ser limitada a contagem de vCPU para metade ou um quarto do tamanho da VM original. Estes novos tamanhos VM tem um sufixo que especifica o número de vCPUs Active Directory para que fiquem mais fáceis de identificar.

Por exemplo, o tamanho atual do VM Standard_GS5 vem com 32 vCPUs, 448 GB RAM, 64 discos (até 256 TB) e 80 000 IOPs ou 2 GB/s de largura de banda de e/s. Tamanhos de nova VM Standard_GS5-16 e Standard_GS5-8 dispõe de 8 e 16 vCPUs Active Directory, respetivamente, mantendo o restante das especificações de Standard_GS5 de memória, armazenamento e largura de banda de e/s.

As tarifas de licenciamento cobrado de SQL Server ou Oracle estão limitadas para a nova contagem de vCPU e outros produtos devem ser cobrados baseiam-se a nova contagem de vCPU. Isso resulta num aumento de 50% para 75% na proporção entre as especificações VM para o Active Directory vCPUs (cobrar). Estes novos tamanhos de VM permitem às cargas de trabalho do cliente utilizar a mesma memória, armazenamento e largura de banda de E/S enquanto otimizam o custo de licenciamento de software. Neste momento, o custo de computação, que inclui o licenciamento de SO, permanece o mesmo um como o tamanho original. Para obter mais informações, consulte [tamanhos de VM do Azure para obter mais cargas de trabalho de base de dados económico](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).


| Name                | vCPU | Especificações           |
|---------------------|------|-----------------|
| Standard_M8 2ms     | 2    | Mesmo que M8ms    |
| Standard_M8 4ms     | 4    | Mesmo que M8ms    |
| Standard_M16-4ms    | 4    | Mesmo que M16ms   |
| Standard_M16-8ms    | 8    | Mesmo que M16ms   |
| Standard_M32-8ms    | 8    | Mesmo que M32ms   |
| Standard_M32-16ms   | 16   | Mesmo que M32ms   |
| Standard_M64-32ms   | 32   | Mesmo que M64ms   |
| Standard_M64-16ms   | 16   | Mesmo que M64ms   |
| Standard_M128-64ms  | 64   | Mesmo que M128ms  |
| Standard_M128 32ms  | 32   | Mesmo que M128ms  |
| Standard_E4-2s_v3   | 2    | Same as E4s_v3  |
| Standard_E8-4s_v3   | 4    | Same as E8s_v3  |
| Standard_E8-2s_v3   | 2    | Same as E8s_v3  |
| Standard_E16-8s_v3  | 8    | Mesmo que E16s_v3 |
| Standard_E16-4s_v3  | 4    | Mesmo que E16s_v3 |
| Standard_E32-16s_v3 | 16   | Mesmo que E32s_v3 |
| Standard_E32-8s_v3  | 8    | Mesmo que E32s_v3 |
| Standard_E64-32s_v3 | 32   | Same as E64s_v3 |
| Standard_E64-16s_v3 | 16   | Same as E64s_v3 |
| Standard_GS4-8      | 8    | Mesmo que GS4     |
| Standard_GS4-4      | 4    | Mesmo que GS4     |
| Standard_GS5-16     | 16   | Mesmo que GS5     |
| Standard_GS5-8      | 8    | Mesmo que GS5     |
| Standard_DS11-1_v2  | 1    | Mesmo que DS11_v2 |
| Standard_DS12-2_v2  | 2    | Mesmo que DS12_v2 |
| Standard_DS12-1_v2  | 1    | Mesmo que DS12_v2 |
| Standard_DS13-4_v2  | 4    | Mesmo que DS13_v2 |
| Standard_DS13-2_v2  | 2    | Mesmo que DS13_v2 |
| Standard_DS14-8_v2  | 8    | Mesmo que DS14_v2 |
| Standard_DS14-4_v2  | 4    | Mesmo que DS14_v2 |
