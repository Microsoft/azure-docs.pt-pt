---
title: 'Quickstart: Biblioteca de clientes do Reconhecimento de Formulários para JavaScript'
description: Neste arranque rápido, comece com a biblioteca de clientes Do Reconhecimento de Formulários para JavaScript.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/08/2020
ms.author: pafarley
ms.openlocfilehash: c24f82d48a1452cdb272abca178a9ba924aacc20
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83997589"
---
[Documentação de](https://docs.microsoft.com/javascript/api/overview/azure/formrecognizer?view=azure-node-preview)  |  referência Código fonte [da biblioteca](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/)  |  [Pacote (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer)  |  [Amostras](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* Uma bolha de armazenamento azure que contém um conjunto de dados de treino. Consulte construir um conjunto de dados de [treino para um modelo personalizado](../../build-training-data-set.md) para dicas e opções para reunir o seu conjunto de dados de treino. Para este arranque rápido, pode utilizar os ficheiros sob a pasta **Train** do conjunto de dados da [amostra](https://go.microsoft.com/fwlink/?linkid=2090451).
* A versão atual do [Node.js](https://nodejs.org/)

## <a name="setting-up"></a>Configuração

### <a name="create-a-form-recognizer-azure-resource"></a>Criar um recurso Do Reconhecimento de Formulários Azure

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Criar variáveis de ambiente

[!INCLUDE [environment-variables](../environment-variables.md)]

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue para ela. 

```console
mkdir myapp && cd myapp
```

Executar o comando para criar uma aplicação de `npm init` nó com um `package.json` ficheiro. 

```console
npm init
```

Crie um ficheiro com o `index.js` nome, abra-o e importe as seguintes bibliotecas:

```javascript
const { FormRecognizerClient, AzureKeyCredential } = require("@azure/ai-form-recognizer");
const fs = require("fs");
```

Também carregue o ficheiro variável ambiental.

```javascript
// Load the .env file if it exists
require("dotenv").config();
```

### <a name="install-the-client-library"></a>Instale a biblioteca do cliente

Instale os `ai-form-recognizer` pacotes e `dotenv` npm:

```console
npm install @azure/ai-form-recognizer dotenv
```

O ficheiro da sua aplicação `package.json` será atualizado com as dependências.


<!-- 
    Object model
-->

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes Do Reconhecimento de Formulários para JavaScript:

* [Autenticar o cliente](#authenticate-the-client)
* [Reconhecer conteúdo de formulário](#recognize-form-content)
* [Reconhecer recibos](#recognize-receipts)
* [Preparar um modelo personalizado](#train-a-custom-model)
* [Analisar formulários com um modelo personalizado](#analyze-forms-with-a-custom-model)
* [Gerencie os seus modelos personalizados](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Na `main` função, crie variáveis para o ponto final e chave Azure do seu recurso. Se criou a variável ambiental depois de lançar a aplicação, terá de fechar e reabrir o editor, IDE ou shell para aceder à variável.

```javascript
// You will need to set these environment variables or edit the following values
const endpoint = process.env["FORM_RECOGNIZER_ENDPOINT"] || "<cognitive services endpoint>";
const apiKey = process.env["FORM_RECOGNIZER_KEY"] || "<api key>";
```

Em seguida, autenticar um objeto cliente utilizando as variáveis de subscrição que definiu. Utilizará um objeto **AzureKeyCredential,** para que, se necessário, possa atualizar a tecla API sem criar novos objetos de cliente. Também vai criar um objeto de cliente de treino.

```javascript
const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));

const trainingClient = client.getFormTrainingClient();
```

### <a name="call-client-specific-functions"></a>Ligue para funções específicas do cliente

O próximo bloco de código utiliza `main` os objetos do cliente para ligar para as funções de cada uma das principais tarefas do SDK do Reconhecimento de Formulários. Definirá estas funções mais tarde.

Também terá de adicionar referências aos URLs para os seus dados de treino e teste.
* Para recuperar o URL SAS para os seus dados de treino de modelo personalizado, abra o Microsoft Azure Storage Explorer, clique no seu recipiente e selecione Obter assinatura de **acesso partilhado**. Certifique-se de que as permissões **de Leitura** e **Lista** são verificadas e clique em **Criar**. Em seguida, copie o valor na secção **URL.** Deve ter a forma: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .
* Para obter um URL de um formulário para testar, pode usar os passos acima para obter o URL SAS de um documento individual no armazenamento de blob. Ou, pegue o URL de um documento localizado em outro lugar.
* Utilize o método acima para obter o URL de uma imagem de recibo também, ou use o URL de imagem da amostra fornecido.

> [!NOTE]
> Os fragmentos de código neste guia utilizam formulários remotos acessados por URLs. Se quiser processar documentos de formulário local, consulte os métodos relacionados na [documentação de referência](https://docs.microsoft.com/javascript/api/overview/azure/formrecognizer?view=azure-node-preview).


```javascript
const trainingDataUrl = "<url/path to the labeled training documents>";
const formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
const receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media/contoso-allinone.jpg";


// Call Form Recognizer scenarios:
await GetContent(recognizerClient, formUrl);
await AnalyzeReceipt(recognizerClient, receiptUrl);
modelId = await TrainModel(trainingClient, trainingDataUrl);
await AnalyzePdfForm(recognizerClient, modelId, formUrl);
await ManageModels(trainingClient, trainingDataUrl);
```

## <a name="recognize-form-content"></a>Reconhecer conteúdo de formulário

Pode usar o Reconhecimento de Formulários para reconhecer mesas, linhas e palavras em documentos, sem precisar de treinar um modelo. Para reconhecer o conteúdo de um ficheiro num determinado URI, utilize o método **beginRecogniseContentFromUrl.**

```javascript
async function GetContent( recognizerClient, invoiceUri)
{
    const poller = await client.beginRecognizeContentFromUrl(invoiceUri);
    await poller.pollUntilDone();
    const response = poller.getResult();
```

O valor devolvido é uma coleção de objetos **FormPage:** um para cada página no documento submetido. O código seguinte itera através destes objetos e imprime os pares de chaves/valor extraídos e os dados da tabela.

```javascript
    for (const page of response.pages) {
    console.log(
        `Page ${page.pageNumber}: width ${page.width} and height ${page.height} with unit ${page.unit}`
    );
        for (const table of page.tables) {
            for (const row of table.rows) {
                for (const cell of row.cells) {
                    console.log(`cell [${cell.rowIndex},${cell.columnIndex}] has text ${cell.text}`);
                }
            }
        }
    }
}
```

## <a name="recognize-receipts"></a>Reconhecer recibos

Esta secção demonstra como reconhecer e extrair campos comuns a partir de recibos dos EUA, utilizando um modelo de recibo pré-treinado.

Para reconhecer recibos de um URI, utilize o método **iniciar RecogniseReceiptsFromUrl.** O valor devolvido é uma coleção de objetos **RecognizedReceipt:** um para cada página no documento submetido. O código seguinte processa um recibo no URI dado e imprime os principais campos e valores para a consola.

```javascript
async function AnalyzeReceipt( client, receiptUri)
{
    const poller = await client.beginRecognizeReceiptsFromUrl(url, {
        includeTextDetails: true,
        onProgress: (state) => {
            console.log(`analyzing status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();


    const usReceipt = response.receipts[0];
    console.log("First receipt:");
    console.log(`Receipt type: ${usReceipt.receiptType}`);
    console.log(
        `Merchant Name: ${usReceipt.merchantName.value} (confidence: ${usReceipt.merchantName.confidence})`
    );
    console.log(
        `Transaction Date: ${usReceipt.transactionDate.value} (confidence: ${usReceipt.transactionDate.confidence})`
    );
    console.log("Receipt items:");
    console.log(`  name\tprice\tquantity\ttotalPrice`);
```

O próximo bloco de iterates de código através dos itens individuais detetados no recibo e imprime os seus dados para a consola.

```csharp
    for (const item of usReceipt.items) {
        const name = `${optionalToString(item.name.value)} (confidence: ${optionalToString(
            item.name.confidence
        )})`;
        const price = `${optionalToString(item.price.value)} (confidence: ${optionalToString(
            item.price.confidence
        )})`;
        const quantity = `${optionalToString(item.quantity.value)} (confidence: ${optionalToString(
            item.quantity.confidence
        )})`;
        const totalPrice = `${optionalToString(item.totalPrice.value)} (confidence: ${optionalToString(
            item.totalPrice.confidence
        )})`;
        console.log(`  ${name}\t${price}\t${quantity}\t${totalPrice}`);
    }
```

Esta função utiliza uma função de ajudante `optionalToString` . Defina esta função na raiz do seu script:

```javascript
function optionalToString(value) {
  return `${value || "<missing>"}`;
}
```


## <a name="train-a-custom-model"></a>Preparar um modelo personalizado

Esta secção demonstra como treinar um modelo com os seus próprios dados. Um modelo treinado pode obter dados estruturados que incluam as relações chave/valor no documento de formulário original. Depois de treinar o modelo, pode testar e retreiná-lo e eventualmente usá-lo para extrair dados de mais formas de acordo com as suas necessidades.

> [!NOTE]
> Também pode treinar modelos com uma interface gráfica do utilizador, como a ferramenta de rotulagem da [amostra 'Reconhecimento](../../quickstarts/label-tool.md)de Formulários'.

### <a name="train-a-model-without-labels"></a>Treine um modelo sem rótulos

Treine modelos personalizados para reconhecer todos os campos e valores encontrados nas suas formas personalizadas sem rotular manualmente os documentos de treino.

A função seguinte treina um modelo num determinado conjunto de documentos e imprime o estado do modelo na consola. 

```javascript
async function TrainModel(trainingClient, trainingDataUrl)
{
    const poller = await trainingClient.beginTraining(trainingDataUrl, false, {
        onProgress: (state) => {
            console.log(`training status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();
    
    if (!response) {
        throw new Error("Expecting valid response!");
    }
    
    console.log(`Model ID: ${response.modelId}`);
    console.log(`Status: ${response.status}`);
    console.log(`Created on: ${response.createdOn}`);
    console.log(`Last modified: ${response.lastModified}`);
```

O objeto **CustomFormModel** devolvido contém informações sobre os tipos de formulário que o modelo pode reconhecer e os campos que pode extrair de cada tipo de formulário. O bloco de código seguinte imprime esta informação para a consola.

```javascript
    if (response.models) {
        for (const submodel of response.models) {
            // since the training data is unlabeled, we are unable to return the accuracy of this model
            console.log("We have recognized the following fields");
            for (const key in submodel.fields) {
                const field = submodel.fields[key];
                console.log(`The model found field '${field.name}'`);
            }
        }
    }
```

Finalmente, este método devolve a identificação única do modelo.

```csharp
    return response.modelId;
}
```

### <a name="train-a-model-with-labels"></a>Treine um modelo com rótulos

Também pode treinar modelos personalizados rotulando manualmente os documentos de treino. Treinar com rótulos leva a um melhor desempenho em alguns cenários. Para treinar com etiquetas, é necessário dispor de ficheiros especiais de informação sobre* \<filename\> etiquetas (.pdf.labels.json*) no seu recipiente de armazenamento blob ao lado dos documentos de treino. A ferramenta de rotulagem da [amostra 'Reconhecimento](../../quickstarts/label-tool.md) de Formulários' fornece um UI para o ajudar a criar estes ficheiros de etiquetas. Uma vez que os tenha, pode chamar o método iniciar o **treino** com o parâmetro de etiquetas de *utilização* definido para `true` .

```javascript
async function TrainModelWithLabelsAsync(
    const poller = await trainingClient.beginTraining(trainingDataUrl, true, {
        onProgress: (state) => {
            console.log(`training status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const model = poller.getResult();
    
    return model.modelId;
}
```

## <a name="analyze-forms-with-a-custom-model"></a>Analisar formulários com um modelo personalizado

Esta secção demonstra como extrair informações chave/valor e outros conteúdos dos seus tipos de formulários personalizados, utilizando modelos treinados com os seus próprios formulários.

> [!IMPORTANT]
> Para implementar este cenário, já deve ter treinado um modelo para que possa passar o seu ID para o método abaixo. Consulte a secção [de modelos do Comboio.](#train-a-model-without-labels)

Utilizará o método **recomeçar RecogniseFormsFromUrl.** O valor devolvido é uma coleção de objetos **RecognizedForm:** um para cada página no documento submetido.

```javascript
// Analyze PDF form document at an accessible URL
async function AnalyzePdfForm(client, modelId, formUrl)
{    
    const poller = await client.beginRecognizeFormsFromUrl(modelId, formUrl, {
        onProgress: (state) => {
            console.log(`status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();
```

O código que se segue imprime os resultados da análise para a consola. Imprime cada campo reconhecido e valor correspondente, juntamente com uma pontuação de confiança.

```javascript
    console.log("Fields:");
    for (const fieldName in form.fields) {
        // each field is of type FormField
        const field = form.fields[fieldName];
        console.log(
            `Field ${fieldName} has value '${field.value}' with a confidence score of ${field.confidence}`
        );
    }
}
```

## <a name="manage-your-custom-models"></a>Gerencie os seus modelos personalizados

Esta secção demonstra como gerir os modelos personalizados armazenados na sua conta. O código seguinte faz todas as tarefas de gestão do modelo numa única função, como exemplo. Comece por copiar a assinatura da função abaixo:

```javascript
async function ManageModels(trainingClient, trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Verifique o número de modelos na conta de recurso FormRecogniser

O seguinte bloco de código verifica quantos modelos guardou na sua conta 'Reconhecimento de Formulários' e compara-o ao limite da conta.

```csharp
    // First, we see how many custom models we have, and what our limit is
    const accountProperties = await trainingClient.getAccountProperties();
    console.log(
        `Our account has ${accountProperties.count} custom models, and we can have at most ${accountProperties.limit} custom models`
    );
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Enumerar os modelos atualmente armazenados na conta de recursos

O bloco de código seguinte lista os modelos atuais na sua conta e imprime os seus dados para a consola. Também guarda uma referência ao primeiro modelo.

```javascript
    // Next, we get a paged async iterator of all of our custom models
    const result = trainingClient.listModels();

    // We could print out information about first ten models
    // and save the first model id for later use
    let i = 0;
    let firstModel;
    for await (const model of result) {
        console.log(`model ${i++}:`);
        console.log(model);
        if (i === 1) {
            firstModel = model;
        }
        if (i > 10) {
            break;
        }
    }
```

### <a name="get-a-specific-model-using-the-models-id"></a>Obtenha um modelo específico usando o ID do modelo

O bloco de código seguem o ID do modelo guardado na secção anterior e utiliza-o para obter detalhes sobre o modelo.

```csharp
    // Now we'll get the first custom model in the paged list
    const model = await trainingClient.getModel(firstModel.modelId);
    console.log(`Model Id: ${model.modelId}`);
    console.log(`Status: ${model.status}`);
    console.log("Documents used in training: [");
    for (const doc of model.trainingDocuments || []) {
        console.log(`  ${doc.documentName}`);
    }
    console.log("]");
```

### <a name="delete-a-model-from-the-resource-account"></a>Eliminar um modelo da conta de recursos

Também pode eliminar um modelo da sua conta fazendo referência ao seu ID. Este código elimina o modelo utilizado na secção anterior.

```javascript
    await client.deleteModel(firstModel.modelId);
    try {
        const deleted = await trainingClient.deleteModel(firstModel.modelId);
        console.log(deleted);
    } catch (err) {
        // Expected
        console.log(`Model with id ${firstModel.modelId} has been deleted`);
    }
}
```


## <a name="run-the-application"></a>Executar a aplicação

Execute o pedido com o `node` comando no seu ficheiro de arranque rápido.

```console
node index.js
```

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos associados ao mesmo.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Resolução de Problemas

### <a name="enable-logs"></a>Ativar registos
Pode definir a seguinte variável ambiental para ver registos de depuração ao utilizar esta biblioteca.

```
export DEBUG=azure*
```

Para obter instruções mais detalhadas sobre como ativar os registos, consulte os [ @azure/logger documentos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger)da embalagem .


## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, usou a biblioteca de clientes Form Recogniser Python para treinar modelos e analisar formas de diferentes maneiras. Em seguida, aprenda dicas para criar um melhor conjunto de dados de treino e produzir modelos mais precisos.

> [!div class="nextstepaction"]
> [Criar um conjunto de dados de preparação](../../build-training-data-set.md)

* [O que é o Reconhecedor de Formato?](../../overview.md)
* O código de amostra deste guia (e muito mais) pode ser encontrado no [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples).