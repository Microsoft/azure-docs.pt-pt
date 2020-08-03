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
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: 7e6ab90010d4379c1640f73a8deeba874e601daf
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513813"
---
# <a name="metrics-for-azure-netapp-files"></a>Métricas do Azure NetApp Files

O Azure NetApp Files fornece métricas sobre armazenamento atribuído, uso real de armazenamento, IOPS de volume e latência. Ao analisar estas métricas, pode obter uma melhor compreensão sobre o padrão de utilização e desempenho de volume das suas contas NetApp.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Métricas de utilização para piscinas de capacidade

<!-- 
- *Pool Provisioned Size*  
    The logical space (GiB) the capacity pool is provisioned with.  
    This size is the size you selected during capacity pool creation. 
--> 
- *Piscina atribuída ao tamanho do volume*  
    O total da quota de volume (GiB) num determinado pool de capacidade (isto é, o total dos tamanhos a provisionados dos volumes no pool de capacidade).  
    Este tamanho é o tamanho que selecionou durante a criação de volume.  
- *Tamanho consumido da piscina*  
    O total de espaço lógico (GiB) utilizado em volumes numa piscina de capacidade.  
<!-- 
- *Pool Consumed Snapshot Size*  
    The total of logical space (GiB) used by snapshots across all volumes in a capacity pool. 
-->

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

- *Média DeOncência*   
    O tempo médio para leituras do volume em milissegundos.
- *Média Desaquimia*   
    O tempo médio para escrever a partir do volume em milissegundos.
- *ReadIops*   
    O número de leituras para o volume por segundo.
- *WriteIops*   
    O número de escreve para o volume por segundo.

## <a name="next-steps"></a>Passos seguintes

* [Compreender a hierarquia de armazenamento do Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Configurar um conjunto de capacidade](azure-netapp-files-set-up-capacity-pool.md)
* [Criar um volume para o Azure NetApp Files](azure-netapp-files-create-volumes.md)
