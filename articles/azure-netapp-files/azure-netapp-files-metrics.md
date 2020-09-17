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
ms.date: 09/10/2020
ms.author: b-juche
ms.openlocfilehash: 1690a844ff700a2975be8e972fd90ba71eeb937c
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707786"
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

- *Tamanho total do instantâneo da piscina*    
    A soma do tamanho do instantâneo de todos os volumes na piscina.

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Métricas de utilização de volumes

<!--
- *Volume Quota Size*    
    The quota size (GiB) the volume is provisioned with.   
    This size is the size you selected during capacity pool creation. 
-->
- *Tamanho consumido do volume*   
    O espaço lógico total utilizado num volume (GiB).  
    Este tamanho inclui o espaço lógico utilizado por sistemas de ficheiros ativos e instantâneos.  
- *Tamanho do snapshot de volume*   
   O espaço lógico incremental usado por instantâneos em volume.  

## <a name="performance-metrics-for-volumes"></a>Métricas de desempenho para volumes

- *Média de leitura de latência*   
    O tempo médio para leituras do volume em milissegundos.
- *Latência de escrita média*   
    O tempo médio para escrever a partir do volume em milissegundos.
- *Ler IOPS*   
    O número de leituras para o volume por segundo.
- *Escrever iops*   
    O número de escreve para o volume por segundo.

## <a name="volume-replication-metrics"></a><a name="replication"></a>Métricas de replicação de volume

- *É o estado de replicação do volume saudável*   
    A condição da relação de replicação. 

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
