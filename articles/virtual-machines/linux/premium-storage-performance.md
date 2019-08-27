---
title: 'Armazenamento Premium do Azure: Design para desempenho em VMs do Windows | Microsoft Docs'
description: Projete aplicativos de alto desempenho usando o armazenamento Premium do Azure. O armazenamento Premium oferece suporte a disco de alto desempenho e baixa latência para cargas de trabalho com uso intensivo de e/s em execução em máquinas virtuais do Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 79bd41c08a566b55efb4a29084847848e001629f
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036041"
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

Se você pretende avaliar o benchmark de seu disco, consulte nosso artigo sobre o parâmetro de [comparação de um disco](disks-benchmarks.md).

Saiba mais sobre os tipos de disco disponíveis: [Selecionar um tipo de disco](disks-types.md)  

Para SQL Server usuários, leia os artigos sobre práticas recomendadas de desempenho para SQL Server:

* [Práticas recomendadas de desempenho para SQL Server em máquinas virtuais do Azure](../windows/sql/virtual-machines-windows-sql-performance.md)
* [O armazenamento Premium do Azure fornece o melhor desempenho para SQL Server na VM do Azure](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)