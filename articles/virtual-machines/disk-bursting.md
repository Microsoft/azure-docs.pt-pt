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
ms.openlocfilehash: dcdbf94e547581cb9ff885ac5896467abdf316ae
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576198"
---
# <a name="managed-disk-bursting"></a>Segurança do disco gerido
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Rebentamento do nível da máquina virtual
A rutura do nível VM é ativada nas seguintes séries de VM em todas as regiões em que são suportadas:
- [Série Lsv2](lsv2-series.md)
- [Série Dsv3](dv3-dsv3-series.md)
- [Série Esv3](ev3-esv3-series.md)

O rebentamento é ativado por padrão para máquinas virtuais que a suportam.

## <a name="disk-level-bursting"></a>Rebentamento do nível do disco
O bursting também está disponível nos [nossos SSDs premium](disks-types.md#premium-ssd) para tamanhos de disco P20 e menores em todas as regiões em Azure Public, Government e China Clouds. A rutura do disco é ativada por padrão em todas as implementações novas e existentes dos tamanhos do disco que o suportam. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]
