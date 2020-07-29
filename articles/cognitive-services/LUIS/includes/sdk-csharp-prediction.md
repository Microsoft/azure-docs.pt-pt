---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 07/28/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 4bf86c616420bb049e1d7a82ad0e942e6eb7b36f
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87369280"
---
Utilize a biblioteca de clientes de previsão de compreensão linguística (LUIS) para .NET para:

* Obtenha previsão por slot
* Previsão por Versão

[Documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime)  |  [Pacote de tempo de previsão (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)  |  [Amostras de C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/LanguageUnderstanding/predict-with-sdk-3x)

## <a name="prerequisites"></a>Pré-requisitos

* Conta portal de compreensão de línguas (LUIS) - [Criar uma gratuitamente](https://www.luis.ai)
* A versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Um ID de aplicativo LUIS - use o ID da aplicação IoT pública de `df67dcdb-c37d-46af-88e1-8b97951ca1c2` . A consulta do utilizador utilizada no código quickstart é específica para essa aplicação.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-c-application"></a>Criar uma nova aplicação C#

Crie uma nova aplicação .NET Core no seu editor preferido ou IDE.

1. Numa janela de consola (como cmd, PowerShell ou Bash), utilize o comando dotnet `new` para criar uma nova aplicação de consola com o nome `language-understanding-quickstart` . Este comando cria um projeto simples "Hello World" C# com um único ficheiro de origem: `Program.cs` .

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Mude o seu diretório para a pasta de aplicações recém-criada.

1. Pode construir a aplicação com:

    ```dotnetcli
    dotnet build
    ```

    A saída de construção não deve conter avisos ou erros.

    ```console
    ...
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ...
    ```

### <a name="install-the-sdk"></a>Instalar o SDK

No diretório de aplicações, instale a biblioteca de clientes de previsão de informação linguística (LUIS) para .NET com o seguinte comando:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

Se estiver a utilizar o Visual Studio IDE, a biblioteca do cliente está disponível como um pacote NuGet transferível.

## <a name="object-model"></a>Modelo de objeto

O cliente de previsão de idioma (LUIS) é um objeto [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) que autentica para Azure, que contém a sua chave de recursos.

Assim que o cliente for criado, utilize este cliente para aceder à funcionalidade, incluindo:

* Previsão por [encenação ou ranhura do produto](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet)
* Previsão por [versão](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet)


## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer o seguinte com a biblioteca de clientes de previsão de idioma (LUIS) para .NET:

* [Previsão por slot](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Adicione as dependências

A partir do diretório do projeto, abra o ficheiro *Program.cs* no seu editor preferido ou IDE. Substitua o código existente `using` pelas `using` seguintes diretivas:

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

1. Crie variáveis para a chave, nome de recurso, ID de aplicação e slot de publicação. Desaprova o ID da aplicação para a aplicação IoT pública:

    **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`**

    [!code-csharp[Create variables](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_variables)]

1. Crie um objeto [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) com a sua chave e use-o com o seu ponto final para criar um objeto [LUISRuntimeClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet)

    [!code-csharp[Create LUIS client object](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_create_client)]

## <a name="get-prediction-from-runtime"></a>Obtenha a previsão do tempo de execução

Adicione o seguinte método para criar o pedido ao tempo de execução da previsão.

A expressão do utilizador faz parte do objeto [PredictionRequest.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet)

O método **GetSlotPredictionAsync** necessita de vários parâmetros, tais como o ID da aplicação, o nome da slot, o objeto de pedido de previsão para cumprir o pedido. As outras opções, como verbose, mostrar todas as intenções e log são opcionais.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_maintask)]

## <a name="main-code-for-the-prediction"></a>Código principal para a previsão

Utilize o seguinte método principal para ligar as variáveis e métodos para obter a previsão.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_main)]

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o `dotnet run` comando do seu diretório de candidaturas.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar as suas previsões, limpe o trabalho a partir deste arranque rápido, eliminando o ficheiro program.cs e as suas subdiretivas.
