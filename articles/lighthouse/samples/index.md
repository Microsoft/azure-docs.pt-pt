---
title: Exemplos e modelos do Azure Lighthouse
description: Estes exemplos e modelos do Azure Resource Manager mostram como integrar clientes na gestão de recursos delegados do Azure e dar suporte a cenários do Azure Lighthouse.
author: JnHs
manager: carmonm
ms.service: lighthouse
ms.topic: sample
ms.date: 10/17/2019
ms.author: jenhayes
ms.openlocfilehash: 6d47534026b6fe815f9756a74ba3438dc67a8e02
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553678"
---
# <a name="azure-lighthouse-samples"></a>Exemplos do Azure Lighthouse

A tabela seguinte inclui ligações para modelos importantes do Azure Resource Manager para o Azure Lighthouse. Também pode encontrar estes ficheiros e muito mais no [Repositório de exemplos do Azure Lighthouse](https://github.com/Azure/Azure-Lighthouse-samples/).

## <a name="onboarding-customers-to-azure-delegated-resource-management"></a>Integrar clientes na gestão de recursos delegados do Azure

Disponibilizamos modelos diferentes para abordar cenários de integração específicos. Escolha a melhor opção para si e certifique-se de que modifica o ficheiro de parâmetros de forma a refletir o seu ambiente. Para obter mais informações sobre como utilizar estes ficheiros na sua implementação, veja [Integrar um cliente na gestão de recursos delegados do Azure ](../how-to/onboard-customer.md).

| **Modelo** | **Descrição** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Integra a subscrição de um cliente na gestão de recursos delegados do Azure. Tem de fazer uma implementação separada para cada subscrição. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Integra um ou mais grupos de recursos de um cliente na gestão de recursos delegados do Azure. Utilize **rgDelegatedResourceManagement** para um único grupo de recursos ou **multipleRgDelegatedResourceManagement** para integrar vários grupos de recursos na mesma subscrição. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | Se [publicou uma oferta de serviços geridos no Azure Marketplace](../how-to/publish-managed-services-offers.md), pode optar por utilizar este modelo para integrar recursos para os clientes que tenham aceitado a oferta. Os valores do Marketplace no ficheiro de parâmetros têm de corresponder aos valores que utilizou quando publicou a sua oferta. |

Normalmente, é necessária uma implementação separada por cada subscrição que está a integrar, mas também pode implementar modelos em várias subscrições.

| **Modelo** | **Descrição** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | Implementa modelos do Azure Resource Manager em várias subscrições. |

## <a name="azure-policy"></a>Azure Policy

Estes exemplos mostram como utilizar o Azure Policy com subscrições que foram integradas na gestão de recursos delegados do Azure.

| **Modelo** | **Descrição** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) | Atribui uma política que adiciona ou remove uma etiqueta (através do efeito de modificação) relativamente a uma subscrição delegada. Para obter mais informações, veja [Implementar uma política que possa ser remediada numa subscrição delegada](../how-to/deploy-policy-remediation.md). |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-audit-delegation) | Atribui uma política que fará a auditoria das atribuições de delegação. |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring) | Atribui uma política que permite diagnósticos nos recursos do Azure Key Vault numa subscrição delegada (através do efeito deployIfNotExists). Para obter mais informações, veja [Implementar uma política que possa ser remediada numa subscrição delegada](../how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-sub-monitoring) | Atribui várias políticas para permitir diagnósticos numa subscrição delegada e liga todas as VMs do Windows e Linux à área de trabalho do Log Analytics criada pela política. Para obter mais informações, veja [Implementar uma política que possa ser remediada numa subscrição delegada](../how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-initiative) | Aplica uma iniciativa (várias definições de política relacionadas) a uma subscrição delegada. |

## <a name="azure-monitor"></a>Azure Monitor

Estes exemplos mostram como utilizar o Azure Monitor para criar alertas para subscrições que foram integradas na gestão de recursos delegados do Azure.

| **Modelo** | **Descrição** |
|---------|---------|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | Este modelo cria um alerta do Azure e liga-se a um Grupo de Ações existente.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | Cria vários alertas de registo com base nas consultas do Kusto.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | Implementa um alerta num inquilino quando um utilizador delega uma subscrição a um inquilino gestor.|

## <a name="additional-cross-tenant-scenarios"></a>Cenários adicionais entre inquilinos

Estes exemplos ilustram várias tarefas que se pode realizar em cenários de gestão entre inquilinos.

| **Modelo** | **Descrição** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | Implementa contas de armazenamento em dois grupos de recursos diferentes.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | Cria os serviços de gestão do Azure, interliga-os e implementa soluções adicionais. Para fazer uma implementação completa, utilize o modelo **rgWithAzureMgmt.json**. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | Ativa e configura o Centro de Segurança do Azure na subscrição do Azure visada. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-sentinel) | Implementa e ativa o Azure Sentinel numa área de trabalho do Log Analytics existente numa subscrição delegada. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-log-analytics-vm-extensions) | Estes modelos permitem-lhe implementar extensões de VM do Log Analytics nas suas VMs do Windows e Linux, ligando-as à área de trabalho do Log Analytics designada |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [Gestão de recursos delegados do Azure](../concepts/azure-delegated-resource-management.md).
- Explore o [repositório de exemplos do Azure Lighthouse](https://github.com/Azure/Azure-Lighthouse-samples/).
