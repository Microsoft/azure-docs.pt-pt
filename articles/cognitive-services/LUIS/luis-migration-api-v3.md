---
title: Alterações no ponto final da previsão na API V3
description: As APIs de previsão de consulta foram alteradas. Utilize este guia para entender como migrar para a versão 3 endpoint APIs.
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: diberry
ms.openlocfilehash: 9a8e8cb331dd11eebaddbcbf8f603c1148415aef
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79117385"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Alterações no ponto final da previsão para v3

As APIs de previsão de consulta foram alteradas. Utilize este guia para entender como migrar para a versão 3 endpoint APIs.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Estado geralmente disponível** - este V3 API inclui significativas alterações de pedido json e resposta da V2 API.

A V3 API fornece as seguintes novas funcionalidades:

* [Entidades externas](#external-entities-passed-in-at-prediction-time)
* [Listas dinâmicas](#dynamic-lists-passed-in-at-prediction-time)
* [Alterações na entidade pré-construída JSON](#prebuilt-entity-changes)

O [pedido](#request-changes) e [resposta](#response-changes) do ponto final de previsão têm alterações significativas para suportar as novas funcionalidades acima listadas, incluindo as seguintes:

* [Alterações de objetode resposta](#top-level-json-changes)
* [Referências de nome de entidade em vez de nome da entidade](#entity-role-name-instead-of-entity-name)
* [Imóveis para marcar entidades em declarações](#marking-placement-of-entities-in-utterances)

[A documentação de referência](https://aka.ms/luis-api-v3) está disponível para V3.

## <a name="v3-changes-from-preview-to-ga"></a>V3 muda da pré-visualização para GA

A V3 fez as seguintes alterações como parte da mudança para a GA:

* As seguintes entidades pré-construídas têm diferentes respostas JSON:
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeografiaV2](luis-reference-prebuilt-geographyv2.md)
    * [DataTimeV2](luis-reference-prebuilt-datetimev2.md)
    * Nome chave da unidade mensurável de `units` a `unit`

* Alteração do corpo de solicitação JSON:
    * de `preferExternalEntities` a `preferExternalEntities`
    * parâmetro opcional `score` para entidades externas

* Alterações do corpo de resposta JSON:
    * `normalizedQuery` removido

## <a name="suggested-adoption-strategy"></a>Estratégia de adoção sugerida

Se utilizar o Bot Framework, bing Spell Check V7, ou pretender migrar apenas a sua aplicação LUIS, continue a utilizar o ponto final V2.

Se você sabe que nenhuma das suas aplicações ou integrações do seu cliente (Bot Framework, e Bing Spell Check V7) são impactados e você está confortável migrando a sua aplicação LUIS autoria e seu ponto final de previsão ao mesmo tempo, comece a usar o ponto final de previsão V3. O ponto final da previsão V2 ainda estará disponível e é uma boa estratégia de recuo.


## <a name="not-supported"></a>Não suportado

### <a name="bing-spell-check"></a>Verificação Ortográfica do Bing

Esta API não é suportada no ponto final de previsão V3 - continue a utilizar o ponto final de previsão V2 API para correções ortográficas. Se necessitar de correção ortográfica durante a utilização da V3 API, faça com que a aplicação do cliente ligue para a API [bing spell check,](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/overview) e altere o texto para a ortografia correta, antes de enviar o texto para a API LUIS.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Aplicações de clientes bot framework e Azure Bot Service

Continue a utilizar o ponto final de previsão v2 aPI até que o V4.7 do Quadro Bot seja libertado.

## <a name="v2-api-deprecation"></a>V2 API Deprecation

A previsão V2 API não será depreciada durante pelo menos 9 meses após a pré-estreia da V3, 8 de junho de 2020.

## <a name="endpoint-url-changes"></a>Alterações de URL de ponto final

### <a name="changes-by-slot-name-and-version-name"></a>Alterações por nome de slot e nome de versão

O formato da chamada http do ponto final V3 mudou.

Se quiser consultar por versão, primeiro tem de [publicar via API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) com `"directVersionPublish":true`. Consulta do ponto final que refere a versão ID em vez do nome da ranhura.

|VERSÃO API DE PREVISÃO|MÉTODO|do IdP|
|--|--|--|
|V3|GET|https://<b>{REGIÃO}</b>.api.cognitive.microsoft.com/luis/<b>previsão</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict?consulta=<b>{QUERY}</b>|
|V3|POST|https://<b>{REGIÃO}</b>.api.cognitive.microsoft.com/luis/<b>previsão</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict|
|V2|GET|https://<b>{REGIÃO}</b>.api.cognitive.microsoft.com/luis/<b>previsão</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/versões/<b>{VERSION-ID}</b>/predict?question=<b>{QUERY}</b>|
|V2|POST|https://<b>{REGIÃO}</b>.api.cognitive.microsoft.com/luis/<b>previsão</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/versões/<b>{VERSION-ID}</b>/predict|

|Valores válidos para `SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Solicitar alterações

### <a name="query-string-changes"></a>Alterações nas cordas de consulta

A V3 API tem diferentes parâmetros de cadeia de consulta.

|Nome do desfile|Tipo|Versão|Predefinição|Objetivo|
|--|--|--|--|--|
|`log`|boolean|V2 e V3|false|Guarde a consulta no ficheiro de registo. O valor predefinido é falso.|
|`query`|string|Apenas V3|Sem incumprimento - é exigido no pedido GET|**Em V2,** a expressão a prever está no parâmetro `q`. <br><br>**Em V3,** a funcionalidade é passada no parâmetro `query`.|
|`show-all-intents`|boolean|Apenas V3|false|Devolva todas as intenções com a pontuação correspondente no objeto **de previsão.intenções.** As intenções são devolvidas como objetos num objeto `intents` dos pais. Isto permite o acesso programático sem necessidade de encontrar a intenção numa matriz: `prediction.intents.give`. Em V2, estes foram devolvidos numa matriz. |
|`verbose`|boolean|V2 e V3|false|**Em V2,** quando definido para verdade, todas as intenções previstas foram devolvidas. Se precisar de todas as intenções previstas, use o parâmetro V3 de `show-all-intents`.<br><br>**No V3,** este parâmetro apenas fornece detalhes de metadados da entidade da previsão da entidade.  |
|`timezoneOffset`|string|V2|-|Fuso horário aplicado a entidades datetimeV2.|
|`datetimeReference`|string|V3|-|[Fuso horário](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) aplicado a entidades datetimeV2. Substitui `timezoneOffset` de V2.|


### <a name="v3-post-body"></a>V3 POST corpo

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

|Propriedade|Tipo|Versão|Predefinição|Objetivo|
|--|--|--|--|--|
|`dynamicLists`|array|Apenas V3|Não é necessário.|[As listas dinâmicas](#dynamic-lists-passed-in-at-prediction-time) permitem-lhe alargar uma entidade de listas já treinada e publicada, já na aplicação LUIS.|
|`externalEntities`|array|Apenas V3|Não é necessário.|[Entidades externas](#external-entities-passed-in-at-prediction-time) dão à sua app LUIS a capacidade de identificar e rotular entidades durante o tempo de execução, que podem ser usadas como funcionalidades para entidades existentes. |
|`options.datetimeReference`|string|Apenas V3|Sem padrão|Utilizado para determinar a data de [compensação V2](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). O formato para a dataReferência é [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|boolean|Apenas V3|false|Especifica se a entidade externa do utilizador [(com o mesmo nome que a entidade existente)](#override-existing-model-predictions) for utilizada ou se a entidade existente no modelo for utilizada para previsão. |
|`query`|string|Apenas V3|Necessário.|**Em V2,** a expressão a prever está no parâmetro `q`. <br><br>**Em V3,** a funcionalidade é passada no parâmetro `query`.|



## <a name="response-changes"></a>Alterações de resposta

A resposta de consulta Que JSON alterou para permitir um maior acesso programático aos dados utilizados com mais frequência.

### <a name="top-level-json-changes"></a>Mudanças jSON de nível superior



As principais propriedades jSON para V2 são, quando `verbose` é definido para verdade, que devolve todas as intenções e suas pontuações na propriedade `intents`:

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
        "topIntent": "intent-name-1",
        "intents": {},
        "entities":{}
    }
}
```

O `intents` objeto é uma lista não ordenada. Não assuma que a primeira criança do `intents` corresponde à `topIntent`. Em vez disso, use o valor `topIntent` para encontrar a pontuação:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

As alterações de esquema da JSON de resposta permitem:

* Distinção clara entre a expressão original, `query`, e a previsão devolvida, `prediction`.
* Acesso programático mais fácil aos dados previstos. Em vez de enumerar através de uma matriz em V2, pode aceder a valores pelo **nome** tanto para intenções como para entidades. Para as funções previstas da entidade, o nome do papel é devolvido porque é único em toda a aplicação.
* Os tipos de dados, se determinados, são respeitados. Os numéricos já não são devolvidos como cordas.
* Distinção entre informação de previsão de primeira prioridade e metadados adicionais, devolvidos no objeto `$instance`.

### <a name="entity-response-changes"></a>Alterações na resposta da entidade

#### <a name="marking-placement-of-entities-in-utterances"></a>Marcação de colocação de entidades em declarações

**Em V2,** uma entidade foi marcada numa expressão com o `startIndex` e `endIndex`.

**Em V3,** a entidade está marcada com `startIndex` e `entityLength`.

#### <a name="access-instance-for-entity-metadata"></a>Acesso `$instance` para metadados de entidades

Se necessitar de metadados de entidades, a cadeia de consulta precisa de usar a bandeira `verbose=true` e a resposta contém os metadados no objeto `$instance`. Exemplos são mostrados nas respostas JSON nas seguintes secções.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Cada entidade prevista é representada como uma matriz

O objeto `prediction.entities.<entity-name>` contém uma matriz porque cada entidade pode ser prevista mais de uma vez na expressão.

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>Alterações de entidades pré-construídas

O objeto de resposta V3 inclui alterações a entidades pré-construídas. Reveja [entidades pré-construídas específicas](luis-reference-prebuilt-entities.md) para saber mais.

#### <a name="list-entity-prediction-changes"></a>Lista de alterações de previsão da entidade

O JSON para uma lista de previsão de entidade mudou para ser um conjunto de matrizes:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Cada matriz interior corresponde ao texto dentro da expressão. O objeto interior é uma matriz porque o mesmo texto pode aparecer em mais de uma sublista de uma entidade da lista.

Ao mapear entre o `entities` objeto ao objeto `$instance`, a ordem dos objetos é preservada para as previsões da entidade da lista.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>Nome de papel da entidade em vez do nome da entidade

Em V2, o conjunto de `entities` devolveu todas as entidades previstas, sendo o nome da entidade o identificador único. No V3, se a entidade usa funções e a previsão é para um papel de entidade, o identificador principal é o nome principal. Isto é possível porque os nomes de papéis da entidade devem ser únicos em toda a app, incluindo outros nomes de modelo (intenção, entidade).

No seguinte exemplo: considere uma expressão que inclua o texto, `Yellow Bird Lane`. Este texto é previsto como um `Location` papel personalizado da entidade de `Destination`.

|Texto de expressão|Nome da entidade|Nome da função|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

Em V2, a entidade é identificada pelo nome da _entidade_ com o papel de propriedade do objeto:

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

Na V3, a entidade é referenciada pelo papel da _entidade,_ se a previsão for para o papel:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

Em V3, o mesmo resultado com a bandeira `verbose` para devolver metadados da entidade:

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

## <a name="external-entities-passed-in-at-prediction-time"></a>Entidades externas passaram no momento da previsão

Entidades externas dão à sua app LUIS a capacidade de identificar e rotular entidades durante o tempo de execução, que podem ser usadas como funcionalidades para entidades existentes. Isto permite-lhe utilizar os seus próprios extratores de entidades separadas e personalizados antes de enviar consultas para o seu ponto final de previsão. Como isto é feito no ponto final da previsão de consulta, não precisa de treinar e publicar o seu modelo.

A aplicação cliente está a fornecer o seu próprio extrator de entidade, gerindo a entidade que corresponde e determina a localização dentro da expressão daquela entidade correspondente e, em seguida, envia essa informação com o pedido.

As entidades externas são o mecanismo de extensão de qualquer tipo de entidade, enquanto ainda são utilizados como sinais para outros modelos como papéis, compósitos, entre outros.

Isto é útil para uma entidade que tem dados disponíveis apenas no tempo de execução da previsão de consulta. Exemplos deste tipo de dados estão constantemente a mudar dados ou específicos por utilizador. Pode estender uma entidade de contacto LUIS com informações externas a partir da lista de contactos de um utilizador.

### <a name="entity-already-exists-in-app"></a>Entidade já existe na app

O valor da `entityName` para a entidade externa, aprovada no organismo post de pedido final, já deve existir na app treinada e publicada no momento em que o pedido é feito. O tipo de entidade não importa, todos os tipos são suportados.

### <a name="first-turn-in-conversation"></a>Primeira volta na conversa

Considere uma primeira expressão numa conversa de chat bot onde um utilizador introduz as seguintes informações incompletas:

`Send Hazem a new message`

O pedido do chat bot para o LUIS pode passar informações no corpo do POST sobre `Hazem`, pelo que é diretamente igualado como um dos contactos do utilizador.

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

Na expressão anterior, a expressão utiliza `him` como referência a `Hazem`. O chat bot conversacional, no corpo do POST, pode mapear `him` ao valor da entidade extraído da primeira expressão, `Hazem`.

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

A propriedade `preferExternalEntities` opções especifica que se o utilizador enviar uma entidade externa que se sobrepõe a uma entidade prevista com o mesmo nome, a LUIS escolhe a entidade que passou ou a entidade existente no modelo.

Por exemplo, considere a consulta `today I'm free`. Luis deteta `today` como dataV2 com a seguinte resposta:

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

Se o `preferExternalEntities` está definido para `false`, o LUIS devolve uma resposta como se a entidade externa não tivesse sido enviada.

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

Se o `preferExternalEntities` estiver definido para `true`, luis devolve uma resposta incluindo:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Resolução

O _`resolution` opcional_ devolve na resposta à previsão, permitindo-lhe passar nos metadados associados à entidade externa, recebendo-o de volta na resposta.

O principal objetivo é alargar as entidades pré-construídas, mas não se limita a esse tipo de entidade.

A propriedade `resolution` pode ser um número, uma corda, um objeto, ou uma matriz:

* "Dallas"
* {"text": "value"}
* 12345
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Listas dinâmicas passadas no momento da previsão

As listas dinâmicas permitem-lhe alargar uma entidade de listas já treinada e publicada, já na aplicação LUIS.

Utilize esta funcionalidade quando os valores da sua entidade da lista precisam de mudar periodicamente. Esta funcionalidade permite-lhe estender uma entidade de lista já treinada e publicada:

* No momento do pedido de previsão de consulta.
* Para um único pedido.

A entidade da lista pode estar vazia na app LUIS, mas tem de existir. A entidade da lista na app LUIS não é alterada, mas a capacidade de previsão no ponto final é alargada para incluir até 2 listas com cerca de 1.000 itens.

### <a name="dynamic-list-json-request-body"></a>Lista dinâmica JSON request body

Envie o seguinte organismo JSON para adicionar uma nova sublista com sinónimos à lista, e prever a entidade da lista para o texto, `LUIS`, com o pedido de previsão de consulta `POST`:

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

## <a name="deprecation"></a>Preterição

A V2 API não será depreciada durante pelo menos 9 meses após a pré-visualização v3.

## <a name="next-steps"></a>Passos seguintes

Utilize a documentação V3 API para atualizar as chamadas DE REST existentes para ASAP [de ponto final](https://aka.ms/luis-api-v3) LUIS.
