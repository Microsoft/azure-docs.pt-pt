---
title: 'Quickstart: Biblioteca de clientes do Reconhecimento de Formulários para .NET'
description: Utilize a biblioteca cliente Do Reconhecimento de Formulários para .NET para criar uma aplicação de processamento de formulários que extrai pares de chaves/valor e dados de tabela dos seus documentos personalizados.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 10/06/2020
ms.author: pafarley
ms.openlocfilehash: d7577668d87ecaf2d769136d64990f95fc212fe6
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356533"
---
> [!IMPORTANT]
> O código deste artigo utiliza métodos sincronizados e armazenamento de credenciais não garantidos por razões de simplicidade.

[Documentação de referência](/dotnet/api/overview/azure/ai.formrecognizer-readme)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src)  |  [Pacote (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer)  |  [Amostras](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* O [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) ou a versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Uma bolha de armazenamento Azure que contém um conjunto de dados de treino. Consulte [Construir um conjunto de dados de treino para um modelo personalizado](../../build-training-data-set.md) para dicas e opções para reunir o seu conjunto de dados de treino. Para este arranque rápido, pode utilizar os ficheiros sob a pasta **Train** do conjunto de [dados](https://go.microsoft.com/fwlink/?linkid=2090451) da amostra (descarregar e extrair *sample_data.zip*).
* Assim que tiver a sua subscrição Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" Crie um recurso De Reconhecimento de "  target="_blank"> Formulários crie um recurso De Reconhecimento de <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Formulários no portal Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API do Reconhecimento de Formulários. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

Numa janela de consola (como cmd, PowerShell ou Bash), utilize o `dotnet new` comando para criar uma nova aplicação de consola com o nome `formrecognizer-quickstart` . Este comando cria um projeto simples "Hello World" C# com um único ficheiro de origem: *program.cs*. 

```console
dotnet new console -n formrecognizer-quickstart
```

Mude o seu diretório para a pasta de aplicações recém-criada. Pode construir a aplicação com:

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

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente 

No diretório de aplicações, instale a biblioteca cliente Do Reconhecimento de Formulários para .NET com o seguinte comando:

#### <a name="version-30"></a>[versão 3.0](#tab/ga)

```console
dotnet add package Azure.AI.FormRecognizer --version 3.0.0
```

#### <a name="version-31-preview"></a>[versão 3.1 pré-visualização](#tab/preview)

```console
dotnet add package Azure.AI.FormRecognizer --version 3.1.0-beta.1
```
---

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)que contém os exemplos de código neste arranque rápido.

A partir do diretório do projeto, abra o ficheiro *Program.cs* no seu editor preferido ou IDE. Adicione as `using` seguintes diretivas:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_using)]

Na classe **programo** da aplicação, crie variáveis para a chave e ponto final do seu recurso.

> [!IMPORTANT]
> Aceda ao portal do Azure. Se o recurso 'Reconhecimento de Formulários' que criou na secção **Pré-Requisitos** implementado com sucesso, clique no botão **'Ir a Recursos'** nos **Passos Seguintes**. Pode encontrar a sua chave e ponto final na **página chave e ponto final** do recurso, sob **gestão de recursos.** 
>
> Lembre-se de remover a chave do seu código quando terminar, e nunca postá-la publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Consulte o artigo [de segurança](../../../cognitive-services-security.md) dos Serviços Cognitivos para obter mais informações.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_creds)]

No método **principal** da aplicação, adicione uma chamada às tarefas assíncronos utilizadas neste arranque rápido. Vai implementá-los mais tarde.

#### <a name="version-30"></a>[versão 3.0](#tab/ga)
[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_main)]
#### <a name="version-31-preview"></a>[versão 3.1 pré-visualização](#tab/preview)
[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_main)]

---


## <a name="object-model"></a>Modelo de objeto 

Com o Form Recogniser, pode criar dois tipos de clientes diferentes. O primeiro, `FormRecognizerClient` é usado para consultar o serviço para campos de forma reconhecidos e conteúdos. A segunda é `FormTrainingClient` a utilização para criar e gerir modelos personalizados que pode usar para melhorar o reconhecimento. 

### <a name="formrecognizerclient"></a>FormulárioRecognizerClient

`FormRecognizerClient` Fornece operações para:

 - Reconhecendo campos de formulários e conteúdos, utilizando modelos personalizados treinados para reconhecer os seus formulários personalizados.  Estes valores são devolvidos numa coleção de `RecognizedForm` objetos. Veja o exemplo [Analisar formulários personalizados.](#analyze-forms-with-a-custom-model)
 - Reconhecendo o conteúdo da forma, incluindo tabelas, linhas e palavras, sem a necessidade de formar um modelo.  O conteúdo do formulário é devolvido numa coleção de `FormPage` objetos. Ver exemplo [Reconhecer o conteúdo da forma.](#recognize-form-content)
 - Reconhecendo campos comuns a partir de recibos dos EUA, utilizando um modelo de recibo pré-treinado no serviço Form Recogniser. Estes campos e meta-dados são devolvidos numa coleção de `RecognizedForm` objetos. Ver exemplo [Reconhecer recibos.](#recognize-receipts)

### <a name="formtrainingclient"></a>FormaTrainingClient

`FormTrainingClient` Fornece operações para:

- Treinando modelos personalizados para reconhecer todos os campos e valores encontrados nas suas formas personalizadas.  A `CustomFormModel` é devolvido indicando os tipos de formulário que o modelo reconhecerá, e os campos que extrairá para cada tipo de formulário.
- Treinando modelos personalizados para reconhecer campos e valores específicos que especifica, rotulando os seus formulários personalizados.  A `CustomFormModel` é devolvido indicando os campos que o modelo irá extrair, bem como a precisão estimada para cada campo.
- Gestão de modelos criados na sua conta.
- Copiar um modelo personalizado de um recurso de Reconhecimento de Formulário para outro.

Ver exemplos para [Treinar um Modelo](#train-a-custom-model) e Gerir [Modelos Personalizados.](#manage-custom-models)

> [!NOTE]
> Os modelos também podem ser treinados utilizando uma interface gráfica do utilizador, como a [Ferramenta de Rotulagem do Reconhecimento de Formulários.](../../quickstarts/label-tool.md)

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer as seguintes tarefas com a biblioteca do cliente do Reconhecimento de Formulários para .NET:

#### <a name="version-30"></a>[versão 3.0](#tab/ga)

* [Autenticar o cliente](#authenticate-the-client)
* [Reconhecer o conteúdo da forma](#recognize-form-content)
* [Reconhecer recibos](#recognize-receipts)
* [Preparar um modelo personalizado](#train-a-custom-model)
* [Analisar formas com um modelo personalizado](#analyze-forms-with-a-custom-model)
* [Gerir os seus modelos personalizados](#manage-your-custom-models)

#### <a name="version-31-preview"></a>[versão 3.1 pré-visualização](#tab/preview)

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

Abaixo **do Main,** crie um novo método chamado `AuthenticateClient` . Utilizará isto noutras tarefas para autenticar os seus pedidos no serviço Desemaçador de Formulários. Este método utiliza o `AzureKeyCredential` objeto, para que, se necessário, possa atualizar a tecla API sem criar novos objetos de cliente.

> [!IMPORTANT]
> Obtenha a sua chave e ponto final no portal Azure. Se o recurso 'Reconhecimento de Formulários' que criou na secção **Pré-Requisitos** implementado com sucesso, clique no botão **'Ir a Recursos'** nos **Passos Seguintes**. Pode encontrar a sua chave e ponto final na **página chave e ponto final** do recurso, sob **gestão de recursos.** 
>
> Lembre-se de remover a chave do seu código quando terminar, e nunca postá-la publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Por exemplo, [cofre de chaves Azure](../../../../key-vault/general/overview.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_auth)]

Repita os passos acima para um novo método que aututenda um cliente de formação.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_auth_training)]

## <a name="get-assets-for-testing"></a>Obter ativos para testes 

Também terá de adicionar referências aos URLs para os seus dados de treino e teste. Adicione isto à raiz da sua aula **de Programa.**

* Para recuperar o URL SAS para os seus dados de treino de modelo personalizados, abra o Microsoft Azure Storage Explorer, clique com o botão direito no seu recipiente e **selecione Obter assinatura de acesso partilhado**. Certifique-se de que as permissões **de Leitura** e **Lista** são verificadas e clique em **Criar**. Em seguida, copie o valor na secção **URL.** Deve ter o formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Em seguida, use os passos acima para obter o URL SAS de um documento individual no armazenamento de bolhas.
* Por último, guarde o URL da(s) imagem(s) da amostra incluída abaixo (também disponível no [GitHub).](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms) 

#### <a name="version-30"></a>[versão 3.0](#tab/ga)
[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_urls)]
#### <a name="version-31-preview"></a>[versão 3.1 pré-visualização](#tab/preview)
[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_urls)]

---


## <a name="recognize-form-content"></a>Reconhecer o conteúdo da forma

Pode utilizar o Form Recogniser para reconhecer tabelas, linhas e palavras em documentos, sem precisar de treinar um modelo. O valor devolvido é uma coleção de objetos **FormPage:** um para cada página no documento submetido. 

Para reconhecer o conteúdo de um ficheiro num dado URL, utilize o `StartRecognizeContentFromUri` método.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_call)]

> [!TIP]
> Também pode obter conteúdo de um ficheiro local. Consulte os métodos [FormRecognizerClient,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient?view=azure-dotnet) tais como **StartRecognizeContent**. Ou, consulte o código de amostra no [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) para cenários que envolvam imagens locais.

O resto desta tarefa imprime a informação de conteúdo para a consola.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_print)]

### <a name="output"></a>Saída

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    Line 6 has 3 words, and text: 'Sunnayvale, CA 87659'.
    Line 7 has 1 word, and text: '99243'.
    Line 8 has 2 words, and text: 'Invoice Number'.
    Line 9 has 2 words, and text: 'Invoice Date'.
    Line 10 has 3 words, and text: 'Invoice Due Date'.
    Line 11 has 1 word, and text: 'Charges'.
    Line 12 has 2 words, and text: 'VAT ID'.
    Line 13 has 1 word, and text: '34278587'.
    Line 14 has 1 word, and text: '6/18/2017'.
    Line 15 has 1 word, and text: '6/24/2017'.
    Line 16 has 1 word, and text: '$56,651.49'.
    Line 17 has 1 word, and text: 'PT'.
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    Cell (0, 5) contains text: 'VAT ID'.
    Cell (1, 0) contains text: '34278587'.
    Cell (1, 1) contains text: '6/18/2017'.
    Cell (1, 2) contains text: '6/24/2017'.
    Cell (1, 3) contains text: '$56,651.49'.
    Cell (1, 5) contains text: 'PT'.
```

## <a name="recognize-receipts"></a>Reconhecer recibos

Esta secção demonstra como reconhecer e extrair campos comuns a partir de recibos dos EUA, utilizando um modelo de recibo pré-treinado.

Para reconhecer os recibos de um URL, utilize o `StartRecognizeReceiptsFromUri` método. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_call)]

> [!TIP]
> Também pode reconhecer imagens de recibo local. Consulte os métodos [FormRecognizerClient,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient?view=azure-dotnet) tais como **StartRecognizeReceipts**. Ou, consulte o código de amostra no [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) para cenários que envolvam imagens locais.

O valor devolvido é uma coleção de `RecognizedReceipt` objetos: um para cada página no documento submetido. O código seguinte processa o recibo no URI dado e imprime os principais campos e valores para a consola.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_print)]

### <a name="output"></a>Saída 

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    Line 6 has 3 words, and text: 'Sunnayvale, CA 87659'.
    Line 7 has 1 word, and text: '99243'.
    Line 8 has 2 words, and text: 'Invoice Number'.
    Line 9 has 2 words, and text: 'Invoice Date'.
    Line 10 has 3 words, and text: 'Invoice Due Date'.
    Line 11 has 1 word, and text: 'Charges'.
    Line 12 has 2 words, and text: 'VAT ID'.
    Line 13 has 1 word, and text: '34278587'.
    Line 14 has 1 word, and text: '6/18/2017'.
    Line 15 has 1 word, and text: '6/24/2017'.
    Line 16 has 1 word, and text: '$56,651.49'.
    Line 17 has 1 word, and text: 'PT'.
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    Cell (0, 5) contains text: 'VAT ID'.
    Cell (1, 0) contains text: '34278587'.
    Cell (1, 1) contains text: '6/18/2017'.
    Cell (1, 2) contains text: '6/24/2017'.
    Cell (1, 3) contains text: '$56,651.49'.
    Cell (1, 5) contains text: 'PT'.
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
```

#### <a name="version-30"></a>[versão 3.0](#tab/ga)

#### <a name="version-31-preview"></a>[versão 3.1 pré-visualização](#tab/preview)

## <a name="recognize-business-cards"></a>Reconhecer cartões de visita

Esta secção demonstra como reconhecer e extrair campos comuns de cartões de visita ingleses, utilizando um modelo pré-treinado.

Para reconhecer cartões de visita de um URL, utilize o `StartRecognizeBusinessCardsFromUriAsync` método. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_bc_call)]

> [!TIP]
> Também pode reconhecer imagens de recibo local. Consulte os métodos [FormRecognizerClient,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient?view=azure-dotnet) tais como **StartRecognizeBusinessCards**. Ou, consulte o código de amostra no [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) para cenários que envolvam imagens locais.

O valor devolvido é uma coleção de `RecognizedForm` objetos: um para cada cartão no documento. O código seguinte processa o cartão de visita no dado URI e imprime os principais campos e valores para a consola.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_bc_print)]

## <a name="recognize-invoices"></a>Reconhecer faturas

Esta secção demonstra como reconhecer e extrair campos comuns das faturas de venda, utilizando um modelo pré-treinado.

Para reconhecer faturas de um URL, utilize o `StartRecognizeInvoicesFromUriAsync` método. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_invoice_call)]

> [!TIP]
> Também pode reconhecer imagens de fatura local. Consulte os métodos [FormRecognizerClient,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient?view=azure-dotnet) tais como **StartRecognizeInvoices**. Ou, consulte o código de amostra no [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) para cenários que envolvam imagens locais.

O valor devolvido é uma coleção de `RecognizedForm` objetos: um para cada fatura no documento submetido. O código seguinte processa a fatura no URI dado e imprime os principais campos e valores para a consola.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_invoice_print)]

---


## <a name="train-a-custom-model"></a>Preparar um modelo personalizado

Esta secção demonstra como treinar um modelo com os seus próprios dados. Um modelo treinado pode obter dados estruturados de saída que incluam as relações chave/valor no documento original do formulário. Depois de treinar o modelo, pode testá-lo e reforçá-lo e eventualmente usá-lo para extrair dados de mais formas de acordo com as suas necessidades.

> [!NOTE]
> Também pode treinar modelos com uma interface gráfica do utilizador, como a ferramenta de rotulagem da [amostra do Form Recogniser.](../../quickstarts/label-tool.md)

### <a name="train-a-model-without-labels"></a>Treine um modelo sem rótulos

Treine modelos personalizados para reconhecer todos os campos e valores encontrados nas suas formas personalizadas sem rotular manualmente os documentos de treino. O método a seguir treina um modelo num determinado conjunto de documentos e imprime o estado do modelo à consola. 


[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train)]


O objeto devolvido `CustomFormModel` contém informações sobre os tipos de formulários que o modelo pode reconhecer e os campos que pode extrair de cada tipo de formulário. O bloco de código que se segue imprime esta informação para a consola.


[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_response)]

Por fim, devolva o ID do modelo treinado para utilização em etapas posteriores.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_return)]

### <a name="output"></a>Saída

Esta resposta foi truncada para a legibilidade.

```console
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    ...
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    ... 
Custom Model Info:
    Model Id: 95035721-f19d-40eb-8820-0c806b42798b
    Model Status: Ready
    Training model started on: 8/24/2020 6:36:44 PM +00:00
    Training model completed on: 8/24/2020 6:36:50 PM +00:00
Submodel Form Type: form-95035721-f19d-40eb-8820-0c806b42798b
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    ... 
Custom Model Info:
    Model Id: e7a1181b-1fb7-40be-bfbe-1ee154183633
    Model Status: Ready
    Training model started on: 8/24/2020 6:36:44 PM +00:00
    Training model completed on: 8/24/2020 6:36:52 PM +00:00
Submodel Form Type: form-0
    FieldName: field-0, FieldLabel: Additional Notes:
    FieldName: field-1, FieldLabel: Address:
    FieldName: field-2, FieldLabel: Company Name:
    FieldName: field-3, FieldLabel: Company Phone:
    FieldName: field-4, FieldLabel: Dated As:
    FieldName: field-5, FieldLabel: Details
    FieldName: field-6, FieldLabel: Email:
    FieldName: field-7, FieldLabel: Hero Limited
    FieldName: field-8, FieldLabel: Name:
    FieldName: field-9, FieldLabel: Phone:
    ...
```

### <a name="train-a-model-with-labels"></a>Treine um modelo com rótulos

Também pode treinar modelos personalizados rotulando manualmente os documentos de treino. O treino com rótulos leva a um melhor desempenho em alguns cenários. Para treinar com etiquetas, precisa de ter ficheiros de informações especiais de etiquetas `\<filename\>.pdf.labels.json` () no seu recipiente de armazenamento de bolhas ao lado dos documentos de treino. A [ferramenta de rotulagem da amostra do Reconhecimento de Formulários](../../quickstarts/label-tool.md) fornece uma UI para ajudá-lo a criar estes ficheiros de etiqueta. Uma vez que os tenha, pode ligar para o `StartTrainingAsync` método com o parâmetro definido para `uselabels` `true` . 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels)]

A `CustomFormModel` devolvição indica os campos que o modelo pode extrair, juntamente com a sua precisão estimada em cada campo. O bloco de código que se segue imprime esta informação para a consola.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels_response)]


### <a name="output"></a>Saída

Esta resposta foi truncada para a legibilidade.

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    ...
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    ... 
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Custom Model Info:
    Model Id: 63c013e3-1cab-43eb-84b0-f4b20cb9214c
    Model Status: Ready
    Training model started on: 8/24/2020 6:42:54 PM +00:00
    Training model completed on: 8/24/2020 6:43:01 PM +00:00
Submodel Form Type: form-63c013e3-1cab-43eb-84b0-f4b20cb9214c
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    FieldName: DatedAs
    FieldName: Email
    FieldName: Merchant
    ... 
```

## <a name="analyze-forms-with-a-custom-model"></a>Analisar formas com um modelo personalizado

Esta secção demonstra como extrair informações de chave/valor e outros conteúdos dos seus tipos de formulários personalizados, utilizando modelos treinados com os seus próprios formulários.

> [!IMPORTANT]
> Para implementar este cenário, já deve ter treinado um modelo para que possa passar o seu ID para o método abaixo.

Vais usar o `StartRecognizeCustomFormsFromUri` método. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze)]

> [!TIP]
> Também pode analisar um ficheiro local. Consulte os métodos [FormRecognizerClient,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient?view=azure-dotnet) tais como **StartRecognizeCustomForms**. Ou, consulte o código de amostra no [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) para cenários que envolvam imagens locais.

O valor devolvido é uma coleção de `RecognizedForm` objetos: um para cada página no documento submetido. O código seguinte imprime os resultados da análise para a consola. Imprime cada campo reconhecido e valor correspondente, juntamente com uma pontuação de confiança.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze_response)]


### <a name="output"></a>Saída

Esta resposta foi truncada para a legibilidade.

```console
Custom Model Info:
    Model Id: 9b0108ee-65c8-450e-b527-bb309d054fc4
    Model Status: Ready
    Training model started on: 8/24/2020 7:00:31 PM +00:00
    Training model completed on: 8/24/2020 7:00:32 PM +00:00
Submodel Form Type: form-9b0108ee-65c8-450e-b527-bb309d054fc4
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    ...
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    ...

Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    ...
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Custom Model Info:
    Model Id: dc115156-ce0e-4202-bbe4-7426e7bee756
    Model Status: Ready
    Training model started on: 8/24/2020 7:00:31 PM +00:00
    Training model completed on: 8/24/2020 7:00:41 PM +00:00
Submodel Form Type: form-0
    FieldName: field-0, FieldLabel: Additional Notes:
    FieldName: field-1, FieldLabel: Address:
    FieldName: field-2, FieldLabel: Company Name:
    FieldName: field-3, FieldLabel: Company Phone:
    FieldName: field-4, FieldLabel: Dated As:
    ... 
Form of type: custom:form
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: '$56,651.49
    Confidence: '0.249
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: 'PT
    Confidence: '0.245
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: '99243
    Confidence: '0.114
   ...
```

## <a name="manage-custom-models"></a>Gerir modelos personalizados

Esta secção demonstra como gerir os modelos personalizados armazenados na sua conta. Fará várias operações dentro do seguinte método:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage)]


### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Verifique o número de modelos na conta de recursos FormRecognizer

O bloco de códigos que se segue verifica quantos modelos guardou na sua conta Desemaçador de Formulários e compara-os ao limite da conta.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_count)]

### <a name="output"></a>Saída 

```console
Account has 20 models.
It can have at most 5000 models.
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Listar os modelos atualmente armazenados na conta de recursos

O bloco de códigos que se segue lista os modelos atuais na sua conta e imprime os seus dados para a consola.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_list)]


### <a name="output"></a>Saída 

Esta resposta foi truncada para a legibilidade.

```console
Custom Model Info:
    Model Id: 05932d5a-a2f8-4030-a2ef-4e5ed7112515
    Model Status: Creating
    Training model started on: 8/24/2020 7:35:02 PM +00:00
    Training model completed on: 8/24/2020 7:35:02 PM +00:00
Custom Model Info:
    Model Id: 150828c4-2eb2-487e-a728-60d5d504bd16
    Model Status: Ready
    Training model started on: 8/24/2020 7:33:25 PM +00:00
    Training model completed on: 8/24/2020 7:33:27 PM +00:00
Custom Model Info:
    Model Id: 3303e9de-6cec-4dfb-9e68-36510a6ecbb2
    Model Status: Ready
    Training model started on: 8/24/2020 7:29:27 PM +00:00
    Training model completed on: 8/24/2020 7:29:36 PM +00:00
```

### <a name="get-a-specific-model-using-the-models-id"></a>Obtenha um modelo específico usando o ID do modelo

O bloco de código que se segue treina um novo modelo (tal como na secção [De Modelos)](#train-a-model-without-labels) e, em seguida, recupera uma segunda referência a ele usando o seu ID.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_get)]

### <a name="output"></a>Saída 

Esta resposta foi truncada para a legibilidade.

```console
Custom Model Info:
    Model Id: 150828c4-2eb2-487e-a728-60d5d504bd16
    Model Status: Ready
    Training model started on: 8/24/2020 7:33:25 PM +00:00
    Training model completed on: 8/24/2020 7:33:27 PM +00:00
Submodel Form Type: form-150828c4-2eb2-487e-a728-60d5d504bd16
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    FieldName: DatedAs
    FieldName: Email
    FieldName: Merchant
    FieldName: PhoneNumber
    FieldName: PurchaseOrderNumber
    FieldName: Quantity
    FieldName: Signature
    FieldName: Subtotal
    FieldName: Tax
    FieldName: Total
    FieldName: VendorName
    FieldName: Website
...
```

### <a name="delete-a-model-from-the-resource-account"></a>Eliminar um modelo da conta de recursos

Também pode eliminar um modelo da sua conta fazendo referência ao seu ID. Este passo também fecha o método.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_delete)]


## <a name="run-the-application"></a>Executar a aplicação

Executar o pedido do seu diretório de candidaturas com o `dotnet run` comando.

```dotnet
dotnet run
```


## <a name="clean-up-resources"></a>Limpar os recursos

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