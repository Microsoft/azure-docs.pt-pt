---
title: 'Quickstart: Biblioteca de clientes do Reconhecimento de Formulários para .NET'
description: Comece com a biblioteca de clientes Do Reconhecimento de Formulários para .NET treinar, extrair, analisar e obter saída de dados estruturada com este arranque rápido.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 8d05f5ad07f9b3affba9442d6caa6e63162b813c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399409"
---
# <a name="quickstart-form-recognizer-client-library-for-net"></a>Quickstart: Biblioteca de clientes do Reconhecimento de Formulários para .NET

Inicie-se com a biblioteca de clientes Do Reconhecimento de Formulários para .NET. O Reconhecimento de Formulários é um Serviço Cognitivo que utiliza tecnologia de machine learning para identificar e extrair pares chave/valor e dados de tabela saem de documentos de formulário. Em seguida, gera a saída de dados estruturados, que inclui as relações no ficheiro original. Siga estes passos para instalar o pacote SDK e experimente o código de exemplo para tarefas básicas.

Utilize a biblioteca cliente Do Reconhecimento de Formulários para .NET para:

* [Treine um modelo personalizado de reconhecimento de formulários](#train-a-custom-model)
* [Obtenha uma lista de chaves extraídas](#get-a-list-of-extracted-keys)
* [Analisar formulários com um modelo personalizado](#analyze-forms-with-a-custom-model)
* [Obtenha uma lista de modelos personalizados](#get-a-list-of-custom-models)
* [Eliminar um modelo personalizado](#delete-a-custom-model)

[Documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/formrecognizer?view=azure-dotnet-preview) | [Pacote de código fonte](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/FormRecognizer) | da biblioteca[(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.FormRecognizer/)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Crie uma gratuitamente.](https://azure.microsoft.com/free/)
* Uma bolha de armazenamento azure que contém um conjunto de dados de treino. Consulte construir um conjunto de dados de [treino para um modelo personalizado](../build-training-data-set.md) para dicas e opções para reunir os seus dados de treino. Para este arranque rápido, pode utilizar os ficheiros sob a pasta **Train** do conjunto de dados da [amostra](https://go.microsoft.com/fwlink/?linkid=2090451).
* A versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Configuração

### <a name="create-a-form-recognizer-azure-resource"></a>Criar um recurso Do Reconhecimento de Formulários Azure

[!INCLUDE [create resource](../includes/create-resource.md)]

Depois de obter uma chave e ponto final, [crie variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave e ponto final, nomeado `FORM_RECOGNIZER_KEY` e, `FORM_RECOGNIZER_ENDPOINT`respectivamente.

### <a name="create-a-new-c-application"></a>Criar uma nova aplicação C#

Numa janela de consola (como cmd, PowerShell `dotnet new` ou Bash), utilize o comando `formrecognizer-quickstart`para criar uma nova aplicação de consola com o nome . Este comando cria um simples projeto C# "Hello World" com um único ficheiro fonte: _Program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
```

Mude o seu diretório para a pasta de aplicações recém-criada. Em seguida, construa a aplicação com:

```console
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

A partir do diretório do projeto, abra o ficheiro _Program.cs_ no seu editor ou IDE preferido. Adicione as seguintes instruções `using`:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_using)]

Em seguida, adicione o seguinte código no método **principal** da aplicação. Definirá esta tarefa assíncrona mais tarde.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_main)]

### <a name="install-the-client-library"></a>Instale a biblioteca do cliente

Dentro do diretório de aplicações, instale a biblioteca cliente Do Reconhecimento de Formulários para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.FormRecognizer --version 0.8.0-preview
```

Se estiver a usar o Visual Studio IDE, a biblioteca de clientes está disponível como um pacote NuGet transferível.

## <a name="object-model"></a>Modelo de objeto

As seguintes classes tratam da funcionalidade principal do SDK do Reconhecimento de Formulários.

|Nome|Descrição|
|---|---|
|[FormRecogniserClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.formrecognizerclient?view=azure-dotnet-preview)|Esta aula é necessária para todas as funcionalidades do Reconhecimento de Formulários. Você instantaneamente com a sua informação de subscrição, e você usa-o para produzir casos de outras classes.|
|[Pedido de Trem](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainrequest?view=azure-dotnet-preview)| Você usa esta classe para treinar um modelo personalizado de Reconhecimento de Formulário usando os seus próprios dados de entrada de treinamento. |
|[TrainResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainresult?view=azure-dotnet-preview)| Esta classe fornece os resultados de uma operação de comboio modelo personalizado, incluindo o ID do modelo, que pode então usar para analisar formulários. |
|[AnalisarResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)| Esta classe fornece os resultados de uma operação de análise de modelo personalizado. Inclui uma lista de instâncias **Extraídas.** |
|[Página extraída](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.extractedpage?view=azure-dotnet-preview)| Esta classe representa todos os dados extraídos de um único documento de formulário.|

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes Do Reconhecimento de Formulários para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Treine um modelo personalizado de reconhecimento de formulários](#train-a-custom-model)
* [Obtenha uma lista de chaves extraídas](#get-a-list-of-extracted-keys)
* [Analisar formulários com um modelo personalizado](#analyze-forms-with-a-custom-model)
* [Obtenha uma lista de modelos personalizados](#get-a-list-of-custom-models)
* [Eliminar um modelo personalizado](#delete-a-custom-model)

## <a name="define-variables"></a>Definir variáveis

Antes de definir quaisquer métodos, adicione as seguintes definições variáveis ao topo da sua classe **Program.** Você mesmo precisa preencher algumas das variáveis. 

* Para recuperar o URL SAS para os seus dados de treino, abra o Microsoft Azure Storage Explorer, clique no seu recipiente e selecione Obter assinatura de **acesso partilhado**. Certifique-se de que as permissões **de Leitura** e **Lista** são verificadas e clique em **Criar**. Em seguida, copie o valor na secção **URL.** Deve ter a `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`forma: .
* Se precisar de um formulário de amostra para analisar, pode utilizar um dos ficheiros sob a pasta **de Teste** do conjunto de dados da [amostra](https://go.microsoft.com/fwlink/?linkid=2090451). Este guia utiliza apenas formulários PDF.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_variables)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Abaixo `Main` do método, defina a `Main`tarefa que é referenciada em . Aqui, irá autenticar o objeto cliente utilizando as variáveis de subscrição que definiu acima. Definirá os outros métodos mais tarde.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_maintask)]

## <a name="train-a-custom-model"></a>Preparar um modelo personalizado

O método seguinte utiliza o seu objeto de cliente Do Reconhecimento de Formulários para formar um novo modelo de reconhecimento nos documentos armazenados no seu recipiente de blob Azure. Utiliza um método de ajuda para exibir informações sobre o modelo recém-treinado (representado por um objeto [ModelResult),](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelresult?view=azure-dotnet-preview) e devolve o ID do modelo.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_train)]

O método auxiliar seguinte exibe informações sobre um modelo de Reconhecimento de Formulários.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displaymodel)]

## <a name="get-a-list-of-extracted-keys"></a>Obtenha uma lista de chaves extraídas

Uma vez concluído o treino, o modelo personalizado manterá uma lista de chaves que extraiu dos documentos de treino. Espera que os documentos futuros contenham estas chaves, e extrairá os seus valores correspondentes na operação Analyze. Utilize o seguinte método para recuperar a lista das teclas extraídas e imprimi-la à consola. Esta é uma boa forma de verificar se o processo de treino foi eficaz.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getkeys)]

## <a name="analyze-forms-with-a-custom-model"></a>Analisar formulários com um modelo personalizado

Este método utiliza o cliente 'Reconhecimento de Formulários' e um ID de modelo para analisar um documento de formulário PDF e extrair dados chave/valor. Utiliza um método de ajuda para visualizar os resultados (representado por um objeto [AnalyzeResult).](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)

> [!NOTE]
> O método seguinte analisa um formulário PDF. Para métodos semelhantes que analisem os formulários JPEG e PNG, consulte o código de amostra completo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_analyzepdf)]

O método auxiliar seguinte apresenta informações sobre uma operação de Análise.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displayanalyze)]

## <a name="get-a-list-of-custom-models"></a>Obtenha uma lista de modelos personalizados

Pode devolver uma lista de todos os modelos treinados que pertencem à sua conta, e pode recuperar informações sobre quando foram criados. A lista de modelos é representada por um objeto [ModelsResult.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelsresult?view=azure-dotnet-preview)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getmodellist)]

## <a name="delete-a-custom-model"></a>Eliminar um modelo personalizado

Se pretender eliminar o modelo personalizado da sua conta, utilize o seguinte método:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_deletemodel)]

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação ligando para o `dotnet run` comando do seu diretório de aplicações.

```console
dotnet run
```

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos associados ao mesmo.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Além disso, se treinou um modelo personalizado que pretende eliminar da sua conta, execute o método em [Eliminar um modelo personalizado](#delete-a-custom-model).

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, usou a biblioteca de clientes Form Recogniser .NET para formar um modelo personalizado e analisar formulários. Em seguida, aprenda dicas para criar um melhor conjunto de dados de treino e produzir modelos mais precisos.

> [!div class="nextstepaction"]
> [Criar um conjunto de dados de preparação](../build-training-data-set.md)

* [O que é o Reconhecedor de Formato?](../overview.md)
* O código fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer).
