---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 5c1857372eb4aad2bf87e4078bae5e1fc41412a8
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100091905"
---
|Nome |Descrição |Políticas |Versão |
|---|---|---|---|
|[Implementar - Configurar pré-requisitos para permitir agentes de segurança Azure Monitor e Azure em máquinas virtuais](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Configure as máquinas para instalar automaticamente os agentes Azure Monitor e Azure Security. O Security Center recolhe eventos dos agentes e utiliza-os para fornecer alertas de segurança e tarefas de endurecimento personalizadas (recomendações). Crie um grupo de recursos e log Analytics espaço de trabalho na mesma região que a máquina para armazenar registos de auditoria. Esta política aplica-se apenas aos VM em algumas regiões. |5 |1.0.0-pré-visualização |
|[Ativar monitor de Azure para conjuntos de balanças de máquinas virtuais](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Ativar o Monitor Azure para os conjuntos de balanças de máquinas virtuais no âmbito especificado (Grupo de gestão, subscrição ou grupo de recursos). Toma o espaço de trabalho log Analytics como parâmetro. Nota: se a sua atualização de escala definir A política está definida como Manual, tem de aplicar a extensão a todos os VMs do conjunto, chamando-lhes a atualização. No CLI, esta seria az vmss atualização-instâncias. |6 |1.0.1 |
|[Ativar monitor Azure para VMs](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Ativar o Azure Monitor para as máquinas virtuais (VMs) no âmbito especificado (grupo de gestão, subscrição ou grupo de recursos). Toma o espaço de trabalho log Analytics como parâmetro. |10 |2.0.0 |
