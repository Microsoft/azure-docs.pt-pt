---
title: 'Início rápido: Reconhecimento vocal (LUIS) biblioteca de cliente de criação para Python'
titleSuffix: Azure Cognitive Services
description: Introdução à biblioteca de cliente do LUIS para Python. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 08/07/2019
ms.author: diberry
ms.openlocfilehash: 1b586af569a9406d7fe9fa3d05c198f62f32744f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672009"
---
# <a name="quickstart-language-understanding-luis-authoring-client-library-for-python"></a>Início rápido: Reconhecimento vocal (LUIS) biblioteca de cliente de criação para Python

Introdução à biblioteca de cliente de criação de Reconhecimento vocal (LUIS) para Python. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas.  O Reconhecimento vocal (LUIS) permite que você aplique inteligência de aprendizado de máquina personalizada ao texto de linguagem natural de um usuário para prever o significado geral e extrair informações relevantes e detalhadas. 

Use a biblioteca de cliente de criação de Reconhecimento vocal (LUIS) para Python para:

* Crie um aplicativo.
* Adicione intenções, entidades e exemplos de declarações.
* Adicione recursos, como uma lista de frases.
* Treine e publique um aplicativo.

[Documentação de referência](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [PyPI (pacote de criação)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)

## <a name="prerequisites"></a>Pré-requisitos

* Conta do portal do Reconhecimento vocal (LUIS): [crie um gratuitamente](https://www.luis.ai).
* [Python 3. x](https://www.python.org/)

## <a name="setting-up"></a>Configurando

### <a name="get-your-language-understanding-luis-starter-key"></a>Obter sua chave de início do Reconhecimento vocal (LUIS)

Obtenha a [chave inicial](luis-how-to-azure-subscription.md#starter-key)e [crie uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave, denominada `LUIS_AUTHORING_KEY` e uma variável de ambiente para a região da chave, `LUIS_REGION`.

### <a name="install-the-python-library-for-luis"></a>Instalar a biblioteca do Python para LUIS

No diretório do aplicativo, instale a biblioteca de cliente de criação de Reconhecimento vocal (LUIS) para Python com o seguinte comando:

```console
pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Modelo de objeto

O cliente de criação de Reconhecimento vocal (LUIS) é um objeto [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) que se autentica no Azure, que contém a sua chave de criação.

Depois que o cliente for criado, use esse cliente para acessar a funcionalidade, incluindo:

* Aplicativos – [criar](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-), [excluir](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#delete-app-id--force-false--custom-headers-none--raw-false----operation-config-), [publicar](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-)
* Exemplo declarações- [Adicionar por lote](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-), [excluir por ID](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#delete-app-id--version-id--example-id--custom-headers-none--raw-false----operation-config-) 
* Recursos – Gerenciar [listas de frases](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.featuresoperations?view=azure-python#add-phrase-list-app-id--version-id--phraselist-create-object--custom-headers-none--raw-false----operation-config-) 
* Modelo – gerenciar [tentativas](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) e entidades
* Padrão-gerenciar [padrões](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.patternoperations?view=azure-python#add-pattern-app-id--version-id--pattern-none--intent-none--custom-headers-none--raw-false----operation-config-)
* Treinar- [treinar](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) o aplicativo e sondar o [status de treinamento](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-)
* [Versões](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.versionsoperations?view=azure-python) -gerenciar com clonagem, exportação e exclusão


## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como fazer o seguinte com a biblioteca de cliente de criação de Reconhecimento vocal (LUIS) para Python:

* [Criar uma aplicação](#create-a-luis-app)
* [Adicionar entidades](#create-entities-for-the-app)
* [Adicionar intenções](#create-intent-for-the-app)
* [Adicionar exemplo declarações](#add-example-utterance-to-intent)
* [Treinar o aplicativo](#train-the-app)
* [Publicar o aplicativo](#publish-a-language-understanding-app)

## <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

Crie um novo aplicativo Python em seu editor ou IDE preferido. Em seguida, importe as bibliotecas a seguir. 

[!code-python[Create a new Python application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Dependencies)]

Crie variáveis para o ponto de extremidade e a chave do Azure do recurso. Se você criou a variável de ambiente depois de iniciar o aplicativo, será necessário fechar e reabrir o editor, IDE ou shell que o executa para acessar a variável.

[!code-python[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=AuthorizationVariables)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie um objeto [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) com sua chave e use-o com seu ponto de extremidade para criar um objeto [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) .

[!code-python[Create LUIS client object](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Client)]

## <a name="create-a-luis-app"></a>Criar um aplicativo LUIS

1. Crie um aplicativo LUIS para conter o modelo de NLP (processamento de idioma natural) que contém tentativas, entidades e declarações de exemplo. 

1. Crie um método [Add](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-) do objeto [AppsOperation](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python) para criar o aplicativo. O nome e a cultura do idioma são propriedades obrigatórias. 

    [!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=createApp)]


## <a name="create-intent-for-the-app"></a>Criar intenção para o aplicativo
O objeto principal no modelo de um aplicativo LUIS é a intenção. A intenção se alinha com um agrupamento de _intenções_de expressão do usuário. Um usuário pode fazer uma pergunta ou fazer uma instrução procurando uma resposta _pretendida_ específica de um bot (ou outro aplicativo cliente). Exemplos de intenções estão reservando um vôo, perguntando o clima em uma cidade de destino e solicitando informações de contato para o atendimento ao cliente.   

Use o método [Model. add_intent](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) com o nome da intenção exclusiva e, em seguida, passe a ID do aplicativo, a ID da versão e o nome da nova tentativa. 

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addIntents)]

## <a name="create-entities-for-the-app"></a>Criar entidades para o aplicativo

Embora as entidades não sejam necessárias, elas são encontradas na maioria dos aplicativos. A entidade extrai informações do expressão do usuário, necessárias para fullfilr a intenção do usuário. Há vários tipos de entidades [predefinidas](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-prebuilt-app-id--version-id--prebuilt-extractor-names--custom-headers-none--raw-false----operation-config-) e personalizadas, cada uma com seus próprios modelos de DTO (objeto de transformação de dados).  Entidades predefinidas comuns para adicionar ao seu aplicativo incluem [Number](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), [geographyV2](luis-reference-prebuilt-geographyv2.md), [ordinal](luis-reference-prebuilt-ordinal.md). 

Esse método **add_entities** criou uma entidade `Location` simples com duas funções, uma `Class` entidade simples, uma entidade composta `Flight` e adiciona várias entidades predefinidas.

É importante saber que as entidades não estão marcadas com uma intenção. Em geral, eles podem se aplicar a muitas intenções. Somente os declarações de usuário de exemplo são marcados para uma única intenção específica.

Os métodos de criação para as entidades fazem parte da classe [ModelOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python) . Cada tipo de entidade tem seu próprio modelo de DTO (objeto de transformação de dados). 

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addEntities)]

## <a name="add-example-utterance-to-intent"></a>Adicionar expressão de exemplo à intenção

Para determinar a intenção e a extração de entidades de um expressão, o aplicativo precisa de exemplos de declarações. Os exemplos precisam ter como destino uma única intenção específica e devem marcar todas as entidades personalizadas. As entidades predefinidas não precisam ser marcadas. 

Adicione o exemplo declarações criando uma lista de objetos [ExampleLabelObject](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-python) , um objeto para cada exemplo expressão. Cada exemplo deve marcar todas as entidades com um dicionário de pares de nome/valor do nome da entidade e do valor da entidade. O valor da entidade deve ser exatamente como aparece no texto do exemplo expressão. 

Chame [Examples. batch](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-) com a ID do aplicativo, a ID da versão e a lista de exemplos. A chamada responde com uma lista de resultados. Você precisa verificar o resultado de cada exemplo para certificar-se de que ele foi adicionado com êxito ao modelo. 

[!code-python[Add example utterances via a batch](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addUtterances)]
    
## <a name="train-the-app"></a>Preparar a aplicação

Depois que o modelo é criado, o aplicativo LUIS precisa ser treinado para esta versão do modelo. Um modelo treinado pode ser usado em um [contêiner](luis-container-howto.md)ou [publicado](luis-how-to-publish-app.md) nos slots de preparo ou de produto. 

O método [Train. train_version](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) precisa da ID do aplicativo e da ID da versão. 

Um modelo muito pequeno, como este guia de início rápido, será treinado muito rapidamente. Para aplicativos de nível de produção, o treinamento do aplicativo deve incluir uma chamada de sondagem para o método [get_Status](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) para determinar quando ou se o treinamento foi bem-sucedido. A resposta é uma lista de objetos [ModelTrainingInfo](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-python) com um status separado para cada objeto. Todos os objetos devem ter êxito para que o treinamento seja considerado concluído.

[!code-python[Train LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=train)]

## <a name="publish-a-language-understanding-app"></a>Publicar um aplicativo Reconhecimento vocal

Publique o aplicativo LUIS usando o método [app. publish](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-) . Isso publica a versão treinada atual no slot especificado no ponto de extremidade. O aplicativo cliente usa esse ponto de extremidade para enviar declarações de usuário para previsão de extração de intenção e entidade.

[!code-python[Publish LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=publish)]

## <a name="run-the-application"></a>Executar a aplicação

Execute o aplicativo com o comando `python` em seu arquivo de início rápido.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources) 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Criar um aplicativo LUIS para determinar as intenções do usuário](luis-quickstart-intents-only.md)

* [O que é a API de Reconhecimento vocal (LUIS)?](what-is-luis.md)
* [Novidades](whats-new.md)
* [Intenções](luis-concept-intent.md), [entidades](luis-concept-entity-types.md)e [declarações de exemplo](luis-concept-utterance.md)e [entidades predefinidas](luis-reference-prebuilt-entities.md)
* O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py).
