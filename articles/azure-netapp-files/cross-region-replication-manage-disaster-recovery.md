---
title: Gerir a recuperação de desastres utilizando a replicação transversal do Azure NetApp Files Microsoft Docs
description: Descreve como gerir a recuperação de desastres utilizando a replicação transversal do Azure NetApp Files.
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
ms.openlocfilehash: ad006279a656758ba856cd3f39c17b0410e525e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708792"
---
# <a name="manage-disaster-recovery-using-cross-region-replication"></a>Gerir a recuperação de desastres utilizando a replicação entre regiões 

Uma replicação contínua entre a fonte e os volumes de destino (ver [Criar olhando para a replicação](cross-region-replication-create-peering.md)) prepara-o para um evento de recuperação de desastres. 

Quando tal evento ocorre, você pode [falhar até o volume de destino,](#break-replication-peering-to-activate-the-destination-volume)permitindo ao cliente ler e escrever para o volume de destino. 

Após a recuperação de desastres, pode falhar de volta ao volume de origem com uma [operação de ressínco](#resync-replication-to-reactivate-the-source-volume) que substitui os dados de volume de origem com os dados do volume de destino.  Em seguida, [restabelece a replicação de origem-destino](#reestablish-source-to-destination-replication) e remonte o volume de origem para o cliente aceder. 

Os detalhes são descritos abaixo. 

## <a name="break-replication-peering-to-activate-the-destination-volume"></a>Quebrar a replicação espreitando para ativar o volume de destino

Quando precisa de ativar o volume de destino (por exemplo, quando pretender falhar na região de destino), tem de quebrar o persimento da replicação e, em seguida, montar o volume de destino.  

1. Para quebrar o olhar de replicação, selecione o volume de destino. Clique em **Replicação** no Serviço de Armazenamento.  

2.  Verifique os seguintes campos antes de continuar:  
    * Certifique-se de que o Estado do Espelho mostra ***espelhado***.   
        Não tente quebrar a replicação olhando se o Estado-Espelho mostrar *Uniinializado*.
    * Certifique-se de que o Estado da Relação mostra ***idle***.   
        Não tente quebrar o olhar de replicação se o Estado da Relação mostrar *a transferência*.   

    Ver [Estado de saúde do Display da relação de replicação](cross-region-replication-display-health-status.md). 

3.  Clique **em Break Peering**.  

4.  Escreva **Sim** quando solicitado e clique no botão **Break.** 

    ![Quebrar olhando de replicação](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)

5.  Monte o volume de destino seguindo os passos no [Monte ou desmonte um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).   
    Este passo permite que um cliente aceda ao volume de destino.

## <a name="resync-replication-to-reactivate-the-source-volume"></a>Replicação resync para reativar o volume de origem   

Após a recuperação de desastres, pode reativar o volume de origem executando uma operação de ressínc.  A operação de ressínca inverte o processo de replicação e sincroniza os dados do volume de destino para o volume de origem.  

> [!IMPORTANT] 
> A operação ressícmo substitui os dados do volume de origem com os dados do volume de destino.  A UI avisa-o sobre o potencial de perda de dados. Ser-lhe-á solicitado que confirme a ação de ressínc antes do início da operação.

1. Para ressíncar a replicação, selecione o volume *de origem.* Clique em **Replicação** no Serviço de Armazenamento. Em seguida, clique em **Resync**.  

2. Escreva **Sim** quando solicitado e clique no botão **Resync.** 
 
    ![Replicação de Resync](../media/azure-netapp-files/cross-region-replication-resync-replication.png)

3. Monitorize o estado de saúde do volume de origem seguindo as etapas no [estado de saúde do display da relação de replicação](cross-region-replication-display-health-status.md).   
    Quando o estado de saúde do volume de origem mostra os seguintes valores, a operação de ressínc está concluída e as alterações efetuadas no volume de destino são agora capturadas no volume de origem:   

    * Estado espelhado: *Espelhado*  
    * Estado de transferência: *Idle*  

## <a name="reestablish-source-to-destination-replication"></a>Reestabelece a replicação de origem-destino

Depois de concluída a operação de ressígnia do destino para a fonte, é necessário quebrar a replicação espreitando novamente para restabelecer a replicação de origem para destino. Também deve voltar a montar o volume de origem para que o cliente possa aceder ao mesmo.  

1. Quebre o olhar de replicação:  
    a. Selecione o volume de *destino.* Clique em **Replicação** no Serviço de Armazenamento.  
    b. Verifique os seguintes campos antes de continuar:   
    * Certifique-se de que o Estado do Espelho mostra ***espelhado***.   
    Não tente quebrar a replicação olhando se o Estado-Espelho mostrar *não ininibido*.  
    * Certifique-se de que o Estado da Relação mostra ***idle***.   
    Não tente quebrar o olhar de replicação se o Estado da Relação mostrar *a transferência*.    

        Ver [Estado de saúde do Display da relação de replicação](cross-region-replication-display-health-status.md). 

    c. Clique **em Break Peering**.   
    d. Escreva **Sim** quando solicitado e clique no botão **Break.**  

2. Resync o volume de origem com o volume de destino:  
    a. Selecione o volume de *destino.* Clique em **Replicação** no Serviço de Armazenamento. Em seguida, clique em **Resync**.   
    b. Escreva **Sim** quando solicitado e clique no botão **Resync.**

3. Remonte o volume de origem seguindo os passos no [Monte ou desmonte um volume para máquinas virtuais Windows ou Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).  
    Este passo permite que um cliente aceda ao volume de origem.

## <a name="next-steps"></a>Passos seguintes  

* [Replicação entre regiões](cross-region-replication-introduction.md)
* [Requisitos e considerações para a utilização da replicação entre regiões](cross-region-replication-requirements-considerations.md)
* [Apresentar o estado de funcionamento da relação de replicação](cross-region-replication-display-health-status.md)
* [Métricas de replicação de volume](azure-netapp-files-metrics.md#replication)
* [Resolver problemas da replicação entre regiões](troubleshoot-cross-region-replication.md)

