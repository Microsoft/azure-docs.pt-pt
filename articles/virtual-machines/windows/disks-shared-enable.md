---
title: Ativar discos partilhados para discos geridos pelo Azure
description: Configure um disco gerido pelo Azure com discos partilhados (pré-visualização) para que possa partilhá-lo em vários VMs
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e7ada64a50d6ace6ea4d34db87e0501d0311071d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471711"
---
# <a name="enable-shared-disk"></a>Ativar o disco partilhado

Este artigo cobre como ativar a funcionalidade de discos partilhados (pré-visualização) para discos geridos pelo Azure. Os discos partilhados azure (pré-visualização) são uma nova funcionalidade para discos geridos pelo Azure que lhe permite ligar simultaneamente um disco gerido a várias máquinas virtuais (VMs). A fixação de um disco gerido a vários VMs permite-lhe implementar aplicações agrupadas novas ou migratórias existentes para o Azure. 

Se procura informações conceptuais sobre discos geridos que tenham discos partilhados, consulte [discos partilhados do Azure.](disks-shared.md)
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]