---
title: 'Início rápido: biblioteca de cliente do personalizador para node. js | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Introdução à biblioteca de cliente do personalizador para node. js usando um loop de aprendizado.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 09/26/2019
ms.author: diberry
ms.openlocfilehash: ee647668e8b5826706e8d9bb8a82acaf53fd3d8c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515201"
---
# <a name="quickstart-personalizer-client-library-for-nodejs"></a>Início rápido: biblioteca de cliente do personalizador para node. js

Exiba o conteúdo personalizado neste guia de início rápido do node. js com o serviço de personalização.

Introdução à biblioteca de cliente do personalizador para node. js. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas.

 * Classifique uma lista de ações para personalização.
 * Relatório de Pontuação de recompensa indicando o sucesso da ação de classificação mais alta.

[Amostras](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js) [de  |  do NPM (](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) [código-fonte](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer)  |  pacote)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [node. js](https://nodejs.org) e do NPM.

## <a name="using-this-quickstart"></a>Usando este guia de início rápido


Há várias etapas para usar este guia de início rápido:

* No portal do Azure, criar um recurso personalizador
* Na portal do Azure, para o recurso personalizador, na página **configurações** , altere a frequência de atualização do modelo
* Em um editor de código, crie um arquivo de código e edite o arquivo de código
* Na linha de comando ou terminal, instale o SDK da linha de comando
* Na linha de comando ou no terminal, execute o arquivo de código


## <a name="create-a-personalizer-azure-resource"></a>Criar um recurso personalizado do Azure

Os serviços cognitivas do Azure são representados pelos recursos do Azure que você assina. Crie um recurso para personalizador usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no computador local. Também pode:

* Obtenha uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services) válida por 7 dias gratuitamente. Depois de se inscrever, ele estará disponível no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Exiba seu recurso no [portal do Azure](https://portal.azure.com/).

Depois de obter uma chave de sua assinatura ou recurso de avaliação, crie duas [variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_KEY` para a chave de recurso.
* `PERSONALIZER_ENDPOINT` para o ponto de extremidade do recurso.

No portal do Azure, os valores de chave e ponto de extremidade estão disponíveis na página **início rápido** .


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

## <a name="change-the-model-update-frequency"></a>Alterar a frequência de atualização do modelo

No portal do Azure, no recurso personalizado na página **configurações** , altere a frequência de atualização do **modelo** para 10 segundos. Isso treinará o serviço rapidamente, permitindo que você veja como as principais ações são alteradas para cada iteração.

![Alterar a frequência de atualização do modelo](./media/settings/configure-model-update-frequency-settings.png)

Quando um loop do personalizador é instanciado pela primeira vez, não há nenhum modelo, pois não há chamadas de API de recompensa para treinar. Chamadas de classificação retornarão probabilidades iguais para cada item. Seu aplicativo ainda deve sempre classificar o conteúdo usando a saída de RewardActionId.

## <a name="object-model"></a>Modelo de objeto

O cliente do personalizador é um objeto PersonalizerClient que se autentica no Azure usando Microsoft. REST. createclientcredentials, que contém sua chave.

Para solicitar uma classificação do conteúdo, crie um RankRequest e, em seguida, passe-o para o cliente. Método de classificação. O método Rank retorna um RankResponse, que contém o conteúdo classificado. 

Para enviar um recompensa ao personalizador, crie um RewardRequest e, em seguida, passe-o para o cliente. Método de recompensa. 

A determinação da recompensa, neste guia de início rápido, é trivial. Em um sistema de produção, a determinação do que afeta a [Pontuação de recompensa](concept-rewards.md) e o quanto pode ser um processo complexo, que você pode decidir alterar ao longo do tempo. Essa deve ser uma das principais decisões de design na arquitetura do personalizador. 

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como fazer o seguinte com a biblioteca de cliente do personalizador para node. js:

* [Criar um cliente personalizado](#create-a-personalizer-client)
* [Solicitar uma classificação](#request-a-rank)
* [Enviar uma recompensa](#send-a-reward)

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

As ações representam as opções de conteúdo que você deseja que o personalizado classifique. Adicione os seguintes métodos à classe Program para obter a entrada de um usuário da linha de comando para a hora do dia e a preferência de alimentos atual.

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>Criar o loop de aprendizagem

O loop de aprendizagem personalizador é um ciclo de chamadas de [classificação](#request-a-rank) e [recompensa](#send-a-reward) . Neste guia de início rápido, cada chamada de classificação, para personalizar o conteúdo, é seguida por uma chamada de recompensa para informar ao personalizado o quão bem o serviço classificou o conteúdo. 

O código de loop a seguir percorre um ciclo de solicitar ao usuário suas preferências na linha de comando, enviando essas informações ao personalizado para classificação, apresentando a seleção classificada ao cliente para escolher entre a lista e, em seguida, enviar uma recompensa para Personalizar a sinalização do quão bem o serviço fez ao classificar a seleção.

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

Examine de perto as chamadas de classificação e recompensa nas seções a seguir.

Adicione os seguintes métodos, que [obtêm as opções de conteúdo](#get-content-choices-represented-as-actions), antes de executar o arquivo de código:

* getactionlist
* getContextFeaturesList

## <a name="request-a-rank"></a>Solicitar uma classificação

Para concluir a solicitação de classificação, o programa solicita as preferências do usuário para criar opções de conteúdo. O processo pode criar conteúdo para excluir da classificação, mostrada como `excludeActions`. A solicitação de classificação precisa das [ações](concepts-features.md#actions-represent-a-list-of-options), CurrentContext, excludeActions e uma ID de evento de classificação exclusiva (como um GUID) para receber a resposta classificada. 

Este guia de início rápido tem recursos de contexto simples de hora do dia e preferência de alimentos do usuário. Em sistemas de produção, determinar e [avaliar](concept-feature-evaluation.md) [ações e recursos](concepts-features.md) pode ser uma questão não trivial.  

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>Enviar uma recompensa

Para concluir a solicitação de recompensa, o programa obtém a seleção do usuário na linha de comando, atribui um valor numérico a cada seleção e, em seguida, envia a ID de evento de classificação exclusiva e o valor numérico para o método de recompensa.

Este início rápido atribui um número simples como um recompensa, um zero ou um 1. Em sistemas de produção, determinar quando e o que enviar para a chamada de [recompensa](concept-rewards.md) pode ser uma questão não trivial, dependendo de suas necessidades específicas. 

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>Execute o programa

Execute o aplicativo com o Node. js do diretório do aplicativo.

```console
node sample.js
```

![O programa de início rápido faz algumas perguntas para reunir as preferências do usuário, conhecidas como recursos, e fornece a ação principal.](./media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Como o personalizador funciona](how-personalizer-works.md)

* [O que é personalizador?](what-is-personalizer.md)
* [Onde você pode usar o personalizador?](where-can-you-use-personalizer.md)
* [Resolução de problemas](troubleshooting.md)
* O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js).
