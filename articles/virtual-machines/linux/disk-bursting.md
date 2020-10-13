---
title: Segurança do disco gerido
description: Saiba mais sobre o disco a rebentar para discos Azure e o disco a rebentar para máquinas virtuais Azure no Linux.
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: ee3b65e95fe3f4c52136cb3645c987fe7f6c9a7a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91975370"
---
# <a name="managed-disk-bursting"></a>Segurança do disco gerido
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Rebentamento do nível da máquina virtual
O suporte de rebentamento de nível VM é ativado em todas as regiões da Nuvem Pública nestes tamanhos suportados: 
- [Série Lsv2](../lsv2-series.md)

A explosão de nível VM também está disponível nos EUA da Central Ocidental para os seguintes tamanhos suportados:
- [Série Dsv3](../dv3-dsv3-series.md)
- [Série Esv3](../ev3-esv3-series.md)

O rebentamento é ativado por padrão para máquinas virtuais que a suportam.

## <a name="disk-level-bursting"></a>Rebentamento do nível do disco
O bursting também está disponível nos [nossos SSDs premium](../disks-types.md#premium-ssd) para tamanhos de disco P20 e menores em todas as regiões em Azure Public, Government e China Clouds. A rutura do disco é ativada por padrão em todas as implementações novas e existentes dos tamanhos do disco que o suportam. 

[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]