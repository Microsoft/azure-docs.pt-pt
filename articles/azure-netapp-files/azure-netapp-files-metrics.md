---
title: Métricas para Ficheiros Azure NetApp / Microsoft Docs
description: O Azure NetApp Files fornece métricas sobre armazenamento atribuído, uso real de armazenamento, IOPS de volume e latência. Use estas métricas para entender o uso e o desempenho.
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
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: b-juche
ms.openlocfilehash: a17e6cc0479cf8ff2306736994a369d9e44dfdda
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745949"
---
# <a name="metrics-for-azure-netapp-files"></a>Métricas do Azure NetApp Files

O Azure NetApp Files fornece métricas sobre armazenamento atribuído, uso real de armazenamento, IOPS de volume e latência. Ao analisar estas métricas, pode obter uma melhor compreensão sobre o padrão de utilização e desempenho de volume das suas contas NetApp.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Métricas de utilização para piscinas de capacidade

- *Tamanho atribuído à piscina*   
    O tamanho da piscina.

- *Piscina atribuída ao tamanho do volume*  
    O total da quota de volume (GiB) num determinado pool de capacidade (isto é, o total dos tamanhos a provisionados dos volumes no pool de capacidade).  
    Este tamanho é o tamanho que selecionou durante a criação de volume.  

- *Tamanho consumido da piscina*  
    O total de espaço lógico (GiB) utilizado em volumes numa piscina de capacidade.  

- *Tamanho total do instantâneo para a piscina*    
    A soma do tamanho do instantâneo de todos os volumes da piscina.

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Métricas de utilização de volumes

- *Volume percentual Tamanho Consumido*    
    A percentagem do volume consumido, incluindo instantâneos.  
- *Tamanho atribuído ao volume*   
    O tamanho previsto de um volume
- *Tamanho da quota de volume*    
    O tamanho da quota (GiB) o volume é a provisionado.   
- *Tamanho consumido do volume*   
    Tamanho lógico do volume (bytes usados).  
    Este tamanho inclui o espaço lógico utilizado por sistemas de ficheiros ativos e instantâneos.  
- *Tamanho do snapshot de volume*   
   O tamanho de todos os instantâneos num volume.  

## <a name="performance-metrics-for-volumes"></a>Métricas de desempenho para volumes

- *Média de leitura de latência*   
    O tempo médio para leituras do volume em milissegundos.
- *Latência de escrita média*   
    O tempo médio para escrever a partir do volume em milissegundos.
- *Ler IOPS*   
    O número de leituras para o volume por segundo.
- *Escrever iops*   
    O número de escreve para o volume por segundo.
<!-- These two metrics are not yet available, until ~ 2020.09
- *Read MiB/s*   
    Read throughput in bytes per second.
- *Write MiB/s*   
    Write throughput in bytes per second.
--> 
<!-- ANF-4128; 2020.07
- *Pool Provisioned Throughput*   
    The total throughput a capacity pool can provide to its volumes based on "Pool Provisioned Size" and "Service Level".
- *Pool Allocated to Volume Throughput*   
    The total throughput allocated to volumes in a given capacity pool (that is, the total of the volumes' allocated throughput in the capacity pool).
-->

<!-- ANF-6443; 2020.11
- *Pool Consumed Throughput*    
    The total throughput being consumed by volumes in a given capacity pool.
-->


## <a name="volume-replication-metrics"></a><a name="replication"></a>Métricas de replicação de volume

> [!NOTE] 
> * O tamanho da transferência de rede (por exemplo, as métricas totais de transferência de replicação do *volume)* podem diferir dos volumes de origem ou destino de uma replicação entre regiões. Este comportamento é o resultado de um motor de replicação eficiente que está a ser usado para minimizar o custo de transferência da rede.
> * As métricas de replicação de volume são atualmente povoadas para volumes de destino de replicação e não a fonte da relação de replicação.

- *É o estado de replicação do volume saudável*   
    A condição da relação de replicação. Um estado saudável é denotado `1` por. Um estado pouco saudável é denotado `0` por.

- *É a transferência de replicação de volume*    
    Se o estado da replicação do volume é "transferência". 
 
- *Tempo de desfasamento da replicação do volume*   
    A quantidade de tempo em segundos através da qual os dados no espelho ficam atrás da fonte. 

- *Duração da transferência de replicação de volume*   
    O tempo em segundos que levou para a última transferência ser concluída. 

- *Tamanho da última transferência de replicação de volume*    
    O número total de bytes transferidos como parte da última transferência. 

- *Progresso da replicação do volume*    
    A quantidade total de dados transferidos para a operação de transferência em curso. 

- *Transferência total de replicação de volume*   
    Os bytes cumulativos transferidos para a relação. 

## <a name="next-steps"></a>Passos seguintes

* [Compreender a hierarquia de armazenamento do Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Configurar um conjunto de capacidade](azure-netapp-files-set-up-capacity-pool.md)
* [Criar um volume para o Azure NetApp Files](azure-netapp-files-create-volumes.md)
