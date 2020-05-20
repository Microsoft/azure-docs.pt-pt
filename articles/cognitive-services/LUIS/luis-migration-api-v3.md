---
title: Alterações no ponto final da previsão na API V3
description: As APIs de previsão de consulta foram alteradas. Utilize este guia para entender como migrar para a versão 3 endpoint APIs.
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: diberry
ms.openlocfilehash: d6e5981bcdc81383454c65d3cf7b6c1195b70a4a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653859"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Alterações no ponto final da previsão para v3

As APIs de previsão de consulta foram alteradas. Utilize este guia para entender como migrar para a versão 3 endpoint APIs.

**Estado geralmente disponível** - este V3 API inclui significativas alterações de pedido json e resposta da V2 API.

A V3 API fornece as seguintes novas funcionalidades:

* [Entidades externas](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)
* [Listas dinâmicas](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
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
    * Nome chave da unidade mensurável de `units` para`unit`

* Alteração do corpo de solicitação JSON:
    * de `preferExternalEntities` a`preferExternalEntities`
    * parâmetro opcional `score` para entidades externas

* Alterações do corpo de resposta JSON:
    * `normalizedQuery`removido

## <a name="suggested-adoption-strategy"></a>Estratégia de adoção sugerida

Se utilizar o Bot Framework, bing Spell Check V7, ou pretender migrar apenas a sua aplicação LUIS, continue a utilizar o ponto final V2.

Se você sabe que nenhuma das suas aplicações ou integrações do seu cliente (Bot Framework, e Bing Spell Check V7) são impactados e você está confortável migrando a sua aplicação LUIS autoria e seu ponto final de previsão ao mesmo tempo, comece a usar o ponto final de previsão V3. O ponto final da previsão V2 ainda estará disponível e é uma boa estratégia de recuo.


## <a name="not-supported"></a>Não suportado

### <a name="bing-spell-check"></a>Verificação de Ortografia do Bing

Esta API não é suportada no ponto final de previsão V3 - continue a utilizar o ponto final de previsão V2 API para correções ortográficas. Se necessitar de correção ortográfica durante a utilização da V3 API, faça com que a aplicação do cliente ligue para a API [bing spell check,](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/overview) e altere o texto para a ortografia correta, antes de enviar o texto para a API LUIS.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Aplicações de clientes bot framework e Azure Bot Service

Continue a utilizar o ponto final de previsão v2 aPI até que o V4.7 do Quadro Bot seja libertado.

## <a name="v2-api-deprecation"></a>V2 API Deprecation

A previsão V2 API não será depreciada durante pelo menos 9 meses após a pré-estreia da V3, 8 de junho de 2020.

## <a name="endpoint-url-changes"></a>Alterações de URL de ponto final

### <a name="changes-by-slot-name-and-version-name"></a>Alterações por nome de slot e nome de versão

O formato da chamada [http do ponto final V3](developer-reference-resource.md#rest-endpoints) mudou.

Se quiser consultar por versão, primeiro precisa [de publicar via API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) com `"directVersionPublish":true` . Consulta do ponto final que refere a versão ID em vez do nome da ranhura.

|Valores válidos para`SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Solicitar alterações

### <a name="query-string-changes"></a>Alterações nas cordas de consulta

A V3 API tem diferentes parâmetros de cadeia de consulta.

|Nome do desfile|Tipo|Versão|Predefinição|Objetivo|
|--|--|--|--|--|
|`log`|boolean|V2 & V3|false|Guarde a consulta no ficheiro de registo. O valor predefinido é falso.|
|`query`|string|Apenas V3|Sem incumprimento - é exigido no pedido GET|**Em V2,** a expressão a prever está no `q` parâmetro. <br><br>**Em V3,** a funcionalidade é passada no `query` parâmetro.|
|`show-all-intents`|boolean|Apenas V3|false|Devolva todas as intenções com a pontuação correspondente no objeto **de previsão.intenções.** As intenções são devolvidas como objetos num `intents` objecto-mãe. Isto permite o acesso programático sem necessidade de encontrar a intenção numa matriz: `prediction.intents.give` . Em V2, estes foram devolvidos numa matriz. |
|`verbose`|boolean|V2 & V3|false|**Em V2,** quando definido para verdade, todas as intenções previstas foram devolvidas. Se precisar de todas as intenções previstas, use o param V3 de `show-all-intents` .<br><br>**No V3,** este parâmetro apenas fornece detalhes de metadados da entidade da previsão da entidade.  |
|`timezoneOffset`|string|V2|-|Fuso horário aplicado a entidades datetimeV2.|
|`datetimeReference`|string|V3|-|[Fuso horário](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) aplicado a entidades datetimeV2. Substitui `timezoneOffset` a V2.|


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
|`dynamicLists`|array|Apenas V3|Não é necessário.|[As listas dinâmicas](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time) permitem-lhe alargar uma entidade de listas já treinada e publicada, já na aplicação LUIS.|
|`externalEntities`|array|Apenas V3|Não é necessário.|[Entidades externas](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time) dão à sua app LUIS a capacidade de identificar e rotular entidades durante o tempo de execução, que podem ser usadas como funcionalidades para entidades existentes. |
|`options.datetimeReference`|string|Apenas V3|Sem padrão|Utilizado para determinar a data de [compensação V2](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). O formato para a dataReferência é [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|boolean|Apenas V3|false|Especifica se a entidade externa do utilizador [(com o mesmo nome que a entidade existente)](schema-change-prediction-runtime.md#override-existing-model-predictions) for utilizada ou se a entidade existente no modelo for utilizada para previsão. |
|`query`|string|Apenas V3|Necessário.|**Em V2,** a expressão a prever está no `q` parâmetro. <br><br>**Em V3,** a funcionalidade é passada no `query` parâmetro.|

## <a name="response-changes"></a>Alterações de resposta

A resposta de consulta Que JSON alterou para permitir um maior acesso programático aos dados utilizados com mais frequência.

### <a name="top-level-json-changes"></a>Mudanças jSON de nível superior



As principais propriedades jSON para V2 são, quando `verbose` é definido para verdade, que devolve todas as intenções e suas pontuações na `intents` propriedade:

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

O `intents` objeto é uma lista não ordenada. Não assuma que a primeira criança corresponde `intents` ao `topIntent` . Em vez disso, use o `topIntent` valor para encontrar a pontuação:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

As alterações de esquema da JSON de resposta permitem:

* Distinção clara entre a expressão `query` original, e a previsão devolvida, `prediction` .
* Acesso programático mais fácil aos dados previstos. Em vez de enumerar através de uma matriz em V2, pode aceder a valores pelo **nome** tanto para intenções como para entidades. Para as funções previstas da entidade, o nome do papel é devolvido porque é único em toda a aplicação.
* Os tipos de dados, se determinados, são respeitados. Os numéricos já não são devolvidos como cordas.
* Distinção entre informação de previsão de primeira prioridade e metadados adicionais, devolvidos no `$instance` objeto.

### <a name="entity-response-changes"></a>Alterações na resposta da entidade

#### <a name="marking-placement-of-entities-in-utterances"></a>Marcação de colocação de entidades em declarações

**Em V2,** uma entidade foi marcada numa expressão com o `startIndex` e `endIndex` .

**Em V3,** a entidade é marcada com `startIndex` `entityLength` e.

#### <a name="access-instance-for-entity-metadata"></a>Acesso `$instance` a metadados de entidades

Se necessitar de metadados de entidades, a cadeia de consulta precisa de usar a `verbose=true` bandeira e a resposta contém os metadados no `$instance` objeto. Exemplos são mostrados nas respostas JSON nas seguintes secções.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Cada entidade prevista é representada como uma matriz

O `prediction.entities.<entity-name>` objeto contém uma matriz porque cada entidade pode ser prevista mais de uma vez na expressão.

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

Ao mapear entre o `entities` objeto ao `$instance` objeto, a ordem dos objetos é preservada para as previsões da entidade da lista.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>Nome de papel da entidade em vez do nome da entidade

Em V2, a `entities` matriz devolveu todas as entidades previstas, sendo o nome da entidade o identificador único. No V3, se a entidade usa funções e a previsão é para um papel de entidade, o identificador principal é o nome principal. Isto é possível porque os nomes de papéis da entidade devem ser únicos em toda a app, incluindo outros nomes de modelo (intenção, entidade).

No seguinte exemplo: considere uma expressão que inclua o texto, `Yellow Bird Lane` . Este texto é previsto como o papel de uma entidade personalizada `Location` de `Destination` .

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

Em V3, o mesmo resultado com a `verbose` bandeira para devolver metadados da entidade:

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

<a name="external-entities-passed-in-at-prediction-time"></a>
<a name="override-existing-model-predictions"></a>

## <a name="extend-the-app-at-prediction-time"></a>Alargar a aplicação no momento da previsão

Saiba [conceitos](schema-change-prediction-runtime.md) sobre como estender a app no tempo de execução da previsão.

## <a name="deprecation"></a>Preterição

A V2 API não será depreciada durante pelo menos 9 meses após a pré-visualização v3.

## <a name="next-steps"></a>Passos seguintes

Utilize a documentação V3 API para atualizar as chamadas DE REST existentes para ASAP [de ponto final](https://aka.ms/luis-api-v3) LUIS.
