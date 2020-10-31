---
title: Segurança do disco gerido
description: Saiba mais sobre o disco a rebentar para discos Azure e máquinas virtuais Azure.
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: a8d1a69070628248fb710a614369ff9311252c05
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93103036"
---
# <a name="managed-disk-bursting"></a>Segurança do disco gerido
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Rebentamento do nível da máquina virtual
O suporte de rebentamento de nível VM é ativado em todas as regiões da Nuvem Pública nestes tamanhos suportados: 
- [Série Lsv2](lsv2-series.md)

A explosão de nível VM também está disponível nos EUA da Central Ocidental para os seguintes tamanhos suportados:
- [Série Dsv3](dv3-dsv3-series.md)
- [Série Esv3](ev3-esv3-series.md)

O rebentamento é ativado por padrão para máquinas virtuais que a suportam.

## <a name="disk-level-bursting"></a>Rebentamento do nível do disco
O bursting também está disponível nos [nossos SSDs premium](disks-types.md#premium-ssd) para tamanhos de disco P20 e menores em todas as regiões em Azure Public, Government e China Clouds. A rutura do disco é ativada por padrão em todas as implementações novas e existentes dos tamanhos do disco que o suportam. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]