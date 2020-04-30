---
title: Alargar a app no tempo de execução - LUIS
description: ''
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: c0f9d71f5d89d73d9cdce2a2f646859d8eba3adc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81538580"
---
# <a name="extend-app-at-prediction-runtime"></a>Estender a app no tempo de execução da previsão

O esquema da aplicação (modelos e funcionalidades) é treinado e publicado para o ponto final de previsão. Este modelo publicado é utilizado no tempo de execução da previsão. Pode passar novas informações, juntamente com a expressão do utilizador, para o tempo de execução da previsão para aumentar a previsão.

Duas alterações de esquema de tempo de previsão incluem:
* [Entidades externas](#external-entities)
* [Listas dinâmicas](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>Entidades externas

Entidades externas dão à sua app LUIS a capacidade de identificar e rotular entidades durante o tempo de execução, que podem ser usadas como funcionalidades para entidades existentes. Isto permite-lhe utilizar os seus próprios extratores de entidades separadas e personalizados antes de enviar consultas para o seu ponto final de previsão. Como isto é feito no ponto final da previsão de consulta, não precisa de treinar e publicar o seu modelo.

A aplicação cliente está a fornecer o seu próprio extrator de entidade, gerindo a entidade que corresponde e determina a localização dentro da expressão daquela entidade correspondente e, em seguida, envia essa informação com o pedido.

As entidades externas são o mecanismo de extensão de qualquer tipo de entidade enquanto ainda são utilizados como sinais para outros modelos.

Isto é útil para uma entidade que tem dados disponíveis apenas no tempo de execução da previsão de consulta. Exemplos deste tipo de dados estão constantemente a mudar dados ou específicos por utilizador. Pode estender uma entidade de contacto LUIS com informações externas a partir da lista de contactos de um utilizador.

Entidades externas fazem parte da V3 autora da API. Saiba mais sobre [a migração](luis-migration-api-v3.md) para esta versão.

### <a name="entity-already-exists-in-app"></a>Entidade já existe na app

O valor `entityName` da entidade externa, aprovado no organismo post de pedido final, já deve existir na app treinada e publicada no momento em que o pedido é feito. O tipo de entidade não importa, todos os tipos são suportados.

### <a name="first-turn-in-conversation"></a>Primeira volta na conversa

Considere uma primeira expressão numa conversa de chat bot onde um utilizador introduz as seguintes informações incompletas:

`Send Hazem a new message`

O pedido do chat bot para o LUIS pode `Hazem` passar informações no corpo do POST sobre o que é diretamente igualado como um dos contactos do utilizador.

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

Nesta volta da conversa, a `him` expressão usa `Hazem`como referência a . O chat bot conversacional, no corpo `him` do POST, pode mapear o `Hazem`valor da entidade extraído da primeira expressão, .

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

### <a name="override-existing-model-predictions"></a>Anular as previsões de modelos existentes

A `preferExternalEntities` propriedade de opções especifica que se o utilizador enviar uma entidade externa que se sobrepõe a uma entidade prevista com o mesmo nome, a LUIS escolhe a entidade que passou ou a entidade existente no modelo.

Por exemplo, considere `today I'm free`a consulta . LUIS deteta `today` como dataV2 com a seguinte resposta:

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

Se `preferExternalEntities` o conjunto `false`for definido, o LUIS devolve uma resposta como se a entidade externa não tivesse sido enviada.

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

Se `preferExternalEntities` o conjunto `true`for definido, luis devolve uma resposta incluindo:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Resolução

A propriedade _opcional_ `resolution` retorna na resposta à previsão, permitindo-lhe passar nos metadados associados à entidade externa, recebendo-o de volta na resposta.

O principal objetivo é alargar as entidades pré-construídas, mas não se limita a esse tipo de entidade.

A `resolution` propriedade pode ser um número, uma corda, um objeto, ou uma matriz:

* "Dallas"
* {"text": "value"}
* 12345
* ["a", "b", "c"]

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>Listas dinâmicas

As listas dinâmicas permitem-lhe alargar uma entidade de listas já treinada e publicada, já na aplicação LUIS.

Utilize esta funcionalidade quando os valores da sua entidade da lista precisam de mudar periodicamente. Esta funcionalidade permite-lhe estender uma entidade de lista já treinada e publicada:

* No momento do pedido de previsão de consulta.
* Para um único pedido.

A entidade da lista pode estar vazia na app LUIS, mas tem de existir. A entidade da lista na app LUIS não é alterada, mas a capacidade de previsão no ponto final é alargada para incluir até 2 listas com cerca de 1.000 itens.

### <a name="dynamic-list-json-request-body"></a>Lista dinâmica JSON request body

Envie o seguinte organismo JSON para adicionar uma nova sublista com sinónimos à lista, e `LUIS`prever `POST` a entidade da lista para o texto, com o pedido de previsão de consulta:

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

A resposta de previsão inclui aquela entidade da lista, com todas as outras entidades previstas, porque está definida no pedido.

## <a name="next-steps"></a>Passos seguintes

* [Classificação da predição](luis-concept-prediction-score.md)
* [Autor de alterações da API V3](luis-migration-api-v3.md)
