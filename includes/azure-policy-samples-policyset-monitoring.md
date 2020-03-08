---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2020
ms.author: dacoulte
ms.openlocfilehash: e6ac52694dd4a786714b571f50dc5adaf79dff06
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78668985"
---
|Nome |Descrição |Políticas |Versão |
|---|---|---|---|
|[Ativar o monitor Azure para conjuntos de escala de máquinavirtual](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Ativar o Monitor Azure para os conjuntos de escala de máquina virtual no âmbito especificado (grupo de gestão, subscrição ou grupo de recursos). Toma o espaço de trabalho log Analytics como parâmetro. Nota: se a atualização do conjunto de escala estiver definida para Manual, tem de aplicar a extensão a todos os VMs definidos, ligando para a atualização dos mesmos. No CLI, estas seriam az vmss update-instances. |6 |1.0.0-pré-visualização |
|[Ativar o Monitor Azure para VMs](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Ativar o Monitor Azure para as Máquinas Virtuais (VMs) no âmbito especificado (Grupo de gestão, subscrição ou grupo de recursos). Toma o espaço de trabalho log Analytics como parâmetro. |6 |1.0.0-pré-visualização |
