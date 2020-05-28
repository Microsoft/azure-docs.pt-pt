---
title: 'Quickstart: Biblioteca de clientes do Reconhecimento de Formulários para Java'
description: Neste arranque rápido, comece com a biblioteca de clientes Do Reconhecimento de Formulários para Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/06/2020
ms.author: pafarley
ms.openlocfilehash: abc61b08770ca011c0f843dff3c2cda080ca7262
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83997603"
---
[Documentação de](https://docs.microsoft.com/java/api/overview/azure/formrecognizer?view=azure-java-preview)  |  referência Código fonte [da biblioteca](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src)  |  [Pacote (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer)  |  [Amostras](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* Uma bolha de armazenamento azure que contém um conjunto de dados de treino. Consulte construir um conjunto de dados de [treino para um modelo personalizado](../../build-training-data-set.md) para dicas e opções para reunir o seu conjunto de dados de treino. Para este arranque rápido, pode utilizar os ficheiros sob a pasta **Train** do conjunto de dados da [amostra](https://go.microsoft.com/fwlink/?linkid=2090451).
* A versão atual do Kit de [Desenvolvimento de Java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A [ferramenta de construção Gradle](https://gradle.org/install/), ou outro gestor de dependência.

## <a name="setting-up"></a>Configuração

### <a name="create-a-form-recognizer-azure-resource"></a>Criar um recurso Do Reconhecimento de Formulários Azure

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Criar variáveis de ambiente

[!INCLUDE [environment-variables](../environment-variables.md)]

### <a name="create-a-new-gradle-project"></a>Criar um novo projeto Gradle


Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue para ela. 

```console
mkdir myapp && cd myapp
```

Mande o `gradle init` comando do seu diretório de trabalho. Este comando criará ficheiros de construção essenciais para Gradle, incluindo *build.gradle.kts* que é usado no tempo de execução para criar e configurar a sua aplicação.

```console
gradle init --type basic
```

Quando for solicitado a escolher um **DSL,** selecione **Kotlin**.

Crie uma pasta para a sua aplicação de amostras. A partir do seu diretório de trabalho, executar o seguinte comando:

```console
mkdir -p src/main/java
```

Navegue para a nova pasta e crie um ficheiro chamado *formrecogniser-quickstart.java*. Abra-o no seu editor preferido ou IDE e adicione as `import` seguintes declarações:

```java
import Azure.AI.FormRecognizer;
import Azure.AI.FormRecognizer.Models;

import java.util.concurrent.atomic.AtomicReference;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.util.Context;
```

No método da `main` aplicação, crie variáveis para o ponto final e chave Azure do seu recurso. Se criou a variável ambiental depois de lançar a aplicação, terá de fechar e reabrir o editor, IDE ou shell para aceder à variável. Definirá os métodos mais tarde.


```java
public static void Main(string[] args)
{
    String key = System.getenv("FORM_RECOGNIZER_KEY");
    String endpoint = System.getenv("FORM_RECOGNIZER_ENDPOINT");
}
```

### <a name="install-the-client-library"></a>Instale a biblioteca do cliente

Este quickstart usa o gestor de dependência gradle. Você pode encontrar a biblioteca de clientes e informações para outros gestores de dependência no [Repositório Central Maven](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer).

No ficheiro *build.gradle.kts* do seu projeto, não se esqueça de incluir a biblioteca de clientes como `implementation` um comunicado. 

```kotlin
dependencies {
    implementation group: 'com.azure', name: 'azure-ai-formrecognizer', version: '1.0.0-beta.1'
}
```

<!-- 
    Object model tbd
-->

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes Do Reconhecimento de Formulários para Java:

* [Autenticar o cliente](#authenticate-the-client)
* [Reconhecer conteúdo de formulário](#recognize-form-content)
* [Reconhecer recibos](#recognize-receipts)
* [Preparar um modelo personalizado](#train-a-custom-model)
* [Analisar formulários com um modelo personalizado](#analyze-forms-with-a-custom-model)
* [Gerencie os seus modelos personalizados](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Dentro do `Main` método, adicione o seguinte código. Aqui, irá autenticar dois objetos de cliente utilizando as variáveis de subscrição que definiu acima. Utilizará um objeto **AzureKeyCredential,** para que, se necessário, possa atualizar a tecla API sem criar novos objetos de cliente.

```java
FormRecognizerClient recognizerClient = new FormRecognizerClientBuilder()
    .credential(new AzureKeyCredential("{key}"))
    .endpoint("{endpoint}")
    .buildClient();
    
FormTrainingClient trainingClient = recognizerClient.getFormTrainingClient();
```

### <a name="call-client-specific-methods"></a>Ligue para métodos específicos do cliente

O próximo bloco de código utiliza os objetos do cliente para chamar métodos para cada uma das principais tarefas no SDK do Reconhecimento de Formulários. Definirá estes métodos mais tarde.

Também terá de adicionar referências aos URLs para os seus dados de treino e teste. 
* Para recuperar o URL SAS para os seus dados de treino de modelo personalizado, abra o Microsoft Azure Storage Explorer, clique no seu recipiente e selecione Obter assinatura de **acesso partilhado**. Certifique-se de que as permissões **de Leitura** e **Lista** são verificadas e clique em **Criar**. Em seguida, copie o valor na secção **URL.** Deve ter a forma: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .
* Para obter um URL de um formulário para testar, pode usar os passos acima para obter o URL SAS de um documento individual no armazenamento de blob. Ou, pegue o URL de um documento localizado em outro lugar.
* Utilize o método acima para obter o URL de uma imagem de recibo também.

> [!NOTE]
> Os fragmentos de código neste guia utilizam formulários remotos acessados por URLs. Se quiser processar documentos de formulário local, consulte os métodos relacionados na [documentação de referência](https://docs.microsoft.com/java/api/overview/azure/formrecognizer?view=azure-java-preview).

```java
    string trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    string formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
    string receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media"
    + "/contoso-allinone.jpg";

    // Call Form Recognizer scenarios:
    System.out.println("Get form content...");
    GetContent(recognizerClient, formUrl);

    System.out.println("Analyze receipt...");
    AnalyzeReceipt(recognizerClient, receiptUrl);

    System.out.println("Train Model with training data...");
    modelId = TrainModel(trainingClient, trainingDataUrl);

    System.out.println("Analyze PDF form...");
    AnalyzePdfForm(recognizerClient, modelId, formUrl);

    System.out.println("Manage models...");
    ManageModels(trainingClient, trainingDataUrl) ;
```


## <a name="recognize-form-content"></a>Reconhecer conteúdo de formulário

Pode usar o Reconhecimento de Formulários para reconhecer mesas, linhas e palavras em documentos, sem precisar de treinar um modelo.

Para reconhecer o conteúdo de um ficheiro num determinado URI, utilize o método **beginRecogniseContentFromUrl.**

```java
private static void GetContent(
    FormRecognizerClient recognizerClient, String invoiceUri)
{
    String analyzeFilePath = invoiceUri;
    SyncPoller<OperationResult, IterableStream<FormPage>> recognizeContentPoller =
        recognizerClient.beginRecognizeContentFromUrl(analyzeFilePath);
    
    IterableStream<FormPage> contentResult = recognizeContentPoller.getFinalResult();
```

O valor devolvido é uma coleção de objetos **FormPage:** um para cada página no documento submetido. O código seguinte itera através destes objetos e imprime os pares de chaves/valor extraídos e os dados da tabela.

```java
    contentResult.forEach(formPage -> {
        // Table information
        System.out.println("----Recognizing content ----");
        System.out.printf("Has width: %d and height: %d, measured with unit: %s.%n", formPage.getWidth(),
            formPage.getHeight(),
            formPage.getUnit());
        formPage.getTables().forEach(formTable -> {
            System.out.printf("Table has %d rows and %d columns.%n", formTable.getRowCount(),
                formTable.getColumnCount());
            formTable.getCells().forEach(formTableCell -> {
                System.out.printf("Cell has text %s.%n", formTableCell.getText());
            });
            System.out.println();
        });
    });
}
```

## <a name="recognize-receipts"></a>Reconhecer recibos

Esta secção demonstra como reconhecer e extrair campos comuns a partir de recibos dos EUA, utilizando um modelo de recibo pré-treinado.

Para reconhecer recibos de um URI, utilize o método **iniciar RecogniseReceiptsFromUrl.** O valor devolvido é uma coleção de objetos **RecognizedReceipt:** um para cada página no documento submetido.

```java
private static void AnalyzeReceipt(
    FormRecognizerClient recognizerClient, string receiptUri)
{
    SyncPoller<OperationResult, IterableStream<RecognizedReceipt>> syncPoller =
        formRecognizerClient.beginRecognizeReceiptsFromUrl(receiptUri);
    IterableStream<RecognizedReceipt> receiptPageResults = syncPoller.getFinalResult();
```

O próximo bloco de códigoita através dos recibos e imprime os seus dados para a consola.

```java
    receiptPageResults.forEach(recognizedReceipt -> {
        USReceipt usReceipt = ReceiptExtensions.asUSReceipt(recognizedReceipt);
        System.out.printf("Page Number: %d%n", usReceipt.getMerchantName().getPageNumber());
        System.out.printf("Merchant Name: %s, confidence: %.2f%n", usReceipt.getMerchantName().getFieldValue(), usReceipt.getMerchantName().getConfidence());
        System.out.printf("Merchant Address: %s, confidence: %.2f%n", usReceipt.getMerchantAddress().getName(), usReceipt.getMerchantAddress().getConfidence());
        System.out.printf("Merchant Phone Number %s, confidence: %.2f%n", usReceipt.getMerchantPhoneNumber().getFieldValue(), usReceipt.getMerchantPhoneNumber().getConfidence());
        System.out.printf("Total: %s confidence: %.2f%n", usReceipt.getTotal().getName(), usReceipt.getTotal().getConfidence());
```
O próximo bloco de iterates de código através dos itens individuais detetados no recibo e imprime os seus dados para a consola.

```java
        System.out.printf("Receipt Items: %n");
        usReceipt.getReceiptItems().forEach(receiptItem -> {
            if (receiptItem.getName() != null) {
                System.out.printf("Name: %s, confidence: %.2f%n", receiptItem.getName().getFieldValue(), receiptItem.getName().getConfidence());
            }
            if (receiptItem.getQuantity() != null) {
                System.out.printf("Quantity: %s, confidence: %.2f%n", receiptItem.getQuantity().getFieldValue(), receiptItem.getQuantity().getConfidence());
            }
            if (receiptItem.getPrice() != null) {
                System.out.printf("Price: %s, confidence: %.2f%n", receiptItem.getPrice().getFieldValue(), receiptItem.getPrice().getConfidence());
            }
            if (receiptItem.getTotalPrice() != null) {
                System.out.printf("Total Price: %s, confidence: %.2f%n", receiptItem.getTotalPrice().getFieldValue(), receiptItem.getTotalPrice().getConfidence());
            }
        });
    });
}
```

## <a name="train-a-custom-model"></a>Preparar um modelo personalizado

Esta secção demonstra como treinar um modelo com os seus próprios dados. Um modelo treinado pode obter dados estruturados que incluam as relações chave/valor no documento de formulário original. Depois de treinar o modelo, pode testar e retreiná-lo e eventualmente usá-lo para extrair dados de mais formas de acordo com as suas necessidades.

> [!NOTE]
> Também pode treinar modelos com uma interface gráfica do utilizador, como a ferramenta de rotulagem da [amostra 'Reconhecimento](../../quickstarts/label-tool.md)de Formulários'.

### <a name="train-a-model-without-labels"></a>Treine um modelo sem rótulos

Treine modelos personalizados para reconhecer todos os campos e valores encontrados nas suas formas personalizadas sem rotular manualmente os documentos de treino.

O método seguinte treina um modelo num determinado conjunto de documentos e imprime o estado do modelo na consola. 

```java
private static String TrainModel(
    FormRecognizerClient trainingClient, string trainingDataUrl)
{
    String trainingSetSource = "{unlabeled_training_set_SAS_URL}";
    SyncPoller<OperationResult, CustomFormModel> trainingPoller =
        formTrainingClient.beginTraining(trainingSetSource, false);
    
    CustomFormModel customFormModel = trainingPoller.getFinalResult();
    
    // Model Info
    System.out.printf("Model Id: %s%n", customFormModel.getModelId());
    System.out.printf("Model Status: %s%n", customFormModel.getModelStatus());
    System.out.printf("Model created on: %s%n", customFormModel.getCreatedOn());
    System.out.printf("Model last updated: %s%n%n", customFormModel.getLastUpdatedOn());
```
O objeto **CustomFormModel** devolvido contém informações sobre os tipos de formulário que o modelo pode reconhecer e os campos que pode extrair de cada tipo de formulário. O bloco de código seguinte imprime esta informação para a consola.

```java 
    System.out.println("Recognized Fields:");
    // looping through the sub-models, which contains the fields they were trained on
    // Since the given training documents are unlabeled, we still group them but they do not have a label.
    customFormModel.getSubModels().forEach(customFormSubModel -> {
        // Since the training data is unlabeled, we are unable to return the accuracy of this model
        customFormSubModel.getFieldMap().forEach((field, customFormModelField) ->
            System.out.printf("Field: %s Field Label: %s%n",
                field, customFormModelField.getLabel()));
    });
```

Finalmente, este método devolve a identificação única do modelo.

```java
    return customFormModel.getModelId();
}
```

### <a name="train-a-model-with-labels"></a>Treine um modelo com rótulos

Também pode treinar modelos personalizados rotulando manualmente os documentos de treino. Treinar com rótulos leva a um melhor desempenho em alguns cenários. Para treinar com etiquetas, é necessário dispor de ficheiros especiais de informação sobre* \<filename\> etiquetas (.pdf.labels.json*) no seu recipiente de armazenamento blob ao lado dos documentos de treino. A ferramenta de rotulagem da [amostra 'Reconhecimento](../../quickstarts/label-tool.md) de Formulários' fornece um UI para o ajudar a criar estes ficheiros de etiquetas. Uma vez que os tenha, pode chamar o método iniciar o **treino** com o parâmetro de etiquetas de *utilização* definido para `true` .

```java
private static String TrainModelWithLabels(
    FormRecognizerClient trainingClient, String trainingDataUrl)
{
    // Train custom model
    String trainingSetSource = trainingDataUrl;
    SyncPoller<OperationResult, CustomFormModel> trainingPoller = client.beginTraining(trainingSetSource, true);

    CustomFormModel customFormModel = trainingPoller.getFinalResult();

    // Model Info
    System.out.printf("Model Id: %s%n", customFormModel.getModelId());
    System.out.printf("Model Status: %s%n", customFormModel.getModelStatus());
    System.out.printf("Model created on: %s%n", customFormModel.getCreatedOn());
    System.out.printf("Model last updated: %s%n%n", customFormModel.getLastUpdatedOn());
```

O **CustomFormModel** devolvido indica os campos que o modelo pode extrair, juntamente com a sua precisão estimada em cada campo. O bloco de código seguinte imprime esta informação para a consola.

```java
    // looping through the sub-models, which contains the fields they were trained on
    // The labels are based on the ones you gave the training document.
    System.out.println("Recognized Fields:");
    // Since the data is labeled, we are able to return the accuracy of the model
    customFormModel.getSubModels().forEach(customFormSubModel -> {
        System.out.printf("Sub-model accuracy: %.2f%n", customFormSubModel.getAccuracy());
        customFormSubModel.getFieldMap().forEach((label, customFormModelField) ->
            System.out.printf("Field: %s Field Name: %s Field Accuracy: %.2f%n",
                label, customFormModelField.getName(), customFormModelField.getAccuracy()));
    });
    return customFormModel.getModelId();
}
```

## <a name="analyze-forms-with-a-custom-model"></a>Analisar formulários com um modelo personalizado

Esta secção demonstra como extrair informações chave/valor e outros conteúdos dos seus tipos de formulários personalizados, utilizando modelos treinados com os seus próprios formulários.

> [!IMPORTANT]
> Para implementar este cenário, já deve ter treinado um modelo para que possa passar o seu ID para o método abaixo. Consulte a secção [de modelos do Comboio.](#train-a-model-without-labels)

Utilizará o método **beginRecogniseCustomFormsFromUrl.** O valor devolvido é uma coleção de objetos **RecognizedForm:** um para cada página no documento submetido.

```java
// Analyze PDF form data
private static void AnalyzePdfForm(
    FormRecognizerClient formClient, String modelId, String pdfFormUrl)
{    
    String modelId = modelId;
    SyncPoller<OperationResult, IterableStream<RecognizedForm>> recognizeFormPoller =
        client.beginRecognizeCustomFormsFromUrl(pdfFormUrl, modelId);

    IterableStream<RecognizedForm> recognizedForms = recognizeFormPoller.getFinalResult();
```

O código que se segue imprime os resultados da análise para a consola. Imprime cada campo reconhecido e valor correspondente, juntamente com uma pontuação de confiança.

```java
    recognizedForms.forEach(form -> {
        System.out.println("----------- Recognized Form -----------");
        System.out.printf("Form type: %s%n", form.getFormType());
        form.getFields().forEach((label, formField) -> {
            System.out.printf("Field %s has value %s with confidence score of %.2f.%n", label,
                formField.getFieldValue(),
                formField.getConfidence());
        });
        System.out.print("-----------------------------------");
    });
}
```

## <a name="manage-your-custom-models"></a>Gerencie os seus modelos personalizados

Esta secção demonstra como gerir os modelos personalizados armazenados na sua conta. O código seguinte faz todas as tarefas de gestão de modelos num único método, como exemplo. Comece por copiar a assinatura do método abaixo:

```java
private static void ManageModels(
    FormRecognizerClient trainingClient, String trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Verifique o número de modelos na conta de recurso FormRecogniser

O seguinte bloco de código verifica quantos modelos guardou na sua conta 'Reconhecimento de Formulários' e compara-o ao limite da conta.

```java
    AtomicReference<String> modelId = new AtomicReference<>();

    // First, we see how many custom models we have, and what our limit is
    AccountProperties accountProperties = client.getAccountProperties();
    System.out.printf("The account has %s custom models, and we can have at most %s custom models",
        accountProperties.getCount(), accountProperties.getLimit());
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Enumerar os modelos atualmente armazenados na conta de recursos

O bloco de código seguinte lista os modelos atuais na sua conta e imprime os seus dados para a consola.

```java    
    // Next, we get a paged list of all of our custom models
    PagedIterable<CustomFormModelInfo> customModels = client.getModelInfos();
    System.out.println("We have following models in the account:");
    customModels.forEach(customFormModelInfo -> {
        System.out.printf("Model Id: %s%n", customFormModelInfo.getModelId());
        // get custom model info
        modelId.set(customFormModelInfo.getModelId());
        CustomFormModel customModel = client.getCustomModel(customFormModelInfo.getModelId());
        System.out.printf("Model Id: %s%n", customModel.getModelId());
        System.out.printf("Model Status: %s%n", customModel.getModelStatus());
        System.out.printf("Created on: %s%n", customModel.getCreatedOn());
        System.out.printf("Updated on: %s%n", customModel.getLastUpdatedOn());
        customModel.getSubModels().forEach(customFormSubModel -> {
            System.out.printf("Custom Model Form type: %s%n", customFormSubModel.getFormType());
            System.out.printf("Custom Model Accuracy: %.2f%n", customFormSubModel.getAccuracy());
            if (customFormSubModel.getFieldMap() != null) {
                customFormSubModel.getFieldMap().forEach((fieldText, customFormModelField) -> {
                    System.out.printf("Field Text: %s%n", fieldText);
                    System.out.printf("Field Accuracy: %.2f%n", customFormModelField.getAccuracy());
                });
            }

        });
    });
```

### <a name="delete-a-model-from-the-resource-account"></a>Eliminar um modelo da conta de recursos

Também pode eliminar um modelo da sua conta fazendo referência ao seu ID.

```java
    // Delete Custom Model
    System.out.printf("Deleted model with model Id: %s operation completed with status: %s%n", modelId.get(),
        client.deleteModelWithResponse(modelId.get(), Context.NONE).getStatusCode());
}
```


## <a name="run-the-application"></a>Executar a aplicação

Pode construir a aplicação com:

```console
gradle build
```

Executar a aplicação com o `run` objetivo:

```console
gradle run
```

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos associados ao mesmo.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Resolução de Problemas

Dos clientes Do Recogniser, as `ErrorResponseException` exceções. Por exemplo, se tentar fornecer um URL de origem de ficheiro inválido, um `ErrorResponseException` seria levantado com um erro que indicasse a causa da falha. No seguinte código, o erro é tratado graciosamente captando a exceção e exibindo as informações adicionais sobre o erro.

```java Snippet:FormRecognizerBadRequest
try {
    formRecognizerClient.beginRecognizeContentFromUrl("invalidSourceUrl");
} catch (ErrorResponseException e) {
    System.out.println(e.getMessage());
}
```

### <a name="enable-client-logging"></a>Ativar a exploração madeireira do cliente
Os SDKs Azure para a Java oferecem uma história de registo consistente para ajudar a ajudar na resolução de erros de aplicação e acelerar a sua resolução. Os registos produzidos capturarão o fluxo de uma aplicação antes de chegarem ao estado terminal para ajudar a localizar o problema da raiz. Veja o [wiki de exploração para](https://github.com/Azure/azure-sdk-for-java/wiki/Logging-with-Azure-SDK) obter orientações sobre a viagem.

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, usou a biblioteca de clientes Do Reconhecimento de Formuláriojava para treinar modelos e analisar formas de diferentes maneiras. Em seguida, aprenda dicas para criar um melhor conjunto de dados de treino e produzir modelos mais precisos.

> [!div class="nextstepaction"]
> [Criar um conjunto de dados de preparação](../../build-training-data-set.md)

* [O que é o Reconhecedor de Formato?](../../overview.md)
* O código de amostra deste guia (e muito mais) pode ser encontrado no [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples).