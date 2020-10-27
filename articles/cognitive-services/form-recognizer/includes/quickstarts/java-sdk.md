---
title: 'Quickstart: Biblioteca de clientes do Reconhecimento de Formulários para Java'
description: Utilize a biblioteca de clientes Do Reconhecimento de Formulários para a Java para criar uma aplicação de processamento de formulários que extrai pares de chaves/valor e dados de tabela dos seus documentos personalizados.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 09/21/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: b692475949645693267ffec69361d9fdc4c328e8
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548277"
---
> [!IMPORTANT]
> O código deste artigo utiliza métodos sincronizados e armazenamento de credenciais não garantidos por razões de simplicidade.

[Documentação de referência](https://docs.microsoft.com/java/api/overview/azure/ai-formrecognizer-readme?view=azure-java-preview)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src)  |  [Pacote (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer)  |  [Amostras](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* A versão atual do Kit de [Desenvolvimento de Java (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A [ferramenta de construção Gradle,](https://gradle.org/install/)ou outro gestor de dependência.
* Assim que tiver a sua subscrição Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" Crie um recurso De Reconhecimento de "  target="_blank"> Formulários crie um recurso De Reconhecimento de <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Formulários no portal Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso** .
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API do Reconhecimento de Formulários. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.
* Uma bolha de armazenamento Azure que contém um conjunto de dados de treino. Consulte [Construir um conjunto de dados de treino para um modelo personalizado](../../build-training-data-set.md) para dicas e opções para reunir o seu conjunto de dados de treino. Para este arranque rápido, pode utilizar os ficheiros sob a pasta **Train** do conjunto de [dados](https://go.microsoft.com/fwlink/?linkid=2090451) da amostra (descarregar e extrair *sample_data.zip* ).


## <a name="setting-up"></a>Configuração

### <a name="create-a-new-gradle-project"></a>Criar um novo projeto Gradle

Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para a sua aplicação e navegue até ela. 

```console
mkdir myapp && cd myapp
```

Executar o comando do seu diretório de `gradle init` trabalho. Este comando criará ficheiros de construção essenciais para Gradle, incluindo *build.gradle.kts* que é usado em tempo de execução para criar e configurar a sua aplicação.

```console
gradle init --type basic
```

Quando solicitado para escolher um **DSL,** selecione **Kotlin** .


### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Este quickstart usa o gestor de dependência gradle. Pode encontrar a biblioteca do cliente e informações para outros gestores de dependência no [Repositório Central de Maven.](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer)

No ficheiro *build.gradle.kts* do seu projeto, inclua a biblioteca do cliente como `implementation` declaração, juntamente com os plugins e configurações necessários.

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.0.0")
}
```

### <a name="create-a-java-file"></a>Criar um ficheiro Java


Do seu diretório de trabalho, executar o seguinte comando:

```console
mkdir -p src/main/java
```

Navegue para a nova pasta e crie um ficheiro chamado *FormRecognizer.java* . Abra-o no seu editor preferido ou IDE e adicione as `import` seguintes declarações:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_imports)]

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/FormRecognizer/FormRecognizer.java)que contém os exemplos de código neste arranque rápido.


Na classe **FormRecognizer** da aplicação, crie variáveis para a chave e ponto final do seu recurso.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_creds)]

> [!IMPORTANT]
> Aceda ao portal do Azure. Se o recurso [nome do produto] que criou na secção **Pré-Requisitos** implementado com sucesso, clique no botão **'Ir a Recursos'** nos **Passos Seguintes** . Pode encontrar a sua chave e ponto final na **página chave e ponto final** do recurso, sob **gestão de recursos.** 
>
> Lembre-se de remover a chave do seu código quando terminar, e nunca postá-la publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Consulte o artigo [de segurança](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) dos Serviços Cognitivos para obter mais informações.

No método **principal** da aplicação, adicione chamadas para os métodos utilizados neste arranque rápido. Vai definir isto mais tarde. Também terá de adicionar referências aos URLs para os seus dados de treino e teste.

* Para recuperar o URL SAS para os seus dados de treino de modelo personalizados, abra o Microsoft Azure Storage Explorer, clique com o botão direito no seu recipiente e **selecione Obter assinatura de acesso partilhado** . Certifique-se de que as permissões **de Leitura** e **Lista** são verificadas e clique em **Criar** . Em seguida, copie o valor na secção **URL.** Deve ter o formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Para obter um URL de um formulário para testar, você pode usar os passos acima para obter o URL SAS de um documento individual no armazenamento de bolhas. Ou, pegue o URL de um documento localizado em outro lugar.
* Utilize o método acima para obter o URL de uma imagem de recibo também.

> [!NOTE]
> Os fragmentos de código neste guia utilizam formulários remotos acedidos por URLs. Se pretender processar documentos de formulário local, consulte os métodos relacionados na [documentação de referência](https://docs.microsoft.com/java/api/overview/azure/ai-formrecognizer-readme-pre?view=azure-java-preview).


[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_maincalls)]



## <a name="object-model"></a>Modelo de objeto 

Com o Form Recogniser, pode criar dois tipos de clientes diferentes. O primeiro, `FormRecognizerClient` é usado para consultar o serviço para campos de forma reconhecidos e conteúdos. A segunda é `FormTrainingClient` a utilização para criar e gerir modelos personalizados que pode usar para melhorar o reconhecimento. 

### <a name="formrecognizerclient"></a>FormulárioRecognizerClient

`FormRecognizerClient` Fornece operações para:

- Reconhecendo campos de formulários e conteúdos, utilizando modelos personalizados treinados para reconhecer os seus formulários personalizados.  Estes valores são devolvidos numa coleção de `RecognizedForm` objetos. Veja o exemplo [Analisar formulários personalizados.](#analyze-forms-with-a-custom-model)
- Reconhecendo o conteúdo da forma, incluindo tabelas, linhas e palavras, sem a necessidade de formar um modelo.  O conteúdo do formulário é devolvido numa coleção de `FormPage` objetos. Ver exemplo [Reconhecer o conteúdo da forma.](#recognize-form-content)
- Reconhecendo campos comuns a partir de recibos dos EUA, utilizando um modelo de recibo pré-treinado no serviço Form Recogniser.  Estes campos e meta-dados são devolvidos numa coleção de `RecognizedForm` objetos. Ver exemplo [Reconhecer recibos.](#recognize-receipts)

### <a name="formtrainingclient"></a>FormaTrainingClient

`FormTrainingClient` Fornece operações para:

- Treinando modelos personalizados para reconhecer todos os campos e valores encontrados nas suas formas personalizadas.  A `CustomFormModel` é devolvido indicando os tipos de formulário que o modelo reconhecerá, e os campos que extrairá para cada tipo de formulário.
- Treinando modelos personalizados para reconhecer campos e valores específicos que especifica, rotulando os seus formulários personalizados.  A `CustomFormModel` é devolvido indicando os campos que o modelo irá extrair, bem como a precisão estimada para cada campo.
- Gestão de modelos criados na sua conta.
- Copiar um modelo personalizado de um recurso de Reconhecimento de Formulário para outro.

> [!NOTE]
> Os modelos também podem ser treinados utilizando uma interface gráfica do utilizador, como a [Ferramenta de Rotulagem do Reconhecimento de Formulários.](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/label-tool)

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes Do Reconhecimento de Formulários para Java:

* [Autenticar o cliente](#authenticate-the-client)
* [Reconhecer o conteúdo da forma](#recognize-form-content)
* [Reconhecer recibos](#recognize-receipts)
* [Preparar um modelo personalizado](#train-a-custom-model)
* [Analisar formas com um modelo personalizado](#analyze-forms-with-a-custom-model)
* [Gerir os seus modelos personalizados](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>Autenticar o cliente

No topo do seu método **principal,** adicione o seguinte código. Aqui, autenticará dois objetos clientes utilizando as variáveis de subscrição acima definidas. Utilizará um objeto **AzureKeyCredential,** para que, se necessário, possa atualizar a tecla API sem criar novos objetos de cliente.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_auth)]

## <a name="recognize-form-content"></a>Reconhecer o conteúdo da forma

Pode utilizar o Form Recogniser para reconhecer tabelas, linhas e palavras em documentos, sem precisar de treinar um modelo.

Para reconhecer o conteúdo de um ficheiro num dado URI, utilize o método **startRecognizeContentFromUrl.**

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_call)]


O valor devolvido é uma coleção de objetos **FormPage:** um para cada página no documento submetido. O código seguinte itera através destes objetos e imprime os pares de chave/valor extraídos e os dados de tabela.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_print)]
### <a name="output"></a>Saída

```console
Get form content...
----Recognizing content ----
Has width: 8.500000 and height: 11.000000, measured with unit: inch.
Table has 2 rows and 6 columns.
Cell has text Invoice Number.
Cell has text Invoice Date.
Cell has text Invoice Due Date.
Cell has text Charges.
Cell has text VAT ID.
Cell has text 458176.
Cell has text 3/28/2018.
Cell has text 4/16/2018.
Cell has text $89,024.34.
Cell has text ET.
```

## <a name="recognize-receipts"></a>Reconhecer recibos

Esta secção demonstra como reconhecer e extrair campos comuns a partir de recibos dos EUA, utilizando um modelo de recibo pré-treinado.

Para reconhecer os recibos de um URI, utilize o método **startRecognizeReceiptsFromUrl.** O valor devolvido é uma coleção de objetos **RecognizedReceipt:** um para cada página no documento submetido.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_call)]


O próximo bloco de códigos iteração através dos recibos e imprime os seus dados para a consola.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print)]

O próximo bloco de códigos iteração através dos itens individuais detetados no recibo e imprime os seus dados para a consola.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print_items)]

### <a name="output"></a>Saída 

```console
Analyze receipt...
----------- Recognized Receipt page 0 -----------
Merchant Name: Contoso Contoso, confidence: 0.62
Merchant Address: 123 Main Street Redmond, WA 98052, confidence: 0.99
Transaction Date: 2020-06-10, confidence: 0.90
Receipt Items:
Name: Cappuccino, confidence: 0.96s
Quantity: null, confidence: 0.957s]
Total Price: 2.200000, confidence: 0.95
Name: BACON & EGGS, confidence: 0.94s
Quantity: null, confidence: 0.927s]
Total Price: null, confidence: 0.93
```
## <a name="train-a-custom-model"></a>Preparar um modelo personalizado

Esta secção demonstra como treinar um modelo com os seus próprios dados. Um modelo treinado pode obter dados estruturados de saída que incluam as relações chave/valor no documento original do formulário. Depois de treinar o modelo, pode testá-lo e reforçá-lo e eventualmente usá-lo para extrair dados de mais formas de acordo com as suas necessidades.

> [!NOTE]
> Também pode treinar modelos com uma interface gráfica do utilizador, como a ferramenta de rotulagem da [amostra do Form Recogniser.](../../quickstarts/label-tool.md)

### <a name="train-a-model-without-labels"></a>Treine um modelo sem rótulos

Treine modelos personalizados para reconhecer todos os campos e valores encontrados nas suas formas personalizadas sem rotular manualmente os documentos de treino.

O método a seguir treina um modelo num determinado conjunto de documentos e imprime o estado do modelo à consola. 


[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_call)]

O objeto **CustomFormModel** devolvido contém informações sobre os tipos de formulários que o modelo pode reconhecer e os campos que pode extrair de cada tipo de formulário. O bloco de código que se segue imprime esta informação para a consola.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_print)]

Finalmente, este método devolve o ID único do modelo.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_return)]


### <a name="output"></a>Saída

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

### <a name="train-a-model-with-labels"></a>Treine um modelo com rótulos

Também pode treinar modelos personalizados rotulando manualmente os documentos de treino. O treino com rótulos leva a um melhor desempenho em alguns cenários. Para treinar com etiquetas, é necessário ter ficheiros de informações especiais de etiquetas *\<filename\> (.pdf.labels.js) no* seu recipiente de armazenamento de bolhas ao lado dos documentos de treino. A [ferramenta de rotulagem da amostra do Reconhecimento de Formulários](../../quickstarts/label-tool.md) fornece uma UI para ajudá-lo a criar estes ficheiros de etiqueta. Uma vez que os tenha, pode chamar o método **startTraining** com o *parâmetro useTrainingLabels* definido para `true` .

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_call)]


O **CustomFormModel** devolvido indica os campos que o modelo pode extrair, juntamente com a sua precisão estimada em cada campo. O bloco de código que se segue imprime esta informação para a consola.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_print)]


### <a name="output"></a>Saída

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

## <a name="analyze-forms-with-a-custom-model"></a>Analisar formas com um modelo personalizado

Esta secção demonstra como extrair informações de chave/valor e outros conteúdos dos seus tipos de formulários personalizados, utilizando modelos treinados com os seus próprios formulários.

> [!IMPORTANT]
> Para implementar este cenário, já deve ter treinado um modelo para que possa passar o seu ID para o método abaixo. Consulte a secção [Modelo train.](#train-a-model-without-labels)

Utilizará o método **startRecognizeCustomFormsFromUrl.** O valor devolvido é uma coleção de objetos **RecognizedForm:** um para cada página no documento submetido.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_call)]


O código seguinte imprime os resultados da análise para a consola. Imprime cada campo reconhecido e valor correspondente, juntamente com uma pontuação de confiança.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_print)]


### <a name="output"></a>Saída

```console
Analyze PDF form...
----------- Recognized custom form info for page 0 -----------
Form type: form-0
Field 'field-0' has label 'Address:' with a confidence score of 0.91.
Field 'field-1' has label 'Invoice For:' with a confidence score of 1.00.
Field 'field-2' has label 'Invoice Number' with a confidence score of 1.00.
Field 'field-3' has label 'Invoice Date' with a confidence score of 1.00.
Field 'field-4' has label 'Invoice Due Date' with a confidence score of 1.00.
Field 'field-5' has label 'Charges' with a confidence score of 1.00.
Field 'field-6' has label 'VAT ID' with a confidence score of 1.00.
```


## <a name="manage-custom-models"></a>Gerir modelos personalizados

Esta secção demonstra como gerir os modelos personalizados armazenados na sua conta. O seguinte código faz todas as tarefas de gestão do modelo num único método, como exemplo. Comece por copiar a assinatura do método abaixo:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage)]


### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Verifique o número de modelos na conta de recursos FormRecognizer

O bloco de códigos que se segue verifica quantos modelos guardou na sua conta Desemaçador de Formulários e compara-os ao limite da conta.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_count)]


#### <a name="output"></a>Saída 

```console
The account has 12 custom models, and we can have at most 250 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Listar os modelos atualmente armazenados na conta de recursos

O bloco de códigos que se segue lista os modelos atuais na sua conta e imprime os seus dados para a consola.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_list)]


#### <a name="output"></a>Saída 

Esta resposta foi truncada para a legibilidade.

```console
We have following models in the account:
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Status: ready
Training started on: 2020-06-04T18:33:08Z
Training completed on: 2020-06-04T18:33:10Z
Custom Model Form type: form-0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Custom Model Accuracy: 1.00
Field Text: invoice date
Field Accuracy: 1.00
Field Text: invoice number
Field Accuracy: 1.00
...
```

### <a name="delete-a-model-from-the-resource-account"></a>Eliminar um modelo da conta de recursos

Também pode eliminar um modelo da sua conta fazendo referência ao seu ID.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_delete)]



## <a name="run-the-application"></a>Executar a aplicação

Volte para o seu diretório principal do projeto. Em seguida, construa a aplicação com o seguinte comando:

```console
gradle build
```

Executar a aplicação com o `run` objetivo:

```console
gradle run
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Resolução de problemas

Dos clientes Recogniser aumentam `ErrorResponseException` as exceções. Por exemplo, se tentar fornecer um URL de fonte de ficheiro inválido, `ErrorResponseException` um será levantado com um erro que indique a causa da falha. No seguinte corte de código, o erro é manuseado graciosamente, capturando a exceção e exibindo as informações adicionais sobre o erro.

```java Snippet:FormRecognizerBadRequest
try {
    formRecognizerClient.beginRecognizeContentFromUrl("invalidSourceUrl");
} catch (ErrorResponseException e) {
    System.out.println(e.getMessage());
}
```

### <a name="enable-client-logging"></a>Ativar a sessão de registo de clientes

Os SDKs Azure para Java oferecem uma história de registo consistente para ajudar a resolver erros de aplicação e acelerar a sua resolução. Os registos produzidos captarão o fluxo de uma aplicação antes de chegarem ao estado terminal para ajudar a localizar o problema de raiz. Consulte o [wiki de registo para](https://github.com/Azure/azure-sdk-for-java/wiki/Logging-with-Azure-SDK) obter orientações sobre a ativação do registo.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você usou a biblioteca cliente Do Reconhecimento de Formulários Java para treinar modelos e analisar formas de diferentes maneiras. Em seguida, aprenda dicas para criar um melhor conjunto de dados de treino e produzir modelos mais precisos.

> [!div class="nextstepaction"]
> [Criar um conjunto de dados de preparação](../../build-training-data-set.md)

* [O que é o Reconhecedor de Formato?](../../overview.md)
* O código de amostra deste guia (e muito mais) pode ser encontrado no [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples).
