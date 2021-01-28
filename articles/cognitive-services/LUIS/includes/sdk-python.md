---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/01/2020
ms.topic: include
ms.custom: include file, cog-serv-seo-aug-2020
ms.openlocfilehash: b44fae4d2a4c8d0a60d751068e765932fed7e8fb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948205"
---
Utilize as bibliotecas de clientes de Compreensão linguística (LUIS) para:

* Criar uma aplicação
* Adicione uma intenção, uma entidade aprendida com máquinas, com um exemplo de expressão
* Treinar e publicar app
* Runtime de predição da consulta

[Documentação de referência](/python/api/azure-cognitiveservices-language-luis/index)  |  [Código fonte da](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis/authoring) Biblioteca de [Autoria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis/runtime) e Previsão | [Pacote (Pypi)](https://pypi.org/project/azure-cognitiveservices-language-luis/)  |  [Amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/sdk-3x/authoring_and_predict.py)

## <a name="prerequisites"></a>Pré-requisitos

* A versão atual do [Python 3.x](https://www.python.org/).
* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* Assim que tiver a sua subscrição Azure, [crie um recurso de autoria de Compreensão de Idiomas](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) no portal Azure para obter a sua chave e ponto final. Aguarde que seja implantado e clique no botão Go para o botão **de recursos.**
    * Necessitará da chave e ponto final do recurso que [criar](../luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) para ligar a sua aplicação à autoria de Compreensão linguística. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido. Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-python-application"></a>Criar uma aplicação Python nova

1. Numa janela de consola, crie um novo diretório para a sua aplicação e mude-se para esse diretório.

    ```console
    mkdir quickstart-sdk && cd quickstart-sdk
    ```

1. Crie um ficheiro com o `authoring_and_predict.py` nome do seu código Python.

    ```console
    touch authoring_and_predict.py
    ```

### <a name="install-the-client-library-with-pip"></a>Instale a biblioteca do cliente com o Pip

No diretório de aplicações, instale a biblioteca cliente de Compreensão linguística (LUIS) para python com o seguinte comando:

```console
pip install azure-cognitiveservices-language-luis
```

## <a name="authoring-object-model"></a>Modelo de objeto de autoria

O cliente de autoria de Compreensão linguística (LUIS) é um objeto [LUISAuthoringClient](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient) que autentica a Azure, que contém a sua chave de autoria.

## <a name="code-examples-for-authoring"></a>Exemplos de código para autoria

Assim que o cliente for criado, utilize este cliente para aceder à funcionalidade, incluindo:

* Apps - [criar,](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations#add-application-create-object--custom-headers-none--raw-false----operation-config-) [excluir,](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations#delete-app-id--force-false--custom-headers-none--raw-false----operation-config-) [publicar](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-)
* Exemplo de declarações - [adicionar por lote](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-), excluir por [ID](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations#delete-app-id--version-id--example-id--custom-headers-none--raw-false----operation-config-)
* Features - gerir [listas de frases](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.featuresoperations#add-phrase-list-app-id--version-id--phraselist-create-object--custom-headers-none--raw-false----operation-config-)
* Modelo - gerir [intenções](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) e entidades
* Pattern - gerir [padrões](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.patternoperations#add-pattern-app-id--version-id--pattern-none--intent-none--custom-headers-none--raw-false----operation-config-)
* Train - [treine](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) a app e a sondagem para [o estado da formação](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-)
* [Versões](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.versionsoperations) - gerir com clone, exportar e eliminar


## <a name="prediction-object-model"></a>Modelo de objeto de previsão

O cliente de previsão de idioma (LUIS) é um objeto [LUISRuntimeClient](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclient) que autentica para Azure, que contém a sua chave de recursos.

## <a name="code-examples-for-prediction-runtime"></a>Exemplos de código para o tempo de execução da previsão

Assim que o cliente for criado, utilize este cliente para aceder à funcionalidade, incluindo:

* Previsão por [encenação ou ranhura de produção](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)
* Previsão por [versão](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations#get-version-prediction-app-id--version-id--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)

[!INCLUDE [Bookmark links to same article](sdk-code-examples.md)]

## <a name="add-the-dependencies"></a>Adicione as dependências

Adicione as bibliotecas do cliente ao ficheiro python.

[!code-python[Add python libraries to code file](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=Dependencies)]


## <a name="add-boilerplate-code"></a>Adicione o código da placa de caldeira

1. Adicione o `quickstart` método e a sua chamada. Este método contém a maior parte do código restante. Este método é chamado no final do ficheiro.

    ```python
    def quickstart():

        # add calls here, remember to indent properly

    quickstart()
    ```

1. Adicione o código restante no método de arranque rápido, salvo especificação em contrário.

## <a name="create-variables-for-the-app"></a>Criar variáveis para a app

Crie dois conjuntos de variáveis: o primeiro conjunto que alterar, o segundo conjunto sai à medida que aparecem na amostra de código. 

1. Crie variáveis para manter os nomes das chaves de autoria e dos recursos.

    [!code-python[Variables you need to change](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=VariablesYouChange)]

1. Crie variáveis para manter os seus pontos finais, nome de aplicação, versão e nome de intenção.

    [!code-python[Variables you don't need to change](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=VariablesYouDontNeedToChangeChange)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie um objeto [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) com a sua chave e use-o com o seu ponto final para criar um objeto [LUISAuthoringClient.](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient)

[!code-python[Authenticate the client](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Criar uma aplicação LUIS

Uma aplicação LUIS contém o modelo de processamento de linguagem natural (NLP), incluindo intenções, entidades e palavras de exemplo.

Crie um método de [adicionar](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations#add-application-create-object--custom-headers-none--raw-false----operation-config-) de um objeto [appsoperação](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations) para criar a aplicação. O nome e a cultura linguística são propriedades necessárias.

[!code-python[Create a LUIS app](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AuthoringCreateApplication)]


## <a name="create-intent-for-the-app"></a>Criar intenção para a app
O principal objeto no modelo de uma aplicação LUIS é a intenção. A intenção alinha-se com um agrupamento de _intenções_ de expressão de utilizador. Um utilizador pode fazer uma pergunta ou fazer uma declaração à procura de uma resposta _determinada de_ um bot (ou outra aplicação de cliente). Exemplos de intenções são reservar um voo, perguntar sobre o tempo numa cidade de destino, e perguntar sobre informações de contato para o atendimento ao cliente.

Use o método [model.add_intent](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) com o nome da intenção única e depois passe o ID da aplicação, o ID da versão e o novo nome de intenção.

O `intentName` valor é codificado em código rígido como `OrderPizzaIntent` parte das variáveis na secção Criar variáveis para a secção [de aplicações.](#create-variables-for-the-app)

[!code-python[Create intent for the app](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AddIntent)]

## <a name="create-entities-for-the-app"></a>Criar entidades para a app

Embora as entidades não sejam necessárias, encontram-se na maioria das aplicações. A entidade extrai informações da expressão do utilizador, necessárias para preencher a intenção do utilizador. Existem vários tipos de entidades [pré-construídas](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations#add-prebuilt-app-id--version-id--prebuilt-extractor-names--custom-headers-none--raw-false----operation-config-) e personalizadas, cada uma com os seus próprios modelos de objeto de transformação de dados (DTO).  As entidades pré-construídas comuns para adicionar à sua app incluem [número,](../luis-reference-prebuilt-number.md) [datatimeV2,](../luis-reference-prebuilt-datetimev2.md) [geografiaV2,](../luis-reference-prebuilt-geographyv2.md) [ordinal.](../luis-reference-prebuilt-ordinal.md)

É importante saber que as entidades não estão marcadas com uma intenção. Podem e geralmente aplicam-se a muitas intenções. Apenas as declarações do utilizador são marcadas para uma intenção específica e única.

Os métodos de criação para entidades fazem parte da classe [ModelOperations.](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations) Cada tipo de entidade tem o seu próprio modelo de objeto de transformação de dados (DTO).

O código de criação de entidades cria uma entidade de aprendizagem automática com subentidades e características aplicadas às `Quantity` subentidades.

:::image type="content" source="../media/quickstart-sdk/machine-learned-entity.png" alt-text="Screenshot parcial do portal mostrando a entidade criada, uma entidade de aprendizagem automática com subentidades e funcionalidades aplicadas às subentidades 'Quantidade'.":::

[!code-python[Create entities for the app](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AuthoringAddEntities)]

Coloque o seguinte método acima do `quickstart` método para encontrar o ID da sub-entidade de Quantidade, a fim de atribuir as funcionalidades a essa sub-entidade.

[!code-python[Find subentity id](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AuthoringSortModelObject)]

## <a name="add-example-utterance-to-intent"></a>Adicione o exemplo da intenção

Para determinar a intenção de uma expressão e extrair entidades, a app precisa de exemplos de expressões. Os exemplos precisam de visar uma intenção específica e única e devem marcar todas as entidades personalizadas. As entidades pré-construídas não precisam de ser marcadas.

Adicione palavras de exemplo criando uma lista de objetos [ExemploLabelObject,](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.examplelabelobject) um objeto para cada palavra de exemplo. Cada exemplo deve marcar todas as entidades com um dicionário de nome/valor pares de nomes e valor de entidade. O valor da entidade deve ser exatamente como aparece no texto da expressão de exemplo.

:::image type="content" source="../media/quickstart-sdk/labeled-example-machine-learned-entity.png" alt-text="Screenshot parcial mostrando a expressão de exemplo rotulada no portal. ":::

Chame [exemplos.adicione](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations) com o ID da aplicação, iD da versão e o exemplo.

[!code-python[Add example utterance to intent](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AuthoringAddLabeledExamples)]

## <a name="train-the-app"></a>Preparar a aplicação

Uma vez criado o modelo, a app LUIS precisa de ser treinada para esta versão do modelo. Um modelo treinado pode ser utilizado num [recipiente,](../luis-container-howto.md)ou [publicado](../luis-how-to-publish-app.md) nas ranhuras de encenação ou do produto.

O [método train.train_version](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) precisa do ID da aplicação e do ID da versão.

Um modelo muito pequeno, como este quickstart shows, vai treinar muito rapidamente. Para aplicações de nível de produção, a formação da app deve incluir uma chamada de sondagem para o método [get_status](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) para determinar quando ou se a formação foi bem sucedida. A resposta é uma lista de objetos [ModelTrainingInfo](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo) com um estado separado para cada objeto. Todos os objetos devem ser bem sucedidos para que o treino seja considerado completo.

[!code-python[Train the app](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=TrainAppVersion)]

## <a name="publish-app-to-production-slot"></a>Publicar app para slot de produção

Publique a aplicação LUIS utilizando o método [app.publish.](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-) Isto publica a versão treinada atual para a ranhura especificada no ponto final. A sua aplicação de cliente utiliza este ponto final para enviar declarações de utilizador para previsão de intenção e extração de entidades.

[!code-python[Publish app to production slot](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=PublishVersion)]

## <a name="authenticate-the-prediction-runtime-client"></a>Autenticar o cliente de tempo de execução da previsão

Use o objeto de credenciais com a sua chave e use-o com o seu ponto final para criar um objeto [LUISRuntimeClientConfiguration.](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclientconfiguration)

[!INCLUDE [Caution about using authoring key](caution-authoring-key.md)]

[!code-python[Authenticate the prediction runtime client](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=PredictionCreateClient)]

## <a name="get-prediction-from-runtime"></a>Obtenha a previsão do tempo de execução

Adicione o seguinte código para criar o pedido ao tempo de execução da previsão.

A expressão do utilizador faz parte do objeto [prediction_request.](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionrequest)

O método **[get_slot_prediction](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)** precisa de vários parâmetros, como o ID da aplicação, o nome da ranhura e o objeto de pedido de previsão para cumprir o pedido. As outras opções, como verbose, mostrar todas as intenções e log são opcionais. O pedido devolve um objeto [PredictionResponse.](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionresponse)

[!code-python[Get prediction from runtime](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=QueryPredictionEndpoint)]

[!INCLUDE [Prediction JSON response](sdk-json.md)]

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o `python` comando no seu ficheiro quickstart.

```console
python authoring_and_predict.py
```
