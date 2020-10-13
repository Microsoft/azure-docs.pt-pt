---
title: Conjunto manual de capacidade QoS dos Ficheiros Azure NetApp / Microsoft Docs
description: Fornece uma introdução ao pool de capacidade qoS manual e referências para informações adicionais.
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
ms.date: 10/12/2020
ms.author: b-juche
ms.openlocfilehash: 239b985bf45942afbff8ca49d7e15ead24456304
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91993988"
---
# <a name="manual-qos-capacity-pool"></a>Conjunto de capacidade QoS manual

Este artigo fornece uma introdução à funcionalidade de capacidade de qualidade de serviço manual (QoS).

## <a name="how-manual-qos-differs-from-auto-qos"></a>Como o QoS manual difere do QoS automático

O [tipo QoS](azure-netapp-files-understand-storage-hierarchy.md#qos_types) é um atributo de um pool de capacidade. O Azure NetApp Files fornece dois tipos de piscinas de capacidade QoS – auto (padrão) e manual.  

Num pool *manual* de capacidade QoS, pode atribuir a capacidade e a produção para um volume de forma independente. A produção total de todos os volumes criados com um pool manual de capacidade QoS é limitada pela produção total da piscina. É determinado pela combinação do tamanho da piscina e da produção de nível de serviço. 

Num pool de capacidade de QoS *automático,* a produção é atribuída automaticamente aos volumes da piscina, proporcional à quota de tamanho atribuída aos volumes.  

Consulte [a hierarquia de armazenamento dos ficheiros Escandaço do Azure NetApp](azure-netapp-files-understand-storage-hierarchy.md) e [considerações de desempenho para ficheiros Azure NetApp](azure-netapp-files-performance-considerations.md) para considerações sobre os tipos de QoS.

## <a name="example-of-using-manual-qos"></a>Exemplo de utilização do QoS manual

Quando utiliza um pool manual de capacidade QoS com, por exemplo, um sistema SAP HANA, uma base de dados Oracle, ou outras cargas de trabalho que exijam vários volumes, o pool de capacidade pode ser usado para criar estes volumes de aplicação.  Cada volume pode fornecer o tamanho e a produção individuais para satisfazer os requisitos de aplicação.  Consulte [os exemplos de limites de produção de volumes num pool manual de capacidade QoS](azure-netapp-files-service-levels.md#throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool) para obter detalhes sobre os benefícios.  

## <a name="how-to-specify-the-manual-qos-type"></a>Como especificar o tipo QoS manual

Ao [criar um pool de capacidade,](azure-netapp-files-set-up-capacity-pool.md)pode especificar para a piscina de capacidade utilizar o tipo QoS manual.  Também pode [alterar uma piscina de capacidade existente](manage-manual-qos-capacity-pool.md#change-to-qos) para utilizar o tipo QoS manual. 

Definir o tipo de capacidade para QoS manual é uma alteração permanente. Não é possível converter uma ferramenta de capacidade manual do tipo QoS num pool automático de capacidade QoS. 

A utilização do tipo QoS manual requer que [registe a função](manage-manual-qos-capacity-pool.md#register-the-feature).  

## <a name="next-steps"></a>Passos seguintes

* [Gerir um conjunto de capacidade QoS manual](manage-manual-qos-capacity-pool.md)
* [Configurar um conjunto de capacidade](azure-netapp-files-set-up-capacity-pool.md)
* [Hierarquia de Armazenamento](azure-netapp-files-understand-storage-hierarchy.md) 
* [Níveis de serviços do Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Considerações de desempenho para o Azure NetApp Files](azure-netapp-files-performance-considerations.md)
* [Modelo de custo para os Azure NetApp Files](azure-netapp-files-cost-model.md)
* [Resource limits for Azure NetApp Files](azure-netapp-files-resource-limits.md) (Limites dos recursos do Azure NetApp Files)
* [Criar um volume NFS](azure-netapp-files-create-volumes.md)
* [Criar um volume SMB](azure-netapp-files-create-volumes-smb.md)
* [Criar um volume de protocolo duplo](create-volumes-dual-protocol.md)
* [Métricas do Azure NetApp Files](azure-netapp-files-metrics.md)
* [Problemas de capacidade de resolução de problemas](troubleshoot-capacity-pools.md)
