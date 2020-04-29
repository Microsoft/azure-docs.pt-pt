---
title: Atividade de validação na Fábrica de Dados Azure
description: A atividade de validação não prossegue a execução do gasoduto até validar o conjunto de dados anexo com determinados critérios que o utilizador especifica.
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
ms.openlocfilehash: 764b41d1823e8edce134c5099e066486f4f08acc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417934"
---
# <a name="validation-activity-in-azure-data-factory"></a>Atividade de validação na Fábrica de Dados Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pode utilizar uma Validação num oleoduto para garantir que o gasoduto só continua a ser executado depois de ter validado a referência do conjunto de dados anexado, que satisfaz os critérios especificados, ou que o prazo foi atingido.


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


## <a name="type-properties"></a>Propriedades de tipo

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
nome | Nome da atividade de 'Validação' | String | Sim |
tipo | Deve ser definido para **validação**. | String | Sim |
conjunto de dados | A atividade bloqueará a execução até que tenha validado esta referência do conjunto de dados e que satisfaça os critérios especificados, ou tenha sido atingido o prazo. O conjunto de dados fornecido deve apoiar a propriedade "MinimumSize" ou "ChildItems". | Referência de conjunto de dados | Sim |
tempo limite | Especifica o tempo limite para a execução da atividade. Se não for especificado qualquer valor, o valor por defeito é de 7 dias ("7.00:00:00"). O formato é d.hh:mm:ss | String | Não |
sono | Um atraso em segundos entre tentativas de validação. Se não for especificado qualquer valor, o valor por defeito é de 10 segundos. | Número inteiro | Não |
criançaItems | Verifique se a pasta tem itens infantis. Pode ser definido como verdadeiro : Valide que a pasta existe e que tem itens. Bloqueia até que pelo menos um item esteja presente na pasta ou o valor do tempo for atingido.-falso: Valide que a pasta existe e que está vazia. Bloqueia até que a pasta esteja vazia ou até que o valor do tempo for atingido. Se não for especificado qualquer valor, a atividade bloqueará até que a pasta exista ou até que o prazo seja atingido. | Booleano | Não |
mínimoTamanho | Tamanho mínimo de um ficheiro em bytes. Se não for especificado qualquer valor, o valor por defeito é de 0 bytes | Número inteiro | Não |


## <a name="next-steps"></a>Passos seguintes
Consulte outras atividades de fluxo de controlo suportadas pela Data Factory:

- [Atividade Se Condição](control-flow-if-condition-activity.md)
- [Executar a Atividade do Pipeline](control-flow-execute-pipeline-activity.md)
- [Para Cada Atividade](control-flow-for-each-activity.md)
- [Obter Atividade de Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade Web](control-flow-web-activity.md)
- [Até a Atividade](control-flow-until-activity.md)
