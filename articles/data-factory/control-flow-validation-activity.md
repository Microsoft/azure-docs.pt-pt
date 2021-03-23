---
title: Atividade de validação na Azure Data Factory
description: A atividade de Validação não continua a executar o gasoduto até validar o conjunto de dados anexado com determinados critérios que o utilizador especifica.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 2c5208f754e66f92cf5019fdad3026decac88284
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785959"
---
# <a name="validation-activity-in-azure-data-factory"></a>Atividade de validação na Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pode utilizar uma Validação num oleoduto para garantir que o gasoduto só continua a execução uma vez que tenha validado a referência de conjunto de dados anexado, que satisfaça os critérios especificados, ou que o tempo limite tenha sido atingido.


## <a name="syntax"></a>Syntax

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


## <a name="type-properties"></a>Tipo de propriedades

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
name | Nome da atividade de 'Validação' | String | Sim |
tipo | Deve ser definido para **validação.** | String | Sim |
conjunto de dados | A atividade bloqueará a execução até que tenha validado esta referência de conjunto de dados e que satisfaça os critérios especificados, ou o tempo limite tenha sido atingido. O conjunto de dados fornecido deve suportar a propriedade "MinimumSize" ou "ChildItems". | Referência do conjunto de dados | Sim |
tempo limite | Especifica o tempo limite para a execução da atividade. Se não for especificado qualquer valor, o valor predefinido é de 7 dias ("7.00:00:00"). O formato é d.hh:mm:ss | String | No |
sono | Um atraso em segundos entre tentativas de validação. Se não for especificado qualquer valor, o valor predefinido é de 10 segundos. | Número inteiro | No |
childItems | Verifique se a pasta tem objetos de criança. Pode ser definido como verdadeiro : Valide que a pasta existe e que tem itens. Bloqueia até que pelo menos um item esteja presente na pasta ou o valor do tempo limite seja atingido.-falso: Valide que a pasta existe e que está vazia. Bloqueia até que a pasta esteja vazia ou até que o valor do tempo limite seja atingido. Se não for especificado qualquer valor, a atividade bloqueará até que a pasta exista ou até que o tempo limite seja atingido. | Booleano | No |
tamanho mínimo | Tamanho mínimo de um ficheiro em bytes. Se nenhum valor for especificado, o valor predefinido é 0 bytes | Número inteiro | Não |


## <a name="next-steps"></a>Próximos passos
Consulte outras atividades de fluxo de controlo suportadas pela Data Factory:

- [Atividade Se Condição](control-flow-if-condition-activity.md)
- [Executar atividade de gasoduto](control-flow-execute-pipeline-activity.md)
- [Para Cada Atividade](control-flow-for-each-activity.md)
- [Obter Atividade de Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade Web](control-flow-web-activity.md)
- [Até a Atividade](control-flow-until-activity.md)
