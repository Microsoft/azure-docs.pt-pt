---
title: Actividade de validação no Azure Data Factory | Documentos da Microsoft
description: A actividade de validação não continue a execução do pipeline até que ele valida o conjunto de dados anexado com determinados critérios que especifica o utilizador.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 46447bdbea93d1f99c5682cf878c2035e6f49b78
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58523030"
---
# <a name="validation-activity-in-azure-data-factory"></a>Actividade de validação no Azure Data Factory
Pode usar uma validação num pipeline para garantir que o pipeline apenas continua a execução assim que validou a anexado existe de referência de conjunto de dados, que ele cumpre os critérios especificados ou tempo limite foi atingido.


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
nome | Nome da atividade 'Validação' | String | Sim |
tipo | Tem de ser definido como **validação**. | String | Sim |
Conjunto de dados | Atividade irá bloquear a execução até que ele foi validado essa referência de conjunto de dados existe e que cumpre os critérios especificados, ou foi atingido o tempo limite. Conjunto de dados fornecido deve dar suporte a propriedade "MinimumSize" ou "ChildItems". | Referência de conjunto de dados | Sim |
tempo limite | Especifica o tempo limite para a execução da atividade. Se for especificado nenhum valor, o valor padrão é sete dias ("7.00:00:00"). O formato é d.hh:mm:ss | String | Não |
Modo de suspensão | Um atraso em segundos, entre tentativas de validação. Se for especificado nenhum valor, o valor predefinido é 10 segundos. | Número inteiro | Não |
childItems | Verifica se a pasta tem itens subordinados. Pode ser definido para verdadeiro: Valide que a pasta existe e que tem itens. Bloqueia até que pelo menos um item está presente na pasta ou valor de tempo limite for atingido.-false: Confirme que a pasta existe e que está vazia. Permanece bloqueado até a pasta está vazia ou até que o tempo limite é atingido o valor. Se for especificado nenhum valor, atividade será bloqueado até que a pasta existe ou até que o tempo limite for atingido. | Booleano | Não |
minimumSize | Tamanho mínimo de um ficheiro em bytes. Se for especificado nenhum valor, o valor predefinido é 0 bytes | Número inteiro | Não |


## <a name="next-steps"></a>Passos Seguintes
Consulte outras atividades de fluxo de controle suportadas pelo Data Factory:

- [Atividade Se Condição](control-flow-if-condition-activity.md)
- [Atividade Executar Pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade Obter Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade Web](control-flow-web-activity.md)
- [Atividade Until](control-flow-until-activity.md)
