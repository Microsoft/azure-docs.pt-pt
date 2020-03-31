---
title: Métricas para Ficheiros Azure NetApp / Microsoft Docs
description: Descreve métricas para Ficheiros Azure NetApp.
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
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: c8e3b616dee1ab4e6bb6e77c6a8bab5661d4e20b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460437"
---
# <a name="metrics-for-azure-netapp-files"></a>Métricas do Azure NetApp Files

Os Ficheiros Azure NetApp fornecem métricas sobre armazenamento atribuído, utilização real de armazenamento, IOPS de volume e latência. Ao analisar estas métricas, pode obter uma melhor compreensão sobre o padrão de utilização e desempenho de volume das suas contas NetApp.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Métricas de utilização para piscinas de capacidade

<!-- 
- *Pool Provisioned Size*  
    The logical space (GiB) the capacity pool is provisioned with.  
    This size is the size you selected during capacity pool creation. 
--> 
- *Piscina atribuída ao tamanho do volume*  
    O total da quota de volume (GiB) numa determinada piscina de capacidade (isto é, o total das dimensões aprovisionadas dos volumes no pool de capacidade).  
    Este tamanho é o tamanho que selecionou durante a criação de volume.  
- *Piscina Consumida Tamanho*  
    O total de espaço lógico (GiB) usado em volumes em uma piscina de capacidade.  
<!-- 
- *Pool Consumed Snapshot Size*  
    The total of logical space (GiB) used by snapshots across all volumes in a capacity pool. 
-->

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Métricas de utilização para volumes

<!--
- *Volume Quota Size*    
    The quota size (GiB) the volume is provisioned with.   
    This size is the size you selected during capacity pool creation. 
-->
- *Volume Consumido Tamanho*   
    O espaço lógico total utilizado num volume (GiB).  
    Este tamanho inclui espaço lógico utilizado por sistemas de ficheiros ativos e instantâneos.  
- *Tamanho do instantâneo de volume*   
   O espaço lógico incremental usado por instantâneos num volume.  

## <a name="performance-metrics-for-volumes"></a>Métricas de desempenho para volumes

- *MédiaDeLeitura*   
    O tempo médio para leituras do volume em milissegundos.
- *MédiaWriteLatency*   
    O tempo médio para escrever a partir do volume em milissegundos.
- *Leituras*   
    O número de leituras para o volume por segundo.
- *WriteIops*   
    O número de escritos para o volume por segundo.

## <a name="next-steps"></a>Passos seguintes

* [Compreender a hierarquia de armazenamento do Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Configurar um conjunto de capacidade](azure-netapp-files-set-up-capacity-pool.md)
* [Criar um volume para o Azure NetApp Files](azure-netapp-files-create-volumes.md)
