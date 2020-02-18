---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: ff4c33aea3d3ce604f44c38e6e3856242388b0e9
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77372354"
---
Utilize a biblioteca de clientes de previsão de linguagem (LUIS) para python para:

* Obtenha previsão por ranhura
* Obtenha previsão por versão

[Documentação de referência](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [código de origem da Biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | Pacote de prazo de previsão [(PyPi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [Amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/LUIS)

## <a name="prerequisites"></a>Pré-requisitos

* Conta do portal De Compreensão da Linguagem (LUIS) - [Criar uma gratuitamente](https://www.luis.ai)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Configuração

### <a name="get-your-language-understanding-luis-runtime-key"></a>Obtenha a sua chave de tempo de execução da linguagem (LUIS)

Obtenha a sua chave de tempo de [execução](../luis-how-to-azure-subscription.md) criando um recurso de tempo de execução LUIS. Mantenha a chave e o ponto final da chave para o próximo passo.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-python-file"></a>Criar um novo ficheiro python

Crie um novo ficheiro python no seu editor preferido ou IDE, chamado `prediction_quickstart.py`.

### <a name="install-the-sdk"></a>Instalar o SDK

Dentro do diretório de aplicações, instale a biblioteca de clientes runtime de previsão de previsão de linguagem (LUIS) para Python com o seguinte comando:

```python
python -m pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Modelo de objeto

O cliente de previsão de previsão de linguagem (LUIS) é um objeto [LUISRuntimeClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-python) que autentica o Azure, que contém a sua chave de recursos.

Assim que o cliente for criado, utilize este cliente para aceder à funcionalidade, incluindo:

* Previsão por [encenação ou ranhura de produção](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)
* Previsão por [versão](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-version-prediction-app-id--version-id--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer o seguinte com a biblioteca de clientes de prazo de execução da previsão de linguagem (LUIS) para Python:

* [Previsão por ranhura](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Adicione as dependências

A partir do diretório do projeto, abra o ficheiro `prediction_quickstart.py` no seu editor ou IDE preferido. Adicione as seguintes dependências:

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Dependencies)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

1. Crie variáveis para a sua própria informação necessária do LUIS:

    Adicione variáveis para gerir a sua chave de previsão retirada de uma variável ambiental chamada `LUIS_RUNTIME_KEY`. Se criou a variável ambiental após o lançamento da aplicação, o editor, IDE ou shell running terá de ser fechado e recarregado para aceder à variável. Os métodos serão criados mais tarde.

    Crie uma variável para manter o seu nome de recurso `LUIS_RUNTIME_ENDPOINT`.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=AuthorizationVariables)]

1. Crie uma variável para o ID da aplicação como uma variável ambiental chamada `LUIS_APP_ID`. Desloque a variável ambiental para a aplicação ioT pública, **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** . Crie uma variável para definir a ranhura publicada `production`.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=OtherVariables)]


1. Crie um objeto de credenciais com a sua chave e use-o com o seu ponto final para criar um objeto https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclientconfiguration?view=azure-python() [LUISRuntimeClientConfiguration].

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Client)]

## <a name="get-prediction-from-runtime"></a>Obtenha previsão do tempo de execução

Adicione o seguinte método para criar o pedido ao tempo de execução da previsão.

A expressão do utilizador faz parte do objeto [prediction_request.](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-python)

O método **[get_slot_prediction](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)** precisa de vários parâmetros, tais como o ID da aplicação, o nome da ranhura e o objeto de pedido de previsão para cumprir o pedido. As outras opções, como verbosa, mostrar todas as intenções e registo são opcionais. O pedido devolve um objeto [De PrevisãoResponse.](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionresponse?view=azure-python)

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=predict)]

## <a name="main-code-for-the-prediction"></a>Código principal para a previsão

Utilize o seguinte método principal para ligar as variáveis e métodos para obter a previsão.

```python
predict(luisAppID, luisSlotName)
```
## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o comando `python prediction_quickstart.py` do seu diretório de aplicações.

```console
python prediction_quickstart.py
```

A consola quickstart apresenta a saída:

```console
Top intent: HomeAutomation.TurnOn
Sentiment: None
Intents:
        "HomeAutomation.TurnOn"
Entities: {'HomeAutomation.Operation': ['on']}
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar as suas previsões, limpe o trabalho a partir deste arranque rápido, apagando o ficheiro e os seus subdiretórios.
