---
title: Armazenamento Azure Premium - Design para alto desempenho
description: Desenhe aplicações de alto desempenho utilizando discos geridos Azure Premium SSD. O Premium Storage oferece suporte a discos de alto desempenho e baixa latência para cargas de trabalho intensivas em I/O em funcionamento em Máquinas Virtuais Azure.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 46c917a2d27f5efaa1e902db0d5da5375578b8b8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86526045"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Armazenamento premium Azure: design para alto desempenho
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Às vezes, o que parece ser um problema de desempenho em disco é, na verdade, um estrangulamento de rede. Nestas situações, deverá otimizar o desempenho da sua [rede.](../../virtual-network/virtual-network-optimize-network-bandwidth.md)
>
> Se procura comparar o seu disco, consulte o nosso artigo sobre [Benchmarking a disk](disks-benchmarks.md).
>
> Se o seu VM suportar uma rede acelerada, deve certificar-se de que está ativado. Se não estiver ativado, pode ative-lo em VMs já implantados tanto no [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) como no [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Antes de começar, se é novo no Premium Storage, leia primeiro o [tipo de disco Azure para alvos IaaS VMs](disks-types.md) e [Escalaability para contas de armazenamento de blob de página premium.](../../storage/blobs/scalability-targets-premium-page-blobs.md)

[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Se procura comparar o seu disco, consulte o nosso artigo sobre [Benchmarking a disk](disks-benchmarks.md).

Saiba mais sobre os tipos de disco disponíveis: [Selecione um tipo de disco](disks-types.md)  

Para os utilizadores do SQL Server, leia artigos sobre as melhores práticas de desempenho para o SQL Server:

* [Melhores práticas de desempenho para o SQL Server em Máquinas Virtuais Azure](../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md)
* [O Azure Premium Storage proporciona o melhor desempenho para o SQL Server em Azure VM](https://cloudblogs.microsoft.com/sqlserver/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm/)
