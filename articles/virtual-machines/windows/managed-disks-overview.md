---
title: Visão geral do armazenamento do disco Azure
description: Visão geral dos discos geridos Azure, que manuseiam as contas de armazenamento para si quando utilizar VMs Azure
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 13237011e160d0190475eadb3f9d20f0d731df60
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86146621"
---
# <a name="introduction-to-azure-managed-disks"></a>Introduction to Azure managed disks (Introdução aos discos geridos do Azure)

Os discos geridos azure são volumes de armazenamento de nível de bloco que são geridos pela Azure e usados com Máquinas Virtuais Azure. Os discos geridos são como um disco físico num servidor no local, mas virtualizados. Com os discos geridos, tudo o que tem de fazer é especificar o tamanho do disco, o tipo de disco e o fornecimento do disco. Uma vez que forja o disco, Azure trata do resto.

Os tipos disponíveis de discos são discos ultra, unidades de estado sólido premium (SSD), SSDs padrão e unidades de disco rígido padrão (HDD). Para obter informações sobre cada tipo de disco individual, consulte [selecionar um tipo de disco para IaaS VMs](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Selecionar um tipo de disco para VMs IaaS](disks-types.md)
