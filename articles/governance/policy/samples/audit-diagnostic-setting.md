---
title: A definição de exemplo - diagnóstico de auditoria
description: Esta definição de política de exemplo audita se especificado de definições de diagnóstico não ativadas para tipos de recursos.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
origin.date: 04/27/2018
ms.date: 03/11/2019
ms.author: v-biyu
ms.openlocfilehash: 66c9c1c21cad7fb4058a91be826a50059691877c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60545644"
---
# <a name="sample---audit-diagnostic-setting"></a>A definição de exemplo - diagnóstico de auditoria

Esta política incorporada audita se as definições de diagnóstico não estão ativadas para tipos de recursos especificados. Especifica uma matriz de tipos de recursos para verificar se as definições de diagnóstico estão ativadas.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Modelo de exemplo
```json
{
    "name": "audit-diagnostic-setting",
    "properties": {
        "displayName": "Audit diagnostic setting",
        "description": "Audit diagnostic setting for selected resource types",
        "mode": "all",
        "parameters": {
            "listOfResourceTypes": {
                "type": "Array",
                "metadata": {
                    "displayName": "Resource Types",
                    "strongType": "resourceTypes"
                }
            }
        },
        "policyRule": {
            "if": {
                "field": "type",
                "in": "[parameters('listOfResourceTypes')]"
            },
            "then": {
                "effect": "auditIfNotExists",
                "details": {
                    "type": "Microsoft.Insights/diagnosticSettings",
                    "existenceCondition": {
                        "allOf": [
                            {
                                "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
                                "equals": "true"
                            },
                            {
                                "field": "Microsoft.Insights/diagnosticSettings/metrics.enabled",
                                "equals": "true"
                            }
                        ]
                    }
                }
            }
        }
    }
}
```
Pode implementar este modelo através do [portal do Azure](#deploy-with-the-portal), do [PowerShell](#deploy-with-powershell) ou da [CLI do Azure](#deploy-with-azure-cli). Para obter a política incorporada, utilize o ID `7f89b1eb-583c-429a-8828-af049802c1d9`.

## <a name="parameters"></a>Parâmetros

Para transmitir o valor do parâmetro, utilize o seguinte formato:

```json
{"listOfResourceTypes":{"value":["Microsoft.Cache/Redis","Microsoft.Compute/virtualmachines"]}}
```

## <a name="deploy-with-the-portal"></a>Implementar com o portal

Ao atribuir uma política, selecione **Auditar definição de diagnóstico** das definições incorporadas disponíveis.

## <a name="deploy-with-powershell"></a>Implementar com o PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```powershell
$definition = Get-AzPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/7f89b1eb-583c-429a-8828-af049802c1d9

New-AzPolicyAssignment -name "Audit diagnostics" -PolicyDefinition $definition -PolicyParameter '{"listOfResourceTypes":{"value":["Microsoft.Cache/Redis","Microsoft.Compute/virtualmachines"]}}' -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>Limpar a implementação do PowerShell

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzPolicyAssignment -Name "Audit diagnostics" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Implementar com a CLI do Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```cli
az policy assignment create --scope <scope> --name "Audit diagnostics" --policy 7f89b1eb-583c-429a-8828-af049802c1d9 --params '{"listOfResourceTypes":{"value":["Microsoft.Cache/Redis","Microsoft.Compute/virtualmachines"]}}'
```

### <a name="clean-up-azure-cli-deployment"></a>Limpar a implementação da CLI do Azure

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```cli
az policy assignment delete --name "Audit diagnostics" --resource-group myResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

- Veja mais exemplos na página [Exemplos do Azure Policy](index.md)
