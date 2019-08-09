---
title: Métricas para Azure NetApp Files | Microsoft Docs
description: Descreve as métricas para Azure NetApp Files.
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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 7cf382f511d2ba8452d77bf207f36b749cb31e94
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848797"
---
# <a name="metrics-for-azure-netapp-files"></a>Métricas do Azure NetApp Files

Azure NetApp Files fornece métricas sobre armazenamento alocado, uso real de armazenamento, taxa de transferência de volume, IOPS e latência. Ao analisar essas métricas, você pode obter uma melhor compreensão sobre o padrão de uso e o desempenho do volume de suas contas do NetApp.  

## <a name="capacity_pools"></a>Métricas de uso para pools de capacidade

<!-- 
- *Volume pool allocated size*  
    The size (GiB) of the provisioned capacity pool  
--> 
- *Pool de volumes alocado usado*  
    O total de cotas de volume (GiB) em um determinado pool de capacidade (ou seja, o total de tamanhos provisionados de volumes no pool de capacidade)  
    Esse é o tamanho que você selecionou durante a criação do volume.  
- *Tamanho lógico total do pool de volumes*  
    O total de espaço lógico (GiB) usado em volumes em um pool de capacidade  
<!-- 
- *Volume pool total snapshot size*  
    The total of incremental logical space used by the snapshots  
-->

## <a name="volumes"></a>Métricas de uso para volumes

<!-- 
- *Volume allocated size*   
    The volume size (quota) provisioned in GiB  
--> 
- *Tamanho lógico do volume*   
    O espaço lógico total usado em um volume (GiB)  
    Esse tamanho inclui o espaço lógico usado por instantâneos e sistemas de arquivos ativos.  
- *Tamanho do instantâneo de volume*   
   O espaço lógico incremental usado por instantâneos em um volume  

## <a name="performance-metrics-for-volumes"></a>Métricas de desempenho para volumes

- *AverageReadLatency*   
    O tempo médio para leituras do volume em milissegundos
- *AverageWriteLatency*   
    O tempo médio para gravações do volume em milissegundos
- *ReadIops*   
    O número de leituras para o volume por segundo
- *WriteIops*   
    O número de gravações no volume por segundo

## <a name="next-steps"></a>Passos Seguintes

* [Entender a hierarquia de armazenamento de Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Configurar um conjunto de capacidade](azure-netapp-files-set-up-capacity-pool.md)
* [Criar um volume para o Azure NetApp Files](azure-netapp-files-create-volumes.md)
