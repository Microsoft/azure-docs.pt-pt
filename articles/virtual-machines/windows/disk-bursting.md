---
title: Segurança do disco gerido
description: Saiba mais sobre o disco a rebentar para discos Azure e explosão de disco para máquinas virtuais Azure
author: albecker1
ms.author: albecker
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 25aa9fc166e831acd2ed0389bbbe4d2dc7e04b19
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82594465"
---
# <a name="disk-bursting"></a>Expansão do disco
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Rebentamento do nível da máquina virtual
O suporte de rebentamento de nível VM é ativado em todas as regiões da Nuvem Pública nestes tamanhos suportados: 
- [Série Lsv2](../lsv2-series.md)

O rebentamento é ativado por padrão para máquinas virtuais que a suportam.

## <a name="disk-level-bursting"></a>Rebentamento do nível do disco
O bursting também está disponível nos [nossos SSDs premium](disks-types.md#premium-ssd) para tamanhos de disco P20 e menores em todas as regiões. O modo de rajada do disco é ativado por predefinição nas novas implementações dos tamanhos de disco que o suportem. Os tamanhos de disco existentes, caso suportem o modo de rajada do disco, podem permitir a rajadas através de um dos seguintes métodos: 
- **Reinicie a VM** 
- **Retire e recoloque o disco**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
