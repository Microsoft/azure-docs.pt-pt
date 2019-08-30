---
title: Atividade de validação no Azure Data Factory | Microsoft Docs
description: A atividade de validação não continua a execução do pipeline até que ele valide o conjunto de dado anexado com determinados critérios que o usuário especifica.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 77fdab04e03429d135875cb2ef223e8c23d312a2
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141612"
---
# <a name="validation-activity-in-azure-data-factory"></a>Atividade de validação no Azure Data Factory
Você pode usar uma validação em um pipeline para garantir que o pipeline continue a execução apenas depois de ter validado a referência do conjunto de entrada anexado, que ele atende aos critérios especificados ou que o tempo limite tenha sido atingido.


## <a name="syntax"></a>Sintaxe

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>Propriedades do tipo

Propriedade | Descrição | Valores permitidos | Requerido
-------- | ----------- | -------------- | --------
name | Nome da atividade de ' validação ' | Cadeia | Sim |
type | Deve ser definido como **validação**. | Cadeia | Sim |
DataSet | A atividade bloqueará a execução até que tenha validado essa referência de conjunto de e que ele atenda aos critérios especificados ou que o tempo limite tenha sido atingido. O conjunto de conjuntos fornecido deve dar suporte à propriedade "MinimumSize" ou "ChildItems". | Referência de DataSet | Sim |
timeout | Especifica o tempo limite para a execução da atividade. Se nenhum valor for especificado, o valor padrão será 7 dias ("7.00:00:00"). O formato é d. hh: mm: SS | Cadeia | Não |
num | Um atraso em segundos entre as tentativas de validação. Se nenhum valor for especificado, o valor padrão será 10 segundos. | Integer | Não |
childItems | Verifica se a pasta tem itens filhos. Pode ser definido como-true: Valide se a pasta existe e se ela tem itens. Bloqueia até que pelo menos um item esteja presente na pasta ou o valor de tempo limite seja atingido.-falso: Valide se a pasta existe e se está vazia. Bloqueia até a pasta estar vazia ou até que o valor de tempo limite seja atingido. Se nenhum valor for especificado, a atividade será bloqueada até que a pasta exista ou até que o tempo limite seja atingido. | Booleano | Não |
minimumSize | Tamanho mínimo de um arquivo em bytes. Se nenhum valor for especificado, o valor padrão será 0 bytes | Integer | Não |


## <a name="next-steps"></a>Passos Seguintes
Consulte outras atividades de fluxo de controle com suporte pelo Data Factory:

- [Atividade Se Condição](control-flow-if-condition-activity.md)
- [Atividade Executar Pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade Obter Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
- [Atividade Until](control-flow-until-activity.md)
