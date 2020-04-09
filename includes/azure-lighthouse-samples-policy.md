---
title: incluir ficheiro
description: incluir ficheiro
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 1c0be8ed8c176fe32b8fe7fd420d65727c5288d2
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986733"
---
Estes exemplos mostram como utilizar o Azure Policy com subscrições que foram integradas na gestão de recursos delegados do Azure.

| **Modelo** | **Descrição** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | Atribui uma política que adiciona ou remove uma etiqueta (através do efeito de modificação) relativamente a uma subscrição delegada. Para obter mais informações, veja [Implementar uma política que possa ser remediada numa subscrição delegada](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | Atribui uma política que fará a auditoria das atribuições de delegação. |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | Atribui uma política que permite diagnósticos nos recursos do Azure Key Vault numa subscrição delegada (através do efeito deployIfNotExists). Para obter mais informações, veja [Implementar uma política que possa ser remediada numa subscrição delegada](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | Atribui várias políticas para permitir diagnósticos numa subscrição delegada e liga todas as VMs do Windows e Linux à área de trabalho do Log Analytics criada pela política. Para obter mais informações, veja [Implementar uma política que possa ser remediada numa subscrição delegada](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | Aplica uma iniciativa (várias definições de política relacionadas) a uma subscrição delegada. |

