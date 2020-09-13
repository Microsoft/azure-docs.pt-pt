---
title: Gerir recursos que são criados durante o processo de movimento VM no Azure Resource Mover
description: Saiba como gerir os recursos que são criados durante o processo de movimento VM no Azure Resource Mover
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 4ff1905eb164d004af69ce5b0df3278bf3a46884
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89670769"
---
# <a name="manage-resources-created-for-the-vm-move"></a>Gerir os recursos criados para o movimento VM

Este artigo descreve como gerir recursos que são criados explicitamente pela [Azure Resource Mover](overview.md) para facilitar o processo de movimento VM. 

Após a deslocação de VMs através das regiões, há uma série de recursos criados pela Resource Mover que devem ser limpos manualmente.

## <a name="delete-resources-created-for-vm-move"></a>Eliminar recursos criados para movimento VM

Elimine manualmente a recolha de movimentos e os recursos de recuperação do local criados para o movimento VM.

1. Na região de origem de onde se moveu VMs, reveja os recursos do grupo de ```RegionMoveRG-<sourceregion>-<target-region>``` recursos.
2. Verifique se o VM e todos os outros recursos de origem na recolha de movimentos foram movidos/eliminados. Isto garante que não existem recursos pendentes que os utilizem.
2. Apague estes recursos.

    - O nome da coleção move é ```movecollection-<sourceregion>-<target-region>``` .
    - O nome da conta de armazenamento de cache é ```resmovecache<guid>```
    - O nome do cofre ```ResourceMove-<sourceregion>-<target-region>-GUID``` é.

## <a name="next-steps"></a>Próximos passos

Tente [mover um VM](tutorial-move-region-virtual-machines.md) para outra região com o Resource Mover.