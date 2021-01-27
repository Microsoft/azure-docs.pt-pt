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
ms.openlocfilehash: 96bcf70d2f5ec8301b16de5d23d94ad8a7e27b5a
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920346"
---
# <a name="dv4-and-dsv4-series"></a>Séries Dv4 e Dsv4

A série Dv4 e Dsv4 funciona nos processadores Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) numa configuração hiper roscada, proporcionando uma proposta de melhor valor para a maioria das cargas de trabalho de uso geral. Apresenta uma velocidade do relógio Turbo de 3,4 GHz. 

> [!NOTE]
> Para perguntas frequentes, consulte os  [tamanhos Azure VM sem disco temporário local](azure-vms-no-temp-disk.md).
## <a name="dv4-series"></a>Série Dv4

Os tamanhos da série Dv4 são executados no Intel &reg; Xeon &reg; Platinum 8272CL (Lago cascade). Os tamanhos da série Dv4 oferecem uma combinação de opções de vCPU, memória e armazenamento remoto para a maioria das cargas de trabalho de produção. Os VMs da série Dv4 apresentam [Tecnologia &reg; intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html).

O armazenamento remoto do disco de dados é faturado separadamente das máquinas virtuais. Para utilizar discos de armazenamento premium, utilize os tamanhos Dsv4. Os preços e os contadores de faturação para tamanhos Dsv4 são os mesmos que a série Dv4.

[ACU](acu.md): 195-210<br>
[Armazenamento Premium](premium-storage-performance.md): Não Suportado<br>
[Caching de armazenamento premium](premium-storage-performance.md): Não suportado<br>
[Migração Ao Vivo](maintenance-and-updates.md): Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Suportado<br>
[Suporte de geração VM](generation-2.md): Geração 1<br>
[Rede Acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): Suportado<br>
<br>

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

Os tamanhos da série Dsv4 são executados no Intel &reg; Xeon &reg; Platinum 8272CL (Lago cascata). Os tamanhos da série Dv4 oferecem uma combinação de opções de vCPU, memória e armazenamento remoto para a maioria das cargas de trabalho de produção. Os VMs da série Dsv4 apresentam [Tecnologia &reg; intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html). O armazenamento remoto do disco de dados é faturado separadamente das máquinas virtuais.

[ACU](acu.md): 195-210<br>
[Armazenamento Premium](premium-storage-performance.md): Suportado<br>
[Caching premium armazenamento](premium-storage-performance.md): Suportado<br>
[Migração Ao Vivo](maintenance-and-updates.md): Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Suportado<br>
[Suporte de geração VM](generation-2.md): Geração 1 e 2<br>
[Rede Acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): Suportado<br>
<br>

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Produção de disco não-abacatado por maxilar: IOPS/MBps | NICs máximos|Largura de banda esperada da rede (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v4 | 2 | 8  | Apenas armazenamento remoto | 4 | 3200/48 | 2|1000 |
| Standard_D4s_v4 | 4 | 16 | Apenas armazenamento remoto | 8 | 6400/96 | 2|2000 |
| Standard_D8s_v4 | 8 | 32 | Apenas armazenamento remoto | 16 | 12800/192 | 4|4000 |
| Standard_D16s_v4 | 16 | 64  | Apenas armazenamento remoto | 32 | 25600/384 | 8|8000 |
| Standard_D32s_v4 | 32 | 128 | Apenas armazenamento remoto | 32 | 51200/768 | 8|16000 |
| Standard_D48s_v4 | 48 | 192 | Apenas armazenamento remoto | 32 | 76800/1152 | 8|24000 |
| Standard_D64s_v4 | 64 | 256 | Apenas armazenamento remoto | 32 | 80000/1200 | 8|30000 |
