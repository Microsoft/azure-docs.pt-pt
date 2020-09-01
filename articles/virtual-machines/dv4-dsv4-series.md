---
title: Série Dv4 e Dsv4 - Azure Virtual Machines
description: Especificações para os VMs da série Dv4 e Dsv4.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: df78d11ce19c1d1fbbb04d2cd1718be74401cd83
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182161"
---
# <a name="dv4-and-dsv4-series"></a>Séries Dv4 e Dsv4

A série Dv4 e Dsv4 funciona nos processadores Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) numa configuração hiper roscada, proporcionando uma proposta de melhor valor para a maioria das cargas de trabalho de uso geral. Apresenta uma velocidade do relógio Turbo de 3,4 GHz. 

> [!NOTE]
> Para perguntas frequentes, consulte os  [tamanhos Azure VM sem disco temporário local](azure-vms-no-temp-disk.md).
## <a name="dv4-series"></a>Série Dv4

Os tamanhos da série Dv4 são executados no Intel &reg; Xeon &reg; Platinum 8272CL (Lago cascade). Os tamanhos da série Dv4 oferecem uma combinação de opções de vCPU, memória e armazenamento remoto para a maioria das cargas de trabalho de produção. Os VMs da série Dv4 apresentam [tecnologia De &reg; Hiper-Rosca Intel](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html).

O armazenamento remoto do disco de dados é faturado separadamente das máquinas virtuais. Para utilizar discos de armazenamento premium, utilize os tamanhos Dsv4. Os preços e os contadores de faturação para tamanhos Dsv4 são os mesmos que a série Dv4.

ACU: 195-210

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

Migração ao Vivo: Suportado

Atualizações de preservação da memória: Suportadas

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | NICs máximos|Largura de banda esperada da rede (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D2_v4 | 2 | 8 | Apenas armazenamento remoto | 4 | 2|1000 |
| Standard_D4_v4 | 4 | 16  | Apenas armazenamento remoto | 8 | 2|2000 |
| Standard_D8_v4 | 8 | 32 | Apenas armazenamento remoto | 16 | 4|4000 |
| Standard_D16_v4 | 16 | 64 | Apenas armazenamento remoto | 32 | 8|8000 |
| Standard_D32_v4 | 32 | 128 | Apenas armazenamento remoto | 32 | 8|16000 |
| Standard_D48_v4 | 48 | 192 | Apenas armazenamento remoto | 32 | 8|24000 |
| Standard_D64_v4 | 64 | 256 | Apenas armazenamento remoto | 32 | 8|30000 |

## <a name="dsv4-series"></a>Série Dsv4

Os tamanhos da série Dsv4 são executados no Intel &reg; Xeon &reg; Platinum 8272CL (Lago cascata). Os tamanhos da série Dv4 oferecem uma combinação de opções de vCPU, memória e armazenamento remoto para a maioria das cargas de trabalho de produção. Os VMs da série DSV4 apresentam [tecnologia de &reg; hiper rosca Intel](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html). O armazenamento remoto do disco de dados é faturado separadamente das máquinas virtuais.

ACU: 195-210

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

Migração ao Vivo: Suportado

Atualizações de preservação da memória: Suportadas

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Produção de disco não-abacatado por maxilar: IOPS/MBps | NICs máximos|Largura de banda esperada da rede (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v4 | 2 | 8  | Apenas armazenamento remoto | 4 | 3200/48 | 2|1000 |
| Standard_D4s_v4 | 4 | 16 | Apenas armazenamento remoto | 8 | 6400/96 | 2|2000 |
| Standard_D8s_v4 | 8 | 32 | Apenas armazenamento remoto | 16 | 12800/192 | 4|4000 |
| Standard_D16s_v4 | 16 | 64  | Apenas armazenamento remoto | 32 | 25600/384 | 8|8000 |
| Standard_D32s_v4 | 32 | 128 | Apenas armazenamento remoto | 32 | 51200/768 | 8|16000 |
| Standard_D48s_v4 | 48 | 192 | Apenas armazenamento remoto | 32 | 76800/1152 | 8|24000 |
| Standard_D64s_v4 | 64 | 256 | Apenas armazenamento remoto | 32 | 80000/1200 | 8|30000 |
