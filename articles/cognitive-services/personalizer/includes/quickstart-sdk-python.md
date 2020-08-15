---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 07/30/2020
ms.openlocfilehash: aab4a59a35b098589adb462f2f0d6385802a9875
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2020
ms.locfileid: "88246202"
---
[Documentação de referência](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer?view=azure-python)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer)  |  [Pacote (pypi)](https://pypi.org/project/azure-cognitiveservices-personalizer/)  |  [Amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Personalizer)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)

## <a name="using-this-quickstart"></a>Usando este arranque rápido


Existem vários passos para usar este arranque rápido:

* No portal Azure, crie um recurso Personalizer
* No portal Azure, para o recurso Personalizer, na página **Configuração,** altere a frequência de atualização do modelo para um intervalo muito curto
* Num editor de código, crie um ficheiro de código e edite o ficheiro de código
* Na linha de comando ou terminal, instale o SDK a partir da linha de comando
* Na linha de comando ou terminal, execute o ficheiro de código

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

## <a name="install-the-python-library-for-personalizer"></a>Instale a biblioteca Python para Personalizar

Instale a biblioteca cliente Personalizer para Python com o seguinte comando:

```console
pip install azure-cognitiveservices-personalizer
```

## <a name="object-model"></a>Modelo de objeto

O cliente Personalizer é um objeto [PersonalizerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.personalizer_client.personalizerclient?view=azure-python) que autentica a Azure usando microsoft.Rest.ServiceClientCredentials, que contém a sua chave.

Para pedir o melhor item do conteúdo, crie um [RankRequest,](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.models.rankrequest?view=azure-python)em seguida, passe-o ao cliente. Método de classificação. O método Rank devolve um RankResponse.

Para enviar uma pontuação de recompensa para Personaler, desempece o ID do evento e a pontuação de recompensa (valor) para enviar para o método [Recompensa](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.operations.events_operations.eventsoperations?view=azure-python#reward-event-id--value--custom-headers-none--raw-false----operation-config-) na classe EventOperations.

Determinar a recompensa, neste arranque rápido é trivial. Num sistema de produção, a determinação do que impacta a [pontuação](../concept-rewards.md) da recompensa e o quanto pode ser um processo complexo, que poderá decidir mudar com o tempo. Esta deve ser uma das principais decisões de design na sua arquitetura Personalizer.

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer o seguinte com a biblioteca de clientes Personalizer para Python:

* [Criar um cliente personalizante](#create-a-personalizer-client)
* [Classificação API](#request-the-best-action)
* [Recompensa API](#send-a-reward)

## <a name="create-a-new-python-application"></a>Criar uma nova aplicação python

Crie uma nova aplicação Python no seu editor preferido ou IDE `sample.py` nomeado.

## <a name="add-the-dependencies"></a>Adicione as dependências

A partir do diretório do projeto, abra o ficheiro **sample.py** no seu editor preferido ou IDE. Adicione o seguinte:

[!code-python[Add module dependencies](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Adicionar informações de recurso personalizador

Edite as variáveis chave e ponto final para o topo do ficheiro de código para a chave Esta do seu recurso E ponto final. 

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Criar um cliente personalizante

Em seguida, crie um método para devolver um cliente Personalizer. O parâmetro para o método é o `PERSONALIZER_RESOURCE_ENDPOINT` e o ApiKey é o `PERSONALIZER_RESOURCE_KEY` .

[!code-python[Create the Personalizer client](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Obtenha escolhas de conteúdo representadas como ações

As ações representam as escolhas de conteúdo a partir das quais deseja que o Personaler selecione o melhor item de conteúdo. Adicione os seguintes métodos à classe Programa para representar o conjunto de ações e as suas características.

[!code-python[Present time out day preference to the user](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>Criar o ciclo de aprendizagem

O ciclo de aprendizagem personalizer é um ciclo de chamadas [Rank](#request-the-best-action) and [Reward.](#send-a-reward) Neste arranque rápido, cada chamada de classificação, para personalizar o conteúdo, é seguida de uma chamada de recompensa para dizer ao Personaler o quão bem o serviço foi executado.

O código que se segue passa por um ciclo de pedir ao utilizador as suas preferências na linha de comando, enviando essa informação ao Personaler para selecionar a melhor ação, apresentando a seleção ao cliente para escolher entre a lista, enviando depois uma recompensa ao Personaler sinalizando o quão bem o serviço se saiu na sua seleção.

[!code-python[The Personalizer learning loop ranks the request.](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=mainLoop&highlight=9,10,29)]

Adicione os seguintes métodos, que [obtêm as escolhas de conteúdo,](#get-content-choices-represented-as-actions)antes de executar o ficheiro de código:

* `get_user_preference`
* `get_user_timeofday`
* `get_actions`

## <a name="request-the-best-action"></a>Solicite a melhor ação


Para completar o pedido rank, o programa pede às preferências do utilizador para criar uma `currentContent` das escolhas de conteúdo. O processo pode criar conteúdo para excluir das ações, mostradas como `excludeActions` . O pedido rank precisa das ações e suas funcionalidades, funcionalidades atualescontexto, exclusões Deacções, e um ID de evento único, para receber a resposta.

Este quickstart tem características de contexto simples de hora do dia e preferência alimentar do utilizador. Nos sistemas de produção, determinar e [avaliar](../concept-feature-evaluation.md) [ações e características](../concepts-features.md) pode ser uma questão não trivial.

[!code-python[The Personalizer learning loop ranks the request.](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=rank)]

## <a name="send-a-reward"></a>Enviar uma recompensa


Para obter a pontuação de recompensa para enviar o pedido de Recompensa, o programa obtém a seleção do utilizador da linha de comando, atribui um valor numérico à seleção, em seguida, envia o ID do evento único e a pontuação da recompensa como o valor numérico para a API Reward.

Este quickstart atribui um número simples como uma pontuação de recompensa, um zero ou um 1. Nos sistemas de produção, determinar quando e o que enviar para a chamada [Decompras](../concept-rewards.md) pode ser uma questão não trivial, dependendo das suas necessidades específicas.

[!code-python[The Personalizer learning loop sends a reward.](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>Execute o programa

Execute a aplicação com a pitão do seu diretório de candidaturas.

```console
python sample.py
```

![O programa quickstart faz algumas perguntas para recolher as preferências dos utilizadores, conhecidas como funcionalidades, e depois fornece a ação de topo.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)