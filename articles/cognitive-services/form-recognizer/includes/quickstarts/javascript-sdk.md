---
title: 'Quickstart: Biblioteca de clientes do Reconhecimento de Formulários para JavaScript'
description: Utilize a biblioteca de clientes Do Reconhecimento de Formulários para o JavaScript para criar uma aplicação de processamento de formulários que extrai pares de chaves/valor e dados de tabela dos seus documentos personalizados.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 3bd3ce63339f7eecef520a5496e5c4a3a64efd18
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356567"
---
> [!IMPORTANT]
> * O código deste artigo utiliza métodos sincronizados e armazenamento de credenciais não garantidos por razões de simplicidade. Consulte a documentação de referência abaixo. 

[Documentação de referência](../../index.yml)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/)  |  [Pacote (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer)  |  [Amostras](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* A versão atual de [Node.js](https://nodejs.org/)
* Uma bolha de armazenamento Azure que contém um conjunto de dados de treino. Consulte [Construir um conjunto de dados de treino para um modelo personalizado](../../build-training-data-set.md) para dicas e opções para reunir o seu conjunto de dados de treino. Para este arranque rápido, pode utilizar os ficheiros sob a pasta **Train** do conjunto de [dados](https://go.microsoft.com/fwlink/?linkid=2090451) da amostra (descarregar e extrair *sample_data.zip*).
* Assim que tiver a sua subscrição Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" Crie um recurso De Reconhecimento de "  target="_blank"> Formulários crie um recurso De Reconhecimento de <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Formulários no portal Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API do Reconhecimento de Formulários. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-nodejs-application"></a>Criar uma nova aplicação Node.js

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue até ela. 

```console
mkdir myapp && cd myapp
```

Executar o `npm init` comando para criar uma aplicação de nó com um `package.json` ficheiro. 

```console
npm init
```

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Instale o `ai-form-recognizer` pacote NPM:

```console
npm install @azure/ai-form-recognizer
```

O ficheiro da sua aplicação `package.json` será atualizado com as dependências.

Criar um ficheiro `index.js` chamado, abri-lo e importar as seguintes bibliotecas:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_imports)]


> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js)que contém os exemplos de código neste arranque rápido.

Crie variáveis para o ponto final e chave Azure do seu recurso. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Aceda ao portal do Azure. Se o recurso [nome do produto] que criou na secção **Pré-Requisitos** implementado com sucesso, clique no botão **'Ir a Recursos'** nos **Passos Seguintes**. Pode encontrar a sua chave e ponto final na **página chave e ponto final** do recurso, sob **gestão de recursos.** 
>
> Lembre-se de remover a chave do seu código quando terminar, e nunca postá-la publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Consulte o artigo [de segurança](../../../cognitive-services-security.md) dos Serviços Cognitivos para obter mais informações.

## <a name="object-model"></a>Modelo de objeto 

Com o Form Recogniser, pode criar dois tipos de clientes diferentes. O primeiro, `FormRecognizerClient` é usado para consultar o serviço para campos de forma reconhecidos e conteúdos. A segunda é `FormTrainingClient` a utilização para criar e gerir modelos personalizados que pode usar para melhorar o reconhecimento. 

### <a name="formrecognizerclient"></a>FormulárioRecognizerClient
`FormRecognizerClient` Fornece operações para:

 * Reconhecendo campos de formulários e conteúdos usando modelos personalizados treinados para reconhecer os seus formulários personalizados. Estes valores são devolvidos numa coleção de `RecognizedForm` objetos.
 * Reconhecendo o conteúdo da forma, incluindo tabelas, linhas e palavras, sem a necessidade de formar um modelo. O conteúdo do formulário é devolvido numa coleção de `FormPage` objetos.
 * Reconhecendo campos comuns a partir de recibos, utilizando um modelo de recibo pré-treinado no serviço De Reconhecimento de Formulários. Estes campos e meta-dados são devolvidos numa coleção de `RecognizedReceipt` .

### <a name="formtrainingclient"></a>FormaTrainingClient
`FormTrainingClient` Fornece operações para:

* Treinando modelos personalizados para reconhecer todos os campos e valores encontrados nas suas formas personalizadas. A `CustomFormModel` é devolvido indicando os tipos de formulário que o modelo reconhecerá, e os campos que extrairá para cada tipo de formulário. Consulte a [documentação do serviço sobre a formação de modelos não rotulados](#train-a-model-without-labels) para obter uma explicação mais detalhada da criação de um conjunto de dados de formação.
* Treinando modelos personalizados para reconhecer campos e valores específicos que especifica, rotulando os seus formulários personalizados. A `CustomFormModel` é devolvido indicando os campos que o modelo irá extrair, bem como a precisão estimada para cada campo. Consulte a [documentação do serviço sobre a formação de modelos rotulados](#train-a-model-with-labels) para obter uma explicação mais detalhada da aplicação de rótulos a um conjunto de dados de formação.
* Gestão de modelos criados na sua conta.
* Copiar um modelo personalizado de um recurso de Reconhecimento de Formulário para outro.

> [!NOTE]
> Os modelos também podem ser treinados utilizando uma interface gráfica do utilizador, como a [Ferramenta de Rotulagem do Reconhecimento de Formulários.](../../quickstarts/label-tool.md)


## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer as seguintes tarefas com a biblioteca do cliente do Reconhecimento de Formulários para JavaScript:

* [Autenticar o cliente](#authenticate-the-client)
* [Reconhecer o conteúdo da forma](#recognize-form-content)
* [Reconhecer recibos](#recognize-receipts)
* [Preparar um modelo personalizado](#train-a-custom-model)
* [Analisar formas com um modelo personalizado](#analyze-forms-with-a-custom-model)
* [Gerir os seus modelos personalizados](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>Autenticar o cliente



Autenticar um objeto de cliente utilizando as variáveis de subscrição que definiu. Você usará um `AzureKeyCredential` objeto, para que, se necessário, possa atualizar a tecla API sem criar novos objetos de cliente. Também vai criar um objeto de cliente de formação.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_auth)]


## <a name="get-assets-for-testing"></a>Obter ativos para testes

Também terá de adicionar referências aos URLs para os seus dados de treino e teste.
* Para recuperar o URL SAS para os seus dados de treino de modelo personalizados, abra o Microsoft Azure Storage Explorer, clique com o botão direito no seu recipiente e **selecione Obter assinatura de acesso partilhado**. Certifique-se de que as permissões **de Leitura** e **Lista** são verificadas e clique em **Criar**. Em seguida, copie o valor na secção **URL.** Deve ter o formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Utilize a amostra e as imagens de receção incluídas nas amostras abaixo (também disponíveis no [GitHub)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/test-assets)ou pode utilizar os passos acima para obter o URL SAS de um documento individual no armazenamento de bolhas. 


## <a name="recognize-form-content"></a>Reconhecer o conteúdo da forma

Pode utilizar o Form Recogniser para reconhecer tabelas, linhas e palavras em documentos, sem precisar de treinar um modelo. Para reconhecer o conteúdo de um ficheiro num dado URI, utilize o `beginRecognizeContentFromUrl` método.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_getcontent)]


> [!TIP]
> Também pode obter conteúdo de um ficheiro local. Consulte os métodos [FormRecognizerClient,](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-latest) tais como **startRecognizeContent**. Ou, consulte o código de amostra no [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) para cenários que envolvam imagens locais.

### <a name="output"></a>Saída

```console
Page 1: width 8.5 and height 11 with unit inch
cell [0,0] has text Invoice Number
cell [0,1] has text Invoice Date
cell [0,2] has text Invoice Due Date
cell [0,3] has text Charges
cell [0,5] has text VAT ID
cell [1,0] has text 34278587
cell [1,1] has text 6/18/2017
cell [1,2] has text 6/24/2017
cell [1,3] has text $56,651.49
cell [1,5] has text PT
```

## <a name="recognize-receipts"></a>Reconhecer recibos

Esta secção demonstra como reconhecer e extrair campos comuns a partir de recibos dos EUA, utilizando um modelo de recibo pré-treinado.

Para reconhecer os recibos de um URI, utilize o `beginRecognizeReceiptsFromUrl` método. O código seguinte processa um recibo no URI dado e imprime os principais campos e valores para a consola.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_receipts)]

> [!TIP]
> Também pode reconhecer imagens de recibo local. Consulte os métodos [FormRecognizerClient,](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-latest) tais como **startRecognizeReceipts**. Ou, consulte o código de amostra no [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) para cenários que envolvam imagens locais.

### <a name="output"></a>Saída

```console
status: notStarted
status: running
status: succeeded
First receipt:
  Receipt Type: 'Itemized', with confidence of 0.659
  Merchant Name: 'Contoso Contoso', with confidence of 0.516
  Transaction Date: 'Sun Jun 09 2019 17:00:00 GMT-0700 (Pacific Daylight Time)', with confidence of 0.985
    Item Name: '8GB RAM (Black)', with confidence of 0.916
    Item Name: 'SurfacePen', with confidence of 0.858
  Total: '1203.39', with confidence of 0.774
```

## <a name="train-a-custom-model"></a>Preparar um modelo personalizado

Esta secção demonstra como treinar um modelo com os seus próprios dados. Um modelo treinado pode obter dados estruturados de saída que incluam as relações chave/valor no documento original do formulário. Depois de treinar o modelo, pode testá-lo e reforçá-lo e eventualmente usá-lo para extrair dados de mais formas de acordo com as suas necessidades.

> [!NOTE]
> Também pode treinar modelos com uma interface gráfica do utilizador, como a ferramenta de rotulagem da [amostra do Form Recogniser.](../../quickstarts/label-tool.md)

### <a name="train-a-model-without-labels"></a>Treine um modelo sem rótulos

Treine modelos personalizados para reconhecer todos os campos e valores encontrados nas suas formas personalizadas sem rotular manualmente os documentos de treino.

A função seguinte treina um modelo num determinado conjunto de documentos e imprime o estado do modelo à consola. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_train)]


### <a name="output"></a>Saída

Esta é a saída para um modelo treinado com os dados de formação disponíveis no [JavaScript SDK](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer). Esta produção de amostra foi truncada para a legibilidade.

```console
training status: creating
training status: ready
Model ID: 9d893595-1690-4cf2-a4b1-fbac0fb11909
Status: ready
Training started on: Thu Aug 20 2020 20:27:26 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:27:37 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'field-0'
The model found field 'field-1'
The model found field 'field-2'
The model found field 'field-3'
The model found field 'field-4'
The model found field 'field-5'
The model found field 'field-6'
The model found field 'field-7'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: 
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: 
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: 
...
```

### <a name="train-a-model-with-labels"></a>Treine um modelo com rótulos

Também pode treinar modelos personalizados rotulando manualmente os documentos de treino. O treino com rótulos leva a um melhor desempenho em alguns cenários. Para treinar com etiquetas, precisa de ter ficheiros de informações especiais de etiquetas `\<filename\>.pdf.labels.json` () no seu recipiente de armazenamento de bolhas ao lado dos documentos de treino. A [ferramenta de rotulagem da amostra do Reconhecimento de Formulários](../../quickstarts/label-tool.md) fornece uma UI para ajudá-lo a criar estes ficheiros de etiqueta. Uma vez que os tenha, pode ligar para o `beginTraining` método com o parâmetro definido para `uselabels` `true` .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_trainlabels)]


### <a name="output"></a>Saída 

Esta é a saída para um modelo treinado com os dados de formação disponíveis no [JavaScript SDK](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples). Esta produção de amostra foi truncada para a legibilidade.

```console
training status: creating
training status: ready
Model ID: 789b1b37-4cc3-4e36-8665-9dde68618072
Status: ready
Training started on: Thu Aug 20 2020 20:30:37 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:30:43 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'CompanyAddress'
The model found field 'CompanyName'
The model found field 'CompanyPhoneNumber'
The model found field 'DatedAs'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
...
```

## <a name="analyze-forms-with-a-custom-model"></a>Analisar formas com um modelo personalizado

Esta secção demonstra como extrair informações de chave/valor e outros conteúdos dos seus tipos de formulários personalizados, utilizando modelos treinados com os seus próprios formulários.

> [!IMPORTANT]
> Para implementar este cenário, já deve ter treinado um modelo para que possa passar o seu ID para o método abaixo. Consulte a secção [Modelo train.](#train-a-model-without-labels)

Vais usar o `beginRecognizeCustomFormsFromUrl` método. O valor devolvido é uma coleção de `RecognizedForm` objetos: um para cada página no documento submetido.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_analyze)]

> [!TIP]
> Também pode analisar ficheiros locais. Consulte os métodos [FormRecognizerClient,](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-latest) tais como **startRecognizeCustomForms**. Ou, consulte o código de amostra no [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) para cenários que envolvam imagens locais.


### <a name="output"></a>Saída

```console
status: notStarted
status: succeeded
Forms:
custom:form, page range: [object Object]
Pages:
Page number: 1
Tables
cell (0,0) Invoice Number
cell (0,1) Invoice Date
cell (0,2) Invoice Due Date
cell (0,3) Charges
cell (0,5) VAT ID
cell (1,0) 34278587
cell (1,1) 6/18/2017
cell (1,2) 6/24/2017
cell (1,3) $56,651.49
cell (1,5) PT
Fields:
Field Merchant has value 'Invoice For:' with a confidence score of 0.116
Field CompanyPhoneNumber has value '$56,651.49' with a confidence score of 0.249
Field VendorName has value 'Charges' with a confidence score of 0.145
Field CompanyAddress has value '1 Redmond way Suite 6000 Redmond, WA' with a confidence score of 0.258
Field CompanyName has value 'PT' with a confidence score of 0.245
Field Website has value '99243' with a confidence score of 0.114
Field DatedAs has value 'undefined' with a confidence score of undefined
Field Email has value 'undefined' with a confidence score of undefined
Field PhoneNumber has value 'undefined' with a confidence score of undefined
Field PurchaseOrderNumber has value 'undefined' with a confidence score of undefined
Field Quantity has value 'undefined' with a confidence score of undefined
Field Signature has value 'undefined' with a confidence score of undefined
Field Subtotal has value 'undefined' with a confidence score of undefined
Field Tax has value 'undefined' with a confidence score of undefined
Field Total has value 'undefined' with a confidence score of undefined
```

## <a name="manage-your-custom-models"></a>Gerir os seus modelos personalizados

Esta secção demonstra como gerir os modelos personalizados armazenados na sua conta. O seguinte código faz todas as tarefas de gestão do modelo numa única função, como exemplo. 

### <a name="get-number-of-models"></a>Obtenha o número de modelos

O seguinte bloco de código obtém o número de modelos atualmente na sua conta.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_count)]


### <a name="get-list-of-models-in-account"></a>Obtenha a lista de modelos na conta

O bloco de códigos que se segue fornece uma lista completa dos modelos disponíveis na sua conta, incluindo informações sobre quando o modelo foi criado e o seu estado atual.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_list)]


### <a name="output"></a>Saída

```console
model 0:
{
  modelId: '453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e',
  status: 'invalid',
  trainingStartedOn: 2020-08-20T22:28:52.000Z,
  trainingCompletedOn: 2020-08-20T22:28:53.000Z
}
model 1:
{
  modelId: '628739de-779c-473d-8214-d35c72d3d4f7',
  status: 'ready',
  trainingStartedOn: 2020-08-20T23:16:51.000Z,
  trainingCompletedOn: 2020-08-20T23:16:59.000Z
}
model 2:
{
  modelId: '789b1b37-4cc3-4e36-8665-9dde68618072',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:30:37.000Z,
  trainingCompletedOn: 2020-08-21T03:30:43.000Z
}
model 3:
{
  modelId: '9d893595-1690-4cf2-a4b1-fbac0fb11909',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:27:26.000Z,
  trainingCompletedOn: 2020-08-21T03:27:37.000Z
}
```

### <a name="get-list-of-model-ids-by-page"></a>Obtenha a lista de IDs do modelo por página

Este bloco de códigos fornece uma lista paginada de modelos e IDs de modelos.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_listpages)]


### <a name="output"></a>Saída

```console
model 1: 453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
model 2: 628739de-779c-473d-8214-d35c72d3d4f7
model 3: 789b1b37-4cc3-4e36-8665-9dde68618072
```

### <a name="get-model-by-id"></a>Obter modelo por ID

A seguinte função requer um ID do modelo e obtém o objeto do modelo correspondente. Esta função não é chamada por defeito.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_getmodel)]


### <a name="delete-a-model-from-the-resource-account"></a>Eliminar um modelo da conta de recursos

Também pode eliminar um modelo da sua conta fazendo referência ao seu ID. Esta função elimina o modelo com o ID dado. Esta função não é chamada por defeito.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_delete)]


### <a name="output"></a>Saída

```console
Model with id 789b1b37-4cc3-4e36-8665-9dde68618072 has been deleted
```

## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o `node` comando no seu ficheiro quickstart.

```console
node index.js
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="enable-logs"></a>Ativar registos

Pode definir a seguinte variável ambiente para ver registos de depurg ao utilizar esta biblioteca.

```
export DEBUG=azure*
```

Para obter instruções mais detalhadas sobre como ativar os registos, consulte os [ @azure/logger documentos de embalagem](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger).

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, usou a biblioteca de clientes Do Reconhecimento de Formulários JavaScript para treinar modelos e analisar formas de diferentes maneiras. Em seguida, aprenda dicas para criar um melhor conjunto de dados de treino e produzir modelos mais precisos.

> [!div class="nextstepaction"]
> [Criar um conjunto de dados de preparação](../../build-training-data-set.md)

## <a name="see-also"></a>Ver também

* [O que é o Reconhecedor de Formato?](../../overview.md)
* O código de amostra deste guia pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js).