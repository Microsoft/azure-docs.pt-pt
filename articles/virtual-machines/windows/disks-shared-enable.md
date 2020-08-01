---
title: Ativar discos partilhados para discos geridos aZure
description: Configurar um disco gerido a Azure com discos partilhados para que possa partilhá-lo em vários VMs
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/30/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: cc365f6eef4da61ab7de5113b8f4b2b185360740
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460065"
---
# <a name="enable-shared-disk"></a>Ativar disco partilhado

Este artigo abrange como ativar a funcionalidade de discos partilhados para discos geridos Azure. Os discos partilhados Azure são uma nova funcionalidade para discos geridos aZure que lhe permite anexar um disco gerido a várias máquinas virtuais (VMs) simultaneamente. A anexação de um disco gerido a vários VMs permite-lhe implementar novas ou migrar aplicações agrupadas existentes para o Azure. 

Se procura informações conceptuais sobre discos geridos que tenham discos partilhados ativados, consulte [os discos partilhados do Azure.](disks-shared.md)
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]