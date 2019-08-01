---
title: Visão geral de Armazenamento em Disco do Azure disco gerenciado para VMs Linux | Microsoft Docs
description: Visão geral dos Azure Managed disks, que manipula as contas de armazenamento para você ao usar VMs do Linux
author: roygara
ms.service: virtual-machines-linux
ms.topic: overview
ms.date: 04/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 5052504b4fb63ce9d638a9d2505ad1c08d3324de
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68695469"
---
# <a name="introduction-to-azure-managed-disks"></a>Introdução aos Azure Managed disks

Um disco gerenciado do Azure é um VHD (disco rígido virtual). Você pode considerar isso como um disco físico em um servidor local, mas virtualizado. Os Azure Managed disks são armazenados como BLOBs de páginas, que são um objeto de armazenamento de e/s aleatório no Azure. Chamamos um disco gerenciado "Managed" porque ele é uma abstração em blobs de página, contêineres de BLOB e contas de armazenamento do Azure. Com os discos gerenciados, tudo o que você precisa fazer é provisionar o disco e o Azure cuida do restante.

Ao optar por usar o Azure Managed disks com suas cargas de trabalho, o Azure cria e gerencia o disco para você. Os tipos de discos disponíveis são ultra discos (versão prévia), unidades de estado sólido Premium (SSD), SSD padrão e unidades de disco rígido padrão (HDD). Para obter mais informações sobre cada tipo de disco individual, consulte [selecionar um tipo de disco para VMs IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Selecionar um tipo de disco para VMs IaaS](disks-types.md)
