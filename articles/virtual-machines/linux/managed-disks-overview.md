---
title: Armazenamento de disco do Azure geridos descrição geral do disco para VMs do Linux | Documentos da Microsoft
description: Descrição geral do Azure os discos geridos, que processa as contas de armazenamento por si ao utilizar VMs do Linux
services: virtual-machines-linux,storage
author: roygara
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: overview
ms.date: 04/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 18dc1bd2eea232d0c2eb73d496dd4bd9d2d5016e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64707802"
---
# <a name="introduction-to-azure-managed-disks"></a>Introdução ao Azure para os discos geridos

Do Azure gerido disco é um disco rígido virtual (VHD). Pode considerá-lo como um disco físico num servidor no local, mas, virtualizados. Managed disks do Azure são armazenados como blobs de páginas, que são um objeto de armazenamento de e/s aleatório no Azure. Chamamos um disco gerido "gerido" porque é uma abstração sobre contas de armazenamento do Azure, contentores de BLOBs e blobs de páginas. Com discos geridos, tudo o que precisa fazer é aprovisionar o disco e Azure trata do resto.

Quando seleciona a utilização do Azure geridos discos com cargas de trabalho, o Azure cria e gere o disco por si. Os tipos disponíveis de discos são discos ultra (pré-visualização), premium unidades de estado sólido (SSD), standard SSD, e unidades de disco de rígido standard (HDD). Para obter mais informações sobre cada tipo de disco individual, veja [selecionar um tipo de disco para IaaS VMs](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Selecione um tipo de disco para IaaS VMs](disks-types.md)
