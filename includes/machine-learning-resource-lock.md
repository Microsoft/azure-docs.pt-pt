---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/16/2020
ms.author: larryfr
ms.openlocfilehash: 25e304daf75b60a7d037640d496ed0972581f13a
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92204472"
---
O Azure permite-lhe colocar _fechaduras_ nos recursos, para que não possam ser apagados ou lidos apenas. __Bloquear um recurso pode levar a resultados inesperados.__ Algumas operações que não parecem modificar o recurso requerem ações que são bloqueadas pelo bloqueio. 

Por exemplo, a aplicação de um bloqueio de eliminação no grupo de recursos para o seu espaço de trabalho impedirá operações de escala para clusters de computação Azure ML.

Para obter mais informações sobre os recursos de bloqueio, consulte [os recursos de bloqueio para evitar alterações inesperadas](../articles/azure-resource-manager/management/lock-resources.md).