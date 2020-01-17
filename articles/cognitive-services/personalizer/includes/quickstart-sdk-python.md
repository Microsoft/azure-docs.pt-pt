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
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122923"
---
[Documentação de referência](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer?view=azure-python) |  | [(PyPI)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | [amostras](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py) do [código-fonte de biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* [Python 3. x](https://www.python.org/)

## <a name="using-this-quickstart"></a>Usando este guia de início rápido


Há várias etapas para usar este guia de início rápido:

* No portal do Azure, criar um recurso personalizador
* Na portal do Azure, para o recurso personalizador, na página **configuração** , altere a frequência de atualização do modelo para um intervalo muito curto
* Em um editor de código, crie um arquivo de código e edite o arquivo de código
* Na linha de comando ou terminal, instale o SDK da linha de comando
* Na linha de comando ou no terminal, execute o arquivo de código

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="install-the-python-library-for-personalizer"></a>Instalar a biblioteca do Python para personalizador

Instale a biblioteca de cliente do personalizador para Python com o seguinte comando:

```console
pip install azure-cognitiveservices-personalizer
```

## <a name="object-model"></a>Modelo de objeto

O cliente do personalizador é um objeto [PersonalizerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.personalizer_client.personalizerclient?view=azure-python) que se autentica no Azure usando Microsoft. REST. createclientcredentials, que contém sua chave.

Para solicitar o melhor item do conteúdo, crie um [RankRequest](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.models.rankrequest?view=azure-python)e, em seguida, passe-o para o cliente. Método de classificação. O método Rank retorna um RankResponse.

Para enviar uma pontuação de recompensa para o personalizador, defina a ID do evento e a pontuação de recompensa (valor) para enviar para o método de [recompensa](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.operations.events_operations.eventsoperations?view=azure-python#reward-event-id--value--custom-headers-none--raw-false----operation-config-) na classe EventOperations.

A determinação da recompensa, neste guia de início rápido, é trivial. Em um sistema de produção, a determinação do que afeta a [Pontuação de recompensa](../concept-rewards.md) e o quanto pode ser um processo complexo, que você pode decidir alterar ao longo do tempo. Essa deve ser uma das principais decisões de design na arquitetura do personalizador.

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como fazer o seguinte com a biblioteca de cliente do personalizador para Python:

* [Criar um cliente personalizado](#create-a-personalizer-client)
* [API de classificação](#request-the-best-action)
* [A API de recompensa](#send-a-reward)

## <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

Crie um novo aplicativo Python em seu editor preferencial ou IDE chamado `sample.py`.

## <a name="add-the-dependencies"></a>Adicionar as dependências

No diretório do projeto, abra o arquivo **Sample.py** no seu editor ou IDE preferido. Adicione o seguinte:

[!code-python[Add module dependencies](~/samples-personalizer/quickstarts/python/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Adicionar informações de recursos do personalizador

Crie variáveis para a chave do Azure do recurso e o ponto de extremidade extraídos das variáveis de ambiente, chamadas `PERSONALIZER_RESOURCE_KEY` e `PERSONALIZER_RESOURCE_ENDPOINT`. Se você criou as variáveis de ambiente depois que o aplicativo é iniciado, o editor, IDE ou shell que o executa precisará ser fechado e recarregado para acessar a variável. Os métodos serão criados posteriormente neste guia de início rápido.

O nome do recurso faz parte da URL do ponto de extremidade: `https://<your-resource-name>.api.cognitive.microsoft.com/`.

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/python/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Criar um cliente personalizado

Em seguida, crie um método para retornar um cliente personalizado. O parâmetro para o método é o `PERSONALIZER_RESOURCE_ENDPOINT` e o ApiKey é o `PERSONALIZER_RESOURCE_KEY`.

[!code-python[Create the Personalizer client](~/samples-personalizer/quickstarts/python/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Obter opções de conteúdo representadas como ações

Ações representam as opções de conteúdo das quais você deseja que o personalizado selecione o melhor item de conteúdo. Adicione os métodos a seguir à classe Program para representar o conjunto de ações e seus recursos.

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>Criar o loop de aprendizagem

O loop de aprendizagem personalizador é um ciclo de chamadas de [classificação](#request-the-best-action) e [recompensa](#send-a-reward) . Neste guia de início rápido, cada chamada de classificação, para personalizar o conteúdo, é seguida por uma chamada de recompensa para informar ao personalizado o quão bem o serviço foi executado.

O código a seguir percorre um ciclo de solicitar ao usuário suas preferências na linha de comando, enviando essas informações ao personalizador para selecionar a melhor ação, apresentar a seleção ao cliente para escolher entre a lista e, em seguida, enviar um prêmio para Personalizar a sinalização do quão bem o serviço fez em sua seleção.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=mainLoop&highlight=9,10,29)]

Adicione os seguintes métodos, que [obtêm as opções de conteúdo](#get-content-choices-represented-as-actions), antes de executar o arquivo de código:

* `get_user_preference`
* `get_user_timeofday`
* `get_actions`

## <a name="request-the-best-action"></a>Solicitar a melhor ação


Para concluir a solicitação de classificação, o programa solicita as preferências do usuário para criar uma `currentContent` das opções de conteúdo. O processo pode criar conteúdo para excluir das ações, mostradas como `excludeActions`. A solicitação de classificação precisa das ações e seus recursos, recursos de currentContext, excludeActions e uma ID de evento exclusiva, para receber a resposta.

Este guia de início rápido tem recursos de contexto simples de hora do dia e preferência de alimentos do usuário. Em sistemas de produção, determinar e [avaliar](../concept-feature-evaluation.md) [ações e recursos](../concepts-features.md) pode ser uma questão não trivial.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=rank)]

## <a name="send-a-reward"></a>Enviar uma recompensa


Para obter a pontuação de recompensa a ser enviada na solicitação de recompensa, o programa obtém a seleção do usuário na linha de comando, atribui um valor numérico à seleção e, em seguida, envia a ID de evento exclusiva e a pontuação de recompensa como o valor numérico para a API de recompensa.

Este início rápido atribui um número simples como uma pontuação de recompensa, um zero ou um 1. Em sistemas de produção, determinar quando e o que enviar para a chamada de [recompensa](../concept-rewards.md) pode ser uma questão não trivial, dependendo de suas necessidades específicas.

[!code-python[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/python/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>Execute o programa

Execute o aplicativo com o Python no diretório do aplicativo.

```console
python sample.py
```

![O programa de início rápido faz algumas perguntas para reunir as preferências do usuário, conhecidas como recursos, e fornece a ação principal.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)