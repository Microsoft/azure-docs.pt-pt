---
title: Gerir recursos que são criados durante o processo de movimento VM no Azure Resource Mover
description: Saiba como gerir os recursos que são criados durante o processo de movimento VM no Azure Resource Mover
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 7ad0e73a90e733af0dd752100ebc71908f68181a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388486"
---
# <a name="manage-resources-created-for-the-vm-move"></a>Gerir os recursos criados para o movimento VM

Este artigo descreve como gerir recursos que são criados explicitamente pela [Azure Resource Mover](overview.md) para facilitar o processo de movimento VM. 

Após a deslocação de VMs através das regiões, há uma série de recursos criados pela Resource Mover que devem ser limpos manualmente.

## <a name="delete-resources-created-for-vm-move"></a>Eliminar recursos criados para movimento VM

Elimine manualmente a recolha de movimentos e os recursos de recuperação do local criados para o movimento VM.

1. Reveja os recursos do grupo de ```ResourceMoverRG-<sourceregion>-<target-region>``` recursos.
2. Verifique se o VM e todos os outros recursos de origem na recolha de movimentos foram movidos/eliminados. Isto garante que não existem recursos pendentes que os utilizem.
2. Apague estes recursos.

    - O nome da coleção move é ```movecollection-<sourceregion>-<target-region>-<metadata-region>``` .
    - O nome da conta de armazenamento de cache é ```resmovecache<guid>```
    - O nome do cofre ```ResourceMove-<sourceregion>-<target-region>-GUID``` é.

## <a name="next-steps"></a>Passos seguintes

Tente [mover um VM](tutorial-move-region-virtual-machines.md) para outra região com o Resource Mover.
