---
title: Segurança do disco gerido
description: Saiba mais sobre o disco a rebentar para discos Azure e explosão de disco para máquinas virtuais Azure
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: ab54b68ab3f7984ee18a39cf3a81fa663af54dee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90889105"
---
# <a name="disk-bursting"></a>Expansão do disco
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Rebentamento do nível da máquina virtual
O suporte de rebentamento de nível VM é ativado em todas as regiões da Nuvem Pública nestes tamanhos suportados: 
- [Série Lsv2](../lsv2-series.md)

A explosão de nível VM também está disponível nos EUA da Central Ocidental para os seguintes tamanhos suportados:
- [Série Dsv3](../dv3-dsv3-series.md)
- [Série Esv3](../ev3-esv3-series.md)

O rebentamento é ativado por padrão para máquinas virtuais que a suportam.

## <a name="disk-level-bursting"></a>Rebentamento do nível do disco
O bursting também está disponível nos [nossos SSDs premium](disks-types.md#premium-ssd) para tamanhos de disco P20 e menores em todas as regiões. O modo de rajada do disco é ativado por predefinição nas novas implementações dos tamanhos de disco que o suportem. Os tamanhos de disco existentes, caso suportem o modo de rajada do disco, podem permitir a rajadas através de um dos seguintes métodos: 
- **Reiniciar o VM** 
- **Retire e recoloque o disco**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
