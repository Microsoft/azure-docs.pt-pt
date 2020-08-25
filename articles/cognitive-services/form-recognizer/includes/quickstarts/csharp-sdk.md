---
title: 'Quickstart: Biblioteca de clientes do Reconhecimento de Formulários para .NET'
description: Neste arranque rápido, começa com a biblioteca de clientes Do Reconhecimento de Formulários para .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 08/17/2020
ms.author: pafarley
ms.openlocfilehash: 520c42b4ff75328bad67e18a6723aafc60e6c19c
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752985"
---
[Documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme-pre)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src)  |  [Pacote (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer)  |  [Amostras](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services).
* Uma bolha de armazenamento Azure que contém um conjunto de dados de treino. Consulte [Construir um conjunto de dados de treino para um modelo personalizado](../../build-training-data-set.md) para dicas e opções para reunir o seu conjunto de dados de treino. Para este arranque rápido, pode utilizar os ficheiros sob a pasta **Train** do conjunto de [dados](https://go.microsoft.com/fwlink/?linkid=2090451)da amostra .
* A versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Configuração

### <a name="create-a-form-recognizer-azure-resource"></a>Criar um recurso de reconhecimento de formulários Azure

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Criar variáveis de ambiente

[!INCLUDE [environment-variables](../environment-variables.md)]

### <a name="create-a-new-c-application"></a>Criar uma nova aplicação C#

Numa janela de consola (como cmd, PowerShell ou Bash), utilize o `dotnet new` comando para criar uma nova aplicação de consola com o nome `formrecognizer-quickstart` . Este comando cria um projeto simples "Hello World" C# com um único ficheiro de origem: _Program.cs_. 

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

A partir do diretório do projeto, abra o ficheiro *Program.cs* no seu editor preferido ou IDE. Adicione as `using` seguintes diretivas:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_using)]

Em seguida, adicione o seguinte código no método **principal** da aplicação. Vai definir esta tarefa assíncrota mais tarde. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_main)]

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

No diretório de aplicações, instale a biblioteca cliente Do Reconhecimento de Formulários para .NET com o seguinte comando:

```console
dotnet add package Azure.AI.FormRecognizer --version 3.0.0
```

Se estiver a utilizar o Visual Studio IDE, a biblioteca do cliente está disponível como um pacote NuGet transferível.


<!-- Objet model TBD -->



## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer as seguintes tarefas com a biblioteca do cliente do Reconhecimento de Formulários para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Reconhecer o conteúdo da forma](#recognize-form-content)
* [Reconhecer recibos](#recognize-receipts)
* [Preparar um modelo personalizado](#train-a-custom-model)
* [Analisar formas com um modelo personalizado](#analyze-forms-with-a-custom-model)
* [Gerir os seus modelos personalizados](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>Autenticar o cliente

Abaixo do `Main` método, defina a tarefa que é referenciada em `Main` . Aqui, autenticará dois objetos clientes utilizando as variáveis de subscrição acima definidas. Utilizará um objeto **AzureKeyCredential,** para que, se necessário, possa atualizar a tecla API sem criar novos objetos de cliente.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_auth)]


### <a name="call-client-specific-methods"></a>Ligue para métodos específicos do cliente

O próximo bloco de código utiliza os objetos do cliente para chamar métodos para cada uma das principais tarefas no Formulário Reconhecedor SDK. Definirá estes métodos mais tarde.

Também terá de adicionar referências aos URLs para os seus dados de treino e teste. 
* Para recuperar o URL SAS para os seus dados de treino de modelo personalizados, abra o Microsoft Azure Storage Explorer, clique com o botão direito no seu recipiente e **selecione Obter assinatura de acesso partilhado**. Certifique-se de que as permissões **de Leitura** e **Lista** são verificadas e clique em **Criar**. Em seguida, copie o valor na secção **URL.** Deve ter o formulário: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .
* Para obter um URL de um formulário para testar, você pode usar os passos acima para obter o URL SAS de um documento individual no armazenamento de bolhas. Ou, pegue o URL de um documento localizado em outro lugar.
* Utilize o método acima para obter o URL de uma imagem de receção também, ou use o URL de imagem de amostra fornecido.

> [!NOTE]
> Os fragmentos de código neste guia utilizam formulários remotos acedidos por URLs. Se pretender processar documentos de formulário local, consulte os métodos relacionados na [documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme-pre).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_calls)]

## <a name="recognize-form-content"></a>Reconhecer o conteúdo da forma

Pode utilizar o Form Recogniser para reconhecer tabelas, linhas e palavras em documentos, sem precisar de treinar um modelo.

Para reconhecer o conteúdo de um ficheiro num dado URI, utilize o método **StartRecognizeContentFromUri.**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_call)]


O valor devolvido é uma coleção de objetos **FormPage:** um para cada página no documento submetido. O código seguinte itera através destes objetos e imprime os pares de chave/valor extraídos e os dados de tabela.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_print)]


## <a name="recognize-receipts"></a>Reconhecer recibos

Esta secção demonstra como reconhecer e extrair campos comuns a partir de recibos dos EUA, utilizando um modelo de recibo pré-treinado.

Para reconhecer os recibos de um URI, utilize o método **StartRecognizeReceiptsFromUri.** O valor devolvido é uma coleção de objetos **RecognizedReceipt:** um para cada página no documento submetido. O código seguinte processa um recibo no URI dado e imprime os principais campos e valores para a consola.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_call)]


O próximo bloco de códigos iteração através dos itens individuais detetados no recibo e imprime os seus dados para a consola.
[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_item_print)]

Finalmente, o último bloco de código imprime o valor total no recibo.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_total_print)]

## <a name="train-a-custom-model"></a>Preparar um modelo personalizado

Esta secção demonstra como treinar um modelo com os seus próprios dados. Um modelo treinado pode obter dados estruturados de saída que incluam as relações chave/valor no documento original do formulário. Depois de treinar o modelo, pode testá-lo e reforçá-lo e eventualmente usá-lo para extrair dados de mais formas de acordo com as suas necessidades.

> [!NOTE]
> Também pode treinar modelos com uma interface gráfica do utilizador, como a ferramenta de rotulagem da [amostra do Form Recogniser.](../../quickstarts/label-tool.md)

### <a name="train-a-model-without-labels"></a>Treine um modelo sem rótulos

Treine modelos personalizados para reconhecer todos os campos e valores encontrados nas suas formas personalizadas sem rotular manualmente os documentos de treino.

O método a seguir treina um modelo num determinado conjunto de documentos e imprime o estado do modelo à consola. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train)]

O objeto **CustomFormModel** devolvido contém informações sobre os tipos de formulários que o modelo pode reconhecer e os campos que pode extrair de cada tipo de formulário. O bloco de código que se segue imprime esta informação para a consola.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_response)]

Finalmente, este método devolve o ID único do modelo.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_return)]

### <a name="train-a-model-with-labels"></a>Treine um modelo com rótulos

Também pode treinar modelos personalizados rotulando manualmente os documentos de treino. O treino com rótulos leva a um melhor desempenho em alguns cenários. Para treinar com etiquetas, é necessário ter ficheiros de informações especiais de etiquetas* \<filename\> (.pdf.labels.js) no*seu recipiente de armazenamento de bolhas ao lado dos documentos de treino. A [ferramenta de rotulagem da amostra do Reconhecimento de Formulários](../../quickstarts/label-tool.md) fornece uma UI para ajudá-lo a criar estes ficheiros de etiqueta. Uma vez que os tenha, pode ligar para o método **StartTrainingAsync** com o parâmetro *uselabels* definido para `true` .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels)]

O **CustomFormModel** devolvido indica os campos que o modelo pode extrair, juntamente com a sua precisão estimada em cada campo. O bloco de código que se segue imprime esta informação para a consola.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels_response)]

## <a name="analyze-forms-with-a-custom-model"></a>Analisar formas com um modelo personalizado

Esta secção demonstra como extrair informações de chave/valor e outros conteúdos dos seus tipos de formulários personalizados, utilizando modelos treinados com os seus próprios formulários.

> [!IMPORTANT]
> Para implementar este cenário, já deve ter treinado um modelo para que possa passar o seu ID para o método abaixo. Consulte a secção [Modelo train.](#train-a-model-without-labels)

Utilizará o método **StartRecognizeCustomFormsFromUri.** O valor devolvido é uma coleção de objetos **RecognizedForm:** um para cada página no documento submetido.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze)]

O código seguinte imprime os resultados da análise para a consola. Imprime cada campo reconhecido e valor correspondente, juntamente com uma pontuação de confiança.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze_response)]

## <a name="manage-your-custom-models"></a>Gerir os seus modelos personalizados

Esta secção demonstra como gerir os modelos personalizados armazenados na sua conta. O seguinte código faz todas as tarefas de gestão do modelo num único método, como exemplo. Comece por copiar a assinatura do método abaixo:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage)]

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Verifique o número de modelos na conta de recursos FormRecognizer

O bloco de códigos que se segue verifica quantos modelos guardou na sua conta Desemaçador de Formulários e compara-os ao limite da conta.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_count)]

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Listar os modelos atualmente armazenados na conta de recursos

O bloco de códigos que se segue lista os modelos atuais na sua conta e imprime os seus dados para a consola.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_list)]

### <a name="get-a-specific-model-using-the-models-id"></a>Obtenha um modelo específico usando o ID do modelo

O bloco de código que se segue treina um novo modelo (tal como na secção [De Modelos)](#train-a-model-without-labels) e, em seguida, recupera uma segunda referência a ele usando o seu ID.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_get)]

### <a name="delete-a-model-from-the-resource-account"></a>Eliminar um modelo da conta de recursos

Também pode eliminar um modelo da sua conta fazendo referência ao seu ID.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_delete)]

## <a name="run-the-application"></a>Executar a aplicação

Executar o pedido do seu diretório de candidaturas com o `dotnet run` comando.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Resolução de problemas

Quando interage com a biblioteca cliente Do Reconhecimento de Serviços Cognitivos utilizando o .NET SDK, os erros devolvidos pelo serviço resultarão em `RequestFailedException` um . Incluirão o mesmo código de estado HTTP que teria sido devolvido por um pedido de API REST.

Por exemplo, se submeter uma imagem de recibo com um URI inválido, é devolvido um `400` erro, indicando "Mau Pedido".

```csharp
try
{
    RecognizedReceiptCollection receipts = await client.StartRecognizeReceiptsFromUri(new Uri(receiptUri)).WaitForCompletionAsync();
}
catch (RequestFailedException e)
{
    Console.WriteLine(e.ToString());
}
```

Vai notar que informações adicionais, como o pedido de identificação do cliente da operação, estão registadas.

```
Message:
    Azure.RequestFailedException: Service request failed.
    Status: 400 (Bad Request)

Content:
    {"error":{"code":"FailedToDownloadImage","innerError":
    {"requestId":"8ca04feb-86db-4552-857c-fde903251518"},
    "message":"Failed to download image from input URL."}}

Headers:
    Transfer-Encoding: chunked
    x-envoy-upstream-service-time: REDACTED
    apim-request-id: REDACTED
    Strict-Transport-Security: REDACTED
    X-Content-Type-Options: REDACTED
    Date: Mon, 20 Apr 2020 22:48:35 GMT
    Content-Type: application/json; charset=utf-8
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, utilizou a biblioteca de clientes Form Recogniser .NET para formar modelos e analisar formas de diferentes formas. Em seguida, aprenda dicas para criar um melhor conjunto de dados de treino e produzir modelos mais precisos.

> [!div class="nextstepaction"]
> [Criar um conjunto de dados de preparação](../../build-training-data-set.md)

* [O que é o Reconhecedor de Formato?](../../overview.md)
* O código de amostra deste guia (e muito mais) pode ser encontrado no [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md).
