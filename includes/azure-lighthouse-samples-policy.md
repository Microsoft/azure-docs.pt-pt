---
title: incluir ficheiro
description: incluir ficheiro
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 09/24/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: a9c9b5081232988cb4ab80bfcbb9f2d92d1d025f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336582"
---
Estes exemplos mostram como utilizar o Azure Policy com subscrições que foram integradas no Azure Lighthouse.

| **Modelo** | **Descrição** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | Atribui uma política que adiciona ou remove uma etiqueta (através do efeito de modificação) relativamente a uma subscrição delegada. Para obter mais informações, veja [Implementar uma política que possa ser remediada numa subscrição delegada](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | Atribui uma política que fará a auditoria das atribuições de delegação. |
| [policy-delegate-management-groups](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups) | Atribui uma política para confirmar que as subscrições dentro de um grupo de gestão foram delegadas a um inquilino responsável e, se não o tiverem sido, cria a atribuição.
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | Atribui uma política que permite diagnósticos nos recursos do Azure Key Vault numa subscrição delegada (através do efeito deployIfNotExists). Para obter mais informações, veja [Implementar uma política que possa ser remediada numa subscrição delegada](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | Atribui várias políticas para permitir diagnósticos numa subscrição delegada e liga todas as VMs do Windows e Linux à área de trabalho do Log Analytics criada pela política. Para obter mais informações, veja [Implementar uma política que possa ser remediada numa subscrição delegada](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | Aplica uma iniciativa (várias definições de política relacionadas) a uma subscrição delegada. |

