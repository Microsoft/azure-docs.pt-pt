---
title: Atividade de validação no Azure Data Factory
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
ms.openlocfilehash: f63c78c59d7d6be3c66ea0785389eff73e3bff60
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678372"
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

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
nome | Nome da atividade de ' validação ' | String | Sim |
tipo | Deve ser definido como **validação**. | String | Sim |
DataSet | A atividade bloqueará a execução até que tenha validado essa referência de conjunto de e que ele atenda aos critérios especificados ou que o tempo limite tenha sido atingido. O conjunto de conjuntos fornecido deve dar suporte à propriedade "MinimumSize" ou "ChildItems". | Referência de DataSet | Sim |
tempo limite | Especifica o tempo limite para a execução da atividade. Se nenhum valor for especificado, o valor padrão será 7 dias ("7.00:00:00"). O formato é d. hh: mm: SS | String | Não |
num | Um atraso em segundos entre as tentativas de validação. Se nenhum valor for especificado, o valor padrão será 10 segundos. | Número inteiro | Não |
childItems | Verifica se a pasta tem itens filhos. Pode ser definido como-true: valide se a pasta existe e se ela tem itens. Bloqueia até que pelo menos um item esteja presente na pasta ou o valor de tempo limite seja atingido.-false: valide se a pasta existe e se está vazia. Bloqueia até a pasta estar vazia ou até que o valor de tempo limite seja atingido. Se nenhum valor for especificado, a atividade será bloqueada até que a pasta exista ou até que o tempo limite seja atingido. | Booleano | Não |
minimumSize | Tamanho mínimo de um arquivo em bytes. Se nenhum valor for especificado, o valor padrão será 0 bytes | Número inteiro | Não |


## <a name="next-steps"></a>Passos seguintes
Consulte outras atividades de fluxo de controle com suporte pelo Data Factory:

- [Atividade Se Condição](control-flow-if-condition-activity.md)
- [Atividade Executar Pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade Obter Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
- [Atividade Until](control-flow-until-activity.md)
