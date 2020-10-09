---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 39acae85e61cf7d5908589589958ca03d367b3ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91820111"
---
|Nome |Descrição |Políticas |Versão |
|---|---|---|---|
|[Ativar monitor de Azure para conjuntos de balanças de máquinas virtuais](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Ativar o Monitor Azure para os conjuntos de balanças de máquinas virtuais no âmbito especificado (Grupo de gestão, subscrição ou grupo de recursos). Toma o espaço de trabalho log Analytics como parâmetro. Nota: se a sua atualização de escala definir A política está definida como Manual, tem de aplicar a extensão a todos os VMs do conjunto, chamando-lhes a atualização. No CLI, esta seria az vmss atualização-instâncias. |6 |1.0.1 |
|[Ativar monitor Azure para VMs](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Ativar o Azure Monitor para as máquinas virtuais (VMs) no âmbito especificado (grupo de gestão, subscrição ou grupo de recursos). Toma o espaço de trabalho log Analytics como parâmetro. |10 |2.0.0 |
