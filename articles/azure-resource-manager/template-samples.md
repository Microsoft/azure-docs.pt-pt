---
title: Exemplos de modelo de Azure Resource Manager
description: Exemplos de modelo de Azure Resource Manager para implantação de recursos de gerenciamento, como funções e bloqueios.
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 11/16/2018
ms.author: tomfitz
ms.openlocfilehash: e342507b584a405637fc6728dfcd6e49199a154f
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390167"
---
# <a name="azure-resource-manager-templates-for-management-features"></a>Modelos de Azure Resource Manager para recursos de gerenciamento

A tabela a seguir inclui links para modelos de Azure Resource Manager para recursos fornecidos pelo Resource Manager.

| | |
|-|-|
|**Atribuições de função**||
| [Atribuir função para grupo de recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-rbac-builtinrole-resourcegroup)| Atribui uma função interna a um usuário para um grupo de recursos existente. |
| [Atribuir função à máquina virtual existente](https://github.com/Azure/azure-quickstart-templates/tree/master/101-rbac-builtinrole-virtualmachine)| Atribui uma função interna a um usuário para uma VM existente. |
| [Atribuir função para várias máquinas virtuais](https://github.com/Azure/azure-quickstart-templates/tree/master/201-rbac-builtinrole-multipleVMs)| Atribui funções internas a usuários para mais de uma máquina virtual. |
| [Atribuir função à assinatura do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/subscription-role-assigment)| Atribui uma função a um usuário para uma assinatura do Azure. |
|**Definição de função**||
| [Criar definição de função personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-role-def)| Cria uma nova definição de função em uma assinatura do Azure. |
|**Bloqueio de recurso**||
| [Bloquear grupo de recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment)| Cria um grupo de recursos e aplica um bloqueio **DoNotDelete** ao grupo de recursos. Atribui a função de colaborador a um usuário. |
| | |
