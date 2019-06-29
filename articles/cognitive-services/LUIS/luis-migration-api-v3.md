---
title: V2 para migração de APIS V3
titleSuffix: Azure Cognitive Services
description: O ponto de extremidade de versão 3 APIs foram alterados. Utilize este guia para compreender como migrar para o ponto final de versão 3 APIs.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: 4c08c95a05d4f22e2338a7264409aec0f64a4755
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442524"
---
# <a name="preview-migrate-to-api-version-3x-for-luis-apps"></a>Pré-visualização: Migrar para a versão de API 3.x para aplicações de LUIS

O ponto final de predição de consulta APIs foram alterados. Utilize este guia para compreender como migrar para o ponto final de versão 3 APIs. 

Esta API V3 fornece as seguintes funcionalidades novas, que incluem alterações significativas de pedido de e/ou a resposta JSON: 

* [Entidades externas](#external-entities-passed-in-at-prediction-time)
* [Listas de dinâmicas](#dynamic-lists-passed-in-at-prediction-time)
* [Alterações JSON de entidade predefinidos](#prebuilt-entities-with-new-json)

<!--
* [Multi-intent detection of utterance](#detect-multiple-intents-within-single-utterance)
-->

O ponto final de predição de consulta [solicitação](#request-changes) e [resposta](#response-changes) tem alterações significativas para suportar os novos recursos listados acima, incluindo o seguinte:

* [Alterações de objetos de resposta](#top-level-json-changes)
* [Referências de nome de função de entidade em vez do nome da entidade](#entity-role-name-instead-of-entity-name)
* [Propriedades para marcar entidades em expressões com](#marking-placement-of-entities-in-utterances)

São as seguintes funcionalidades de LUIS **nepodporuje** na V3 API:

* V7 de verificação de ortografia do Bing

[Documentação de referência](https://aka.ms/luis-api-v3) está disponível para V3.

## <a name="endpoint-url-changes-by-slot-name"></a>Alterações de URL de ponto final por nome da ranhura

O formato da chamada de ponto de extremidade HTTP V3 foi alterado.

|MÉTODO|do IdP|
|--|--|
|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict?query=<b>{QUERY}</b>|
|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict|
|||

## <a name="endpoint-url-changes-by-version-id"></a>Alterações de URL de ponto final por ID de versão

Se quiser consultar por versão, tem primeiro de [publicar através da API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) com o `"directVersionPublish":true`. Consulte o ponto final a referenciar o ID de versão em vez do nome de ranhura.


|MÉTODO|do IdP|
|--|--|
|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict?query=<b>{QUERY}</b>|
|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict|
|||

## <a name="prebuilt-entities-with-new-json"></a>Entidades previamente concebidas com novo JSON

As alterações de objeto de resposta de V3 incluem [entidades pré-concebidas](luis-reference-prebuilt-entities.md). 

## <a name="request-changes"></a>Solicitar alterações 

### <a name="query-string-parameters"></a>Parâmetros de cadeia de caracteres de consulta

A API de V3 tem parâmetros de cadeia de caracteres de consulta diferentes.

|Nome do parâmetro|Tipo|Version|Predefinição|Objetivo|
|--|--|--|--|--|
|`log`|boolean|V2 E V3|false|Consulta de Store no ficheiro de registo.| 
|`query`|string|Apenas v3|Sem predefinição - é necessária na solicitação GET|**No V2**, está a expressão a ser prevista o `q` parâmetro. <br><br>**Na V3**, a funcionalidade é passada o `query` parâmetro.|
|`show-all-intents`|boolean|Apenas v3|false|Devolver todos os objetivos com a pontuação correspondente no **prediction.intents** objeto. Objetivos são retornados como objetos num elemento principal `intents` objeto. Isso permite o acesso programático sem a necessidade de localizar a intenção numa matriz: `prediction.intents.give`. No V2, estes foram devolvidos numa matriz. |
|`verbose`|boolean|V2 E V3|false|**No V2**, quando definido como true, previstas todas as intenções foram devolvidos. Se precisar de prevista todas as intenções, utilize o parâmetro V3 de `show-all-intents`.<br><br>**Na V3**, este parâmetro só fornece entidade dos detalhes de metadados de predição de entidade.  |



<!--
|`multiple-segments`|boolean|V3 only|Break utterance into segments and predict each segment for intents and entities.|
-->


### <a name="the-query-prediction-json-body-for-the-post-request"></a>A predição de consulta JSON corpo para a `POST` pedido

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "overridePredictions": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Propriedade|Tipo|Version|Predefinição|Objetivo|
|--|--|--|--|--|
|`dynamicLists`|array|Apenas v3|Não é necessário.|[Listas dinâmicas](#dynamic-lists-passed-in-at-prediction-time) permitem-lhe expandir uma entidade de lista treinado e publicada existente, já na aplicação do LUIS.|
|`externalEntities`|array|Apenas v3|Não é necessário.|[Entidades externas](#external-entities-passed-in-at-prediction-time) dê à sua aplicação de LUIS a capacidade de identificar e etiquetar entidades durante o tempo de execução, o que pode ser usado como recursos para entidades existentes. |
|`options.datetimeReference`|string|Apenas v3|Sem predefinição|Utilizado para determinar [datetimeV2 deslocamento](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity).|
|`options.overridePredictions`|boolean|Apenas v3|false|Especifica se do usuário [entidade externa (com o mesmo nome da entidade existente)](#override-existing-model-predictions) é utilizado ou a entidade existente no modelo é utilizada para predição. |
|`query`|string|Apenas v3|Necessário.|**No V2**, está a expressão a ser prevista o `q` parâmetro. <br><br>**Na V3**, a funcionalidade é passada o `query` parâmetro.|



## <a name="response-changes"></a>Alterações de resposta

Resposta da consulta JSON alterado para permitir maior acesso programático aos dados utilizados com mais frequência. 

### <a name="top-level-json-changes"></a>Alterações JSON de nível superior

As principais propriedades JSON para o V2 são, quando `verbose` está definido como true, o que devolve todos os objetivos e as suas pontuações o `intents` propriedade:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

As principais propriedades JSON para V3 são:

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "normalizedQuery": "this is your utterance you want predicted - after normalization",
        "topIntent": "intent-name-1",
        "intents": {}, 
        "entities":{}
    }
}
```

O `intents` objeto é uma lista não ordenada. Não pressuponha que o primeiro subordinado no `intents` corresponde ao `topIntent`. Em alternativa, utilize o `topIntent` valor para localizar a classificação:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Permitir que as alterações de esquema JSON de resposta para:

* Limpar a distinção entre a expressão original, `query`e retornada a predição, `prediction`.
* Acesso mais fácil programático a dados previstos. Em vez de enumerar através de uma matriz no V2, pode aceder a valores por **com o nome** para intenções e entidades. Para funções de entidade prevista, o nome da função é devolvido uma vez que é exclusivo em todo o aplicativo.
* Tipos de dados, se for determinado, são respeitados. Numéricos já não são retornados como cadeias de caracteres.
* Distinção entre a primeira informações de predição de prioridade e metadados adicionais, é devolvido no `$instance` objeto. 

### <a name="access-instance-for-entity-metadata"></a>Acesso `$instance` para metadados de entidade

Se precisar de metadados de entidade, a cadeia de consulta tem de utilizar o `verbose=true` sinalizador e a resposta contém os metadados no `$instance` objeto. Exemplos são mostrados das respostas JSON nas seções a seguir.

### <a name="each-predicted-entity-is-represented-as-an-array"></a>Cada entidade prevista é representada como uma matriz

O `prediction.entities.<entity-name>` objeto contém uma matriz, uma vez que cada entidade pode ser prevista mais de uma vez em que a expressão. 

### <a name="list-entity-prediction-changes"></a>Alterações de predição de entidade de lista

O JSON para uma predição de entidade de lista foi alterado para ser uma matriz de matrizes:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Cada matriz interior corresponde ao texto entre a expressão. O objeto interior é uma matriz, porque o mesmo texto pode aparecer mais do que um sublista de uma entidade de lista. 

Quando o mapeamento entre o `entities` objeto para o `$instance` de objeto, a ordem dos objetos é preservada para as previsões de entidade de lista.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

### <a name="entity-role-name-instead-of-entity-name"></a>Nome da função de entidade em vez do nome da entidade 

No V2, a `entities` matriz devolvida todas as entidades previstas com o nome da entidade a ser o identificador exclusivo. V3, se a entidade utiliza funções e a predição para uma função de entidade, o identificador principal é o nome da função. Isso é possível porque os nomes de funções de entidade tem de ser exclusivos em todo o aplicativo, incluindo outros nomes de modelo (entidade intencional,).

No exemplo seguinte: considere uma expressão que inclui o texto, `Yellow Bird Lane`. Este texto é previsto como um personalizado `Location` função da entidade de `Destination`.

|Texto de expressão|Nome da entidade|Nome da função|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

No V2, a entidade é identificada pela _nome da entidade_ com a função como uma propriedade do objeto:

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

Na V3, a entidade é referenciada pela _função de entidade_, se a predição é para a função:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

Na V3, com o mesmo resultado a `verbose` sinalizador para devolver metadados de entidade:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

## <a name="external-entities-passed-in-at-prediction-time"></a>Entidades externas, transmitidas em tempo de predição

Entidades externas dê à sua aplicação do LUIS a capacidade de identificar e etiquetar entidades durante o tempo de execução, o que pode ser usado como recursos para entidades existentes. Isto permite-lhe utilizar o seu próprio extratores de entidade separada e personalizadas antes de enviar consultas para o ponto final de predição. Porque isso é feito no ponto de final de predição de consulta, não precisa de voltar a preparar e publicar o seu modelo.

A aplicação de cliente está a fornecer seu próprio extrator de entidades através da gestão de correspondência de entidade e determinar a localização dentro da expressão dessa entidade correspondente e, em seguida, enviar essas informações com o pedido. 

Entidades externas são o mecanismo para estender qualquer tipo de entidade ao mesmo tempo que continua a ser utilizado como sinais a outros modelos, como funções, compostos e outras pessoas.

Isto é útil para uma entidade que tem dados disponíveis apenas no tempo de execução de predição de consulta. Exemplos deste tipo de dados são constantes, específicas por utilizador ou de dados. Pode expandir uma entidade de contacto do LUIS com informações externas da lista de contactos de um utilizador. 

### <a name="entity-already-exists-in-app"></a>Entidade já existe na aplicação

O valor de `entityName` para a entidade externa, transmitida no corpo do POST, de pedido de ponto final tem de existir na aplicação com formação e publicada no momento a solicitação é feita. Não importa o tipo de entidade, todos os tipos são suportados.

### <a name="first-turn-in-conversation"></a>Primeiro, na conversação

Considere uma primeira expressão numa conversa de bot de bate-papo em que um usuário insere as seguintes informações incompletas:

`Send Hazem a new message`

O pedido a partir do bot de bate-papo para LUIS pode transmitir informações no corpo da POSTAGEM sobre `Hazem` para que ele é correspondido diretamente como um dos contactos do utilizador.

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

A resposta de previsão inclui essa entidade externa, com todas as outras previstas entidades, porque está definido no pedido.  

### <a name="second-turn-in-conversation"></a>Ativar em segundo lugar na conversação

A seguinte expressão de utilizador para o bot de bate-papo utiliza um termo mais vaga:

`Send him a calendar reminder for the party.`

A expressão anterior, a expressão usa `him` como uma referência a `Hazem`. O bot de bate-papo conversacional, no corpo da mensagem, pode mapear `him` para o valor de entidade extraído da primeira expressão, `Hazem`.

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

A resposta de previsão inclui essa entidade externa, com todas as outras previstas entidades, porque está definido no pedido.  

### <a name="override-existing-model-predictions"></a>Substituir as previsões do modelo existente

O `overridePredictions` vlastnost options Especifica que, se o usuário envia uma entidade externa que sobrepõe-se com uma entidade prevista com o mesmo nome, LUIS escolhe a entidade passado ou a entidade existente no modelo. 

Por exemplo, considere a consulta `today I'm free`. LUIS Deteta `today` como um datetimeV2 com a seguinte resposta:

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

Se o usuário envia a entidade externa:

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

Se o `overridePredictions` está definido como `false`, LUIS devolve uma resposta como se a entidade externa não foram enviada. 

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

Se o `overridePredictions` está definido como `true`, LUIS devolve uma resposta, incluindo:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Resolução

O _opcionais_ `resolution` propriedade devolve na resposta de predição, permitindo que passar os metadados associados a entidade externa, em seguida, recebê-lo de volta na resposta. 

O objetivo principal é estender entidades previamente concebidas, mas não está limitado a esse tipo de entidade. 

O `resolution` propriedade pode ser um número, uma cadeia de caracteres, um objeto ou uma matriz:

* "Dallas"
* {"text": "value"}
* 12345 
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Listas dinâmicas transmitido em tempo de predição

Listas dinâmicas permitem-lhe expandir uma entidade de lista treinado e publicada existente, já na aplicação do LUIS. 

Utilize esta funcionalidade quando os valores da entidade de lista necessário alterar periodicamente. Esta funcionalidade permite-lhe expandir uma entidade de lista já treinado e publicada:

* No momento da solicitação de ponto final da consulta de predição.
* Para um único pedido.

A entidade de lista pode estar vazia na aplicação do LUIS, mas tem de existir. A entidade de lista na aplicação do LUIS não é alterada, mas a capacidade de previsão no ponto final é expandida para incluir até 2 listas com 1000 itens.

### <a name="dynamic-list-json-request-body"></a>Lista dinâmica corpo do pedido JSON

Enviar no corpo JSON seguinte para adicionar uma novo sublista com sinónimos para a lista e a entidade de lista para o texto, de prever `LUIS`, com o `POST` pedido de predição de consulta:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntityName":"ProductList",
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

A resposta de previsão inclui essa entidade de lista, com todas as outras previstas entidades, porque está definido no pedido. 

## <a name="timezoneoffset-renamed-to-datetimereference"></a>TimezoneOffset mudado para datetimeReference

**No V2**, o `timezoneOffset` [parâmetro](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) é enviado no pedido de predição como um parâmetro de cadeia de caracteres de consulta, seja como for, se a solicitação é enviada como um pedido GET ou POST. 

**Na V3**, a mesma funcionalidade é fornecida com o parâmetro de corpo de mensagem, `datetimeReference`. 

## <a name="marking-placement-of-entities-in-utterances"></a>A marcação de colocação de entidades em expressões com

**No V2**, uma entidade foi marcada numa expressão com o `startIndex` e `endIndex`. 

**Na V3**, a entidade está marcada com `startIndex` e `entityLength`.

## <a name="deprecation"></a>Preterição 

A API V2 não vão ser preterida para, pelo menos, 9 meses após a pré-visualização de V3. 

## <a name="next-steps"></a>Passos Seguintes

Utilize a documentação da API de V3 para atualizar o REST existente chama para LUIS [ponto final](https://aka.ms/luis-api-v3) APIs. 