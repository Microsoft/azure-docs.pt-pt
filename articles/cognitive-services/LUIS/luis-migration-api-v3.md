---
title: Alterações de ponto de extremidade de previsão na API v3
titleSuffix: Azure Cognitive Services
description: As APIs de ponto de extremidade de previsão de consulta foram alteradas. Use este guia para entender como migrar para as APIs de ponto de extremidade da versão 3.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 8756d8e60e7612c1610e07b0567465e3a0ea8884
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531501"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Alterações de ponto de extremidade de previsão para v3

As APIs de ponto de extremidade de previsão de consulta foram alteradas. Use este guia para entender como migrar para as APIs de ponto de extremidade da versão 3. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Status de disponibilidade geral** – esta API v3 inclui alterações de resposta e solicitação JSON significativas da API v2.

A API v3 fornece os seguintes novos recursos:

* [Entidades externas](#external-entities-passed-in-at-prediction-time)
* [Listas dinâmicas](#dynamic-lists-passed-in-at-prediction-time)
* [Alterações de JSON de entidade predefinida](#prebuilt-entity-changes)

A [solicitação](#request-changes) e a [resposta](#response-changes) do ponto de extremidade de previsão têm alterações significativas para dar suporte aos novos recursos listados acima, incluindo o seguinte:

* [Alterações de objeto de resposta](#top-level-json-changes)
* [Referências de nome de função de entidade em vez do nome da entidade](#entity-role-name-instead-of-entity-name)
* [Propriedades para marcar entidades em declarações](#marking-placement-of-entities-in-utterances)

A [documentação de referência](https://aka.ms/luis-api-v3) está disponível para v3.

## <a name="v3-changes-from-preview-to-ga"></a>O V3 muda de visualização para GA

A V3 fez as seguintes alterações como parte da mudança para GA: 

* As seguintes entidades predefinidas têm respostas JSON diferentes: 
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2](luis-reference-prebuilt-datetimev2.md)
    * Nome da chave de unidade mensurável de `units` para `unit`

* Alteração de JSON do corpo da solicitação:
    * de `preferExternalEntities` para `preferExternalEntities`
    * parâmetro `score` opcional para entidades externas

* Alterações de JSON do corpo da resposta:
    * `normalizedQuery` removido

## <a name="suggested-adoption-strategy"></a>Estratégia de adoção sugerida

Se você usar o bot Framework, Verificação Ortográfica do Bing v7 ou desejar migrar apenas a criação de aplicativos LUIS, continue a usar o ponto de extremidade v2. 

Se você souber que nenhum de seus aplicativos cliente ou integrações (bot Framework e Verificação Ortográfica do Bing v7) são afetados e você está confortável migrando sua criação de aplicativo LUIS e seu ponto de extremidade de previsão ao mesmo tempo, comece a usar o ponto de extremidade de previsão v3. O ponto de extremidade de previsão v2 ainda estará disponível e será uma boa estratégia de retorno. 

## <a name="not-supported"></a>Não suportado

* Não há suporte para a API de Verificação Ortográfica do Bing no ponto de extremidade de previsão v3-continue a usar o ponto de extremidade de previsão da API v2 para correções ortográficas

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Aplicativos cliente do bot Framework e do serviço de bot do Azure

Continue a usar o ponto de extremidade de previsão da API v2 até que a V 4.7 da estrutura de bot seja lançada. 

## <a name="v2-api-deprecation"></a>Reprovação da API v2 

A API de previsão v2 não será preterida por pelo menos 9 meses após a visualização v3, 8 de junho de 2020. 

## <a name="endpoint-url-changes"></a>Alterações de URL de ponto de extremidade 

### <a name="changes-by-slot-name-and-version-name"></a>Alterações por nome do slot e nome da versão

O formato da chamada HTTP do ponto de extremidade v3 foi alterado.

Se desejar consultar por versão, primeiro você precisará [publicar via API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) com `"directVersionPublish":true`. Consulte o ponto de extremidade que referencia a ID de versão em vez do nome do slot.

|VERSÃO DA API DE PREVISÃO|FORMA|URL|
|--|--|--|
|V3|GET|https://<b>{Region}</b>. api.cognitive.microsoft.com/luis/<b>preditiva</b>/<b>v 3.0</b>/apps/<b>{app-ID}</b>/Slots/<b>{slot-Name}</b>/Predict? Query =<b>{Query}</b>|
|V3|POST|https://<b>{Region}</b>. api.cognitive.microsoft.com/luis/<b>preditiva</b>/<b>v 3.0</b>/apps/<b>{app-ID}</b>/Slots/<b>{slot-Name}</b>/Predict|
|V2|GET|https://<b>{Region}</b>. api.cognitive.microsoft.com/luis/<b>preditiva</b>/<b>v 3.0</b>/apps/<b>{app-ID}</b>/Versions/<b>{version-ID}</b>/Predict? consulta =<b>{Query}</b>|
|V2|POST|https://<b>{Region}</b>. api.cognitive.microsoft.com/luis/<b>preditiva</b>/<b>v 3.0</b>/apps/<b>{app-ID}</b>/Versions/<b>{version-ID}</b>/Predict|

|Valores válidos para `SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Solicitar alterações 

### <a name="query-string-changes"></a>Alterações na cadeia de consulta

A API v3 tem parâmetros de cadeia de caracteres de consulta diferentes.

|Nome do parâmetro|Tipo|Versão|Predefinição|Finalidade|
|--|--|--|--|--|
|`log`|boolean|V2 & v3|false|Armazenar consulta no arquivo de log. O valor padrão é false.| 
|`query`|string|Somente v3|Nenhum padrão-ele é necessário na solicitação GET|**Em v2**, o expressão a ser previsto está no parâmetro `q`. <br><br>**No v3**, a funcionalidade é passada no parâmetro `query`.|
|`show-all-intents`|boolean|Somente v3|false|Retorne todas as intenções com a pontuação correspondente no objeto **preditiva. retenções** . As intenções são retornadas como objetos em um objeto de `intents` pai. Isso permite o acesso programático sem a necessidade de encontrar a intenção em uma matriz: `prediction.intents.give`. Em v2, elas foram retornadas em uma matriz. |
|`verbose`|boolean|V2 & v3|false|**Em v2**, quando definido como true, todas as intenções previstas foram retornadas. Se você precisar de todas as intenções previstas, use o parâmetro v3 de `show-all-intents`.<br><br>**No v3**, esse parâmetro fornece apenas detalhes de metadados de entidade de previsão de entidade.  |
|`timezoneOffset`|string|V2|-|Fuso horário aplicado às entidades datetimeV2.|
|`datetimeReference`|string|V3|-|[Fuso horário](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) aplicado às entidades datetimeV2. Substitui `timezoneOffset` de v2.|


### <a name="v3-post-body"></a>Corpo da POSTAgem v3

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "preferExternalEntities": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Propriedade|Tipo|Versão|Predefinição|Finalidade|
|--|--|--|--|--|
|`dynamicLists`|array|Somente v3|Não é necessário.|As [listas dinâmicas](#dynamic-lists-passed-in-at-prediction-time) permitem que você estenda uma entidade de lista treinada e publicada existente, já no aplicativo Luis.|
|`externalEntities`|array|Somente v3|Não é necessário.|[Entidades externas](#external-entities-passed-in-at-prediction-time) dão ao seu aplicativo Luis a capacidade de identificar e rotular entidades durante o tempo de execução, que pode ser usado como recursos para entidades existentes. |
|`options.datetimeReference`|string|Somente v3|Nenhum padrão|Usado para determinar o [deslocamento de datetimeV2](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). O formato para o datetimeReference é [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|boolean|Somente v3|false|Especifica se a [entidade externa do usuário (com o mesmo nome da entidade existente)](#override-existing-model-predictions) é usada ou a entidade existente no modelo é usada para previsão. |
|`query`|string|Somente v3|Necessário.|**Em v2**, o expressão a ser previsto está no parâmetro `q`. <br><br>**No v3**, a funcionalidade é passada no parâmetro `query`.|



## <a name="response-changes"></a>Alterações de resposta

O JSON de resposta de consulta foi alterado para permitir um maior acesso programático aos dados usados com mais frequência. 

### <a name="top-level-json-changes"></a>Alterações de JSON de nível superior



As principais propriedades JSON para v2 são, quando `verbose` é definido como true, que retorna todas as intenções e suas pontuações na propriedade `intents`:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

As principais propriedades JSON para v3 são:

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "topIntent": "intent-name-1",
        "intents": {}, 
        "entities":{}
    }
}
```

O objeto `intents` é uma lista não ordenada. Não assuma o primeiro filho na `intents` corresponde ao `topIntent`. Em vez disso, use o valor `topIntent` para localizar a Pontuação:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

As alterações de esquema JSON de resposta permitem:

* Desmarque a distinção entre expressão original, `query`e previsão retornada, `prediction`.
* Acesso programático fácil aos dados previstos. Em vez de enumerar por meio de uma matriz na v2, você pode acessar valores por **nome** para as intenções e as entidades. Para funções de entidade previstas, o nome da função é retornado porque é exclusivo em todo o aplicativo.
* Os tipos de dados, se determinados, são respeitados. Os numéricos não são mais retornados como cadeias de caracteres.
* Distinção entre informações de previsão da primeira prioridade e metadados adicionais, retornados no objeto `$instance`. 

### <a name="entity-response-changes"></a>Alterações de resposta de entidade

#### <a name="marking-placement-of-entities-in-utterances"></a>Marcando o posicionamento de entidades no declarações

**Na v2**, uma entidade foi marcada em um expressão com a `startIndex` e `endIndex`. 

**Em v3**, a entidade é marcada com `startIndex` e `entityLength`.

#### <a name="access-instance-for-entity-metadata"></a>`$instance` de acesso para metadados de entidade

Se você precisar de metadados de entidade, a cadeia de caracteres de consulta precisará usar o sinalizador `verbose=true` e a resposta conterá os metadados no objeto `$instance`. Os exemplos são mostrados nas respostas JSON nas seções a seguir.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Cada entidade prevista é representada como uma matriz

O objeto `prediction.entities.<entity-name>` contém uma matriz porque cada entidade pode ser prevista mais de uma vez no expressão. 

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>Alterações de entidade predefinidas

O objeto de resposta v3 inclui alterações em entidades predefinidas. Examine [entidades predefinidas específicas](luis-reference-prebuilt-entities.md) para saber mais. 

#### <a name="list-entity-prediction-changes"></a>Listar alterações de previsão de entidade

O JSON para uma previsão de entidade de lista mudou para ser uma matriz de matrizes:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Cada matriz interior corresponde ao texto dentro de expressão. O objeto interior é uma matriz porque o mesmo texto pode aparecer em mais de uma sublista de uma entidade de lista. 

Ao mapear entre o objeto de `entities` para o objeto `$instance`, a ordem dos objetos é preservada para as previsões de entidade de lista.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>Nome da função de entidade em vez do nome da entidade 

Na v2, a matriz de `entities` retornou todas as entidades previstas com o nome da entidade sendo o identificador exclusivo. Em v3, se a entidade usar funções e a previsão for para uma função de entidade, o identificador primário será o nome da função. Isso é possível porque os nomes de função de entidade devem ser exclusivos em todo o aplicativo, incluindo outros nomes de modelo (intenção, entidade).

No exemplo a seguir: Considere um expressão que inclui o texto, `Yellow Bird Lane`. Esse texto é previsto como uma função de entidade de `Location` personalizada de `Destination`.

|Texto expressão|Nome da entidade|Nome da função|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

Na v2, a entidade é identificada pelo _nome da entidade_ com a função como uma propriedade do objeto:

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

Em v3, a entidade é referenciada pela _função de entidade_, se a previsão for para a função:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

Em v3, o mesmo resultado com o sinalizador `verbose` para retornar os metadados da entidade:

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

## <a name="external-entities-passed-in-at-prediction-time"></a>Entidades externas passadas no momento da previsão

Entidades externas dão ao seu aplicativo LUIS a capacidade de identificar e rotular entidades durante o tempo de execução, que pode ser usado como recursos para entidades existentes. Isso permite que você use seus próprios extratores de entidade personalizados e separados antes de enviar consultas para seu ponto de extremidade de previsão. Como isso é feito no ponto de extremidade de previsão de consulta, você não precisa treinar novamente e publicar seu modelo.

O aplicativo cliente está fornecendo seu próprio extrator de entidade Gerenciando a correspondência de entidades e determinando o local dentro do expressão dessa entidade correspondente e, em seguida, enviando essas informações com a solicitação. 

Entidades externas são o mecanismo para estender qualquer tipo de entidade e, ao mesmo tempo, ser usado como sinais para outros modelos, como funções, composição e outros.

Isso é útil para uma entidade que tem dados disponíveis somente no tempo de execução de previsão de consulta. Exemplos desse tipo de dados estão constantemente mudando de dados ou específicos por usuário. Você pode estender uma entidade de contato LUIS com informações externas da lista de contatos de um usuário. 

### <a name="entity-already-exists-in-app"></a>A entidade já existe no aplicativo

O valor de `entityName` para a entidade externa, passado no corpo de POSTAgem de solicitação de ponto de extremidade, já deve existir no aplicativo treinado e publicado no momento em que a solicitação é feita. O tipo de entidade não importa, todos os tipos têm suporte.

### <a name="first-turn-in-conversation"></a>Primeiro ativar conversa

Considere um primeiro expressão em uma conversa de bot de chat, em que um usuário insere as seguintes informações incompletas:

`Send Hazem a new message`

A solicitação do bot de chat para LUIS pode passar informações no corpo da POSTAgem sobre `Hazem` para que ele seja diretamente correspondido como um dos contatos do usuário.

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

A resposta de previsão inclui a entidade externa, com todas as outras entidades previstas, porque ela é definida na solicitação.  

### <a name="second-turn-in-conversation"></a>Segunda vez em conversa

O próximo usuário expressão no bot de chat usa um termo mais vago:

`Send him a calendar reminder for the party.`

No expressão anterior, o expressão usa `him` como referência a `Hazem`. O bot de chat de conversa, no corpo da POSTAgem, pode mapear `him` para o valor da entidade extraído da primeira expressão, `Hazem`.

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

A resposta de previsão inclui a entidade externa, com todas as outras entidades previstas, porque ela é definida na solicitação.  

### <a name="override-existing-model-predictions"></a>Substituir previsões de modelo existentes

A propriedade de opções de `preferExternalEntities` especifica que, se o usuário enviar uma entidade externa que se sobrepõe a uma entidade prevista com o mesmo nome, LUIS escolherá a entidade passada ou a entidade existente no modelo. 

Por exemplo, considere o `today I'm free`de consulta. LUIS detecta `today` como um datetimeV2 com a seguinte resposta:

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

Se o usuário enviar a entidade externa:

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

Se o `preferExternalEntities` for definido como `false`, LUIS retornará uma resposta como se a entidade externa não fosse enviada. 

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

Se o `preferExternalEntities` for definido como `true`, LUIS retornará uma resposta, incluindo:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Resolução

A propriedade _opcional_ `resolution` retorna na resposta de previsão, permitindo que você passe os metadados associados à entidade externa e, em seguida, receba-os novamente na resposta. 

A principal finalidade é estender entidades predefinidas, mas não se limita a esse tipo de entidade. 

A propriedade `resolution` pode ser um número, uma cadeia de caracteres, um objeto ou uma matriz:

* Dallas
* {"text": "value"}
* 12345 
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Listas dinâmicas passadas no momento da previsão

As listas dinâmicas permitem que você estenda uma entidade de lista treinada e publicada existente, já no aplicativo LUIS. 

Use esse recurso quando os valores de sua entidade de lista precisarem ser alterados periodicamente. Esse recurso permite que você estenda uma entidade de lista já treinada e publicada:

* No momento da solicitação de ponto de extremidade de previsão de consulta.
* Para uma única solicitação.

A entidade List pode estar vazia no aplicativo LUIS, mas ela deve existir. A entidade de lista no aplicativo LUIS não é alterada, mas a capacidade de previsão no ponto de extremidade é estendida para incluir até 2 listas com cerca de 1.000 itens.

### <a name="dynamic-list-json-request-body"></a>Corpo da solicitação JSON da lista dinâmica

Envie o seguinte corpo JSON para adicionar uma nova sublista com sinônimos à lista e prever a entidade de lista para o texto, `LUIS`, com a solicitação de previsão de consulta `POST`:

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

A resposta de previsão inclui essa entidade de lista, com todas as outras entidades previstas, porque ela é definida na solicitação. 

## <a name="deprecation"></a>Preterição 

A API v2 não será preterida por pelo menos 9 meses após a visualização v3. 

## <a name="next-steps"></a>Passos seguintes

Use a documentação da API V3 para atualizar as chamadas REST existentes para as APIs de [ponto de extremidade](https://aka.ms/luis-api-v3) do Luis. 
