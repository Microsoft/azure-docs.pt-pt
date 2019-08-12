---
title: 'Início rápido: Biblioteca de cliente personalizado para Python | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Introdução à biblioteca de cliente do personalizador para Python usando um loop de aprendizado.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 08/09/2019
ms.author: diberry
ms.openlocfilehash: ca1478801ad704888266175a23b6f436d067dd10
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950794"
---
# <a name="quickstart-personalize-client-library-for-python"></a>Início rápido: Personalizar a biblioteca de cliente para Python

Exiba o conteúdo personalizado neste guia de início rápido do Python com o serviço personalizado.

Introdução à biblioteca de cliente do personalizador para Python. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas.

 * Classifique uma lista de ações para personalização.
 * Relatório de Pontuação de recompensa indicando o sucesso da ação de classificação mais alta.

[Amostras de pacote (PyPI)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | [](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* [Python 3. x](https://www.python.org/)

## <a name="setting-up"></a>Configurando

### <a name="create-a-personalizer-azure-resource"></a>Criar um recurso personalizado do Azure

Os serviços cognitivas do Azure são representados pelos recursos do Azure que você assina. Crie um recurso para personalizador usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no computador local. Também pode:

* Obtenha uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services) válida por 7 dias gratuitamente. Depois de se inscrever, ele estará disponível no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Exiba seu recurso no [portal do Azure](https://portal.azure.com/).

Depois de obter uma chave de sua assinatura ou recurso de avaliação, crie duas [variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_KEY`para a chave de recurso.
* `PERSONALIZER_ENDPOINT`para o ponto de extremidade do recurso.

### <a name="install-the-python-library-for-personalizer"></a>Instalar a biblioteca do Python para personalizador

Instale a biblioteca de cliente do personalizador para Python com o seguinte comando:

```console
pip install azure-cognitiveservices-personalizer
```

Se você estiver usando o IDE do Visual Studio, a biblioteca de cliente estará disponível como um pacote NuGet baixável.

### <a name="change-the-model-update-frequency"></a>Alterar a frequência de atualização do modelo

No recurso personalizado no portal do Azure, altere a **frequência de atualização do modelo** para 10 segundos. Isso treinará o serviço rapidamente, permitindo que você veja como as principais ações são alteradas para cada iteração.

![Alterar a frequência de atualização do modelo](./media/settings/configure-model-update-frequency-settings.png)

Quando um loop do personalizador é instanciado pela primeira vez, não há nenhum modelo, pois não há chamadas de API de recompensa para treinar. Chamadas de classificação retornarão probabilidades iguais para cada item. Seu aplicativo ainda deve sempre classificar o conteúdo usando a saída de RewardActionId.

## <a name="object-model"></a>Modelo de objeto

O cliente do personalizador é um objeto PersonalizerClient que se autentica no Azure usando Microsoft. REST. createclientcredentials, que contém sua chave.

Para solicitar uma classificação do conteúdo, crie um RankRequest e, em seguida, passe-o para o cliente. Método de classificação. O método Rank retorna um RankResponse, que contém o conteúdo classificado. 

Para enviar um recompensa ao personalizador, crie um RewardRequest e, em seguida, passe-o para o cliente. Método de recompensa. 

A determinação da recompensa, neste guia de início rápido, é trivial. Em um sistema de produção, a determinação do que afeta a [Pontuação de recompensa](concept-rewards.md) e o quanto pode ser um processo complexo, que você pode decidir alterar ao longo do tempo. Essa deve ser uma das principais decisões de design na arquitetura do personalizador. 

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como fazer o seguinte com a biblioteca de cliente do personalizador para Python:

* [Criar um cliente personalizado](#create-a-personalizer-client)
* [Solicitar uma classificação](#request-a-rank)
* [Enviar uma recompensa](#send-a-reward)

## <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

Crie um novo aplicativo Python no editor preferencial ou no IDE chamado `sample.py`. 

## <a name="add-the-dependencies"></a>Adicionar as dependências

No diretório do projeto, abra o arquivo **Program.cs** no seu editor ou IDE preferido. Substitua o código `using` existente pelas seguintes `using` diretivas:

[!code-python[Add module dependencies](~/samples-personalizer/quickstarts/python/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Adicionar informações de recursos do personalizador

Na classe **programa** , crie variáveis para a chave do Azure do recurso e o ponto de extremidade extraídos das variáveis `PERSONALIZER_RESOURCE_KEY` de `PERSONALIZER_RESOURCE_ENDPOINT`ambiente, nomeadas e. Se você criou as variáveis de ambiente depois que o aplicativo é iniciado, o editor, IDE ou shell que o executa precisará ser fechado e recarregado para acessar a variável. Os métodos serão criados posteriormente neste guia de início rápido.

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/python/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Criar um cliente personalizado

Em seguida, crie um método para retornar um cliente personalizado. O parâmetro para o método é o `PERSONALIZER_RESOURCE_ENDPOINT` e o ApiKey é o `PERSONALIZER_RESOURCE_KEY`.

[!code-python[Create the Personalizer client](~/samples-personalizer/quickstarts/python/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Obter opções de conteúdo representadas como ações

As ações representam as opções de conteúdo que você deseja que o personalizado classifique. Adicione os seguintes métodos à classe Program para obter a entrada de um usuário da linha de comando para a hora do dia e a preferência de alimentos atual.

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>Criar o loop de aprendizagem

O loop de aprendizagem personalizador é um ciclo de chamadas de [classificação](#request-a-rank) e [recompensa](#send-a-reward) . Neste guia de início rápido, cada chamada de classificação, para personalizar o conteúdo, é seguida por uma chamada de recompensa para informar ao personalizado o quão bem o serviço classificou o conteúdo. 

O código a seguir no `main` método do programa executa um loop em um ciclo de solicitar ao usuário suas preferências na linha de comando, enviando essas informações ao personalizador para classificação, apresentando a seleção classificada ao cliente para escolher entre as e, em seguida, enviar um prêmio para o personalizado sinalizando o quão bem o serviço fez ao classificar a seleção.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=mainLoop&highlight=9,10,29)]

Examine de perto as chamadas de classificação e recompensa nas seções a seguir.

## <a name="request-a-rank"></a>Solicitar uma classificação

Para concluir a solicitação de classificação, o programa solicita as preferências do usuário para criar `currentContent` uma das opções de conteúdo. O processo pode criar conteúdo para excluir da classificação, mostrada como `excludeActions`. A solicitação de classificação precisa das ações, currentContext, excludeActions e uma ID de evento de classificação exclusiva (como um GUID) para receber a resposta classificada. 

Este guia de início rápido tem recursos de contexto simples de hora do dia e preferência de alimentos do usuário. Em sistemas de produção, determinar e [avaliar](concept-feature-evaluation.md) [ações e recursos](concepts-features.md) pode ser uma questão não trivial.  

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=rank)]

## <a name="send-a-reward"></a>Enviar uma recompensa

Para concluir a solicitação de recompensa, o programa obtém a seleção do usuário na linha de comando, atribui um valor numérico a cada seleção e, em seguida, envia a ID de evento de classificação exclusiva e o valor numérico para o método de recompensa.

Este início rápido atribui um número simples como um recompensa, um zero ou um 1. Em sistemas de produção, determinar quando e o que enviar para a chamada de [recompensa](concept-rewards.md) pode ser uma questão não trivial, dependendo de suas necessidades específicas. 

[!code-python[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/python/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>Execute o programa

Execute o aplicativo com o Python no diretório do aplicativo.

```console
python sample.py
```

![O programa de início rápido faz algumas perguntas para reunir as preferências do usuário, conhecidas como recursos, e fornece a ação principal.](./media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
>[Como o personalizador funciona](how-personalizer-works.md)

* [O que é personalizador?](what-is-personalizer.md)
* [Onde você pode usar o personalizador?](where-can-you-use-personalizer.md)
* [Resolução de problemas](troubleshooting.md)
* O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py).
