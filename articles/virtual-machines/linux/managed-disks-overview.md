---
title: Visão geral do armazenamento do disco
description: Visão geral dos discos geridos Azure, que manuseiam as contas de armazenamento para si quando utilizar VMs.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d3932047422571399ad0562b2f9f2d19c3e6799b
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145303"
---
# <a name="introduction-to-azure-managed-disks"></a>Introduction to Azure managed disks (Introdução aos discos geridos do Azure)

Os discos geridos azure são volumes de armazenamento de nível de bloco que são geridos pela Azure e usados com Máquinas Virtuais Azure. Os discos geridos são como um disco físico num servidor no local, mas virtualizados. Com os discos geridos, tudo o que tem de fazer é especificar o tamanho do disco, o tipo de disco e o fornecimento do disco. Uma vez que forja o disco, Azure trata do resto.

Os tipos disponíveis de discos são discos ultra, unidades de estado sólido premium (SSD), SSDs padrão e unidades de disco rígido padrão (HDD). Para obter informações sobre cada tipo de disco individual, consulte [selecionar um tipo de disco para IaaS VMs](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Selecionar um tipo de disco para VMs IaaS](disks-types.md)