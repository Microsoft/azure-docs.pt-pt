---
title: Mostrar estado de saúde da relação de replicação dos Ficheiros Azure NetApp | Microsoft Docs
description: Descreve como visualizar o estado de replicação no volume de origem ou no volume de destino dos Ficheiros Azure NetApp.
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
ms.date: 03/11/2021
ms.author: b-juche
ms.openlocfilehash: 2819ee3bc76c0b9ff0f35d442e52149096ddc9f7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590981"
---
# <a name="display-health-status-of-replication-relationship"></a>Apresentar o estado de funcionamento da relação de replicação 

Pode ver o estado de replicação no volume de origem ou no volume de destino.  

## <a name="display-replication-status"></a>Display estado de replicação

1. A partir do volume de origem ou do volume de destino, clique em **Replication** under Storage Service para obter qualquer volume.

    São apresentadas as seguintes informações sobre o estado de replicação e a saúde:  
    * **Tipo de ponto final** – Identifica se o volume é a fonte ou destino da replicação.
    * **Saúde** – Apresenta o estado de saúde da relação de replicação.
    * **Estado do espelho** – Mostra um dos seguintes valores:
        * *Não dissenciado:*  
            Este é o estado inicial e padrão quando uma relação de espreitar é criada. O Estado permanece ininicializado até que a inicialização termine com sucesso.
        * *Espelhado:*   
            O volume de destino foi inicializado e está pronto para receber atualizações de espelhamento.
        * *Quebrado:*   
            Este é o estado depois de quebrares a relação de espreitar. O volume de destino está `‘RW’` e os instantâneos estão presentes.
    * **Estado da relação** – Mostra um dos seguintes valores: 
        * *Idle:*  
            Nenhuma operação de transferência está em curso e as transferências futuras não são desativadas.
        * *Transferência:*  
            Está em curso uma operação de transferência e as transferências futuras não são desativadas.
    * **Calendário de replicação** – Mostra a frequência com que serão realizadas atualizações de espelhamento incremental quando a inicialização (cópia de base) estiver concluída.

    * **Progresso total** -- Mostra a quantidade total de dados em bytes transferidos para a operação de transferência atual. Este montante é o pedaço real transferido, e pode diferir do espaço lógico que os volumes de origem e destino reportam.  

    ![Estado de saúde de replicação](../media/azure-netapp-files/cross-region-replication-health-status.png)

> [!NOTE] 
> A relação de replicação mostra o estado de saúde como *pouco saudável* se os trabalhos de replicação anteriores não estiverem completos. Este estado é o resultado de grandes volumes serem transferidos com uma janela de transferência mais baixa (por exemplo, um tempo de transferência de dez minutos para um grande volume). Neste caso, o estado da relação mostra *que a transferência* e o estado de saúde não são *saudáveis.*

## <a name="next-steps"></a>Passos seguintes  

* [Replicação entre regiões](cross-region-replication-introduction.md)
* [Gerir a recuperação após desastre](cross-region-replication-manage-disaster-recovery.md)
* [Redimensionar um volume de destino de replicação transversal](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)
* [Métricas de replicação de volume](azure-netapp-files-metrics.md#replication)
* [Eliminar volumes ou replicações de volume](cross-region-replication-delete.md)
* [Resolver problemas da replicação entre regiões](troubleshoot-cross-region-replication.md)

