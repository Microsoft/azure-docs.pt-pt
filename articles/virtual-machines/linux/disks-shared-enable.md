---
title: Ativar discos partilhados para discos geridos pelo Azure
description: Configure um disco gerido pelo Azure com discos partilhados (pré-visualização) para que possa partilhá-lo em vários VMs
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 4562567aea667dd7efadbcfb033e782ec9418e3f
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472065"
---
# <a name="enable-shared-disk"></a>Ativar o disco partilhado

Este artigo cobre como ativar a funcionalidade de discos partilhados (pré-visualização) para discos geridos pelo Azure. Os discos partilhados azure (pré-visualização) são uma nova funcionalidade para discos geridos pelo Azure que lhe permite ligar simultaneamente um disco gerido a várias máquinas virtuais (VMs). A fixação de um disco gerido a vários VMs permite-lhe implementar aplicações agrupadas novas ou migratórias existentes para o Azure. 

Se procura informações conceptuais sobre discos geridos que tenham discos partilhados, consulte [discos partilhados do Azure.](disks-shared.md)
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]