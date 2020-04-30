---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 04/27/2020
ms.author: diberry
ms.openlocfilehash: 237ba5ba390b4065a67f29611fbd43375c239578
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188884"
---
[Documentação de referência](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest) |[Biblioteca Código fonte](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [Pacote (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | [Amostras](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual de [Node.js](https://nodejs.org) e NPM.

## <a name="using-this-quickstart"></a>Usando este arranque rápido


Há vários passos para usar este arranque rápido:

* No portal Azure, crie um recurso Personalizer
* No portal Azure, para o recurso Personalizer, na página **de Configuração,** altere a frequência de atualização do modelo para um intervalo muito curto
* Num editor de código, crie um ficheiro de código e edite o ficheiro de código
* Na linha de comando ou terminal, instale o SDK a partir da linha de comando
* Na linha de comando ou terminal, executar o ficheiro de código

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue para ela.

```console
mkdir myapp && cd myapp
```

Executar `npm init -y` o comando `package.json` para criar um ficheiro.

```console
npm init -y
```

## <a name="install-the-nodejs-library-for-personalizer"></a>Instale a biblioteca Node.js para Personalizar

Instale a biblioteca de clientes Personalizer para Node.js com o seguinte comando:

```console
npm install @azure/cognitiveservices-personalizer --save
```

Instale os restantes pacotes NPM para este arranque rápido:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="object-model"></a>Modelo de objeto

O cliente Personalizer é um objeto [PersonalizerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest) que autentica o Azure usando o Microsoft.Rest.ServiceClientCredentials, que contém a sua chave.

Para pedir o melhor item do conteúdo, crie um [RankRequest,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rankrequest?view=azure-node-latest)em seguida, passe-o para o [cliente. Método de classificação.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest#rank-rankrequest--msrest-requestoptionsbase-) O método Rank devolve uma RankResponse.

Para enviar uma recompensa ao Personalizer, crie um [RewardRequest,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rewardrequest?view=azure-node-latest)em seguida, passe-o para o método [Reward](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/events?view=azure-node-latest#reward-string--rewardrequest--servicecallback-void--) na classe Eventos.

Determinar a recompensa, neste arranque rápido é trivial. Num sistema produtivo, a determinação do que impacta a [pontuação](../concept-rewards.md) da recompensa e pelo quanto pode ser um processo complexo, que pode decidir mudar ao longo do tempo. Esta deve ser uma das principais decisões de design na sua arquitetura Personalizer.

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer o seguinte com a biblioteca de clientes Personalizer para Node.js:

* [Criar um cliente Personalizer](#create-a-personalizer-client)
* [Classificação API](#request-the-best-action)
* [API recompensa](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Crie uma nova aplicação Node.js no `sample.js`seu editor preferido ou IDE nomeado.

## <a name="add-the-dependencies"></a>Adicione as dependências

Abra o ficheiro **sample.js** no seu editor preferido ou IDE. Adicione o `requires` seguinte para adicionar os pacotes NPM:

[!code-javascript[Add module dependencies](~/samples-personalizer/quickstarts/node/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Adicionar informações de recursos personalizantes

Crie variáveis para a chave Azure do seu recurso e `PERSONALIZER_KEY` ponto `PERSONALIZER_ENDPOINT`final retirado das variáveis ambientais, nomeadas e . Se criou as variáveis ambientais após o lançamento da aplicação, o editor, IDE ou shell running terá de ser fechado e recarregado para aceder à variável. Os métodos serão criados mais tarde neste arranque rápido.

[!code-javascript[Add Personalizer resource information](~/samples-personalizer/quickstarts/node/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Criar um cliente Personalizer

Em seguida, crie um método para devolver um cliente Personalizer. O parâmetro para o `PERSONALIZER_RESOURCE_ENDPOINT` método é o e `PERSONALIZER_RESOURCE_KEY`o ApiKey é o .

[!code-javascript[Create a Personalizer client](~/samples-personalizer/quickstarts/node/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Obtenha escolhas de conteúdo representadas como ações

As ações representam as escolhas de conteúdo a partir das quais pretende que o Personalizer selecione o melhor item de conteúdo. Adicione os seguintes métodos à classe Programa para representar o conjunto de ações e suas funcionalidades.

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>Criar o ciclo de aprendizagem

O ciclo de aprendizagem personalizer é um ciclo de chamadas [De Rank](#request-the-best-action) e [Reward.](#send-a-reward) Neste arranque rápido, cada chamada de Rank, para personalizar o conteúdo, é seguida por uma chamada de Recompensa para dizer ao Personalizer o quão bem o serviço foi executado.

O código seguinte passa por um ciclo de pedir ao utilizador as suas preferências na linha de comando, enviando essa informação ao Personalizer para selecionar a melhor ação, apresentando a seleção ao cliente para escolher entre a lista, enviando depois uma recompensa ao Personalizer sinalizando o quão bem o serviço fez na sua seleção.

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

Veja mais de perto as chamadas de classificação e recompensa nas seguintes secções.

Adicione os seguintes métodos, que [obtêm as escolhas de conteúdo,](#get-content-choices-represented-as-actions)antes de executar o ficheiro de código:

* getActionsList
* getContextFeaturesList

## <a name="request-the-best-action"></a>Solicite a melhor ação

Para completar o pedido de Rank, o programa pede às preferências do utilizador para criar escolhas de conteúdo. O processo pode criar conteúdo para excluir `excludeActions`das ações, mostradas como . O pedido de Rank necessita das [ações](../concepts-features.md#actions-represent-a-list-of-options) e suas funcionalidades, funcionalidades atuais Contexto, exclusãoDeAções e um ID de evento de classificação único, para receber a resposta classificada.

Este quickstart tem características de contexto simples de hora do dia e preferência alimentar do utilizador. Nos sistemas de produção, determinar e [avaliar](../concept-feature-evaluation.md) [ações e funcionalidades](../concepts-features.md) pode ser uma questão não trivial.

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>Enviar uma recompensa


Para obter a pontuação de recompensa para enviar o pedido de Recompensa, o programa obtém a seleção do utilizador da linha de comando, atribui um valor numérico à seleção, em seguida, envia o ID de evento único e a pontuação de recompensa como o valor numérico para a API recompensa.

Este quickstart atribui um número simples como uma pontuação de recompensa, um zero ou um 1. Nos sistemas de produção, determinar quando e o que enviar para a chamada [Reward](../concept-rewards.md) pode ser uma questão não trivial, dependendo das suas necessidades específicas.

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>Execute o programa

Execute a aplicação com o Node.js do seu diretório de candidaturas.

```console
node sample.js
```

![O programa quickstart faz algumas perguntas para reunir as preferências dos utilizadores, conhecidas como funcionalidades, e depois fornece a ação de topo.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
