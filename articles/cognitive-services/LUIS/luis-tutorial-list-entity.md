---
title: Entidades de correspondência de texto Extact
description: Saiba como adicionar uma entidade de lista para o ajudar a variações de etiqueta de LUIS de uma palavra ou frase.
services: cognitive-services
author: diberry
titleSuffix: Azure
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 929dc7a86d141446a2070b046c6febfda4a07f0f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58092570"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Utilize uma entidade de lista para aumentar a deteção de entidade 
Este tutorial demonstra o uso de um [listar entidade](luis-concept-entity-types.md) para aumentar a deteção de entidade. Não é necessário ser rotulada como são uma correspondência exata dos termos de entidades de lista.  

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma entidade de lista 
> * Adicionar valores normalizados e sinónimos
> * Validar a identificação de entidade melhorada

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Mais recente [node. js](https://nodejs.org)
> * [Aplicação do LUIS HomeAutomation](luis-get-started-create-app.md). Se não tiver a aplicação de home page de automatização criada, criar uma nova aplicação e adicionar o domínio pré-criado **HomeAutomation**. Prepare e publique a aplicação. 
> * [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key) (se a consultar o número de vezes), ID de aplicação, ID de versão, e [região](luis-reference-regions.md) para a aplicação do LUIS.

> [!Tip]
> Se ainda não tiver uma subscrição, pode se registrar para uma [conta gratuita](https://azure.microsoft.com/free/).

Todo o código neste tutorial está disponível na [repositório do GitHub do Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-list-entity). 

## <a name="use-homeautomation-app"></a>Utilizar a aplicação HomeAutomation
O HomeAutomation aplicação dá-controle de dispositivos, como as luzes, sistemas de entretenimento e ambiente controla como aquecer e refrigeração. Estes sistemas de ter vários nomes diferentes que podem incluir os nomes, nicknames, acrônimos e gíria do fabricante. 

Um sistema que tenha muitos nomes em diferentes culturas e a demografia é o termóstato. Um termóstato pode controlar o resfriamento e aquecer sistemas para uma casa ou de criação.

O ideal é que as seguintes expressões devem resolver para a entidade pré-criados **HomeAutomation.Device**:

|#|Expressão|entidade identificada|pontuação|
|--|--|--|--|
|1|Ativar a ac|HomeAutomation.Device - "CA"|0.8748562|
|2|Ativar cópia de segurança do calor|HomeAutomation.Device - "térmico"|0.784990132|
|3|torná-lo mais frio|||

As duas primeiras expressões mapeiam para diferentes dispositivos. A terceira expressão, "torná-lo mais frio", não mapeiam para um dispositivo, mas em vez disso, solicita um resultado. LUIS não sabe o que significa que o termo "mais frio", o termóstato é o dispositivo pedido. Idealmente, LUIS deve resolver todas essas expressões com para o mesmo dispositivo. 

## <a name="use-a-list-entity"></a>Utilizar uma entidade de lista
A entidade de HomeAutomation.Device é ótima para um pequeno número de dispositivos ou com algumas variações dos nomes. Para um edifício de escritórios ou campus, os nomes de dispositivo ultrapassar a utilidade da entidade HomeAutomation.Device. 

R **listar entidade** é uma boa escolha para este cenário porque o conjunto de termos de um dispositivo num prédio ou do campus é um conjunto conhecido, mesmo se for um conjunto enorme. Ao utilizar uma entidade de lista, LUIS pode receber qualquer valor possíveis no conjunto para o termóstato e resolvê-lo para apenas o dispositivo "termóstato". 

Este tutorial vai criar uma lista de entidades com o termóstato. Os nomes alternativos para um termóstato neste tutorial são: 

|nomes alternativos para termóstato|
|--|
| AC |
| : a/c|
| -c|
|heater|
|acesso frequente|
|muita|
|frio|
|mais frio|

Se for necessário determinar uma nova alternativa muitas vezes, LUIS, então, um [lista de frase](luis-concept-feature.md#how-to-use-phrase-lists) é uma resposta melhor.

## <a name="create-a-list-entity"></a>Criar uma entidade de lista
Crie um ficheiro de node. js e copie o seguinte código para o mesmo. Altere os valores de authoringKey, appId, versionId e região.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

Utilize o seguinte comando para instalar as dependências NPM e executar o código para criar a entidade de lista:

```console
npm install && node add-entity-list.js
```

O resultado da execução é o ID da entidade de lista:

```console
026e92b3-4834-484f-8608-6114a83b03a6
```

## <a name="train-the-model"></a>Dar formação sobre o modelo
Preparar o LUIS para que a nova lista afetar os resultados da consulta. O treinamento é um processo de duas partes de treinamento, a, em seguida, verificar o estado se o treinamento é feito. Uma aplicação com o número de modelos pode demorar alguns momentos para preparar. O seguinte código prepara a aplicação, em seguida, aguarda até que o treinamento é efetuada com êxito. O código usa uma estratégia de espera e repetição para evitar o 429 "demasiados pedidos" erro. 

Crie um ficheiro de node. js e copie o seguinte código para o mesmo. Altere os valores de authoringKey, appId, versionId e região.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

Utilize o seguinte comando para executar o código para preparar a aplicação:

```console
node train.js
```

O resultado da execução é o estado de cada iteração de treinamento dos modelos de LUIS. A execução seguinte necessários apenas uma verificação de treinamento:

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
## <a name="publish-the-model"></a>Publique o modelo
Publica para que a entidade de lista está disponível a partir do ponto final.

Crie um ficheiro de node. js e copie o seguinte código para o mesmo. Altere os valores de endpointKey appId e região. Pode usar sua authoringKey se não planear chamar este ficheiro além do seu limite de quota.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Utilize o seguinte comando para executar o código para consultar a aplicação:

```console
node publish.js
```

O resultado seguinte inclui o url de ponto de extremidade para quaisquer consultas. Resultados JSON reais incluiria o appID real. 

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

## <a name="query-the-app"></a>Consulta a aplicação 
Consulte a aplicação a partir do ponto final para provar que a entidade de lista ajuda a determinar o tipo de dispositivo do LUIS.

Crie um ficheiro de node. js e copie o seguinte código para o mesmo. Altere os valores de endpointKey appId e região. Pode usar sua authoringKey se não planear chamar este ficheiro além do seu limite de quota.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

Utilize o seguinte comando para executar o código e a aplicação de consulta:

```console
node train.js
```

O resultado é que os resultados da consulta. Uma vez que o código adicionado a **verboso** par nome/valor para a cadeia de consulta, a saída inclui todas as intenções e suas classificações:

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

O dispositivo específico de **termóstato** é identificado com uma consulta orientadas a resultados de "ativar cópia de segurança do calor". Uma vez que a entidade de HomeAutomation.Device original é ainda na aplicação, pode ver os resultados também. 

Experimente as outras duas expressões com ver que também são devolvidos como um termóstato. 

|#|Expressão|entidade|tipo|valor|
|--|--|--|--|--|
|1|Ativar a ac| AC | DevicesList | termóstato|
|2|Ativar cópia de segurança do calor|térmico| DevicesList |termóstato|
|3|torná-lo mais frio|mais frio|DevicesList|termóstato|

## <a name="next-steps"></a>Passos Seguintes

Pode criar outra entidade de lista para expandir as localizações de dispositivos para salas, pisos ou edifícios. 
