---
title: A bordo de uma máquina para Azure Automanage com um modelo ARM
description: Aprenda a embarcar numa máquina para Azure Automanage com um modelo de Gestor de Recursos Azure.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 04/09/2021
ms.author: alsin
ms.openlocfilehash: 78cf28903311c542a83c9ace4f794e1cdda9a61c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368642"
---
# <a name="onboard-a-machine-to-automanage-with-an-azure-resource-manager-arm-template"></a>A bordo de uma máquina para automanage com um modelo de Gestor de Recursos Azure (ARM)


## <a name="overview"></a>Descrição Geral
Siga os passos abaixo para embarcar uma máquina para auto-corridas de boas práticas. O modelo ARM abaixo criará um `configurationProfileAssignment` objeto, que é o recurso Azure que representa uma máquina que foi a bordo da Automanage.

## <a name="prerequisites"></a>Pré-requisitos
* Deve ter criado uma Conta auto-piloto existente. Consulte [este documento](./automanage-account.md) para obter mais informações sobre a Conta De Gestão Automática e como criar uma.
* Tem de ter a função **de Contribuinte** no grupo de recursos que contém as máquinas que pretende embarcar para a Automanage

## <a name="arm-template-overview"></a>Visão geral do modelo de ARM
O seguinte modelo ARM irá embarcar a sua máquina especificada para as melhores práticas de auto-manização Azure. Os detalhes sobre o modelo ARM e os passos sobre a forma de implantação estão localizados na secção de implantação do modelo ARM [abaixo](#arm-template-deployment).
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "machineName": {
            "type": "String"
        },
        "automanageAccountName": {
            "type": "String"
        },
        "configurationProfileAssignment": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/configurationProfileAssignments",
            "apiVersion": "2020-06-30-preview",
            "name": "[concat(parameters('machineName'), '/Microsoft.Automanage/', 'default')]",
            "properties": {
                "configurationProfile": "[parameters('configurationProfileAssignment')]",
                "accountId": "[concat(resourceGroup().id, '/providers/Microsoft.Automanage/accounts/', parameters('automanageAccountName'))]"
            }
        }
    ]
}
```

## <a name="arm-template-deployment"></a>Implementação do modelo ARM
O modelo ARM acima criará uma atribuição de perfil de configuração para a sua máquina especificada, utilizando uma Conta de Automanagem especificada. Se não criou uma Conta de Auto-Produção, saiba mais [neste doc](./automanage-account.md).

O `configurationProfileAssignment` valor pode ser um dos seguintes valores:
* "Produção"
* "DevTest"

Siga estes passos para implementar o modelo ARM:
1. Guarde o modelo de ARM abaixo como `azuredeploy.json`
1. Executar a implementação do modelo ARM com `az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json`
1. Fornecer os valores para o nome de máquinasName, auto-gestageAccountName e configuraçãoProfilesignment quando solicitado
1. Acabou-se!

Como em qualquer modelo ARM, é possível calcular os parâmetros num ficheiro separado `azuredeploy.parameters.json` e usá-lo como argumento ao ser implementado.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a auto-produção para [Linux](./automanage-linux.md) e [Windows](./automanage-windows-server.md)