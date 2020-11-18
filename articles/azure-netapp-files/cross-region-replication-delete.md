---
title: Eliminar replicações para a replicação transversal do Azure NetApp Files Microsoft Docs
description: Descreve como eliminar uma ligação de replicação que já não é necessária entre a fonte e os volumes de destino.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/17/2020
ms.author: b-juche
ms.openlocfilehash: e08b69271ba9d115c26418bc5e421ee6c94b031d
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695712"
---
# <a name="delete-replications"></a>Eliminar replicações

Pode terminar a ligação de replicação entre a fonte e os volumes de destino eliminando a replicação do volume. Pode efetuar a operação de eliminação a partir da fonte ou do volume de destino. A operação de eliminação remove apenas a autorização para a replicação; não remove a fonte nem o volume de destino. 

## <a name="steps"></a>Passos

1. Certifique-se de que o espreitamento de replicação foi quebrado antes de eliminar a replicação do volume.    
    Consulte [o estado de saúde do Display da relação de replicação](cross-region-replication-display-health-status.md) e o olhar de [replicação de Break](cross-region-replication-manage-disaster-recovery.md#break-replication-peering-to-activate-the-destination-volume).  

1. Para eliminar a replicação do volume, selecione **Replication** a partir da fonte ou do volume de destino.  

2. Clique em **Eliminar**.    

3. Confirme a eliminação digitando **Sim** e clicando em **Eliminar**.   

    ![Eliminar a replicação](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="next-steps"></a>Passos seguintes  

* [Replicação entre regiões](cross-region-replication-introduction.md)
* [Requisitos e considerações para a utilização da replicação entre regiões](cross-region-replication-requirements-considerations.md)
* [Apresentar o estado de funcionamento da relação de replicação](cross-region-replication-display-health-status.md)
* [Resolução de problemas transversal à replicação da região](troubleshoot-cross-region-replication.md)

