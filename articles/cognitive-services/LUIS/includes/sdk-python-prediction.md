---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/28/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 8aeb6b964ab38a68a6d8681a4e5c93e1650c6a69
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84171290"
---
Utilize a biblioteca de clientes de previsão de compreensão linguística (LUIS) para:

* Obtenha previsão por slot
* Obtenha previsão por versão

[Documentação de referência](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis)  |  [Pacote de tempo de previsão (PyPi)](https://pypi.org/project/azure-cognitiveservices-language-luis/)  |  [Amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/LUIS)

## <a name="prerequisites"></a>Pré-requisitos

* Conta portal de compreensão de línguas (LUIS) - [Criar uma gratuitamente](https://www.luis.ai)
* [Python 3.x](https://www.python.org/)
* Um ID de aplicativo LUIS - use o ID da aplicação IoT pública de `df67dcdb-c37d-46af-88e1-8b97951ca1c2` . A consulta do utilizador utilizada no código quickstart é específica para essa aplicação.

## <a name="setting-up"></a>Configuração

### <a name="get-your-language-understanding-luis-runtime-key"></a>Obtenha a sua chave de tempo de execução (LUIS)

Obtenha a sua [chave de tempo de execução](../luis-how-to-azure-subscription.md) criando um recurso de tempo de execução LUIS. Guarde a chave e o ponto final da chave para o próximo passo.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-python-file"></a>Criar um novo ficheiro python

Crie um novo ficheiro python no seu editor preferido ou IDE, nomeado `prediction_quickstart.py` .

### <a name="install-the-sdk"></a>Instalar o SDK

Dentro do diretório de aplicações, instale a biblioteca de clientes de previsão de informação linguística (LUIS) para Python com o seguinte comando:

```python
python -m pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Modelo de objeto

O cliente de previsão de idioma (LUIS) é um objeto [LUISRuntimeClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-python) que autentica para Azure, que contém a sua chave de recursos.

Assim que o cliente for criado, utilize este cliente para aceder à funcionalidade, incluindo:

* Previsão por [encenação ou ranhura de produção](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)
* Previsão por [versão](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-version-prediction-app-id--version-id--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer o seguinte com a biblioteca de clientes de previsão de idioma (LUIS) para Python:

* [Previsão por slot](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Adicione as dependências

A partir do diretório do projeto, abra o `prediction_quickstart.py` arquivo no seu editor preferido ou IDE. Adicione as seguintes dependências:

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=Dependencies)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

1. Crie variáveis para a sua própria informação LUIS requerida:

    Adicione variáveis para gerir a sua chave de previsão puxada de uma variável ambiental chamada `LUIS_RUNTIME_KEY` . Se criou a variável ambiental após o lançamento da aplicação, o editor, o IDE ou o shell running terá de ser fechado e recarregado para aceder à variável. Os métodos serão criados mais tarde.

    Crie uma variável para manter o seu nome de `LUIS_RUNTIME_ENDPOINT` recurso.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=AuthorizationVariables)]

1. Crie uma variável para o ID da aplicação como uma variável ambiental chamada `LUIS_APP_ID` . Desaprote a variável ambiente para a aplicação IoT pública, **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** . Crie uma variável para definir a `production` ranhura publicada.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=OtherVariables)]


1. Crie um objeto de credenciais com a sua chave e use-o com o seu ponto final para criar um objeto [LUISRuntimeClientConfiguration]. https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclientconfiguration?view=azure-python()

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=Client)]

## <a name="get-prediction-from-runtime"></a>Obtenha a previsão do tempo de execução

Adicione o seguinte método para criar o pedido ao tempo de execução da previsão.

A expressão do utilizador faz parte do objeto [prediction_request.](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-python)

O método **[get_slot_prediction](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)** precisa de vários parâmetros, como o ID da aplicação, o nome da ranhura e o objeto de pedido de previsão para cumprir o pedido. As outras opções, como verbose, mostrar todas as intenções e log são opcionais. O pedido devolve um objeto [PredictionResponse.](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionresponse?view=azure-python)

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=predict)]

## <a name="main-code-for-the-prediction"></a>Código principal para a previsão

Utilize o seguinte método principal para ligar as variáveis e métodos para obter a previsão.

```python
predict(luisAppID, luisSlotName)
```
## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o `python prediction_quickstart.py` comando do seu diretório de candidaturas.

```console
python prediction_quickstart.py
```

A consola de arranque rápido exibe a saída:

```console
Top intent: HomeAutomation.TurnOn
Sentiment: None
Intents:
        "HomeAutomation.TurnOn"
Entities: {'HomeAutomation.Operation': ['on']}
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar as suas previsões, limpe o trabalho a partir deste arranque rápido, eliminando o ficheiro e as suas subdiretas.
