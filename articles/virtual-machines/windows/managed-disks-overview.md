---
title: Visão geral de Armazenamento em Disco do Azure disco gerenciado para VMs do Windows | Microsoft Docs
description: Visão geral dos Azure Managed disks, que manipula as contas de armazenamento para você ao usar VMs do Windows do Azure
services: virtual-machines-windows,storage
author: roygara
ms.service: virtual-machines-windows
ms.workload: storage
ms.tgt_pltfrm: vm-windows
ms.topic: overview
ms.date: 04/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: fb1ee8556935b141dfee6a18c96ecafb476aa584
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "64725810"
---
# <a name="introduction-to-azure-managed-disks"></a>Introdução aos Azure Managed disks

Um disco gerenciado do Azure é um VHD (disco rígido virtual). Você pode considerar isso como um disco físico em um servidor local, mas virtualizado. Os Azure Managed disks são armazenados como BLOBs de páginas, que são um objeto de armazenamento de e/s aleatório no Azure. Chamamos um disco gerenciado "Managed" porque ele é uma abstração em blobs de página, contêineres de BLOB e contas de armazenamento do Azure. Com os discos gerenciados, tudo o que você precisa fazer é provisionar o disco e o Azure cuida do restante.

Ao optar por usar o Azure Managed disks com suas cargas de trabalho, o Azure cria e gerencia o disco para você. Os tipos de discos disponíveis são unidades de estado sólido (SSD) (versão prévia), SSD Premium, SSD Standard e HDD (unidades de disco rígido) padrão. Para obter mais informações sobre cada tipo de disco individual, consulte [selecionar um tipo de disco para VMs IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Selecionar um tipo de disco para VMs IaaS](disks-types.md)