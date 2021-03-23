---
title: Definir atividade variável na fábrica de dados Azure
description: Saiba como utilizar a atividade de Variável Definida para definir o valor de uma variável existente definida num pipeline de Fábrica de Dados
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/07/2020
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.openlocfilehash: 113829dd35c14b5efae39c55a8085dcd2c1ecef4
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786163"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Definir atividade variável na fábrica de dados Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilize a atividade de Variável Definida para definir o valor de uma variável existente de tipo String, Bool ou Array definida num pipeline de Fábrica de Dados.

## <a name="type-properties"></a>Tipo de propriedades

Propriedade | Descrição | Necessário
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
