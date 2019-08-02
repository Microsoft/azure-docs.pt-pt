---
title: 'Armazenamento Premium do Azure: Design para desempenho em VMs do Windows | Microsoft Docs'
description: Projete aplicativos de alto desempenho usando o armazenamento Premium do Azure. O armazenamento Premium oferece suporte a disco de alto desempenho e baixa latência para cargas de trabalho com uso intensivo de e/s em execução em máquinas virtuais do Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 70da3509ce44fe260f8010ccf6eb1d2192ca6e73
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68695507"
---
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Às vezes, o que parece ser um problema de desempenho de disco é, na verdade, um afunilamento de rede. Nessas situações, você deve otimizar o [desempenho da rede](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Se você pretende avaliar o benchmark de seu disco, consulte nosso artigo sobre o parâmetro de [comparação de um disco](disks-benchmarks.md).
>
> Se sua VM der suporte à rede acelerada, verifique se ela está habilitada. Se ele não estiver habilitado, você poderá habilitá-lo em VMs já implantadas no [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) e no [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Antes de começar, se você for novo no armazenamento Premium, leia primeiro o [tipo de disco do Azure para VMs de IaaS](disks-types.md) e [metas de desempenho e escalabilidade do armazenamento do Azure para contas de armazenamento](../../storage/common/storage-scalability-targets.md).


[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]
