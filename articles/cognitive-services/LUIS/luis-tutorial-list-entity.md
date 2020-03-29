---
title: Entidades de jogo de texto extact - LUIS
titleSuffix: Azure Cognitive Services
description: Saiba como adicionar uma entidade da lista para ajudar o LUIS a rotular variações de uma palavra ou frase.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: f3c99856eaffc454754618a1eac34630b985a77e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73499482"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Utilize uma entidade de lista para aumentar a deteção de entidades 
Este artigo demonstra o uso de uma [entidade de lista](luis-concept-entity-types.md) para aumentar a deteção de entidades. As entidades da lista não precisam de ser rotuladas por serem uma combinação exata de termos.  

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar uma entidade de lista 
> * Adicione valores e sinónimos normalizados
> * Validar a identificação melhorada da entidade

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Último [Nó.js](https://nodejs.org)
> * [Aplicação HOMEAutomation LUIS](luis-get-started-create-app.md). Se não tiver a aplicação Home Automation criada, crie uma nova app e adicione o **Domain HomeAutomation**pré-construído. Prepare e publique a aplicação. 
> * [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key) (se consultado muitas vezes), ID de aplicação, id de versão e [região](luis-reference-regions.md) para a aplicação LUIS.

> [!Tip]
> Se ainda não tiver uma subscrição, pode inscrever-se para uma [conta gratuita.](https://azure.microsoft.com/free/)

Todo o código deste artigo está disponível no [repositório Azure-Samples GitHub](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-list-entity). 

## <a name="use-homeautomation-app"></a>Use aplicativo HomeAutomation
A aplicação HomeAutomation dá-lhe o controlo de dispositivos como luzes, sistemas de entretenimento e controlos ambientais, como aquecimento e arrefecimento. Estes sistemas têm vários nomes diferentes que podem incluir nomes do fabricante, alcunhas, siglas e gíria. 

Um sistema que tem muitos nomes em diferentes culturas e demografia é o termóstato. Um termóstato pode controlar os sistemas de arrefecimento e aquecimento para uma casa ou edifício.

Idealmente, as seguintes declarações devem ser resolvidas à entidade pré-construída **HomeAutomation.Device**:

|#|expressão|entidade identificada|classificação|
|--|--|--|--|
|1|ligar o ac|HomeAutomation.Device - "ac"|0.8748562|
|2|aumentar o calor|HomeAutomation.Device - "heat"|0.784990132|
|3|torná-lo mais frio|||

As duas primeiras expressões mapeiam para diferentes dispositivos. A terceira expressão, "torná-lo mais frio", não mapeia para um dispositivo, mas pede um resultado. Luis não sabe que o termo "mais frio", significa que o termóstato é o dispositivo solicitado. Idealmente, o LUIS deve resolver todas estas declarações para o mesmo dispositivo. 

## <a name="use-a-list-entity"></a>Use uma entidade de lista
A entidade HomeAutomation.Device é ótima para um pequeno número de dispositivos ou com poucas variações dos nomes. Para um edifício de escritórios ou campus, os nomes do dispositivo crescem para além da utilidade da entidade HomeAutomation.Device. 

Uma **entidade de lista** é uma boa escolha para este cenário porque o conjunto de termos para um dispositivo em um edifício ou campus é um conjunto conhecido, mesmo que seja um conjunto enorme. Utilizando uma entidade de lista, a LUIS pode receber qualquer valor possível no conjunto para o termóstato, e resolvê-lo até apenas ao dispositivo único "termóstato". 

Este artigo vai criar uma lista de entidades com o termóstato. Os nomes alternativos para um termóstato neste artigo são: 

|nomes alternativos para termóstato|
|--|
| ac |
| a/c|
| a-c|
|aquecedor|
|atalho|
|mais quente|
|frio|
|mais frio|

Se o LUIS precisa de determinar uma nova alternativa muitas vezes, então uma lista de [frases](luis-concept-feature.md#how-to-use-phrase-lists) é uma resposta melhor.

## <a name="create-a-list-entity"></a>Criar uma entidade de lista
Crie um ficheiro Node.js e copie o seguinte código nele. Altere os valores da autoriaKey, appId, versionId e região.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

Utilize o seguinte comando para instalar as dependências do NPM e executar o código para criar a entidade da lista:

```console
npm install && node add-entity-list.js
```

A saída da execução é a identificação da entidade da lista:

```console
026e92b3-4834-484f-8608-6114a83b03a6
```

## <a name="train-the-model"></a>Dar formação sobre o modelo
Treinar O LUIS para que a nova lista afete os resultados da consulta. A formação é um processo de treino em duas partes, verificando depois o estado se a formação for feita. Uma aplicação com muitos modelos pode demorar alguns momentos a treinar. O código seguinte treina a app e espera até que o treino seja bem sucedido. O código usa uma estratégia de espera e repetição para evitar o erro de 429 "pedidos a mais". 

Crie um ficheiro Node.js e copie o seguinte código nele. Altere os valores da autoriaKey, appId, versionId e região.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

Utilize o seguinte comando para executar o código para treinar a aplicação:

```console
node train.js
```

A saída da corrida é o estatuto de cada iteração da formação dos modelos LUIS. A seguinte execução exigia apenas um controlo da formação:

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
Publique para que a entidade da lista esteja disponível a partir do ponto final.

Crie um ficheiro Node.js e copie o seguinte código nele. Altere os valores endpointKey, appId e região. Pode utilizar a sua autoriaChave se não pretender ligar para este ficheiro para além do limite de quota.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Utilize o seguinte comando para executar o código para consultar a aplicação:

```console
node publish.js
```

A saída seguinte inclui o url de ponto final para quaisquer consultas. Os resultados reais da JSON incluiriam o verdadeiro appID. 

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

## <a name="query-the-app"></a>Consulta da app 
Consultar a app a partir do ponto final para provar que a entidade da lista ajuda a LUIS a determinar o tipo de dispositivo.

Crie um ficheiro Node.js e copie o seguinte código nele. Altere os valores endpointKey, appId e região. Pode utilizar a sua autoriaChave se não pretender ligar para este ficheiro para além do limite de quota.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

Utilize o seguinte comando para executar o código e consultar a aplicação:

```console
node train.js
```

A saída é os resultados da consulta. Como o código adicionou o nome **verboso/par** de valor à corda de consulta, a saída inclui todas as intenções e suas pontuações:

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

O dispositivo específico do **termóstato** é identificado com uma consulta orientada para o resultado de "aumentar o calor". Uma vez que a entidade Original HomeAutomation.Device ainda se encontra na aplicação, também pode ver os seus resultados. 

Experimente as outras duas expressões para ver se também são devolvidas como termóstato. 

|#|expressão|entidade|tipo|valor|
|--|--|--|--|--|
|1|ligar o ac| ac | Lista de Dispositivos | Termóstato|
|2|aumentar o calor|calor| Lista de Dispositivos |Termóstato|
|3|torná-lo mais frio|mais frio|Lista de Dispositivos|Termóstato|

## <a name="next-steps"></a>Passos seguintes

Você pode criar outra entidade lista para expandir a localização do dispositivo para quartos, pisos ou edifícios. 
