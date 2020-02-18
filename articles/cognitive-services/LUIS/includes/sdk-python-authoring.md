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
ms.openlocfilehash: 631185c20b816191530158fab2b7cd1ed68c3092
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77372339"
---
Utilize a biblioteca de clientes autoras (LUIS) autorada da python para:

* Criar uma aplicação.
* Adicione intençãos, entidades e declarações de exemplo.
* Adicione funcionalidades, como uma lista de frases.
* Treine e publique uma aplicação.

[Documentação de referência](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [código fonte da Biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | Pacote de Autor [(Pypi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [Amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)

## <a name="prerequisites"></a>Pré-requisitos

* Conta do portal de Compreensão da Linguagem (LUIS): [Criar uma gratuitamente](https://www.luis.ai).
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Configuração

### <a name="get-your-language-understanding-luis-starter-key"></a>Obtenha a sua chave de arranque de Compreensão linguística (LUIS)

Obtenha a sua [chave de arranque](../luis-how-to-azure-subscription.md#starter-key) criando um recurso de autor LUIS. Mantenha a chave, e a região da chave para o próximo passo.

### <a name="create-an-environment-variable"></a>Criar uma variável ambiental

Utilizando a sua chave, e a região para a chave, crie duas variáveis ambientais para autenticação:

* `LUIS_AUTHORING_KEY` - A chave de recursos para autenticar os seus pedidos.
* `LUIS_REGION` - A região associada à sua chave. Por exemplo, `westus`.

Utilize as instruções para o seu sistema operativo.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_AUTHORING_KEY <replace-with-your-luis-authoring-key
setx LUIS_REGION <replace-with-your-luis-region>
```

Depois de adicionar a variável ambiente, reinicie a janela da consola.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_REGION=<replace-with-your-luis-region>
```

Depois de adicionar a variável de ambiente, execute `source ~/.bashrc` a partir da janela da consola para que as alterações entrem em vigor.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Edite o seu `.bash_profile`e adicione a variável ambiental:

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_REGION=<replace-with-your-luis-region>
```

Depois de adicionar a variável de ambiente, execute `source .bash_profile` a partir da janela da consola para que as alterações entrem em vigor.
***

### <a name="install-the-python-library-for-luis"></a>Instale a biblioteca Python para LUIS

No âmbito do diretório de aplicações, instale a biblioteca de clientes de autor de linguagem (LUIS) para python com o seguinte comando:

```console
pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Modelo de objeto

O cliente autor de Linguagem (LUIS) é um objeto [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) que autentica o Azure, que contém a sua chave de autoria.

Assim que o cliente for criado, utilize este cliente para aceder à funcionalidade, incluindo:

* Apps - [criar,](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-) [excluir,](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#delete-app-id--force-false--custom-headers-none--raw-false----operation-config-) [publicar](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-)
* Declarações de exemplo - [adicionar por lote](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-), excluir por [ID](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#delete-app-id--version-id--example-id--custom-headers-none--raw-false----operation-config-)
* Características - gerencie [listas de frases](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.featuresoperations?view=azure-python#add-phrase-list-app-id--version-id--phraselist-create-object--custom-headers-none--raw-false----operation-config-)
* Modelo - gerir intenções e [entidades](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-)
* Padrão - gerir [padrões](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.patternoperations?view=azure-python#add-pattern-app-id--version-id--pattern-none--intent-none--custom-headers-none--raw-false----operation-config-)
* Comboio - [treine](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) a app e a sondagem para [o estado de formação](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-)
* [Versões](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.versionsoperations?view=azure-python) - gerencie com clone, exporte e elimine


## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer o seguinte com a biblioteca de clientes de autor de linguagem (LUIS) para python:

* [Criar uma aplicação](#create-a-luis-app)
* [Adicionar entidades](#create-entities-for-the-app)
* [Adicionar intenções](#create-intent-for-the-app)
* [Adicione expressões de exemplo](#add-example-utterance-to-intent)
* [Treine a app](#train-the-app)
* [Publicar a app](#publish-a-language-understanding-app)

## <a name="create-a-new-python-application"></a>Criar uma nova aplicação python

Crie uma nova aplicação Python no seu editor preferido ou IDE. Em seguida, importar as seguintes bibliotecas.

[!code-python[Create a new Python application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Dependencies)]

Crie variáveis para o ponto final e chave azure do seu recurso. Se criou a variável ambiental depois de lançar a aplicação, terá de fechar e reabrir o editor, IDE, ou a shell executá-la para aceder à variável.

[!code-python[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=AuthorizationVariables)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie um objeto [CognitiveServicesCredenciais](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) com a sua chave e use-o com o seu ponto final para criar um objeto [LUISAuthoringClient.](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python)

[!code-python[Create LUIS client object](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Client)]

## <a name="create-a-luis-app"></a>Criar uma aplicação LUIS

1. Crie uma app LUIS para conter o modelo de processamento de linguagem natural (NLP) com intençãos, entidades e pronunciações de exemplo.

1. Crie um método de [adição](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-) de objeto [soperation apps](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python) para criar a aplicação. O nome e a cultura linguística são propriedades necessárias.

    [!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=createApp)]


## <a name="create-intent-for-the-app"></a>Criar intenção para a app
O principal objeto no modelo de uma app LUIS é a intenção. A intenção alinha-se com um agrupamento de _intenções_de expressão do utilizador . Um utilizador pode fazer uma pergunta ou fazer uma declaração à procura de uma resposta _específica de_ um bot (ou outra aplicação de cliente). Exemplos de intenções são reservar um voo, perguntar sobre o tempo em uma cidade de destino, e perguntar sobre informações de contato para o atendimento ao cliente.

Utilize o [método modelo.add_intent](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) com o nome da intenção única e, em seguida, passe o ID da aplicação, id versão e novo nome de intenção.

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addIntents)]

## <a name="create-entities-for-the-app"></a>Criar entidades para a app

Embora as entidades não sejam necessárias, encontram-se na maioria das aplicações. A entidade extrai informação da expressão do utilizador, necessária para plenar a intenção do utilizador. Existem vários tipos de entidades [pré-construídas](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-prebuilt-app-id--version-id--prebuilt-extractor-names--custom-headers-none--raw-false----operation-config-) e personalizadas, cada uma com os seus próprios modelos de objeto de transformação de dados (DTO).  Entidades pré-construídas comuns para adicionar à sua aplicação incluem [número](../luis-reference-prebuilt-number.md), [dataV2](../luis-reference-prebuilt-datetimev2.md), [geografiaV2](../luis-reference-prebuilt-geographyv2.md), [ordinal](../luis-reference-prebuilt-ordinal.md).

Este método **add_entities** criou uma entidade `Location` simples com duas funções, uma entidade `Class` simples, uma entidade `Flight` composta e acrescenta várias entidades pré-construídas.

É importante saber que as entidades não estão marcadas com uma intenção. Podem e geralmente aplicam-se a muitas intenções. Apenas as expressões dos utilizadores exemplo estão marcadas para uma intenção específica e única.

Os métodos de criação para entidades fazem parte da classe [ModelOperations.](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python) Cada tipo de entidade tem o seu próprio modelo de objeto de transformação de dados (DTO).

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addEntities)]

## <a name="add-example-utterance-to-intent"></a>Adicione a expressão do exemplo à intenção

Para determinar a intenção de uma expressão e extrair entidades, a app precisa de exemplos de expressões. Os exemplos precisam de visar uma intenção específica e única e devem marcar todas as entidades personalizadas. As entidades pré-construídas não precisam de ser marcadas.

Adicione expressões exemplo criando uma lista de objetos [ExampleLabelObject,](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-python) um objeto para cada expressão de exemplo. Cada exemplo deve marcar todas as entidades com um dicionário de nome/valor par de nomes e valor da entidade. O valor da entidade deve ser exatamente como aparece no texto da expressão do exemplo.

Ligue [exemplos.batch](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-) com o ID da aplicação, id da versão e a lista de exemplos. A chamada responde com uma lista de resultados. É necessário verificar o resultado de cada exemplo para se certificar de que foi adicionado com sucesso ao modelo.

[!code-python[Add example utterances via a batch](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addUtterances)]

## <a name="train-the-app"></a>Preparar a aplicação

Uma vez criado o modelo, a aplicação LUIS precisa de ser treinada para esta versão do modelo. Um modelo treinado pode ser utilizado num [recipiente,](../luis-container-howto.md)ou [publicado](../luis-how-to-publish-app.md) nas ranhuras de preparação ou produtos.

O método [train.train_version](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) precisa do ID da aplicação e do ID da versão.

Um modelo muito pequeno, como este quickstart mostra, vai treinar muito rapidamente. Para aplicações de nível de produção, a formação da app deve incluir uma chamada de votação para o método [get_status](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) para determinar quando ou se a formação foi bem sucedida. A resposta é uma lista de objetos [ModelTrainingInfo](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-python) com um estado separado para cada objeto. Todos os objetos devem ser bem sucedidos para que o treino seja considerado completo.

[!code-python[Train LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=train)]

## <a name="publish-a-language-understanding-app"></a>Publicar uma aplicação de compreensão de linguagem

Publique a aplicação LUIS utilizando o método [app.publish.](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-) Isto publica a versão treinada atual para a ranhura especificada no ponto final. A sua aplicação de cliente utiliza este ponto final para enviar declarações de utilizadores para previsão de intenção e extração de entidades.

[!code-python[Publish LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=publish)]

## <a name="run-the-application"></a>Executar a aplicação

Faça o pedido com o comando `python` no seu ficheiro de arranque rápido.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar as suas previsões, limpe o trabalho a partir deste arranque rápido, apagando o ficheiro e os seus subdiretórios.