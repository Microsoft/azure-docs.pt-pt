---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/12/2020
ms.author: dacoulte
ms.openlocfilehash: c0a55737063e9867303b93c23a58df699636d7da
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192510"
---
|Nome |Descrição |Políticas |Versão |
|---|---|---|---|
|[Ativar o Monitor Azure para conjuntos de escala VM (VMSS)](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Ativar o Monitor Azure para os conjuntos de escala VM no âmbito especificado (grupo de gestão, subscrição ou grupo de recursos). Toma o espaço de trabalho log Analytics como parâmetro. Nota: se a atualização do conjunto de escala estiver definida para Manual, tem de aplicar a extensão a todos os VMs definidos, ligando para a atualização dos mesmos. No CLI, estas seriam az vmss update-instances. |6 |1.0.0-pré-visualização |
|[Ativar o Monitor Azure para VMs](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Ativar o Monitor Azure para as Máquinas Virtuais (VMs) no âmbito especificado (Grupo de gestão, subscrição ou grupo de recursos). Toma o espaço de trabalho log Analytics como parâmetro. |6 |1.0.0-pré-visualização |
