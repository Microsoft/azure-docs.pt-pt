---
title: 'Armazenamento Premium do Azure: design de desempenho em VMs do Linux | Microsoft Docs'
description: Crie aplicativos de alto desempenho usando os discos gerenciados do SSD Premium do Azure. O armazenamento Premium oferece suporte a disco de alto desempenho e baixa latência para cargas de trabalho com uso intensivo de e/s em execução em máquinas virtuais do Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 9940ee4cfce9721ac65f2b3cf1469e180adfa098
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458688"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Armazenamento Premium do Azure: design para alto desempenho
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Às vezes, o que parece ser um problema de desempenho de disco é, na verdade, um afunilamento de rede. Nessas situações, você deve otimizar o [desempenho da rede](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Se você pretende avaliar o benchmark de seu disco, consulte nosso artigo sobre o parâmetro de [comparação de um disco](disks-benchmarks.md).
>
> Se sua VM der suporte à rede acelerada, verifique se ela está habilitada. Se ele não estiver habilitado, você poderá habilitá-lo em VMs já implantadas no [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) e no [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Antes de começar, se você for novo no armazenamento Premium, leia primeiro o [tipo de disco do Azure para VMs de IaaS](disks-types.md) e [metas de escalabilidade para contas de armazenamento de blob de página Premium](../../storage/blobs/scalability-targets-premium-page-blobs.md).


[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Se você pretende avaliar o benchmark de seu disco, consulte nosso artigo sobre o parâmetro de [comparação de um disco](disks-benchmarks.md).

Saiba mais sobre os tipos de disco disponíveis: [Selecione um tipo de disco](disks-types.md)  

Para SQL Server usuários, leia os artigos sobre práticas recomendadas de desempenho para SQL Server:

* [Práticas recomendadas de desempenho para SQL Server em máquinas virtuais do Azure](../windows/sql/virtual-machines-windows-sql-performance.md)
* [O armazenamento Premium do Azure fornece o melhor desempenho para SQL Server na VM do Azure](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)