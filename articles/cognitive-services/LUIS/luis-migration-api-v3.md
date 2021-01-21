---
title: Alterações no ponto final de previsão na API V3
description: O ponto final de previsão de consulta V3 APIs mudou. Utilize este guia para entender como migrar para as APIs de ponto final da versão 3.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 59dfa439f6428f2db972a8f848887e1a74bc2622
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624308"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Alterações no ponto final de previsão para V3

O ponto final de previsão de consulta V3 APIs mudou. Utilize este guia para entender como migrar para as APIs de ponto final da versão 3.

**Estado geralmente disponível** - esta API V3 inclui pedidos significativos de JSON e alterações de resposta da V2 API.

A API V3 fornece as seguintes novas funcionalidades:

* [Entidades externas](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)
* [Listas dinâmicas](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Alterações de entidade pré-construída JSON](#prebuilt-entity-changes)

O [pedido](#request-changes) e [resposta](#response-changes) do ponto final de previsão têm alterações significativas para apoiar as novas funcionalidades acima enumeradas, incluindo as seguintes:

* [Alterações de objetos de resposta](#top-level-json-changes)
* [Referências de nome de função de entidade em vez de nome de entidade](#entity-role-name-instead-of-entity-name)
* [Imóveis para marcar entidades em expressões](#marking-placement-of-entities-in-utterances)

[A documentação de referência](https://aka.ms/luis-api-v3) está disponível para v3.

## <a name="v3-changes-from-preview-to-ga"></a>V3 muda de pré-visualização para GA

A V3 e fez as seguintes alterações como parte da mudança para a AG:

* As seguintes entidades pré-construídas têm diferentes respostas JSON:
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeografiaV2](luis-reference-prebuilt-geographyv2.md)
    * [DatatimeV2](luis-reference-prebuilt-datetimev2.md)
    * Nome-chave da unidade mensurável de `units``unit`

* Alteração do corpo do JSON:
    * de `preferExternalEntities``preferExternalEntities`
    * parâmetro opcional `score` para entidades externas

* Alterações do corpo de resposta JSON:
    * `normalizedQuery` removido

## <a name="suggested-adoption-strategy"></a>Estratégia de adoção sugerida

Se utilizar o Bot Framework, Bing Spell Check V7 ou quiser migrar apenas a sua aplicação LUIS, continue a utilizar o ponto final V2.

Se não souber que nenhuma das suas aplicações ou integrações do seu cliente (Bot Framework e Bing Spell Check V7) são impactadas e se sentir confortável em migrar a sua aplicação LUIS e o seu ponto final de previsão ao mesmo tempo, comece a usar o ponto final de previsão V3. O ponto final da previsão V2 ainda estará disponível e é uma boa estratégia de recuo.


## <a name="not-supported"></a>Não suportado

### <a name="bing-spell-check"></a>Verificação Ortográfica do Bing

Esta API não é suportada no ponto final de previsão V3 - continue a utilizar o ponto final de previsão da API V2 para correções ortográficas. Se precisar de correção ortográfica durante a utilização da API V3, mande o pedido do cliente ligar para a API [de Verificação ortográfica de Bing e](../bing-spell-check/overview.md) alterar o texto para a ortografia correta, antes de enviar o texto para a API LUIS.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Aplicações de clientes bot framework e azure Bot service

Continue a utilizar o ponto final de previsão da API V2 até que o V4.7 do Quadro Bot seja libertado.


## <a name="endpoint-url-changes"></a>Alterações de URL de ponto final

### <a name="changes-by-slot-name-and-version-name"></a>Alterações pelo nome da ranhura e nome da versão

O formato da chamada [HTTP do ponto final V3](developer-reference-resource.md#rest-endpoints) mudou.

Se quiser consultar por versão, primeiro tem de [publicar via API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) com `"directVersionPublish":true` . Consultar o ponto final referente ao ID da versão em vez do nome da ranhura.

|Valores válidos para `SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Solicitar alterações

### <a name="query-string-changes"></a>Alterações nas cordas de consulta

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

### <a name="v3-post-body"></a>Corpo V3 POST

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
|`dynamicLists`|matriz|Apenas V3|Não necessárias.|[As listas dinâmicas](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time) permitem-lhe alargar uma entidade de listas treinada e publicada existente, já na app LUIS.|
|`externalEntities`|matriz|Apenas V3|Não necessárias.|[Entidades externas](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time) dão à sua app LUIS a capacidade de identificar e rotular entidades durante o tempo de execução, que podem ser usadas como funcionalidades para as entidades existentes. |
|`options.datetimeReference`|string|Apenas V3|Sem incumprimento|Utilizado para determinar a [compensação do tempo de dataV2](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). O formato para a datatimeReference é [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|boolean|Apenas V3|false|Especifica se a entidade externa do utilizador [(com o mesmo nome que a entidade existente)](schema-change-prediction-runtime.md#override-existing-model-predictions) é utilizada ou se a entidade existente no modelo é utilizada para previsão. |
|`query`|string|Apenas V3|Obrigatório.|**Em V2,** a expressão a prever está no `q` parâmetro. <br><br>**Em V3,** a funcionalidade é passada no `query` parâmetro.|

## <a name="response-changes"></a>Alterações de resposta

A resposta de consulta JSON mudou para permitir um maior acesso programático aos dados mais utilizados.

### <a name="top-level-json-changes"></a>Mudanças de JSON de nível superior



As principais propriedades JSON para V2 são, quando `verbose` definidas como verdadeiras, que devolve todas as intenções e suas pontuações na `intents` propriedade:

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

O `intents` objeto é uma lista não ordenado. Não assuma que o primeiro filho `intents` na casa corresponde ao `topIntent` . Em vez disso, use o `topIntent` valor para encontrar a pontuação:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

As alterações no esquema JSON de resposta permitem:

* Distinção clara entre a expressão `query` original, e a previsão devolvida, `prediction` .
* Acesso programático mais fácil aos dados previstos. Em vez de enumerar através de uma matriz em V2, pode aceder a valores pelo **nome** tanto para intenções como para entidades. Para funções de entidades previstas, o nome da função é devolvido porque é único em toda a aplicação.
* Os tipos de dados, se determinados, são respeitados. Os numéricos já não são devolvidos como cordas.
* Distinção entre primeira prioridade de previsão e metadados adicionais, devolvidos no `$instance` objeto.

### <a name="entity-response-changes"></a>Alterações na resposta da entidade

#### <a name="marking-placement-of-entities-in-utterances"></a>Marcação de colocação de entidades em declarações

**Em V2,** uma entidade foi marcada numa expressão com o `startIndex` e `endIndex` .

**Na V3,** a entidade é marcada com `startIndex` e `entityLength` .

#### <a name="access-instance-for-entity-metadata"></a>Acesso `$instance` aos metadados da entidade

Se necessitar de metadados de entidade, o fio de consulta precisa de usar a `verbose=true` bandeira e a resposta contém os metadados no `$instance` objeto. Os exemplos são mostrados nas respostas do JSON nas seguintes secções.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Cada entidade prevista é representada como uma matriz

O `prediction.entities.<entity-name>` objeto contém uma matriz porque cada entidade pode ser prevista mais de uma vez na expressão.

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>Alterações de entidades pré-construídas

O objeto de resposta V3 inclui alterações a entidades pré-construídas. Reveja [entidades pré-construídas específicas](luis-reference-prebuilt-entities.md) para saber mais.

#### <a name="list-entity-prediction-changes"></a>Alterações de previsão da entidade de lista

O JSON para uma previsão de entidade de lista mudou para ser um conjunto de matrizes:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Cada matriz interior corresponde ao texto dentro da expressão. O objeto interior é uma matriz porque o mesmo texto pode aparecer em mais de uma sublistista de uma entidade de lista.

Ao mapear entre o `entities` objeto e o `$instance` objeto, a ordem dos objetos é preservada para as previsões da entidade da lista.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>Nome da função da entidade em vez do nome da entidade

Em V2, a `entities` matriz devolveu todas as entidades previstas com o nome da entidade sendo o identificador único. Em V3, se a entidade usa funções e a previsão é para um papel de entidade, o identificador principal é o nome da função. Isto é possível porque os nomes de papéis de entidade devem ser únicos em toda a aplicação, incluindo outros nomes de modelos (intenção, entidade).

No seguinte exemplo: considere uma expressão que inclua o texto, `Yellow Bird Lane` . Este texto é previsto como um papel de entidade personalizada `Location` de `Destination` .

|Texto de expressão|Nome da entidade|Nome da função|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

Em V2, a entidade é identificada pelo nome da _entidade_ com a função de propriedade do objeto:

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

Em V3, a entidade é referenciada pela função da _entidade,_ se a previsão for para o papel:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

Na V3, o mesmo resultado com a `verbose` bandeira para devolver metadados da entidade:

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

## <a name="extend-the-app-at-prediction-time"></a>Prolongar a app no momento da previsão

Aprenda [conceitos](schema-change-prediction-runtime.md) sobre como estender a app no tempo de previsão.


## <a name="next-steps"></a>Passos seguintes

Utilize a documentação V3 API para atualizar as chamadas REST existentes para ASP do [ponto final](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/operations/5cb0a9459a1fe8fa44c28dd8) da LUIS.
