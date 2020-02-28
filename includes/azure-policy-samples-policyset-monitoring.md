---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/26/2020
ms.author: dacoulte
ms.openlocfilehash: 1bc83a4c93d4fbd252a99cb801e7484a8e15a689
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77780289"
---
|Nome |Descrição |Políticas |Versão |
|---|---|---|---|
|[Ativar o monitor Azure para conjuntos de escala de máquinavirtual](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Ativar o Monitor Azure para os conjuntos de escala de máquina virtual no âmbito especificado (grupo de gestão, subscrição ou grupo de recursos). Toma o espaço de trabalho log Analytics como parâmetro. Nota: se a atualização do conjunto de escala estiver definida para Manual, tem de aplicar a extensão a todos os VMs definidos, ligando para a atualização dos mesmos. No CLI, estas seriam az vmss update-instances. |6 |1.0.0-pré-visualização |
|[Ativar o Monitor Azure para VMs](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Ativar o Monitor Azure para as Máquinas Virtuais (VMs) no âmbito especificado (Grupo de gestão, subscrição ou grupo de recursos). Toma o espaço de trabalho log Analytics como parâmetro. |6 |1.0.0-pré-visualização |
