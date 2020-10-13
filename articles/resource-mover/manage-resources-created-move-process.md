---
title: Gerir recursos que são criados durante o processo de movimento VM no Azure Resource Mover
description: Saiba como gerir os recursos que são criados durante o processo de movimento VM no Azure Resource Mover
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 6d6f56fb47bfdaadc6704e2a13ebcf0e893b8b1c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90602358"
---
# <a name="manage-resources-created-for-the-vm-move"></a>Gerir os recursos criados para o movimento VM

Este artigo descreve como gerir recursos que são criados explicitamente pela [Azure Resource Mover](overview.md) para facilitar o processo de movimento VM. 

Após a deslocação de VMs através das regiões, há uma série de recursos criados pela Resource Mover que devem ser limpos manualmente.

## <a name="delete-resources-created-for-vm-move"></a>Eliminar recursos criados para movimento VM

Elimine manualmente a recolha de movimentos e os recursos de recuperação do local criados para o movimento VM.

1. Reveja os recursos do grupo de ```ResourceMoverRG-<sourceregion>-<target-region>``` recursos.
2. Verifique se o VM e todos os outros recursos de origem na recolha de movimentos foram movidos/eliminados. Isto garante que não existem recursos pendentes que os utilizem.
2. Apague estes recursos.

    - O nome da coleção move é ```movecollection-<sourceregion>-<target-region>``` .
    - O nome da conta de armazenamento de cache é ```resmovecache<guid>```
    - O nome do cofre ```ResourceMove-<sourceregion>-<target-region>-GUID``` é.

## <a name="next-steps"></a>Passos seguintes

Tente [mover um VM](tutorial-move-region-virtual-machines.md) para outra região com o Resource Mover.