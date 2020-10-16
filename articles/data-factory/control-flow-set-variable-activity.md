---
title: Definir atividade variável na fábrica de dados Azure
description: Saiba como utilizar a atividade de Variável Definida para definir o valor de uma variável existente definida num pipeline de Fábrica de Dados
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/07/2020
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: a0b5fa16658d3e354bcb4f90ad998997fc844a84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83832797"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Definir atividade variável na fábrica de dados Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilize a atividade de Variável Definida para definir o valor de uma variável existente de tipo String, Bool ou Array definida num pipeline de Fábrica de Dados.

## <a name="type-properties"></a>Tipo de propriedades

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
name | Nome da atividade em pipeline | sim
descrição | Texto descrevendo o que a atividade faz | não
tipo | Deve ser definido para **SetVariable** | sim
valor | Valor de objeto literal ou expressão de corda a que a variável é atribuída | sim
nome variável | Nome da variável que é definida por esta atividade | sim

## <a name="incrementing-a-variable"></a>Incrementando uma variável

Um cenário comum envolvendo variáveis na Azure Data Factory está usando uma variável como iterador dentro de uma atividade até ou forecach. Numa atividade variável definida não é possível fazer referência à variável definida no `value` campo. Para contornar esta limitação, desabine uma variável temporária e, em seguida, crie uma segunda atividade variável definida. A segunda atividade variável definida define o valor do iterador para a variável temporária. 

Abaixo está um exemplo deste padrão:

![Variável incremento](media/control-flow-set-variable-activity/increment-variable.png "Variável incremento")

``` json
{
    "name": "pipeline3",
    "properties": {
        "activities": [
            {
                "name": "Set I",
                "type": "SetVariable",
                "dependsOn": [
                    {
                        "activity": "Increment J",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "i",
                    "value": {
                        "value": "@variables('j')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "Increment J",
                "type": "SetVariable",
                "dependsOn": [],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "j",
                    "value": {
                        "value": "@string(add(int(variables('i')), 1))",
                        "type": "Expression"
                    }
                }
            }
        ],
        "variables": {
            "i": {
                "type": "String",
                "defaultValue": "0"
            },
            "j": {
                "type": "String",
                "defaultValue": "0"
            }
        },
        "annotations": []
    }
}
```


## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre uma atividade de fluxo de controlo relacionada suportada pela Data Factory: 

- [Acrescentar a Atividade Variável](control-flow-append-variable-activity.md)
