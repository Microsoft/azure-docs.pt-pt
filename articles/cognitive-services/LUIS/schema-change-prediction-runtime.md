---
title: Estender app no tempo de execução - LUIS
description: Saiba como estender um ponto final de previsão já publicado para passar novas informações.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 1d94e9f59062e4d730b8f3b71022442e81e6eeda
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98953026"
---
# <a name="extend-app-at-prediction-runtime"></a>Estender app no tempo de execução da previsão

O esquema da aplicação (modelos e funcionalidades) é treinado e publicado no ponto final de previsão. Este modelo publicado é utilizado no tempo de execução da previsão. Pode passar novas informações, juntamente com a expressão do utilizador, para o tempo de previsão para aumentar a previsão.

Duas alterações de esquema de tempo de previsão incluem:
* [Entidades externas](#external-entities)
* [Listas dinâmicas](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>Entidades externas

Entidades externas dão à sua app LUIS a capacidade de identificar e rotular entidades durante o tempo de execução, que podem ser usadas como funcionalidades para as entidades existentes. Isto permite-lhe utilizar os seus próprios extratores de entidades separadas e personalizadas antes de enviar consultas para o seu ponto final de previsão. Como isto é feito no ponto final da previsão de consulta, não é preciso voltar a treinar e publicar o seu modelo.

A aplicação do cliente está a fornecer o seu próprio extrator de entidade, gerindo a entidade que combina e determina a localização dentro da expressão dessa entidade correspondente e, em seguida, envia essa informação com o pedido.

As entidades externas são o mecanismo para alargar qualquer tipo de entidade enquanto ainda são utilizadas como sinais para outros modelos.

Isto é útil para uma entidade que tem dados disponíveis apenas no tempo de execução da previsão de consulta. Exemplos deste tipo de dados estão constantemente a mudar dados ou específicos por utilizador. Pode estender uma entidade de contacto LUIS com informações externas a partir da lista de contactos de um utilizador.

Entidades externas fazem parte da V3 autoria da API. Saiba mais sobre [migração](luis-migration-api-v3.md) para esta versão.

### <a name="entity-already-exists-in-app"></a>Entidade já existe na app

O valor da `entityName` entidade externa, passado no pedido de acesso post organismo, já deve existir na aplicação treinada e publicada no momento em que o pedido é feito. O tipo de entidade não importa, todos os tipos são suportados.

### <a name="first-turn-in-conversation"></a>Primeira volta na conversa

Considere uma primeira expressão numa conversa de chat bot onde um utilizador introduz as seguintes informações incompletas:

`Send Hazem a new message`

O pedido do chat bot para o LUIS pode passar informações no corpo do POST sobre `Hazem` o qual é diretamente correspondido como um dos contactos do utilizador.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

A resposta de previsão inclui aquela entidade externa, com todas as outras entidades previstas, porque está definida no pedido.

### <a name="second-turn-in-conversation"></a>Segunda volta na conversa

A próxima expressão do utilizador no chat bot usa um termo mais vago:

`Send him a calendar reminder for the party.`

Nesta virada da conversa, a expressão usa `him` como referência a `Hazem` . O bot de conversação, no corpo do POST, pode `him` mapear o valor da entidade extraído da primeira expressão, `Hazem` .

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

A resposta de previsão inclui aquela entidade externa, com todas as outras entidades previstas, porque está definida no pedido.

### <a name="override-existing-model-predictions"></a>Anular as previsões dos modelos existentes

A `preferExternalEntities` propriedade de opções especifica que se o utilizador enviar uma entidade externa que se sobrepõe a uma entidade prevista com o mesmo nome, a LUIS escolhe a entidade aprovada ou a entidade existente no modelo.

Por exemplo, considere a `today I'm free` consulta. LUIS deteta `today` como dataV2 com a seguinte resposta:

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Se o utilizador enviar a entidade externa:

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

Se o `preferExternalEntities` conjunto for definido para , LUIS devolve uma resposta como se a entidade externa não fosse `false` enviada.

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Se o `preferExternalEntities` conjunto for definido para , LUIS devolve uma resposta `true` incluindo:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Resolução

A propriedade _opcional_ `resolution` retorna na resposta de previsão, permitindo-lhe passar nos metadados associados à entidade externa, recebendo-o de volta na resposta.

O objetivo principal é alargar as entidades pré-construídas, mas não se limita a esse tipo de entidade.

A `resolution` propriedade pode ser um número, uma corda, um objeto, ou uma matriz:

* "Dallas"
* {"texto": "valor"}
* 12345
* ["a", "b", "c"]

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>Listas dinâmicas

As listas dinâmicas permitem-lhe alargar uma entidade de listas treinada e publicada existente, já na app LUIS.

Utilize esta função quando os valores da sua entidade lista precisam de ser alterados periodicamente. Esta funcionalidade permite-lhe alargar uma entidade de lista já treinada e publicada:

* No momento do pedido de previsão de consulta.
* Para um único pedido.

A entidade da lista pode estar vazia na app LUIS, mas tem de existir. A entidade de lista na app LUIS não é alterada, mas a capacidade de previsão no ponto final é alargada para incluir até 2 listas com cerca de 1.000 itens.

### <a name="dynamic-list-json-request-body"></a>Corpo de pedido JSON de lista dinâmica

Envie o seguinte organismo JSON para adicionar uma nova sublistação com sinónimos à lista, e prever a entidade de lista para o texto, `LUIS` com o pedido de previsão de `POST` consulta:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntity*":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

A resposta de previsão inclui aquela entidade de lista, com todas as outras entidades previstas, porque está definida no pedido.

## <a name="next-steps"></a>Passos seguintes

* [Classificação da predição](luis-concept-prediction-score.md)
* [Autoria de alterações da API V3](luis-migration-api-v3.md)
