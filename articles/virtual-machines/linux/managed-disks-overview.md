---
title: Visão geral de Armazenamento em Disco do Azure para VMs Linux
description: Visão geral dos Azure Managed disks, que lidam com as contas de armazenamento para você ao usar VMs do Linux
author: roygara
ms.service: virtual-machines-linux
ms.topic: overview
ms.date: 12/02/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 2b25da174399b092fe821a46b235d7a2bd14572b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355895"
---
# <a name="introduction-to-azure-managed-disks"></a>Introdução aos Azure Managed disks

O Azure Managed disks são volumes de armazenamento de nível de bloco que são gerenciados pelo Azure e usados com máquinas virtuais do Azure. Os discos gerenciados são como um disco físico em um servidor local, mas virtualizados. Com os discos gerenciados, tudo o que você precisa fazer é especificar o tamanho do disco, o tipo de disco e provisionar o disco. Depois de provisionar o disco, o Azure lida com o resto.

Os tipos de discos disponíveis são ultra discos, unidades de estado sólido Premium (SSD), SSDs padrão e unidades de disco rígido padrão (HDD). Para obter informações sobre cada tipo de disco individual, consulte [selecionar um tipo de disco para VMs IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Selecionar um tipo de disco para VMs IaaS](disks-types.md)