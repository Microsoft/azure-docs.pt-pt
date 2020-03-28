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
ms.openlocfilehash: fe800280a7a652b5d9a397a21a5b0a66b40af4dc
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122923"
---
[Documentação de referência](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer?view=azure-python) | Pacote[de código fonte](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer) | da biblioteca[(pipi)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | [Samples](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="using-this-quickstart"></a>Usando este arranque rápido


Há vários passos para usar este arranque rápido:

* No portal Azure, crie um recurso Personalizer
* No portal Azure, para o recurso Personalizer, na página **de Configuração,** altere a frequência de atualização do modelo para um intervalo muito curto
* Num editor de código, crie um ficheiro de código e edite o ficheiro de código
* Na linha de comando ou terminal, instale o SDK a partir da linha de comando
* Na linha de comando ou terminal, executar o ficheiro de código

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="install-the-python-library-for-personalizer"></a>Instale a biblioteca Python para Personalizar

Instale a biblioteca cliente Personalizer para Python com o seguinte comando:

```console
pip install azure-cognitiveservices-personalizer
```

## <a name="object-model"></a>Modelo de objeto

O cliente Personalizer é um objeto [PersonalizerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.personalizer_client.personalizerclient?view=azure-python) que autentica o Azure usando o Microsoft.Rest.ServiceClientCredentials, que contém a sua chave.

Para pedir o melhor item do conteúdo, crie um [RankRequest,](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.models.rankrequest?view=azure-python)em seguida, passe-o para o cliente. Método de classificação. O método Rank devolve uma RankResponse.

Para enviar uma pontuação de recompensa ao Personalizer, detete o ID do evento e a pontuação de recompensa (valor) para enviar para o método [Reward](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.operations.events_operations.eventsoperations?view=azure-python#reward-event-id--value--custom-headers-none--raw-false----operation-config-) na classe EventOperations.

Determinar a recompensa, neste arranque rápido é trivial. Num sistema produtivo, a determinação do que impacta a [pontuação](../concept-rewards.md) da recompensa e pelo quanto pode ser um processo complexo, que pode decidir mudar ao longo do tempo. Esta deve ser uma das principais decisões de design na sua arquitetura Personalizer.

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer o seguinte com a biblioteca de clientes Personalizer para Python:

* [Criar um cliente Personalizer](#create-a-personalizer-client)
* [Classificação API](#request-the-best-action)
* [API recompensa](#send-a-reward)

## <a name="create-a-new-python-application"></a>Criar uma nova aplicação python

Crie uma nova aplicação Python no `sample.py`seu editor preferido ou IDE nomeado.

## <a name="add-the-dependencies"></a>Adicione as dependências

A partir do diretório do projeto, abra o ficheiro **sample.py** no seu editor ou IDE preferido. Adicione o seguinte:

[!code-python[Add module dependencies](~/samples-personalizer/quickstarts/python/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Adicionar informações de recursos personalizantes

Crie variáveis para a chave Azure do seu recurso e `PERSONALIZER_RESOURCE_KEY` ponto `PERSONALIZER_RESOURCE_ENDPOINT`final retirado das variáveis ambientais, nomeadas e . Se criou as variáveis ambientais após o lançamento da aplicação, o editor, IDE ou shell running terá de ser fechado e recarregado para aceder à variável. Os métodos serão criados mais tarde neste arranque rápido.

O nome do recurso faz parte `https://<your-resource-name>.api.cognitive.microsoft.com/`do URL do ponto final: .

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/python/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Criar um cliente Personalizer

Em seguida, crie um método para devolver um cliente Personalizer. O parâmetro para o `PERSONALIZER_RESOURCE_ENDPOINT` método é o e `PERSONALIZER_RESOURCE_KEY`o ApiKey é o .

[!code-python[Create the Personalizer client](~/samples-personalizer/quickstarts/python/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Obtenha escolhas de conteúdo representadas como ações

As ações representam as escolhas de conteúdo a partir das quais pretende que o Personalizer selecione o melhor item de conteúdo. Adicione os seguintes métodos à classe Programa para representar o conjunto de ações e suas funcionalidades.

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>Criar o ciclo de aprendizagem

O ciclo de aprendizagem personalizer é um ciclo de chamadas [De Rank](#request-the-best-action) e [Reward.](#send-a-reward) Neste arranque rápido, cada chamada de classificação, para personalizar o conteúdo, é seguida por uma chamada de recompensa para dizer ao Personalizer o quão bem o serviço foi executado.

O código seguinte passa por um ciclo de pedir ao utilizador as suas preferências na linha de comando, enviando essa informação ao Personalizer para selecionar a melhor ação, apresentando a seleção ao cliente para escolher entre a lista, enviando depois uma recompensa para Personalizer sinalizando como o serviço se saiu bem na sua seleção.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=mainLoop&highlight=9,10,29)]

Adicione os seguintes métodos, que [obtêm as escolhas de conteúdo,](#get-content-choices-represented-as-actions)antes de executar o ficheiro de código:

* `get_user_preference`
* `get_user_timeofday`
* `get_actions`

## <a name="request-the-best-action"></a>Solicite a melhor ação


Para completar o pedido de Rank, o programa pede `currentContent` às preferências do utilizador para criar uma das escolhas de conteúdo. O processo pode criar conteúdo para excluir `excludeActions`das ações, mostradas como . O pedido de Rank necessita das ações e suas funcionalidades, funcionalidades atuais Contexto, exclusão DeAções e um ID de evento único, para receber a resposta.

Este quickstart tem características de contexto simples de hora do dia e preferência alimentar do utilizador. Nos sistemas de produção, determinar e [avaliar](../concept-feature-evaluation.md) [ações e funcionalidades](../concepts-features.md) pode ser uma questão não trivial.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=rank)]

## <a name="send-a-reward"></a>Enviar uma recompensa


Para obter a pontuação de recompensa para enviar o pedido de Recompensa, o programa obtém a seleção do utilizador da linha de comando, atribui um valor numérico à seleção, em seguida, envia o ID de evento único e a pontuação de recompensa como o valor numérico para a API recompensa.

Este quickstart atribui um número simples como uma pontuação de recompensa, um zero ou um 1. Nos sistemas de produção, determinar quando e o que enviar para a chamada [Reward](../concept-rewards.md) pode ser uma questão não trivial, dependendo das suas necessidades específicas.

[!code-python[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/python/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>Execute o programa

Execute a aplicação com a pitão do seu diretório de candidaturas.

```console
python sample.py
```

![O programa quickstart faz algumas perguntas para reunir as preferências dos utilizadores, conhecidas como funcionalidades, e depois fornece a ação de topo.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)