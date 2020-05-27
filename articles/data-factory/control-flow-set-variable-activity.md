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
ms.openlocfilehash: a0b5fa16658d3e354bcb4f90ad998997fc844a84
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832797"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Definir atividade variável na fábrica de dados azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilize a atividade Variável definida para definir o valor de uma variável existente de cadeia de tipo, bool ou Matriz definida num pipeline data Factory.

## <a name="type-properties"></a>Propriedades de tipo

Propriedade | Descrição | Necessário
-------- | ----------- | --------
name | Nome da atividade em pipeline | sim
descrição | Texto descrevendo o que a atividade faz | não
tipo | Deve ser definido para **Definir Variável** | sim
valor | Valor de objeto literal ou de expressão de corda a que a variável é atribuída | sim
nome variável | Nome da variável que é definida por esta atividade | sim

## <a name="incrementing-a-variable"></a>Incrementando uma variável

Um cenário comum que envolve variáveis na Azure Data Factory está a usar uma variável como iterador dentro de uma atividade de até ou foreach. Numa atividade variável definida não se pode referenciar a variável que está a ser definida no `value` campo. Para contornar esta limitação, delineie uma variável temporária e, em seguida, crie uma segunda atividade variável. A segunda atividade variável define o valor do iterante para a variável temporária. 

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
