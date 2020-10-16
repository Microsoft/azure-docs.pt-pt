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
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: 0904ac36a9453e51dbb1efc50eee2b9bf3c669c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708822"
---
# <a name="delete-replications"></a>Eliminar replicações

Pode terminar a ligação de replicação entre a fonte e os volumes de destino eliminando a replicação do volume. Pode efetuar a operação de eliminação a partir da fonte ou do volume de destino. A operação de eliminação remove apenas a autorização para a replicação; não remove a fonte nem o volume de destino. 

## <a name="steps"></a>Passos

1. Para eliminar a replicação do volume, selecione **Replication** a partir da fonte ou do volume de destino.  

2. Clique em **Eliminar**.    

3. Confirme a eliminação digitando **Sim** e clicando em **Eliminar**.   

    ![Eliminar a replicação](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="next-steps"></a>Passos seguintes  

* [Replicação entre regiões](cross-region-replication-introduction.md)
* [Requisitos e considerações para a utilização da replicação entre regiões](cross-region-replication-requirements-considerations.md)
* [Apresentar o estado de funcionamento da relação de replicação](cross-region-replication-display-health-status.md)
* [Resolução de problemas transversal à replicação da região](troubleshoot-cross-region-replication.md)

