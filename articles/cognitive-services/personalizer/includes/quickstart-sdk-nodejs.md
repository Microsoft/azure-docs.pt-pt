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
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 786efcb712557da4363384c9d05c33f4f16d6731
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122881"
---
[Documentação de referência](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest) | | [(NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | [amostras](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js) do [código-fonte de biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [node. js](https://nodejs.org) e do NPM.

## <a name="using-this-quickstart"></a>Usando este guia de início rápido


Há várias etapas para usar este guia de início rápido:

* No portal do Azure, criar um recurso personalizador
* Na portal do Azure, para o recurso personalizador, na página **configuração** , altere a frequência de atualização do modelo para um intervalo muito curto
* Em um editor de código, crie um arquivo de código e edite o arquivo de código
* Na linha de comando ou terminal, instale o SDK da linha de comando
* Na linha de comando ou no terminal, execute o arquivo de código

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Em uma janela de console (como cmd, PowerShell ou bash), crie um novo diretório para seu aplicativo e navegue até ele.

```console
mkdir myapp && cd myapp
```

Execute o comando `npm init -y` para criar um arquivo de `package.json`.

```console
npm init -y
```

## <a name="install-the-nodejs-library-for-personalizer"></a>Instalar a biblioteca do node. js para personalizar

Instale a biblioteca de cliente do personalizador para node. js com o seguinte comando:

```console
npm install @azure/cognitiveservices-personalizer --save
```

Instale os pacotes de NPM restantes para este guia de início rápido:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="object-model"></a>Modelo de objeto

O cliente do personalizador é um objeto [PersonalizerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest) que se autentica no Azure usando Microsoft. REST. createclientcredentials, que contém sua chave.

Para solicitar o melhor item do conteúdo, crie um [RankRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rankrequest?view=azure-node-latest)e, em seguida, passe-o para o [cliente. ](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest#rank-rankrequest--msrest-requestoptionsbase-)Método de classificação. O método Rank retorna um RankResponse.

Para enviar um recompensa ao personalizador, crie um [RewardRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rewardrequest?view=azure-node-latest)e, em seguida, passe-o para o método [recompensa](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/events?view=azure-node-latest#reward-string--rewardrequest--servicecallback-void--) na classe Events.

A determinação da recompensa, neste guia de início rápido, é trivial. Em um sistema de produção, a determinação do que afeta a [Pontuação de recompensa](../concept-rewards.md) e o quanto pode ser um processo complexo, que você pode decidir alterar ao longo do tempo. Essa deve ser uma das principais decisões de design na arquitetura do personalizador.

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como fazer o seguinte com a biblioteca de cliente do personalizador para node. js:

* [Criar um cliente personalizado](#create-a-personalizer-client)
* [API de classificação](#request-the-best-action)
* [A API de recompensa](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Crie um novo aplicativo node. js em seu editor preferencial ou IDE chamado `sample.js`.

## <a name="add-the-dependencies"></a>Adicionar as dependências

Abra o arquivo **Sample. js** em seu editor ou IDE preferido. Adicione o seguinte `requires` para adicionar os pacotes NPM:

[!code-javascript[Add module dependencies](~/samples-personalizer/quickstarts/node/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Adicionar informações de recursos do personalizador

Crie variáveis para a chave do Azure do recurso e o ponto de extremidade extraídos das variáveis de ambiente, chamadas `PERSONALIZER_KEY` e `PERSONALIZER_ENDPOINT`. Se você criou as variáveis de ambiente depois que o aplicativo é iniciado, o editor, IDE ou shell que o executa precisará ser fechado e recarregado para acessar a variável. Os métodos serão criados posteriormente neste guia de início rápido.

[!code-javascript[Add Personalizer resource information](~/samples-personalizer/quickstarts/node/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Criar um cliente personalizado

Em seguida, crie um método para retornar um cliente personalizado. O parâmetro para o método é o `PERSONALIZER_RESOURCE_ENDPOINT` e o ApiKey é o `PERSONALIZER_RESOURCE_KEY`.

[!code-javascript[Create a Personalizer client](~/samples-personalizer/quickstarts/node/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Obter opções de conteúdo representadas como ações

Ações representam as opções de conteúdo das quais você deseja que o personalizado selecione o melhor item de conteúdo. Adicione os métodos a seguir à classe Program para representar o conjunto de ações e seus recursos.

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>Criar o loop de aprendizagem

O loop de aprendizagem personalizador é um ciclo de chamadas de [classificação](#request-the-best-action) e [recompensa](#send-a-reward) . Neste guia de início rápido, cada chamada de classificação, para personalizar o conteúdo, é seguida por uma chamada de recompensa para informar ao personalizado o quão bem o serviço foi executado.

O código a seguir percorre um ciclo de solicitar ao usuário suas preferências na linha de comando, enviando essas informações ao personalizador para selecionar a melhor ação, apresentar a seleção ao cliente para escolher entre a lista e, em seguida, enviar um prêmio para Personalizar a sinalização do quão bem o serviço fez em sua seleção.

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

Examine de perto as chamadas de classificação e recompensa nas seções a seguir.

Adicione os seguintes métodos, que [obtêm as opções de conteúdo](#get-content-choices-represented-as-actions), antes de executar o arquivo de código:

* getactionlist
* getContextFeaturesList

## <a name="request-the-best-action"></a>Solicitar a melhor ação

Para concluir a solicitação de classificação, o programa solicita as preferências do usuário para criar opções de conteúdo. O processo pode criar conteúdo para excluir das ações, mostradas como `excludeActions`. A solicitação de classificação precisa das [ações](../concepts-features.md#actions-represent-a-list-of-options) e seus recursos, recursos de CurrentContext, excludeActions e uma ID de evento de classificação exclusiva, para receber a resposta classificada.

Este guia de início rápido tem recursos de contexto simples de hora do dia e preferência de alimentos do usuário. Em sistemas de produção, determinar e [avaliar](../concept-feature-evaluation.md) [ações e recursos](../concepts-features.md) pode ser uma questão não trivial.

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>Enviar uma recompensa


Para obter a pontuação de recompensa a ser enviada na solicitação de recompensa, o programa obtém a seleção do usuário na linha de comando, atribui um valor numérico à seleção e, em seguida, envia a ID de evento exclusiva e a pontuação de recompensa como o valor numérico para a API de recompensa.

Este início rápido atribui um número simples como uma pontuação de recompensa, um zero ou um 1. Em sistemas de produção, determinar quando e o que enviar para a chamada de [recompensa](../concept-rewards.md) pode ser uma questão não trivial, dependendo de suas necessidades específicas.

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>Execute o programa

Execute o aplicativo com o Node. js do diretório do aplicativo.

```console
node sample.js
```

![O programa de início rápido faz algumas perguntas para reunir as preferências do usuário, conhecidas como recursos, e fornece a ação principal.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
