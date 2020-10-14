---
title: incluir ficheiro
description: incluir ficheiro
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 10/12/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 20d80fe1a09d388552b6289f8a9b23c878672f94
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974295"
---
Estes exemplos mostram como utilizar o Azure Policy com subscrições que foram integradas no Azure Lighthouse.

| **Modelo** | **Descrição** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | Atribui uma política que adiciona ou remove uma etiqueta (através do efeito de modificação) relativamente a uma subscrição delegada. Para obter mais informações, veja [Implementar uma política que possa ser remediada numa subscrição delegada](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-allow-certain-managing-tenants](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-allow-certain-managing-tenants) | Atribui uma política que restringe as delegações do Azure Lighthouse a inquilinos responsáveis específicos. |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | Atribui uma política que fará a auditoria das atribuições de delegação. |
| [policy-delegate-management-groups](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups) | Atribui uma política para confirmar que as subscrições dentro de um grupo de gestão foram delegadas a um inquilino responsável e, se não o tiverem sido, cria a atribuição.
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | Atribui uma política que permite diagnósticos nos recursos do Azure Key Vault numa subscrição delegada (através do efeito deployIfNotExists). Para obter mais informações, veja [Implementar uma política que possa ser remediada numa subscrição delegada](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | Atribui várias políticas para permitir diagnósticos numa subscrição delegada e liga todas as VMs do Windows e Linux à área de trabalho do Log Analytics criada pela política. Para obter mais informações, veja [Implementar uma política que possa ser remediada numa subscrição delegada](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | Aplica uma iniciativa (várias definições de política relacionadas) a uma subscrição delegada. |

