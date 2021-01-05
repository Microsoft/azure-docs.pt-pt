---
title: 'Quickstart: Biblioteca de clientes do Reconhecimento de Formulários para Python'
description: Utilize a biblioteca de clientes Do Reconhecimento de Formulários para Python para criar uma aplicação de processamento de formulários que extrai pares de chaves/valor e dados de tabela dos seus documentos personalizados.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: 9762ca79f73b3333045d1c11376ab315aac2d55e
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97808662"
---
> [!IMPORTANT]
> * O código deste artigo utiliza métodos sincronizados e armazenamento de credenciais não garantidos por razões de simplicidade. Consulte a documentação de referência abaixo. 

[Documentação de referência](/python/api/azure-ai-formrecognizer)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer)  |  [Pacote (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/)  |  [Amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Uma bolha de armazenamento Azure que contém um conjunto de dados de treino. Consulte [Construir um conjunto de dados de treino para um modelo personalizado](../../build-training-data-set.md) para dicas e opções para reunir o seu conjunto de dados de treino. Para este arranque rápido, pode utilizar os ficheiros sob a pasta **Train** do conjunto de [dados](https://go.microsoft.com/fwlink/?linkid=2090451) da amostra (descarregar e extrair *sample_data.zip*).
* Assim que tiver a sua subscrição Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" Crie um recurso De Reconhecimento de "  target="_blank"> Formulários crie um recurso De Reconhecimento de <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Formulários no portal Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API do Reconhecimento de Formulários. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Depois de instalar o Python, pode instalar a versão mais recente da biblioteca do cliente Do Reconhecimento de Formulários com:

#### <a name="version-20"></a>[versão 2.0](#tab/ga)

```console
pip install azure-ai-formrecognizer
```

> [!NOTE]
> O mais recente Reconhecimento de Formulários reflete a versão API 2.0

#### <a name="version-21-preview"></a>[versão 2.1 pré-visualização](#tab/preview)

```console
pip install azure-ai-formrecognizer --pre
```

> [!NOTE]
> A pré-visualização do SDK do Reconhecimento de Formulários reflete a versão API 2.1

---

### <a name="create-a-new-python-application"></a>Criar uma nova aplicação python

Crie uma nova aplicação Python no seu editor preferido ou IDE. Em seguida, importe as seguintes bibliotecas.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_imports)]

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py)que contém os exemplos de código neste arranque rápido.


Crie variáveis para o ponto final e chave Azure do seu recurso. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_creds)]


## <a name="object-model"></a>Modelo de objeto 

Com o Form Recogniser, pode criar dois tipos de clientes diferentes. O primeiro, `form_recognizer_client` é usado para consultar o serviço para campos de forma reconhecidos e conteúdos. A segunda é `form_training_client` a utilização para criar e gerir modelos personalizados que pode usar para melhorar o reconhecimento. 

### <a name="formrecognizerclient"></a>FormulárioRecognizerClient
`form_recognizer_client` Fornece operações para:

 * Reconhecendo campos de formulários e conteúdos usando modelos personalizados treinados para reconhecer os seus formulários personalizados. 
 * Reconhecendo o conteúdo da forma, incluindo tabelas, linhas e palavras, sem a necessidade de formar um modelo. 
 * Reconhecendo campos comuns a partir de recibos, utilizando um modelo de recibo pré-treinado no serviço De Reconhecimento de Formulários.

### <a name="formtrainingclient"></a>FormaTrainingClient
`form_training_client` Fornece operações para:

* Treinando modelos personalizados para reconhecer todos os campos e valores encontrados nas suas formas personalizadas. Consulte a [documentação do serviço sobre a formação de modelos não rotulados](#train-a-model-without-labels) para obter uma explicação mais detalhada da criação de um conjunto de dados de formação.
* Treinando modelos personalizados para reconhecer campos e valores específicos que especifica, rotulando os seus formulários personalizados. Consulte a [documentação do serviço sobre a formação de modelos rotulados](#train-a-model-with-labels) para obter uma explicação mais detalhada da aplicação de rótulos a um conjunto de dados de formação.
* Gestão de modelos criados na sua conta.
* Copiar um modelo personalizado de um recurso de Reconhecimento de Formulário para outro.

> [!NOTE]
> Os modelos também podem ser treinados utilizando uma interface gráfica do utilizador, como a [Ferramenta de Rotulagem do Reconhecimento de Formulários.](../../quickstarts/label-tool.md)

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes Do Reconhecimento de Formulários para Python:

#### <a name="version-20"></a>[versão 2.0](#tab/ga)

* [Autenticar o cliente](#authenticate-the-client)
* [Reconhecer o conteúdo da forma](#recognize-form-content)
* [Reconhecer recibos](#recognize-receipts)
* [Preparar um modelo personalizado](#train-a-custom-model)
* [Analisar formas com um modelo personalizado](#analyze-forms-with-a-custom-model)
* [Gerir os seus modelos personalizados](#manage-your-custom-models)

#### <a name="version-21-preview"></a>[versão 2.1 pré-visualização](#tab/preview)

* [Autenticar o cliente](#authenticate-the-client)
* [Reconhecer o conteúdo da forma](#recognize-form-content)
* [Reconhecer recibos](#recognize-receipts)
* [Reconhecer cartões de visita](#recognize-business-cards)
* [Reconhecer faturas](#recognize-invoices)
* [Preparar um modelo personalizado](#train-a-custom-model)
* [Analisar formas com um modelo personalizado](#analyze-forms-with-a-custom-model)
* [Gerir os seus modelos personalizados](#manage-your-custom-models)

---

## <a name="authenticate-the-client"></a>Autenticar o cliente

Aqui, autenticará dois objetos clientes utilizando as variáveis de subscrição acima definidas. Utilizará um objeto **AzureKeyCredential,** para que, se necessário, possa atualizar a tecla API sem criar novos objetos de cliente.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_auth)]


## <a name="get-assets-for-testing"></a>Obter ativos para testes

Terá de adicionar referências aos URLs para os seus dados de treino e teste.
* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]
  
   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Recuperação de URL SAS":::
* Utilize a amostra e as imagens de receção incluídas nas amostras abaixo (também disponível no [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms) ou pode utilizar os passos acima para obter o URL SAS de um documento individual no armazenamento de bolhas. 

> [!NOTE]
> Os fragmentos de código neste guia utilizam formulários remotos acedidos por URLs. Se pretender processar documentos de formulário local, consulte os métodos relacionados na [documentação](/python/api/azure-ai-formrecognizer) de referência e [nas amostras.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="recognize-form-content"></a>Reconhecer o conteúdo da forma

Pode utilizar o Form Recogniser para reconhecer tabelas, linhas e palavras em documentos, sem precisar de treinar um modelo.

Para reconhecer o conteúdo de um ficheiro num dado URL, utilize o `begin_recognize_content_from_url` método. O valor devolvido é uma coleção de `FormPage` objetos: um para cada página no documento submetido. O código seguinte itera através destes objetos e imprime os pares de chave/valor extraídos e os dados de tabela.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_getcontent)]

> [!TIP]
> Também pode obter conteúdo a partir de imagens locais. Consulte os métodos [FormRecognizerClient,](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python) tais como `begin_recognize_content` . Ou, consulte o código de amostra no [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) para cenários que envolvam imagens locais.

### <a name="output"></a>Saída

```console
Table found on page 1:
Cell text: Invoice Number
Location: [Point(x=0.5075, y=2.8088), Point(x=1.9061, y=2.8088), Point(x=1.9061, y=3.3219), Point(x=0.5075, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Date
Location: [Point(x=1.9061, y=2.8088), Point(x=3.3074, y=2.8088), Point(x=3.3074, y=3.3219), Point(x=1.9061, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Due Date
Location: [Point(x=3.3074, y=2.8088), Point(x=4.7074, y=2.8088), Point(x=4.7074, y=3.3219), Point(x=3.3074, y=3.3219)]
Confidence score: 1.0

Cell text: Charges
Location: [Point(x=4.7074, y=2.8088), Point(x=5.386, y=2.8088), Point(x=5.386, y=3.3219), Point(x=4.7074, y=3.3219)]
Confidence score: 1.0
...

```

## <a name="recognize-receipts"></a>Reconhecer recibos

Esta secção demonstra como reconhecer e extrair campos comuns a partir de recibos dos EUA, utilizando um modelo de recibo pré-treinado. Para reconhecer os recibos de um URL, utilize o `begin_recognize_receipts_from_url` método. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_receipts)]

> [!TIP]
> Também pode reconhecer imagens de recibo local. Consulte os métodos [FormRecognizerClient,](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python) tais como `begin_recognize_receipts` . Ou, consulte o código de amostra no [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) para cenários que envolvam imagens locais.

### <a name="output"></a>Saída

```console
ReceiptType: Itemized has confidence 0.659
MerchantName: Contoso Contoso has confidence 0.516
MerchantAddress: 123 Main Street Redmond, WA 98052 has confidence 0.986
MerchantPhoneNumber: None has confidence 0.99
TransactionDate: 2019-06-10 has confidence 0.985
TransactionTime: 13:59:00 has confidence 0.968
Receipt Items:
...Item #1
......Name: 8GB RAM (Black) has confidence 0.916
......TotalPrice: 999.0 has confidence 0.559
...Item #2
......Quantity: None has confidence 0.858
......Name: SurfacePen has confidence 0.858
......TotalPrice: 99.99 has confidence 0.386
Subtotal: 1098.99 has confidence 0.964
Tax: 104.4 has confidence 0.713
Total: 1203.39 has confidence 0.774
```


## <a name="recognize-business-cards"></a>Reconhecer cartões de visita

#### <a name="version-20"></a>[versão 2.0](#tab/ga)

> [!IMPORTANT]
> Esta funcionalidade não está disponível na versão API selecionada.

#### <a name="version-21-preview"></a>[versão 2.1 pré-visualização](#tab/preview)

Esta secção demonstra como reconhecer e extrair campos comuns de cartões de visita ingleses, utilizando um modelo pré-treinado. Para reconhecer cartões de visita de um URL, utilize o `begin_recognize_business_cards_from_url` método. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_bc)]

> [!TIP]
> Também pode reconhecer imagens de cartões de visita locais. Consulte os métodos [FormRecognizerClient,](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python) tais como `begin_recognize_business_cards` . Ou, consulte o código de amostra no [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) para cenários que envolvam imagens locais.

---

## <a name="recognize-invoices"></a>Reconhecer faturas

#### <a name="version-20"></a>[versão 2.0](#tab/ga)

> [!IMPORTANT]
> Esta funcionalidade não está disponível na versão API selecionada.

#### <a name="version-21-preview"></a>[versão 2.1 pré-visualização](#tab/preview)

Esta secção demonstra como reconhecer e extrair campos comuns das faturas de venda, utilizando um modelo pré-treinado. Para reconhecer faturas de um URL, utilize o `begin_recognize_invoices_from_url` método. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_invoice)]

> [!TIP]
> Também pode reconhecer imagens de fatura local. Consulte os métodos [FormRecognizerClient,](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python) tais como `begin_recognize_invoices` . Ou, consulte o código de amostra no [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) para cenários que envolvam imagens locais.

---

## <a name="train-a-custom-model"></a>Preparar um modelo personalizado

Esta secção demonstra como treinar um modelo com os seus próprios dados. Um modelo treinado pode obter dados estruturados de saída que incluam as relações chave/valor no documento original do formulário. Depois de treinar o modelo, pode testá-lo e reforçá-lo e eventualmente usá-lo para extrair dados de mais formas de acordo com as suas necessidades.

> [!NOTE]
> Também pode treinar modelos com uma interface gráfica do utilizador, como a ferramenta de rotulagem da [amostra do Form Recogniser.](../../quickstarts/label-tool.md)

### <a name="train-a-model-without-labels"></a>Treine um modelo sem rótulos

Treine modelos personalizados para reconhecer todos os campos e valores encontrados nas suas formas personalizadas sem rotular manualmente os documentos de treino.

O seguinte código utiliza o cliente de formação com a `begin_training` função para treinar um modelo num determinado conjunto de documentos. O objeto devolvido `CustomFormModel` contém informações sobre os tipos de formulários que o modelo pode reconhecer e os campos que pode extrair de cada tipo de formulário. O bloco de código que se segue imprime esta informação para a consola.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_train)]


### <a name="output"></a>Saída

Esta é a saída para um modelo treinado com os dados de treino disponíveis no [Python SDK.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training)

```console
Model ID: 628739de-779c-473d-8214-d35c72d3d4f7
Status: ready
Training started on: 2020-08-20 23:16:51+00:00
Training completed on: 2020-08-20 23:16:59+00:00

Recognized fields:
The submodel with form type 'form-0' has recognized the following fields: Additional Notes:, Address:, Company Name:, Company Phone:, Dated As:, Details, Email:, Hero Limited, Name:, Phone:, Purchase Order, Purchase Order #:, Quantity, SUBTOTAL, Seattle, WA 93849 Phone:, Shipped From, Shipped To, TAX, TOTAL, Total, Unit Price, Vendor Name:, Website:
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

### <a name="train-a-model-with-labels"></a>Treine um modelo com rótulos

Também pode treinar modelos personalizados rotulando manualmente os documentos de treino. O treino com rótulos leva a um melhor desempenho em alguns cenários. A `CustomFormModel` devolvição indica os campos que o modelo pode extrair, juntamente com a sua precisão estimada em cada campo. O bloco de código que se segue imprime esta informação para a consola.

> [!IMPORTANT]
> Para treinar com etiquetas, precisa de ter ficheiros de informações especiais de etiquetas `\<filename\>.pdf.labels.json` () no seu recipiente de armazenamento de bolhas ao lado dos documentos de treino. A [ferramenta de rotulagem da amostra do Reconhecimento de Formulários](../../quickstarts/label-tool.md) fornece uma UI para ajudá-lo a criar estes ficheiros de etiqueta. Uma vez que os tenha, pode ligar para a `begin_training` função com o *parâmetro use_training_labels* definido para `true` .

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_trainlabels)]

### <a name="output"></a>Saída

Esta é a saída para um modelo treinado com os dados de treino disponíveis no [Python SDK.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training)

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91

Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00

Recognized fields:
The submodel with form type 'form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91' has recognized the following fields: CompanyAddress, CompanyName, CompanyPhoneNumber, DatedAs, Email, Merchant, PhoneNumber, PurchaseOrderNumber, Quantity, Signature, Subtotal, Tax, Total, VendorName, Website
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

## <a name="analyze-forms-with-a-custom-model"></a>Analisar formas com um modelo personalizado

Esta secção demonstra como extrair informações de chave/valor e outros conteúdos dos seus tipos de formulários personalizados, utilizando modelos treinados com os seus próprios formulários.

> [!IMPORTANT]
> Para implementar este cenário, já deve ter treinado um modelo para que possa passar o seu ID para o método abaixo. Consulte a secção [Modelo train.](#train-a-model-without-labels)

Vais usar o `begin_recognize_custom_forms_from_url` método. O valor devolvido é uma coleção de `RecognizedForm` objetos: um para cada página no documento submetido. O código seguinte imprime os resultados da análise para a consola. Imprime cada campo reconhecido e valor correspondente, juntamente com uma pontuação de confiança.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_analyze)]

> [!TIP]
> Também pode analisar imagens locais. Consulte os métodos [FormRecognizerClient,](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python) tais como `begin_recognize_custom_forms` . Ou, consulte o código de amostra no [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) para cenários que envolvam imagens locais.


### <a name="output"></a>Saída

Utilizando o modelo a partir do exemplo anterior, é fornecida a seguinte saída.

```console
Form type: form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Field 'Merchant' has label 'Merchant' with value 'Invoice For:' and a confidence score of 0.116
Field 'CompanyAddress' has label 'CompanyAddress' with value '1 Redmond way Suite 6000 Redmond, WA' and a confidence score of 0.258
Field 'Website' has label 'Website' with value '99243' and a confidence score of 0.114
Field 'VendorName' has label 'VendorName' with value 'Charges' and a confidence score of 0.145
Field 'CompanyPhoneNumber' has label 'CompanyPhoneNumber' with value '$56,651.49' and a confidence score of 0.249
Field 'CompanyName' has label 'CompanyName' with value 'PT' and a confidence score of 0.245
Field 'DatedAs' has label 'DatedAs' with value 'None' and a confidence score of None
Field 'Email' has label 'Email' with value 'None' and a confidence score of None
Field 'PhoneNumber' has label 'PhoneNumber' with value 'None' and a confidence score of None
Field 'PurchaseOrderNumber' has label 'PurchaseOrderNumber' with value 'None' and a confidence score of None
Field 'Quantity' has label 'Quantity' with value 'None' and a confidence score of None
Field 'Signature' has label 'Signature' with value 'None' and a confidence score of None
Field 'Subtotal' has label 'Subtotal' with value 'None' and a confidence score of None
Field 'Tax' has label 'Tax' with value 'None' and a confidence score of None
Field 'Total' has label 'Total' with value 'None' and a confidence score of None
```

## <a name="manage-your-custom-models"></a>Gerir os seus modelos personalizados

Esta secção demonstra como gerir os modelos personalizados armazenados na sua conta. 

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Verifique o número de modelos na conta de recursos FormRecognizer

O bloco de códigos que se segue verifica quantos modelos guardou na sua conta Desemaçador de Formulários e compara-os ao limite da conta.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_count)]


### <a name="output"></a>Saída

```console
Our account has 5 custom models, and we can have at most 5000 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Listar os modelos atualmente armazenados na conta de recursos

O bloco de códigos que se segue lista os modelos atuais na sua conta e imprime os seus dados para a consola. Também guarda uma referência ao primeiro modelo.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_list)]


### <a name="output"></a>Saída

Esta é uma amostra da conta de teste.

```console
We have models with the following ids:
453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
628739de-779c-473d-8214-d35c72d3d4f7
ae636292-0b14-4e26-81a7-a0bfcbaf7c91
b4b5df77-8538-4ffb-a996-f67158ecd305
c6309148-6b64-4fef-aea0-d39521452699
```

### <a name="get-a-specific-model-using-the-models-id"></a>Obtenha um modelo específico usando o ID do modelo

O bloco de código que se segue utiliza o ID do modelo guardado na secção anterior e utiliza-o para recuperar detalhes sobre o modelo.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_getmodel)]


### <a name="output"></a>Saída

Esta é a saída da amostra para o modelo personalizado criado no exemplo anterior.

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00
```

### <a name="delete-a-model-from-the-resource-account"></a>Eliminar um modelo da conta de recursos

Também pode eliminar um modelo da sua conta fazendo referência ao seu ID. Este código elimina o modelo utilizado na secção anterior.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_delete)]


## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o `python` comando no seu ficheiro quickstart.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="general"></a>Geral

A biblioteca do cliente Do Reconhecimento de Formulários levantará exceções definidas no [Núcleo Azure.](https://aka.ms/azsdk-python-azure-core)

### <a name="logging"></a>Registo

Esta biblioteca utiliza a [biblioteca de registos padrão](https://docs.python.org/3/library/logging.html) para registar registos. Informações básicas sobre sessões HTTP (URLs, cabeçalhos, e assim por diante) são registadas ao nível info.

A registo detalhado do nível DEBUG, incluindo os órgãos de pedido/resposta e os cabeçalhos não redigidos, pode ser ativado num cliente com o argumento da `logging_enable` palavra-chave:

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_logging)]


Da mesma forma, `logging_enable` pode permitir a registo detalhado para uma única operação, mesmo quando não está habilitado para o cliente:

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_example)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você usou a biblioteca cliente Do Reconhecimento de Formulários Python para treinar modelos e analisar formas de diferentes maneiras. Em seguida, aprenda dicas para criar um melhor conjunto de dados de treino e produzir modelos mais precisos.

> [!div class="nextstepaction"]
> [Criar um conjunto de dados de preparação](../../build-training-data-set.md)

* [O que é o Reconhecedor de Formato?](../../overview.md)
* O código de amostra deste guia pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py).