---
title: 'Armazenamento Azure Premium: Design para desempenho em VMs windows / Microsoft Docs'
description: Conceber aplicações de alto desempenho utilizando discos geridos por SSD premium Azure. O Premium Storage oferece suporte de disco de alto desempenho e baixa latência para cargas de trabalho intensivas em I/O em execução em Máquinas Virtuais Azure.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 12fb94bb4f98bde5c70343f18762cefe1ab120f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371343"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Armazenamento premium Azure: design para alto desempenho
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Às vezes, o que parece ser um problema de desempenho do disco é, na verdade, um estrangulamento de rede. Nestas situações, deve otimizar o desempenho da sua [rede.](../../virtual-network/virtual-network-optimize-network-bandwidth.md)
>
> Se procura comparar o seu disco, consulte o nosso artigo sobre [Benchmarking a disk](disks-benchmarks.md).
>
> Se o seu VM suportar uma rede acelerada, deve certificar-se de que está ativado. Se não estiver ativado, pode ativar os VMs já implantados tanto no [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) como no [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Antes de começar, se for novo no Armazenamento Premium, leia primeiro o tipo de [disco Select a Azure para VMs IaaS](disks-types.md) e metas de [escalabilidade para contas](../../storage/blobs/scalability-targets-premium-page-blobs.md)de armazenamento de blob de página premium .

[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Se procura comparar o seu disco, consulte o nosso artigo sobre [Benchmarking a disk](disks-benchmarks.md).

Saiba mais sobre os tipos de disco disponíveis: [Selecione um tipo](disks-types.md) de disco  

Para utilizadores do SQL Server, leia artigos sobre as melhores práticas de desempenho para o Servidor SQL:

* [Melhores práticas de desempenho para o Servidor SQL em Máquinas Virtuais Azure](sql/virtual-machines-windows-sql-performance.md)
* [Armazenamento Azure Premium proporciona maior desempenho para O Servidor SQL em Azure VM](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)