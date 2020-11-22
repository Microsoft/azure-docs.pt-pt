---
title: Eliminar replicações ou volumes de replicação de volumes para Azure NetApp Files replicação transversal de regiões Microsoft Docs
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
ms.date: 11/18/2020
ms.author: b-juche
ms.openlocfilehash: 5ce7a591acd8203775808457219b0ec392cd696e
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2020
ms.locfileid: "95249899"
---
# <a name="delete-volume-replications-or-volumes"></a>Eliminar replicações ou volumes de volume

Este artigo descreve como eliminar as replicações de volume. Também descreve como eliminar o volume de origem ou destino.

## <a name="delete-volume-replications"></a>Eliminar replicações de volume

Pode terminar a ligação de replicação entre a fonte e os volumes de destino eliminando a replicação do volume. Deve eliminar a replicação do volume de destino. A operação de eliminação remove apenas a autorização para a replicação; não remove a fonte nem o volume de destino. 

1. Certifique-se de que o espreitamento de replicação foi quebrado antes de eliminar a replicação do volume. Para quebrar o olhar de replicação: 

    1. Selecione o volume de *destino.* Clique em **Replicação** no Serviço de Armazenamento.  

    2.  Verifique os seguintes campos antes de continuar:  
        * Certifique-se de que o Estado do Espelho aparece ***Espelhado** _.   
            Não tente quebrar a replicação olhando se o Estado do Espelho mostrar _Uninitialized*.
        * Certifique-se de que o Estado da Relação mostra ***Idle** _.   
            Não tente quebrar a replicação espreitando se o Estado da Relação mostrar _Transferring*.   

        Ver [Estado de saúde do Display da relação de replicação](cross-region-replication-display-health-status.md). 

    3.  Clique **em Break Peering**.  

    4.  Tipo **Sim** quando solicitado e clique em **Break**. 

        ![Quebrar olhando de replicação](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)


1. Para eliminar a replicação do volume, selecione **Replication** a partir da fonte ou do volume de destino.  

2. Clique em **Eliminar**.    

3. Confirme a eliminação digitando **Sim** e clicando em **Eliminar**.   

    ![Eliminar a replicação](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="delete-source-or-destination-volumes"></a>Eliminar volumes de origem ou destino

Se pretender eliminar o volume de origem ou destino, deve executar os seguintes passos na ordem descrita. Caso contrário, `Volume with replication cannot be deleted` o erro ocorre.  

1. A partir do volume de destino, [elimine a replicação do volume.](#delete-volume-replications)   

2. Elimine o destino ou o volume de origem conforme necessário clicando no nome do volume e selecione **Delete**.   

    ![Screenshot que mostra o menu de um volume com clique à direita.](../media/azure-netapp-files/cross-region-replication-delete-volume.png)

## <a name="next-steps"></a>Próximos passos  

* [Replicação entre regiões](cross-region-replication-introduction.md)
* [Requisitos e considerações para a utilização da replicação entre regiões](cross-region-replication-requirements-considerations.md)
* [Apresentar o estado de funcionamento da relação de replicação](cross-region-replication-display-health-status.md)
* [Resolução de problemas transversal à replicação da região](troubleshoot-cross-region-replication.md)

