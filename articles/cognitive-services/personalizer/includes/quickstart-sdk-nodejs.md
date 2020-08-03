---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file, devx-track-javascript
ms.date: 07/30/2020
ms.openlocfilehash: 4f57d3a6c959a8ec912722a617496c52f412c13f
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461158"
---
[Documentação de referência](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest)  | [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer)  |  [Pacote (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer)  |  [Amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Personalizer)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual de [Node.js](https://nodejs.org) e NPM.

## <a name="using-this-quickstart"></a>Usando este arranque rápido


Existem vários passos para usar este arranque rápido:

* No portal Azure, crie um recurso Personalizer
* No portal Azure, para o recurso Personalizer, na página **Configuração,** altere a frequência de atualização do modelo para um intervalo muito curto
* Num editor de código, crie um ficheiro de código e edite o ficheiro de código
* Na linha de comando ou terminal, instale o SDK a partir da linha de comando
* Na linha de comando ou terminal, execute o ficheiro de código

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue até ela.

```console
mkdir myapp && cd myapp
```

Executar o `npm init -y` comando para criar um `package.json` ficheiro.

```console
npm init -y
```

## <a name="install-the-nodejs-library-for-personalizer"></a>Instale a biblioteca Node.js para Personalizar

Instale a biblioteca cliente Personalizer para Node.js com o seguinte comando:

```console
npm install @azure/cognitiveservices-personalizer --save
```

Instale os restantes pacotes NPM para este arranque rápido:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="object-model"></a>Modelo de objeto

O cliente Personalizer é um objeto [PersonalizerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest) que autentica a Azure usando microsoft.Rest.ServiceClientCredentials, que contém a sua chave.

Para pedir o melhor item do conteúdo, crie um [RankRequest,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rankrequest?view=azure-node-latest)em seguida, passe-o ao [cliente. Método de classificação.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest#rank-rankrequest--msrest-requestoptionsbase-) O método Rank devolve um RankResponse.

Para enviar uma recompensa ao Personaler, crie um [RewardRequest,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rewardrequest?view=azure-node-latest)em seguida, passe-o para o método [Recompensa](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/events?view=azure-node-latest#reward-string--rewardrequest--servicecallback-void--) na classe Eventos.

Determinar a recompensa, neste arranque rápido é trivial. Num sistema de produção, a determinação do que impacta a [pontuação](../concept-rewards.md) da recompensa e o quanto pode ser um processo complexo, que poderá decidir mudar com o tempo. Esta deve ser uma das principais decisões de design na sua arquitetura Personalizer.

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer o seguinte com a biblioteca de clientes Personalizer para Node.js:

* [Criar um cliente personalizante](#create-a-personalizer-client)
* [Classificação API](#request-the-best-action)
* [Recompensa API](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Crie uma nova aplicação Node.js no seu editor preferido ou IDE nomeado `sample.js` .

## <a name="add-the-dependencies"></a>Adicione as dependências

Abra o ficheiro **sample.js** no seu editor preferido ou IDE. Adicione o seguinte `requires` para adicionar os pacotes NPM:

[!code-javascript[Add module dependencies](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Adicionar informações de recurso personalizador

Edite as variáveis chave e ponto final para o topo do ficheiro de código para a chave Esta do seu recurso E ponto final. 

[!code-javascript[Add Personalizer resource information](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Criar um cliente personalizante

Em seguida, crie um método para devolver um cliente Personalizer. O parâmetro para o método é o `PERSONALIZER_RESOURCE_ENDPOINT` e o ApiKey é o `PERSONALIZER_RESOURCE_KEY` .

[!code-javascript[Create a Personalizer client](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Obtenha escolhas de conteúdo representadas como ações

As ações representam as escolhas de conteúdo a partir das quais deseja que o Personaler selecione o melhor item de conteúdo. Adicione os seguintes métodos à classe Programa para representar o conjunto de ações e as suas características.

[!code-javascript[Create user features](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>Criar o ciclo de aprendizagem

O ciclo de aprendizagem personalizer é um ciclo de chamadas [Rank](#request-the-best-action) and [Reward.](#send-a-reward) Neste quickstart, cada chamada Rank, para personalizar o conteúdo, é seguida por uma chamada Reward para dizer ao Personaler o quão bem o serviço foi executado.

O código que se segue passa por um ciclo de pedir ao utilizador as suas preferências na linha de comando, enviando essa informação ao Personaler para selecionar a melhor ação, apresentando a seleção ao cliente para escolher entre a lista, enviando depois uma recompensa ao Personaler sinalizando o quão bem o serviço se saiu na sua seleção.

[!code-javascript[Create the learning loop](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=mainLoop)]

Olhe mais de perto para o posto e recompense as chamadas nas seguintes secções.

Adicione os seguintes métodos, que [obtêm as escolhas de conteúdo,](#get-content-choices-represented-as-actions)antes de executar o ficheiro de código:

* getActionsList
* obterContextFeaturesList

## <a name="request-the-best-action"></a>Solicite a melhor ação

Para completar o pedido Rank, o programa pede às preferências do utilizador para criar escolhas de conteúdo. O processo pode criar conteúdo para excluir das ações, mostradas como `excludeActions` . O pedido rank precisa das [ações](../concepts-features.md#actions-represent-a-list-of-options) e suas funcionalidades, funcionalidades atuais doContexto, exclusões Deactions, e um ID de evento de classificação único, para receber a resposta classificada.

Este quickstart tem características de contexto simples de hora do dia e preferência alimentar do utilizador. Nos sistemas de produção, determinar e [avaliar](../concept-feature-evaluation.md) [ações e características](../concepts-features.md) pode ser uma questão não trivial.

[!code-javascript[The Personalizer learning loop ranks the request.](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=rank)]

## <a name="send-a-reward"></a>Enviar uma recompensa


Para obter a pontuação de recompensa para enviar o pedido de Recompensa, o programa obtém a seleção do utilizador da linha de comando, atribui um valor numérico à seleção, em seguida, envia o ID do evento único e a pontuação da recompensa como o valor numérico para a API Reward.

Este quickstart atribui um número simples como uma pontuação de recompensa, um zero ou um 1. Nos sistemas de produção, determinar quando e o que enviar para a chamada [Decompras](../concept-rewards.md) pode ser uma questão não trivial, dependendo das suas necessidades específicas.

[!code-javascript[The Personalizer learning loop sends a reward.](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=reward)]

## <a name="run-the-program"></a>Executar o programa

Execute o requerimento com o Node.js do seu diretório de candidaturas.

```console
node sample.js
```

![O programa quickstart faz algumas perguntas para recolher as preferências dos utilizadores, conhecidas como funcionalidades, e depois fornece a ação de topo.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
