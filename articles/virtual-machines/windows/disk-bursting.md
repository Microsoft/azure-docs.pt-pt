---
title: Segurança do disco gerido
description: Saiba sobre a explosão de discos Azure e a explosão de disco para máquinas virtuais Azure
author: albecker1
ms.author: albecker
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 25aa9fc166e831acd2ed0389bbbe4d2dc7e04b19
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594465"
---
# <a name="disk-bursting"></a>Expansão do disco
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Explosão de nível de máquina virtual
O suporte de rebentamento do nível vm é ativado em todas as regiões da Nuvem Pública nestes tamanhos suportados: 
- [Série Lsv2](../lsv2-series.md)

A rutura é ativada por padrão para máquinas virtuais que o suportam.

## <a name="disk-level-bursting"></a>Rebentamento do nível do disco
O bursting também está disponível nos [nossos SSDs premium](disks-types.md#premium-ssd) para tamanhos de disco P20 e menores em todas as regiões. O modo de rajada do disco é ativado por predefinição nas novas implementações dos tamanhos de disco que o suportem. Os tamanhos de disco existentes, caso suportem o modo de rajada do disco, podem permitir a rajadas através de um dos seguintes métodos: 
- **Reiniciar o VM** 
- **Desmontar e voltar a colocar o disco**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
