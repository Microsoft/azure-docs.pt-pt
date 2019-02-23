---
title: As métricas para os ficheiros NetApp do Azure | Documentos da Microsoft
description: Descreve as métricas para os ficheiros do Azure NetApp.
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
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 1563b60ef26ac5e4d40f45095d0109dd9dd71570
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56672581"
---
# <a name="metrics-for-azure-netapp-files"></a>Métricas do Azure NetApp Files

Os ficheiros do Azure NetApp fornece métricas sobre o armazenamento atribuído, utilização de armazenamento real, débito de volume, IOPS e latência. Ao analisar estas métricas, pode obter uma melhor compreensão sobre o desempenho de padrão e o volume de utilização das suas contas de NetApp.  

## <a name="capacity_pools"></a>Métrica de utilização para os conjuntos de capacidade

- *Tamanho do volume de agrupamento alocado*  
    Este é o tamanho (GiB) do conjunto de capacidade aprovisionada.  
- *Conjunto de volume alocado utilizado*  
    Este é o total da quota de volume (GiB) num conjunto especificado de capacidade (ou seja, o total de tamanhos de aprovisionamento dos volumes no agrupamento de capacidade). Este é o tamanho selecionado durante a criação do volume.  
- *Tamanho lógico total do volume conjunto*  
    Este é o total de espaço lógico (GiB) utilizado nos volumes de um conjunto de capacidade.  
- *Tamanho total do instantâneo do volume de agrupamento*  
    Este é o total de incremental espaço lógico utilizado pelos instantâneos.  

## <a name="volumes"></a>Métrica de utilização de volumes

- *Atribuído o tamanho do volume*   
    Este é o tamanho do volume aprovisionado em GiB (quota).  
- *Tamanho lógico do volume*   
    Este é o total de espaço lógico utilizado num volume (GiB). Este tamanho inclui lógico espaço usado por sistemas de ficheiros Active Directory e instantâneos.  
- *Tamanho do instantâneo de volume*   
    Este é o espaço de lógico incremental utilizado pelo instantâneos num volume.  

## <a name="next-steps"></a>Passos Seguintes

* [Compreender a hierarquia de armazenamento de ficheiros do Azure NetApp](azure-netapp-files-understand-storage-hierarchy.md)
* [Configurar um conjunto de capacidade](azure-netapp-files-set-up-capacity-pool.md)
* [Criar um volume para o Azure NetApp Files](azure-netapp-files-create-volumes.md)
