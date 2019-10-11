---
title: 'Início rápido: biblioteca de cliente do reconhecedor de formulário para .NET | Microsoft Docs'
description: Introdução à biblioteca de cliente do reconhecedor de formulário para .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/12/2019
ms.author: pafarley
ms.openlocfilehash: fc0964508f3031efd91db827524042bf0577ab5e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242504"
---
# <a name="quickstart-form-recognizer-client-library-for-net"></a>Início rápido: biblioteca de cliente do reconhecedor de formulário para .NET

Introdução à biblioteca de cliente do reconhecedor de formulário para .NET. O reconhecedor de formulário é um serviço cognitiva que usa a tecnologia de aprendizado de máquina para identificar e extrair pares de chave/valor e dados de tabela de documentos de formulário. Em seguida, gera a saída de dados estruturados, que inclui as relações no ficheiro original. Siga estas etapas para instalar o pacote do SDK e experimentar o código de exemplo para tarefas básicas.

Use a biblioteca de cliente do formulário Recognizer para .NET para:

* [Treinar um modelo de reconhecedor de formulário personalizado](#train-a-custom-model)
* [Obter uma lista de chaves extraídas](#get-a-list-of-extracted-keys)
* [Analisar formulários com um modelo personalizado](#analyze-forms-with-a-custom-model)
* [Obter uma lista de modelos personalizados](#get-a-list-of-custom-models)
* [Excluir um modelo personalizado](#delete-a-custom-model)

[Documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/formrecognizer?view=azure-dotnet-preview) | [código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.FormRecognizer) | [pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.FormRecognizer/)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie um gratuitamente](https://azure.microsoft.com/free/).
* Acesso à visualização de acesso limitado do reconhecedor de formulário. Para obter acesso à visualização, preencha e envie o formulário [solicitação de acesso do reconhecedor de formulário](https://aka.ms/FormRecognizerRequestAccess) .
* Um blob de armazenamento do Azure que contém um conjunto de dados de treinamento. Consulte [criar um conjunto de dados de treinamento para um modelo personalizado](../build-training-data-set.md) para obter dicas e opções para reunir seus dados de treinamento. Você pode usar um [conjunto de dados de exemplo](https://go.microsoft.com/fwlink/?linkid=2090451) para este guia de início rápido.
* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Configurando

### <a name="create-a-form-recognizer-azure-resource"></a>Criar um recurso do reconhecedor de formulário do Azure

[!INCLUDE [create resource](../includes/create-resource.md)]

Depois de obter uma chave de sua assinatura ou recurso de avaliação, [crie uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave, denominada `FORM_RECOGNIZER_KEY`.

### <a name="create-a-new-c-application"></a>Criar um novo C# aplicativo

Em uma janela de console (como cmd, PowerShell ou bash), use o comando `dotnet new` para criar um novo aplicativo de console com o nome `formrecognizer-quickstart`. Este comando cria um projeto simples de " C# Olá, mundo" com um único arquivo de origem: _Program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
```

Altere o diretório para a pasta de aplicativos recém-criada. Em seguida, compile o aplicativo com:

```console
dotnet build
```

A saída da compilação não deve conter nenhum aviso ou erro. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

No diretório do projeto, abra o arquivo _Program.cs_ no seu editor ou IDE preferido. Adicione as seguintes instruções `using`:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_using)]

Em seguida, adicione o seguinte código ao método **Main** do aplicativo. Você definirá essa tarefa assíncrona posteriormente.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_main)]

### <a name="install-the-client-library"></a>Instalar a biblioteca de cliente

No diretório do aplicativo, instale a biblioteca de cliente do reconhecedor de formulário para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.FormRecognizer --version 0.8.0-preview
```

Se você estiver usando o IDE do Visual Studio, a biblioteca de cliente estará disponível como um pacote NuGet baixável.

## <a name="object-model"></a>Modelo de objeto

As classes a seguir tratam da funcionalidade principal do SDK do reconhecedor de formulário.

|Nome|Descrição|
|---|---|
|[FormRecognizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.formrecognizerclient?view=azure-dotnet-preview)|Essa classe é necessária para toda a funcionalidade do reconhecedor de formulário. Você a instancia com suas informações de assinatura e a usa para produzir instâncias de outras classes.|
|[TrainRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainrequest?view=azure-dotnet-preview)| Você usa essa classe para treinar um modelo de reconhecedor de formulário personalizado usando seus próprios dados de entrada de treinamento. |
|[TrainResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainresult?view=azure-dotnet-preview)| Essa classe fornece os resultados de uma operação de treinamento de modelo personalizado, incluindo a ID do modelo, que você pode usar para analisar formulários. |
|[AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)| Essa classe fornece os resultados de uma operação de análise de modelo personalizado. Ele inclui uma lista de instâncias de **ExtractedPage** . |
|[ExtractedPage](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.extractedpage?view=azure-dotnet-preview)| Essa classe representa todos os dados extraídos de um único documento de formulário.|

## <a name="code-examples"></a>Exemplos de código

<!--
    Include code snippets and short descriptions for each task you list in the the bulleted list. Briefly explain each operation, but include enough clarity to explain complex or otherwise tricky operations.

    Include links to the service's reference content when introducing a class for the first time
-->

Esses trechos de código mostram como realizar as seguintes tarefas com a biblioteca de cliente do reconhecedor de formulário para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Treinar um modelo de reconhecedor de formulário personalizado](#train-a-custom-model)
* [Obter uma lista de chaves extraídas](#get-a-list-of-extracted-keys)
* [Analisar formulários com um modelo personalizado](#analyze-forms-with-a-custom-model)
* [Obter uma lista de modelos personalizados](#get-a-list-of-custom-models)
* [Excluir um modelo personalizado](#delete-a-custom-model)

## <a name="define-variables"></a>Definir variáveis

Antes de definir qualquer método, adicione as definições de variável a seguir à parte superior da classe **programa** . Você precisará preencher algumas das variáveis por conta própria. 

* Você pode encontrar o valor do ponto de extremidade do serviço na seção **visão geral** do portal do Azure. 
* Para recuperar a URL SAS para seus dados de treinamento, abra o Gerenciador de Armazenamento do Microsoft Azure, clique com o botão direito do mouse no contêiner e selecione **obter assinatura de acesso compartilhado**. Verifique se as permissões de **leitura** e **lista** estão marcadas e clique em **criar**. Em seguida, copie o valor na seção **URL** . Ele deve ter o formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_variables)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Abaixo do método `Main`, defina a tarefa que é referenciada em `Main`. Aqui, você autenticará o objeto de cliente usando as variáveis de assinatura que você definiu acima. Você definirá os outros métodos posteriormente.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_maintask)]

## <a name="train-a-custom-model"></a>Preparar um modelo personalizado

O método a seguir usa o objeto de cliente do formulário Recognizer para treinar um novo modelo de reconhecimento nos documentos armazenados no contêiner de blob do Azure. Ele usa um método auxiliar para exibir informações sobre o modelo treinado recentemente (representado por um objeto [ModelResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelresult?view=azure-dotnet-preview) ) e retorna a ID do modelo.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_train)]

O método auxiliar a seguir exibe informações sobre um modelo de reconhecimento de formulário.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displaymodel)]

## <a name="get-a-list-of-extracted-keys"></a>Obter uma lista de chaves extraídas

Quando o treinamento for concluído, o modelo personalizado manterá uma lista de chaves extraídas dos documentos de treinamento. Ele espera que documentos de formulário futuros contenham essas chaves, e isso extrairá seus valores correspondentes na operação de análise. Use o método a seguir para recuperar a lista de chaves extraídas e imprimi-la no console. Essa é uma boa maneira de verificar se o processo de treinamento foi eficaz.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getkeys)]

## <a name="analyze-forms-with-a-custom-model"></a>Analisar formulários com um modelo personalizado

Esse método usa o cliente do reconhecedor de formulário e uma ID de modelo para analisar um documento de formulário PDF e extrair dados de chave/valor. Ele usa um método auxiliar para exibir os resultados (representados por um objeto [AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview) ).

> [!NOTE]
> O método a seguir analisa um formulário PDF. Para métodos semelhantes que analisam formulários JPEG e PNG, consulte o código de exemplo completo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_analyzepdf)]

O método auxiliar a seguir exibe informações sobre uma operação de análise.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displayanalyze)]

## <a name="get-a-list-of-custom-models"></a>Obter uma lista de modelos personalizados

Você pode retornar uma lista de todos os modelos treinados que pertencem à sua conta e pode recuperar informações sobre quando eles foram criados. A lista de modelos é representada por um objeto [ModelsResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelsresult?view=azure-dotnet-preview) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getmodellist)]

## <a name="delete-a-custom-model"></a>Excluir um modelo personalizado

Se você quiser excluir o modelo personalizado de sua conta, use o seguinte método:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_deletemodel)]

## <a name="run-the-application"></a>Executar a aplicação

Execute o aplicativo chamando o comando `dotnet run` do diretório do aplicativo.

```console
dotnet run
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Além disso, se você tiver treinado um modelo personalizado que deseja excluir de sua conta, execute o método em [excluir um modelo personalizado](#delete-a-custom-model).

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você usou a biblioteca de cliente .NET Recognizer formulário para treinar um modelo personalizado e analisar formulários. Em seguida, aprenda dicas para criar um conjunto de dados de treinamento melhor e produzir modelos mais precisos.

> [!div class="nextstepaction"]
>[Criar um conjunto de dados de treinamento](../build-training-data-set.md)

* [O que é o reconhecedor de formulário?](../overview.md)
* O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer).
