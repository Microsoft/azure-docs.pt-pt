---
title: 'Quickstart: Biblioteca de clientes do Reconhecimento de Formulários para .NET'
description: Neste arranque rápido, comece com a biblioteca de clientes Do Reconhecimento de Formulários para .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/06/2020
ms.author: pafarley
ms.openlocfilehash: efb07605d692b4980c108d60cc8f57babae68082
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83997624"
---
[Documentação de](https://docs.microsoft.com/dotnet/api/overview/azure/formrecognizer?view=azure-dotnet-preview)  |  referência Código fonte [da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src)  |  [Pacote (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer)  |  [Amostras](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Crie uma gratuitamente.](https://azure.microsoft.com/free/)
* Uma bolha de armazenamento azure que contém um conjunto de dados de treino. Consulte construir um conjunto de dados de [treino para um modelo personalizado](../../build-training-data-set.md) para dicas e opções para reunir o seu conjunto de dados de treino. Para este arranque rápido, pode utilizar os ficheiros sob a pasta **Train** do conjunto de dados da [amostra](https://go.microsoft.com/fwlink/?linkid=2090451).
* A versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Configuração

### <a name="create-a-form-recognizer-azure-resource"></a>Criar um recurso Do Reconhecimento de Formulários Azure

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Criar variáveis de ambiente

[!INCLUDE [environment-variables](../environment-variables.md)]

### <a name="create-a-new-c-application"></a>Criar uma nova aplicação C#

Numa janela de consola (como cmd, PowerShell ou Bash), utilize o comando para criar uma nova aplicação de `dotnet new` consola com o nome `formrecognizer-quickstart` . Este comando cria um simples projeto C# "Hello World" com um único ficheiro fonte: _Program.cs_. 

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

A partir do diretório do projeto, abra o ficheiro *Program.cs* no seu editor ou IDE preferido. Adicione as `using` seguintes diretivas:

```csharp
using Azure.AI.FormRecognizer;
using Azure.AI.FormRecognizer.Models;

using System;
using System.IO;
using System.Threading.Tasks;
```

Em seguida, adicione o seguinte código no método **principal** da aplicação. Definirá esta tarefa assíncrona mais tarde. 

```csharp
static void Main(string[] args)
{
    var t1 = RunFormRecognizerClient();

    Task.WaitAll(t1);
}
```

### <a name="install-the-client-library"></a>Instale a biblioteca do cliente

Dentro do diretório de aplicações, instale a biblioteca cliente Do Reconhecimento de Formulários para .NET com o seguinte comando:

```console
dotnet add package Azure.AI.FormRecognizer --version 1.0.0-preview.1
```

Se estiver a usar o Visual Studio IDE, a biblioteca de clientes está disponível como um pacote NuGet transferível.


<!-- Objet model TBD -->



## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes Do Reconhecimento de Formulários para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Reconhecer conteúdo de formulário](#recognize-form-content)
* [Reconhecer recibos](#recognize-receipts)
* [Preparar um modelo personalizado](#train-a-custom-model)
* [Analisar formulários com um modelo personalizado](#analyze-forms-with-a-custom-model)
* [Gerencie os seus modelos personalizados](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>Autenticar o cliente

Abaixo do `Main` método, defina a tarefa que é referenciada em `Main` . Aqui, irá autenticar dois objetos de cliente utilizando as variáveis de subscrição que definiu acima. Utilizará um objeto **AzureKeyCredential,** para que, se necessário, possa atualizar a tecla API sem criar novos objetos de cliente.

```csharp
static async Task RunFormRecognizerClient()
{ 
    string endpoint = Environment.GetEnvironmentVariable(
        "FORM_RECOGNIZER_ENDPOINT");
    string apiKey = Environment.GetEnvironmentVariable(
        "FORM_RECOGNIZER_KEY");
    var credential = new AzureKeyCredential(apiKey);
    
    var trainingClient = new FormTrainingClient(new Uri(endpoint), credential);
    var recognizerClient = new FormRecognizerClient(new Uri(endpoint), credential);
```

### <a name="call-client-specific-methods"></a>Ligue para métodos específicos do cliente

O próximo bloco de código utiliza os objetos do cliente para chamar métodos para cada uma das principais tarefas no SDK do Reconhecimento de Formulários. Definirá estes métodos mais tarde.

Também terá de adicionar referências aos URLs para os seus dados de treino e teste. 
* Para recuperar o URL SAS para os seus dados de treino de modelo personalizado, abra o Microsoft Azure Storage Explorer, clique no seu recipiente e selecione Obter assinatura de **acesso partilhado**. Certifique-se de que as permissões **de Leitura** e **Lista** são verificadas e clique em **Criar**. Em seguida, copie o valor na secção **URL.** Deve ter a forma: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .
* Para obter um URL de um formulário para testar, pode usar os passos acima para obter o URL SAS de um documento individual no armazenamento de blob. Ou, pegue o URL de um documento localizado em outro lugar.
* Utilize o método acima para obter o URL de uma imagem de recibo também, ou use o URL de imagem da amostra fornecido.

> [!NOTE]
> Os fragmentos de código neste guia utilizam formulários remotos acessados por URLs. Se quiser processar documentos de formulário local, consulte os métodos relacionados na [documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/formrecognizer?view=azure-dotnet-preview).

```csharp
    string trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    string formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
    string receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media"
    + "/contoso-allinone.jpg";

    // Call Form Recognizer scenarios:
    Console.WriteLine("Get form content...");
    await GetContent(recognizerClient, formUrl);

    Console.WriteLine("Analyze receipt...");
    await AnalyzeReceipt(recognizerClient, receiptUrl);

    Console.WriteLine("Train Model with training data...");
    Guid modelId = await TrainModel(trainingClient, trainingDataUrl);

    Console.WriteLine("Analyze PDF form...");
    await AnalyzePdfForm(recognizerClient, modelId, formUrl);

    Console.WriteLine("Manage models...");
    await ManageModels(trainingClient, trainingDataUrl) ;
}
```

## <a name="recognize-form-content"></a>Reconhecer conteúdo de formulário

Pode usar o Reconhecimento de Formulários para reconhecer mesas, linhas e palavras em documentos, sem precisar de treinar um modelo.

Para reconhecer o conteúdo de um ficheiro num determinado URI, utilize o método **StartRecogniseContentFromUri.**

```csharp
private static async Task<Guid> GetContent(
    FormRecognizerClient recognizerClient, string invoiceUri)
{
    Response<IReadOnlyList<FormPage>> formPages = await recognizerClient
        .StartRecognizeContentFromUri(new Uri(invoiceUri))
        .WaitForCompletionAsync();
```

O valor devolvido é uma coleção de objetos **FormPage:** um para cada página no documento submetido. O código seguinte itera através destes objetos e imprime os pares de chaves/valor extraídos e os dados da tabela.

```csharp
    foreach (FormPage page in formPages.Value)
    {
        Console.WriteLine($"Form Page {page.PageNumber} has {page.Lines.Count}" + 
            $" lines.");
    
        for (int i = 0; i < page.Lines.Count; i++)
        {
            FormLine line = page.Lines[i];
            Console.WriteLine($"    Line {i} has {line.Words.Count}" + 
                $" word{(line.Words.Count > 1 ? "s" : "")}," +
                $" and text: '{line.Text}'.");
        }
    
        for (int i = 0; i < page.Tables.Count; i++)
        {
            FormTable table = page.Tables[i];
            Console.WriteLine($"Table {i} has {table.RowCount} rows and" +
                $" {table.ColumnCount} columns.");
            foreach (FormTableCell cell in table.Cells)
            {
                Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex})"
                    $" contains text: '{cell.Text}'.");
            }
        }
    }
}
```

## <a name="recognize-receipts"></a>Reconhecer recibos

Esta secção demonstra como reconhecer e extrair campos comuns a partir de recibos dos EUA, utilizando um modelo de recibo pré-treinado.

Para reconhecer recibos de um URI, utilize o método **StartRecogniseReceiptsFromUri.** O valor devolvido é uma coleção de objetos **RecognizedReceipt:** um para cada página no documento submetido. O código seguinte processa um recibo no URI dado e imprime os principais campos e valores para a consola.

```csharp
private static async Task<Guid> AnalyzeReceipt(
    FormRecognizerClient recognizerClient, string receiptUri)
{
    Response<IReadOnlyList<RecognizedReceipt>> receipts = await recognizerClient
        .StartRecognizeReceiptsFromUri(new Uri(receiptUri)).WaitForCompletionAsync();
    foreach (var receipt in receipts.Value)
    {
        USReceipt usReceipt = receipt.AsUSReceipt();
    
        string merchantName = usReceipt.MerchantName?.Value ?? default;
        DateTime transactionDate = usReceipt.TransactionDate?.Value ?? default;
        IReadOnlyList<USReceiptItem> items = usReceipt.Items ?? default;
    
        Console.WriteLine($"Recognized USReceipt fields:");
        Console.WriteLine($"    Merchant Name: '{merchantName}', with confidence " +
            $"{usReceipt.MerchantName.Confidence}");
        Console.WriteLine($"    Transaction Date: '{transactionDate}', with" +
            $" confidence {usReceipt.TransactionDate.Confidence}");
```

O próximo bloco de iterates de código através dos itens individuais detetados no recibo e imprime os seus dados para a consola.

```csharp
        for (int i = 0; i < items.Count; i++)
        {
            USReceiptItem item = usReceipt.Items[i];
            Console.WriteLine($"    Item {i}:  Name: '{item.Name.Value}'," +
                $" Quantity: '{item.Quantity?.Value}', Price: '{item.Price?.Value}'");
            Console.WriteLine($"    TotalPrice: '{item.TotalPrice.Value}'");
        }
```

Finalmente, o último bloco de código imprime o resto dos principais detalhes do recibo.

```csharp
        float subtotal = usReceipt.Subtotal?.Value ?? default;
        float tax = usReceipt.Tax?.Value ?? default;
        float tip = usReceipt.Tip?.Value ?? default;
        float total = usReceipt.Total?.Value ?? default;
    
        Console.WriteLine($"    Subtotal: '{subtotal}', with confidence" +
            $" '{usReceipt.Subtotal.Confidence}'");
        Console.WriteLine($"    Tax: '{tax}', with confidence '{usReceipt.Tax.Confidence}'");
        Console.WriteLine($"    Tip: '{tip}', with confidence '{usReceipt.Tip?.Confidence ?? 0.0f}'");
        Console.WriteLine($"    Total: '{total}', with confidence '{usReceipt.Total.Confidence}'");
    }
}
```

## <a name="train-a-custom-model"></a>Preparar um modelo personalizado

Esta secção demonstra como treinar um modelo com os seus próprios dados. Um modelo treinado pode obter dados estruturados que incluam as relações chave/valor no documento de formulário original. Depois de treinar o modelo, pode testar e retreiná-lo e eventualmente usá-lo para extrair dados de mais formas de acordo com as suas necessidades.

> [!NOTE]
> Também pode treinar modelos com uma interface gráfica do utilizador, como a ferramenta de rotulagem da [amostra 'Reconhecimento](../../quickstarts/label-tool.md)de Formulários'.

### <a name="train-a-model-without-labels"></a>Treine um modelo sem rótulos

Treine modelos personalizados para reconhecer todos os campos e valores encontrados nas suas formas personalizadas sem rotular manualmente os documentos de treino.

O método seguinte treina um modelo num determinado conjunto de documentos e imprime o estado do modelo na consola. 

```csharp
private static async Task<Guid> TrainModel(
    FormRecognizerClient trainingClient, string trainingDataUrl)
{
    CustomFormModel model = await trainingClient
        .StartTrainingAsync(new Uri(trainingDataUrl)).WaitForCompletionAsync();
    
    Console.WriteLine($"Custom Model Info:");
    Console.WriteLine($"    Model Id: {model.ModelId}");
    Console.WriteLine($"    Model Status: {model.Status}");
    Console.WriteLine($"    Created On: {model.CreatedOn}");
    Console.WriteLine($"    Last Modified: {model.LastModified}");
```

O objeto **CustomFormModel** devolvido contém informações sobre os tipos de formulário que o modelo pode reconhecer e os campos que pode extrair de cada tipo de formulário. O bloco de código seguinte imprime esta informação para a consola.

```csharp
    foreach (CustomFormSubModel subModel in model.Models)
    {
        Console.WriteLine($"SubModel Form Type: {subModel.FormType}");
        foreach (CustomFormModelField field in subModel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Label != null)
            {
                Console.Write($", FieldLabel: {field.Label}");
            }
            Console.WriteLine("");
        }
    }
```

Finalmente, este método devolve a identificação única do modelo.

```csharp
    return model.ModelId;
}
```

### <a name="train-a-model-with-labels"></a>Treine um modelo com rótulos

Também pode treinar modelos personalizados rotulando manualmente os documentos de treino. Treinar com rótulos leva a um melhor desempenho em alguns cenários. Para treinar com etiquetas, é necessário dispor de ficheiros especiais de informação sobre* \<filename\> etiquetas (.pdf.labels.json*) no seu recipiente de armazenamento blob ao lado dos documentos de treino. A ferramenta de rotulagem da [amostra 'Reconhecimento](../../quickstarts/label-tool.md) de Formulários' fornece um UI para o ajudar a criar estes ficheiros de etiquetas. Uma vez que os tenha, pode ligar para o método **StartTrainingAsync** com o parâmetro de etiquetas de *utilização* definido para `true` .

```csharp
private static async Task<Guid> TrainModelWithLabelsAsync(
    FormRecognizerClient trainingClient, string trainingDataUrl)
{
    CustomFormModel model = await trainingClient.StartTrainingAsync(
        new Uri(trainingDataUrl), useLabels: true).WaitForCompletionAsync();
    
    Console.WriteLine($"Custom Model Info:");
    Console.WriteLine($"    Model Id: {model.ModelId}");
    Console.WriteLine($"    Model Status: {model.Status}");
    Console.WriteLine($"    Created On: {model.CreatedOn}");
    Console.WriteLine($"    Last Modified: {model.LastModified}");
```

O **CustomFormModel** devolvido indica os campos que o modelo pode extrair, juntamente com a sua precisão estimada em cada campo. O bloco de código seguinte imprime esta informação para a consola.

```csharp
    foreach (CustomFormSubModel subModel in model.Models)
    {
        Console.WriteLine($"SubModel Form Type: {subModel.FormType}");
        foreach (CustomFormModelField field in subModel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Accuracy != null)
            {
                Console.Write($", Accuracy: {field.Accuracy}");
            }
            Console.WriteLine("");
        }
    }
    return model.ModelId;
}
```

## <a name="analyze-forms-with-a-custom-model"></a>Analisar formulários com um modelo personalizado

Esta secção demonstra como extrair informações chave/valor e outros conteúdos dos seus tipos de formulários personalizados, utilizando modelos treinados com os seus próprios formulários.

> [!IMPORTANT]
> Para implementar este cenário, já deve ter treinado um modelo para que possa passar o seu ID para o método abaixo. Consulte a secção [de modelos do Comboio.](#train-a-model-without-labels)

Utilizará o método **StartRecogniseCustomFormsFromUri.** O valor devolvido é uma coleção de objetos **RecognizedForm:** um para cada página no documento submetido.

```csharp
// Analyze PDF form data
private static async Task AnalyzePdfForm(
    FormRecognizerClient formClient, Guid modelId, string pdfFormFile)
{    
    Response<IReadOnlyList<RecognizedForm>> forms = await recognizerClient
        .StartRecognizeCustomFormsFromUri(modelId, new Uri(formUri))
        .WaitForCompletionAsync();
```

O código que se segue imprime os resultados da análise para a consola. Imprime cada campo reconhecido e valor correspondente, juntamente com uma pontuação de confiança.

```csharp
    foreach (RecognizedForm form in forms.Value)
    {
        Console.WriteLine($"Form of type: {form.FormType}");
        foreach (FormField field in form.Fields.Values)
        {
            Console.WriteLine($"Field '{field.Name}: ");
    
            if (field.LabelText != null)
            {
                Console.WriteLine($"    Label: '{field.LabelText.Text}");
            }
    
            Console.WriteLine($"    Value: '{field.ValueText.Text}");
            Console.WriteLine($"    Confidence: '{field.Confidence}");
        }
    }
}
```

## <a name="manage-your-custom-models"></a>Gerencie os seus modelos personalizados

Esta secção demonstra como gerir os modelos personalizados armazenados na sua conta. O código seguinte faz todas as tarefas de gestão de modelos num único método, como exemplo. Comece por copiar a assinatura do método abaixo:

```csharp
private static async Task ManageModels(
    FormRecognizerClient trainingClient, string trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Verifique o número de modelos na conta de recurso FormRecogniser

O seguinte bloco de código verifica quantos modelos guardou na sua conta 'Reconhecimento de Formulários' e compara-o ao limite da conta.

```csharp
    // Check number of models in the FormRecognizer account, 
    // and the maximum number of models that can be stored.
    AccountProperties accountProperties = trainingClient.GetAccountProperties();
    Console.WriteLine($"Account has {accountProperties.CustomModelCount} models.");
    Console.WriteLine($"It can have at most {accountProperties.CustomModelLimit}" +
        $" models.");
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Enumerar os modelos atualmente armazenados na conta de recursos

O bloco de código seguinte lista os modelos atuais na sua conta e imprime os seus dados para a consola.

```csharp
    // List the first ten or fewer models currently stored in the account.
    Pageable<CustomFormModelInfo> models = trainingClient.GetModelInfos();
    
    foreach (CustomFormModelInfo modelInfo in models.Take(10))
    {
        Console.WriteLine($"Custom Model Info:");
        Console.WriteLine($"    Model Id: {modelInfo.ModelId}");
        Console.WriteLine($"    Model Status: {modelInfo.Status}");
        Console.WriteLine($"    Created On: {modelInfo.CreatedOn}");
        Console.WriteLine($"    Last Modified: {modelInfo.LastModified}");
    }
```

### <a name="get-a-specific-model-using-the-models-id"></a>Obtenha um modelo específico usando o ID do modelo

O bloco de código seguinte treina um novo modelo (tal como no Comboio uma secção [modelo)](#train-a-model-without-labels) e depois recupera uma segunda referência ao mesmo usando o seu ID.

```csharp
    // Create a new model to store in the account
    CustomFormModel model = await trainingClient.StartTrainingAsync(
        new Uri(trainingFileUrl)).WaitForCompletionAsync();
    
    // Get the model that was just created
    CustomFormModel modelCopy = trainingClient.GetCustomModel(model.ModelId);
    
    Console.WriteLine($"Custom Model {modelCopy.ModelId} recognizes the following" +
        " form types:");
    
    foreach (CustomFormSubModel subModel in modelCopy.Models)
    {
        Console.WriteLine($"SubModel Form Type: {subModel.FormType}");
        foreach (CustomFormModelField field in subModel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Label != null)
            {
                Console.Write($", FieldLabel: {field.Label}");
            }
            Console.WriteLine("");
        }
    }
```

### <a name="delete-a-model-from-the-resource-account"></a>Eliminar um modelo da conta de recursos

Também pode eliminar um modelo da sua conta fazendo referência ao seu ID.

```csharp
    // Delete the model from the account.
    trainingClient.DeleteModel(model.ModelId);
}
```

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação a partir do seu diretório de candidatura com o `dotnet run` comando.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos associados ao mesmo.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Resolução de Problemas

Quando interage com a biblioteca cliente do Reconheço de Formulários de Serviços Cognitivos utilizando o .NET SDK, os erros devolvidos pelo serviço resultarão em `RequestFailedException` . Incluirão o mesmo código de estado http que teria sido devolvido por um pedido de API REST.

Por exemplo, se submeter uma imagem de recibo com um URI inválido, é devolvido um `400` erro, indicando "Pedido Mau".

```csharp Snippet:FormRecognizerBadRequest
try
{
    Response<IReadOnlyList<RecognizedReceipt>> receipts = await client
    .StartRecognizeReceiptsFromUri(new Uri("http://invalid.uri"))
    .WaitForCompletionAsync();
}
catch (RequestFailedException e)
{
    Console.WriteLine(e.ToString());
}
```

Vai notar que informações adicionais, como a identificação do pedido do cliente da operação, estão registadas.

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

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, usou a biblioteca de clientes Form Recogniser .NET para treinar modelos e analisar formas de diferentes formas. Em seguida, aprenda dicas para criar um melhor conjunto de dados de treino e produzir modelos mais precisos.

> [!div class="nextstepaction"]
> [Criar um conjunto de dados de preparação](../../build-training-data-set.md)

* [O que é o Reconhecedor de Formato?](../../overview.md)
* O código de amostra deste guia (e muito mais) pode ser encontrado no [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md).