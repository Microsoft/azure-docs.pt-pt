---
title: Ativar discos partilhados para discos geridos pelo Azure
description: Configure um disco gerido pelo Azure com discos partilhados (pré-visualização) para que possa partilhá-lo em vários VMs
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 1cb581f0914dbafff4a46510924115e82e42b990
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011145"
---
# <a name="enable-shared-disk"></a>Ativar o disco partilhado

Este artigo cobre como ativar a funcionalidade de discos partilhados (pré-visualização) para discos geridos pelo Azure. Os discos partilhados azure (pré-visualização) são uma nova funcionalidade para discos geridos pelo Azure que lhe permite ligar simultaneamente um disco gerido a várias máquinas virtuais (VMs). A fixação de um disco gerido a vários VMs permite-lhe implementar aplicações agrupadas novas ou migratórias existentes para o Azure. 

Se procura informações conceptuais sobre discos geridos que tenham discos partilhados, consulte [discos partilhados do Azure.](disks-shared.md)
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]