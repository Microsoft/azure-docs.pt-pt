---
title: Definir atividade variável na fábrica de dados azure
description: Saiba como utilizar a atividade variável definida para definir o valor de uma variável existente definida num pipeline data Factory
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
ms.openlocfilehash: e736cc95628bd0e15bdb7ffd425608278788c353
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879275"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Definir atividade variável na Fábrica de Dados Azure

Utilize a atividade Variável definida para definir o valor de uma variável existente de cadeia de tipo, bool ou Matriz definida num pipeline data Factory.

## <a name="type-properties"></a>Propriedades de tipo

Propriedade | Descrição | Necessário
-------- | ----------- | --------
nome | Nome da atividade em pipeline | sim
descrição | Texto descrevendo o que a atividade faz | não
tipo | Deve ser definido para **Definir Variável** | sim
valor | Valor de objeto literal ou de expressão de cadeia a que a variável será atribuída | sim
nome variável | Nome da variável que será definida por esta atividade | sim

## <a name="incrementing-a-variable"></a>Incrementando uma variável

Um cenário comum que envolve variáveis na Azure Data Factory está a usar uma variável como iterador dentro de uma atividade de até ou foreach. Numa atividade variável definida não se pode `value` referenciar a variável que está a ser definida no campo. Para contornar esta limitação, delineie uma variável temporária e, em seguida, crie uma segunda atividade variável. A segunda atividade variável define o valor do iterante para a variável temporária. 

Abaixo está um exemplo deste padrão:

![Variável de incremento](media/control-flow-set-variable-activity/increment-variable.png "Variável de incremento")

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
Conheça uma atividade de fluxo de controlo relacionada suportada pela Data Factory: 

- [Acrescentar a Atividade Variável](control-flow-append-variable-activity.md)
