---
title: 'Armazenamento Premium do Azure: Design para desempenho em VMs do Windows | Documentos da Microsoft'
description: Crie aplicativos de alto desempenho com o armazenamento Premium do Azure. O armazenamento Premium oferece suporte de disco de elevado desempenho e de baixa latência para cargas de trabalho de e/S intensivas executadas em máquinas de virtuais do Azure.
services: virtual-machines-windows,storage
author: roygara
ms.service: virtual-machines-windows
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 61f84dc871b4d7261dcb57ab430ce6feee9a0c64
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67658154"
---
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Por vezes, o que parece ser um problema de desempenho de disco é, na verdade, um afunilamento de rede. Nestas situações, deve otimizar seus [desempenho de rede](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Se quiser efetuar testes de desempenho do disco, consulte nosso artigo sobre [comparações de benchmark entre um disco](disks-benchmarks.md).
>
> Se a VM suportar redes aceleradas, certifique-se de que está ativada. Se não estiver ativada, pode ativá-la em VMs já implementadas em ambos [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) e [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Antes de começar, se estiver familiarizado com o armazenamento Premium, leia primeiro o [selecione um tipo de disco do Azure para IaaS VMs](disks-types.md) e [metas de escalabilidade e desempenho de armazenamento do Azure para contas de armazenamento](../../storage/common/storage-scalability-targets.md).

[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]
