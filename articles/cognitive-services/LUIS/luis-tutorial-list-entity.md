---
title: Entidades de correspondência de texto extact-LUIS
titleSuffix: Azure Cognitive Services
description: Saiba como adicionar uma entidade de lista para ajudar as variações de rótulo LUIS de uma palavra ou frase.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: f3c99856eaffc454754618a1eac34630b985a77e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499482"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Usar uma entidade de lista para aumentar a detecção de entidades 
Este artigo demonstra o uso de uma [entidade List](luis-concept-entity-types.md) para aumentar a detecção de entidades. As entidades de lista não precisam ser rotuladas, pois são uma correspondência exata dos termos.  

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar uma entidade de lista 
> * Adicionar valores normalizados e sinônimos
> * Validar identificação de entidade aprimorada

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * [Node. js](https://nodejs.org) mais recente
> * [Aplicativo HOMEAUTOMATION Luis](luis-get-started-create-app.md). Se você não tiver o aplicativo de automação inicial criado, crie um novo aplicativo e adicione o **HomeAutomation**de domínio predefinido. Prepare e publique a aplicação. 
> * [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key) (se consulta muitas vezes), ID do aplicativo, ID da versão e [região](luis-reference-regions.md) para o aplicativo Luis.

> [!Tip]
> Se você ainda não tiver uma assinatura, poderá registrar-se para uma [conta gratuita](https://azure.microsoft.com/free/).

Todo o código deste artigo está disponível no [repositório GitHub Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-list-entity). 

## <a name="use-homeautomation-app"></a>Usar o aplicativo HomeAutomation
O aplicativo HomeAutomation oferece controle de dispositivos como luzes, sistemas de entretenimento e controles de ambiente, como aquecimento e resfriamento. Esses sistemas têm vários nomes diferentes que podem incluir nomes de fabricantes, apelidos, acrônimos e gírias. 

Um sistema que tem muitos nomes entre diferentes culturas e dados demográficos é o termostato. Um termostato pode controlar sistemas de refrigeração e aquecimento para uma casa ou prédio.

Idealmente, o declarações a seguir deve ser resolvido para a entidade predefinida **HomeAutomation. Device**:

|#|expressão|entidade identificada|placar|
|--|--|--|--|
|1|ligar o AC|HomeAutomation. Device-"AC"|0,8748562|
|2|ativar o calor|HomeAutomation. Device-"calor"|0,784990132|
|3|Torne-o mais frio|||

Os dois primeiros declarações são mapeados para dispositivos diferentes. O terceiro expressão, "torná-lo mais frio", não é mapeado para um dispositivo, mas, em vez disso, solicita um resultado. LUIS não sabe que o termo "Cold" significa que o termostato é o dispositivo solicitado. O ideal é que o LUIS resolva todos esses declarações para o mesmo dispositivo. 

## <a name="use-a-list-entity"></a>Usar uma entidade de lista
A entidade HomeAutomation. Device é ótima para um pequeno número de dispositivos ou com poucas variações dos nomes. Para um prédio ou campus de um escritório, os nomes dos dispositivos crescem além da utilidade da entidade HomeAutomation. Device. 

Uma **entidade de lista** é uma boa opção para esse cenário porque o conjunto de termos para um dispositivo em um prédio ou campus é um conjunto conhecido, mesmo que seja um grande conjunto. Usando uma entidade de lista, LUIS pode receber qualquer valor possível no conjunto para o termostato e resolvê-lo apenas para o único dispositivo "termostato". 

Este artigo vai criar uma lista de entidades com o termostato. Os nomes alternativos para um termostato neste artigo são: 

|nomes alternativos para termostato|
|--|
| alterna |
| a/c|
| a-c|
|aquecedor|
|pontos|
|mais quente|
|Frio|
|frio|

Se o LUIS precisar determinar uma nova alternativa com frequência, uma [lista de frases](luis-concept-feature.md#how-to-use-phrase-lists) será uma resposta melhor.

## <a name="create-a-list-entity"></a>Criar uma entidade de lista
Crie um arquivo node. js e copie o código a seguir nele. Altere os valores de authoringKey, appId, versionID e Region.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

Use o seguinte comando para instalar as dependências de NPM e executar o código para criar a entidade de lista:

```console
npm install && node add-entity-list.js
```

A saída da execução é a ID da entidade de lista:

```console
026e92b3-4834-484f-8608-6114a83b03a6
```

## <a name="train-the-model"></a>Dar formação sobre o modelo
Treine o LUIS para que a nova lista afete os resultados da consulta. O treinamento é um processo de duas partes de treinamento e, em seguida, verificando o status se o treinamento for feito. Um aplicativo com muitos modelos pode levar alguns minutos para treinar. O código a seguir treina o aplicativo e aguarda até que o treinamento seja bem-sucedido. O código usa uma estratégia de espera e repetição para evitar o erro de 429 "muitas solicitações". 

Crie um arquivo node. js e copie o código a seguir nele. Altere os valores de authoringKey, appId, versionID e Region.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

Use o seguinte comando para executar o código para treinar o aplicativo:

```console
node train.js
```

A saída da execução é o status de cada iteração do treinamento dos modelos LUIS. A execução a seguir exigiu apenas uma verificação de treinamento:

```console
1 trained = true
[ { modelId: '2c549f95-867a-4189-9c35-44b95c78b70f',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '5530e900-571d-40ec-9c78-63e66b50c7d4',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '519faa39-ae1a-4d98-965c-abff6f743fe6',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9671a485-36a9-46d5-aacd-b16d05115415',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9ef7d891-54ab-48bf-8112-c34dcd75d5e2',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '8e16a660-8781-4abf-bf3d-f296ebe1bf2d',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } } ]

```
## <a name="publish-the-model"></a>Publicar o modelo
Publicar para que a entidade de lista esteja disponível no ponto de extremidade.

Crie um arquivo node. js e copie o código a seguir nele. Altere os valores de endpointKey, appId e Region. Você pode usar o authoringKey se não planeja chamar esse arquivo além do limite de cota.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Use o seguinte comando para executar o código para consultar o aplicativo:

```console
node publish.js
```

A saída a seguir inclui a URL do ponto de extremidade para qualquer consulta. Os resultados reais de JSON incluiriam a appID real. 

```json
{ 
  versionId: null,
  isStaging: false,
  endpointUrl: 'https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>',
  region: null,
  assignedEndpointKey: null,
  endpointRegion: 'westus',
  publishedDateTime: '2018-01-29T22:17:38Z' }
}
```

## <a name="query-the-app"></a>Consultar o aplicativo 
Consulte o aplicativo do ponto de extremidade para provar que a entidade de lista ajuda a LUIS a determinar o tipo de dispositivo.

Crie um arquivo node. js e copie o código a seguir nele. Altere os valores de endpointKey, appId e Region. Você pode usar o authoringKey se não planeja chamar esse arquivo além do limite de cota.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

Use o seguinte comando para executar o código e consultar o aplicativo:

```console
node train.js
```

A saída são os resultados da consulta. Como o código adicionou o par nome/valor **detalhado** à cadeia de caracteres de consulta, a saída inclui todas as intenções e suas pontuações:

```json
{
  "query": "turn up the heat",
  "topScoringIntent": {
    "intent": "HomeAutomation.TurnOn",
    "score": 0.139018849
  },
  "intents": [
    {
      "intent": "HomeAutomation.TurnOn",
      "score": 0.139018849
    },
    {
      "intent": "None",
      "score": 0.120624863
    },
    {
      "intent": "HomeAutomation.TurnOff",
      "score": 0.06746891
    }
  ],
  "entities": [
    {
      "entity": "heat",
      "type": "HomeAutomation.Device",
      "startIndex": 12,
      "endIndex": 15,
      "score": 0.784990132
    },
    {
      "entity": "heat",
      "type": "DevicesList",
      "startIndex": 12,
      "endIndex": 15,
      "resolution": {
        "values": [
          "Thermostat"
        ]
      }
    }
  ]
}
```

O dispositivo específico do **termostato** é identificado com uma consulta orientada a resultados de "ativar o calor". Como a entidade HomeAutomation. Device original ainda está no aplicativo, você também pode ver seus resultados. 

Experimente os outros dois declarações para ver que eles também são retornados como um termostato. 

|#|expressão|Entidade|tipo|valor|
|--|--|--|--|--|
|1|ligar o AC| alterna | Dispositivos | Termostato|
|2|ativar o calor|mapa| Dispositivos |Termostato|
|3|Torne-o mais frio|frio|Dispositivos|Termostato|

## <a name="next-steps"></a>Passos seguintes

Você pode criar outra entidade de lista para expandir os locais do dispositivo para salas, andares ou edifícios. 
