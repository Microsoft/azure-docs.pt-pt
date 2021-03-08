---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 08/27/2020
ms.openlocfilehash: b3e80612cb83d13f674b9f0e7710e59a7c27696a
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445343"
---
[Documentação de referência](/javascript/api/@azure/cognitiveservices-personalizer/)  | [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer)  |  [Pacote (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer)  |  [Amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Personalizer)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* A versão atual de [Node.js](https://nodejs.org) e NPM.
* Assim que tiver a subscrição do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title=" crie um recurso Personalizer no portal "  target="_blank"> </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API Personalizada. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

[!INCLUDE [Change model frequency](change-model-frequency.md)]

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue até ela.

```console
mkdir myapp && cd myapp
```

Executar o `npm init -y` comando para criar um `package.json` ficheiro.

```console
npm init -y
```

Crie uma nova aplicação Node.js no seu editor preferido ou IDE nomeado `sample.js` e crie variáveis para a chave final e subscrição do seu recurso. 

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```javascript
const uuidv1 = require('uuid/v1');
const Personalizer = require('@azure/cognitiveservices-personalizer');
const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
const readline = require('readline-sync');

// The key specific to your personalization service instance; e.g. "0123456789abcdef0123456789ABCDEF"
const serviceKey = "<REPLACE-WITH-YOUR-PERSONALIZER-KEY>";

// The endpoint specific to your personalization service instance; 
// e.g. https://<your-resource-name>.cognitiveservices.azure.com
const baseUri = "https://<REPLACE-WITH-YOUR-PERSONALIZER-ENDPOINT>.cognitiveservices.azure.com";
```

### <a name="install-the-nodejs-library-for-personalizer"></a>Instale a biblioteca Node.js para Personalizar

Instale a biblioteca cliente Personalizer para Node.js com o seguinte comando:

```console
npm install @azure/cognitiveservices-personalizer --save
```

Instale os restantes pacotes NPM para este arranque rápido:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="object-model"></a>Modelo de objeto

O cliente Personalizer é um objeto [PersonalizerClient](/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient) que autentica a Azure usando microsoft.Rest.ServiceClientCredentials, que contém a sua chave.

Para pedir o melhor item do conteúdo, crie um [RankRequest,](/javascript/api/@azure/cognitiveservices-personalizer/rankrequest)em seguida, passe-o ao [cliente. Método de classificação.](/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient#rank-rankrequest--msrest-requestoptionsbase-) O método Rank devolve um RankResponse.

Para enviar uma recompensa ao Personaler, crie um [RewardRequest,](/javascript/api/@azure/cognitiveservices-personalizer/rewardrequest)em seguida, passe-o para o método [Recompensa](/javascript/api/@azure/cognitiveservices-personalizer/events#reward-string--rewardrequest--servicecallback-void--) na classe Eventos.

Determinar a recompensa, neste arranque rápido é trivial. Num sistema de produção, a determinação do que impacta a [pontuação](../concept-rewards.md) da recompensa e o quanto pode ser um processo complexo, que poderá decidir mudar com o tempo. Esta deve ser uma das principais decisões de design na sua arquitetura Personalizer.

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer o seguinte com a biblioteca de clientes Personalizer para Node.js:

* [Criar um cliente personalizante](#authenticate-the-client)
* [Classificação API](#request-the-best-action)
* [Recompensa API](#send-a-reward)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Instantiu o `PersonalizerClient` com o seu e que criou mais `serviceKey` `baseUri` cedo.

```javascript
const credentials = new CognitiveServicesCredentials(serviceKey);

// Initialize Personalization client.
const personalizerClient = new Personalizer.PersonalizerClient(credentials, baseUri);
```

## <a name="get-content-choices-represented-as-actions"></a>Obtenha escolhas de conteúdo representadas como ações

As ações representam as escolhas de conteúdo a partir das quais deseja que o Personaler selecione o melhor item de conteúdo. Adicione os seguintes métodos à classe Programa para representar o conjunto de ações e as suas características.

```javascript
function getContextFeaturesList() {
  const timeOfDayFeatures = ['morning', 'afternoon', 'evening', 'night'];
  const tasteFeatures = ['salty', 'sweet'];

  let answer = readline.question("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night\n");
  let selection = parseInt(answer);
  const timeOfDay = selection >= 1 && selection <= 4 ? timeOfDayFeatures[selection - 1] : timeOfDayFeatures[0];

  answer = readline.question("\nWhat type of food would you prefer (enter number)? 1. salty 2. sweet\n");
  selection = parseInt(answer);
  const taste = selection >= 1 && selection <= 2 ? tasteFeatures[selection - 1] : tasteFeatures[0];

  console.log("Selected features:\n");
  console.log("Time of day: " + timeOfDay + "\n");
  console.log("Taste: " + taste + "\n");

  return [
    {
      "time": timeOfDay
    },
    {
      "taste": taste
    }
  ];
}
```

```javascript
function getActionsList() {
  return [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "medium"
        },
        {
          "nutritionLevel": 5,
          "cuisine": "italian"
        }
      ]
    },
    {
      "id": "ice cream",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none"
        },
        {
          "nutritionalLevel": 2
        }
      ]
    },
    {
      "id": "juice",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none"
        },
        {
          "nutritionLevel": 5
        },
        {
          "drink": true
        }
      ]
    },
    {
      "id": "salad",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "low"
        },
        {
          "nutritionLevel": 8
        }
      ]
    }
  ];
}
```

## <a name="create-the-learning-loop"></a>Criar o ciclo de aprendizagem

O ciclo de aprendizagem personalizer é um ciclo de chamadas [Rank](#request-the-best-action) and [Reward.](#send-a-reward) Neste quickstart, cada chamada Rank, para personalizar o conteúdo, é seguida por uma chamada Reward para dizer ao Personaler o quão bem o serviço foi executado.

O código que se segue passa por um ciclo de pedir ao utilizador as suas preferências na linha de comando, enviando essa informação ao Personaler para selecionar a melhor ação, apresentando a seleção ao cliente para escolher entre a lista, enviando depois uma recompensa ao Personaler sinalizando o quão bem o serviço se saiu na sua seleção.

```javascript
let runLoop = true;

do {

  let rankRequest = {}

  // Generate an ID to associate with the request.
  rankRequest.eventId = uuidv1();

  // Get context information from the user.
  rankRequest.contextFeatures = getContextFeaturesList();

  // Get the actions list to choose from personalization with their features.
  rankRequest.actions = getActionsList();

  // Exclude an action for personalization ranking. This action will be held at its current position.
  rankRequest.excludedActions = getExcludedActionsList();

  rankRequest.deferActivation = false;

  // Rank the actions
  const rankResponse = await personalizerClient.rank(rankRequest);

  console.log("\nPersonalization service thinks you would like to have:\n")
  console.log(rankResponse.rewardActionId);

  // Display top choice to user, user agrees or disagrees with top choice
  const reward = getReward();

  console.log("\nPersonalization service ranked the actions with the probabilities as below:\n");
  for (let i = 0; i < rankResponse.ranking.length; i++) {
    console.log(JSON.stringify(rankResponse.ranking[i]) + "\n");
  }

  // Send the reward for the action based on user response.

  const rewardRequest = {
    value: reward
  }

  await personalizerClient.events.reward(rankRequest.eventId, rewardRequest);

  runLoop = continueLoop();

} while (runLoop);
```

Olhe mais de perto para o posto e recompense as chamadas nas seguintes secções.

Adicione os seguintes métodos, que [obtêm as escolhas de conteúdo,](#get-content-choices-represented-as-actions)antes de executar o ficheiro de código:

* getActionsList
* obterContextFeaturesList

## <a name="request-the-best-action"></a>Solicite a melhor ação

Para completar o pedido Rank, o programa pede às preferências do utilizador para criar escolhas de conteúdo. O processo pode criar conteúdo para excluir das ações, mostradas como `excludeActions` . O pedido rank precisa das [ações](../concepts-features.md#actions-represent-a-list-of-options) e suas funcionalidades, funcionalidades atuais doContexto, exclusões Deactions, e um ID de evento de classificação único, para receber a resposta classificada.

Este quickstart tem características de contexto simples de hora do dia e preferência alimentar do utilizador. Nos sistemas de produção, determinar e [avaliar](../concept-feature-evaluation.md) [ações e características](../concepts-features.md) pode ser uma questão não trivial.

```javascript
let rankRequest = {}

// Generate an ID to associate with the request.
rankRequest.eventId = uuidv1();

// Get context information from the user.
rankRequest.contextFeatures = getContextFeaturesList();

// Get the actions list to choose from personalization with their features.
rankRequest.actions = getActionsList();

// Exclude an action for personalization ranking. This action will be held at its current position.
rankRequest.excludedActions = getExcludedActionsList();

rankRequest.deferActivation = false;

// Rank the actions
const rankResponse = await personalizerClient.rank(rankRequest);
```

## <a name="send-a-reward"></a>Enviar uma recompensa

Para obter a pontuação de recompensa para enviar o pedido de Recompensa, o programa obtém a seleção do utilizador da linha de comando, atribui um valor numérico à seleção, em seguida, envia o ID do evento único e a pontuação da recompensa como o valor numérico para a API Reward.

Este quickstart atribui um número simples como uma pontuação de recompensa, um zero ou um 1. Nos sistemas de produção, determinar quando e o que enviar para a chamada [Decompras](../concept-rewards.md) pode ser uma questão não trivial, dependendo das suas necessidades específicas.

```javascript
const rewardRequest = {
  value: reward
}

await personalizerClient.events.reward(rankRequest.eventId, rewardRequest);
```

## <a name="run-the-program"></a>Execute o programa

Execute o requerimento com o Node.js do seu diretório de candidaturas.

```console
node sample.js
```

![O programa quickstart faz algumas perguntas para recolher as preferências dos utilizadores, conhecidas como funcionalidades, e depois fornece a ação de topo.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
