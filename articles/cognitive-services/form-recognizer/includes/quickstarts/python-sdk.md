---
title: 'Quickstart: Biblioteca de clientes do Reconhecimento de Formulários para Python'
description: Neste arranque rápido, comece com a biblioteca de clientes Do Reconhecimento de Formulários para python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/07/2020
ms.author: pafarley
ms.openlocfilehash: 1a2c5bfb2866e2cc28c013be60dbe791edeb9ac1
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83997596"
---
[Documentação de](https://docs.microsoft.com/python/api/overview/azure/formrecognizer?view=azure-python-preview)  |  referência Código fonte [da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer)  |  [Pacote (Pipi)](https://pypi.org/project/azure-ai-formrecognizer/)  |  [Amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* Uma bolha de armazenamento azure que contém um conjunto de dados de treino. Consulte construir um conjunto de dados de [treino para um modelo personalizado](../../build-training-data-set.md) para dicas e opções para reunir o seu conjunto de dados de treino. Para este arranque rápido, pode utilizar os ficheiros sob a pasta **Train** do conjunto de dados da [amostra](https://go.microsoft.com/fwlink/?linkid=2090451).
* [Python 2.7, ou 3.5 ou mais tarde](https://www.python.org/)

## <a name="setting-up"></a>Configuração

### <a name="create-a-form-recognizer-azure-resource"></a>Criar um recurso Do Reconhecimento de Formulários Azure

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Criar variáveis de ambiente

[!INCLUDE [environment-variables](../environment-variables.md)]


### <a name="create-a-new-python-application"></a>Criar uma nova aplicação python

Crie uma nova aplicação Python no seu editor preferido ou IDE. Em seguida, importar as seguintes bibliotecas.

```python
import os
import azure.ai.formrecognizer
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import ResourceNotFoundError
```

Crie variáveis para o ponto final e chave azure do seu recurso. Se criou a variável ambiental depois de lançar a aplicação, terá de fechar e reabrir o editor, IDE ou shell para aceder à variável.

```python
endpoint = os.environ["FORM_RECOGNIZER_ENDPOINT"]
key = os.environ["FORM_RECOGNIZER_KEY"]
```

### <a name="install-the-client-library"></a>Instale a biblioteca do cliente

Depois de instalar a Python, pode instalar a biblioteca do cliente com:

```console
pip install azure-ai-formrecognizer
```

<!-- 
tbd object model
-->

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes Do Reconhecimento de Formulários para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Reconhecer conteúdo de formulário](#recognize-form-content)
* [Reconhecer recibos](#recognize-receipts)
* [Preparar um modelo personalizado](#train-a-custom-model)
* [Analisar formulários com um modelo personalizado](#analyze-forms-with-a-custom-model)
* [Gerencie os seus modelos personalizados](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>Autenticar o cliente

Aqui, irá autenticar dois objetos de cliente utilizando as variáveis de subscrição que definiu acima. Utilizará um objeto **AzureKeyCredential,** para que, se necessário, possa atualizar a tecla API sem criar novos objetos de cliente.

```python
form_recognizer_client = FormRecognizerClient(endpoint=self.endpoint, credential=AzureKeyCredential(self.key))

form_training_client = FormTrainingClient(self.endpoint, AzureKeyCredential(self.key))
```

## <a name="define-variables"></a>Definir variáveis

> [!NOTE]
> Os fragmentos de código neste guia utilizam formulários remotos acessados por URLs. Se quiser processar documentos de formulário local, consulte os métodos relacionados na [documentação](https://docs.microsoft.com/python/api/overview/azure/formrecognizer?view=azure-python-preview) de referência e [amostras.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

Também terá de adicionar referências aos URLs para os seus dados de treino e teste.
* Para recuperar o URL SAS para os seus dados de treino de modelo personalizado, abra o Microsoft Azure Storage Explorer, clique no seu recipiente e selecione Obter assinatura de **acesso partilhado**. Certifique-se de que as permissões **de Leitura** e **Lista** são verificadas e clique em **Criar**. Em seguida, copie o valor na secção **URL.** Deve ter a forma: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .
* Para obter um URL de um formulário para testar, pode usar os passos acima para obter o URL SAS de um documento individual no armazenamento de blob. Ou, pegue o URL de um documento localizado em outro lugar.
* Utilize o método acima para obter o URL de uma imagem de recibo também, ou use o URL de imagem da amostra fornecido.

```python
trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>"
formUrl = "<SAS-URL-of-a-form-in-blob-storage>"
receiptUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/receipt/contoso-receipt.png"
```

## <a name="recognize-form-content"></a>Reconhecer conteúdo de formulário

Pode usar o Reconhecimento de Formulários para reconhecer mesas, linhas e palavras em documentos, sem precisar de treinar um modelo.

Para reconhecer o conteúdo de um ficheiro num determinado URL, utilize o método **begin_recognize_content.**

```Python
poller = form_recognizer_client.begin_recognize_content_from_url(formUrl)
contents = poller.result()
```

O valor devolvido é uma coleção de objetos **FormPage:** um para cada página no documento submetido. O código seguinte itera através destes objetos e imprime os pares de chaves/valor extraídos e os dados da tabela.

```python
for idx, content in enumerate(contents):
    print("----Recognizing content from page #{}----".format(idx))
    print("Has width: {} and height: {}, measured with unit: {}".format(
        content.width,
        content.height,
        content.unit
    ))
    for table_idx, table in enumerate(content.tables):
        print("Table # {} has {} rows and {} columns".format(table_idx, table.row_count, table.column_count))
        for cell in table.cells:
            print("...Cell[{}][{}] has text '{}' within bounding box '{}'".format(
                cell.row_index,
                cell.column_index,
                cell.text,
                format_bounding_box(cell.bounding_box)
            ))
    for line_idx, line in enumerate(content.lines):
        print("Line # {} has word count '{}' and text '{}' within bounding box '{}'".format(
            line_idx,
            len(line.words),
            line.text,
            format_bounding_box(line.bounding_box)
        ))
    print("----------------------------------------")
```

O código acima utiliza uma função de ajudante para simplificar as coordenadas de uma caixa de `format_bounding_box` delimitação. Defina-o separadamente:

```python
def format_bounding_box(bounding_box):
    if not bounding_box:
        return "N/A"
    return ", ".join(["[{}, {}]".format(p.x, p.y) for p in bounding_box])
```

## <a name="recognize-receipts"></a>Reconhecer recibos

Esta secção demonstra como reconhecer e extrair campos comuns a partir de recibos dos EUA, utilizando um modelo de recibo pré-treinado. Para reconhecer recibos de um URL, utilize o método **begin_recognize_receipts_from_url.** 

```python
poller = form_recognizer_client.begin_recognize_receipts_from_url(receiptUrl)
receipts = poller.result()
```

O valor devolvido é uma coleção de objetos **USReceipt:** um para cada página no documento submetido. O seguinte bloco de código imprime informações básicas de recibo para a consola.

```python
for idx, receipt in enumerate(receipts):
    print("--------Recognizing receipt #{}--------".format(idx))
    print("Receipt Type: {} has confidence: {}".format(receipt.receipt_type.type, receipt.receipt_type.confidence))
    print("Merchant Name: {} has confidence: {}".format(receipt.merchant_name.value, receipt.merchant_name.confidence))
    print("Transaction Date: {} has confidence: {}".format(receipt.transaction_date.value, receipt.transaction_date.confidence))
```

O próximo bloco de iterates de código através dos itens individuais detetados no recibo e imprime os seus dados para a consola.


```python
    print("Receipt items:")
    for item in receipt.receipt_items:
        print("...Item Name: {} has confidence: {}".format(item.name.value, item.name.confidence))
        print("...Item Quantity: {} has confidence: {}".format(item.quantity.value, item.quantity.confidence))
        print("...Individual Item Price: {} has confidence: {}".format(item.price.value, item.price.confidence))
        print("...Total Item Price: {} has confidence: {}".format(item.total_price.value, item.total_price.confidence))
```

Finalmente, o último bloco de código imprime o resto dos principais detalhes do recibo.

```python
    print("Subtotal: {} has confidence: {}".format(receipt.subtotal.value, receipt.subtotal.confidence))
    print("Tax: {} has confidence: {}".format(receipt.tax.value, receipt.tax.confidence))
    print("Tip: {} has confidence: {}".format(receipt.tip.value, receipt.tip.confidence))
    print("Total: {} has confidence: {}".format(receipt.total.value, receipt.total.confidence))
    print("--------------------------------------")
```


## <a name="train-a-custom-model"></a>Preparar um modelo personalizado

Esta secção demonstra como treinar um modelo com os seus próprios dados. Um modelo treinado pode obter dados estruturados que incluam as relações chave/valor no documento de formulário original. Depois de treinar o modelo, pode testar e retreiná-lo e eventualmente usá-lo para extrair dados de mais formas de acordo com as suas necessidades.

> [!NOTE]
> Também pode treinar modelos com uma interface gráfica do utilizador, como a ferramenta de rotulagem da [amostra 'Reconhecimento](../../quickstarts/label-tool.md)de Formulários'.

### <a name="train-a-model-without-labels"></a>Treine um modelo sem rótulos

Treine modelos personalizados para reconhecer todos os campos e valores encontrados nas suas formas personalizadas sem rotular manualmente os documentos de treino.

O código seguinte utiliza o cliente de formação com a **função begin_train_model** para formar um modelo num determinado conjunto de documentos.

```python
poller = form_training_client.begin_train_model(self.trainingDataUrl)
model = poller.result()
```

O objeto **CustomFormModel** devolvido contém informações sobre os tipos de formulário que o modelo pode reconhecer e os campos que pode extrair de cada tipo de formulário. O bloco de código seguinte imprime esta informação para a consola.

```python
# Custom model information
print("Model ID: {}".format(model.model_id))
print("Status: {}".format(model.status))
print("Created on: {}".format(model.created_on))
print("Last modified: {}".format(model.last_modified))

print("Recognized fields:")
# Looping through the submodels, which contains the fields they were trained on
for submodel in model.models:
    print("...The submodel has form type '{}'".format(submodel.form_type))
    for name, field in submodel.fields.items():
        print("...The model found field '{}' to have label '{}'".format(
            name, field.label
        ))
```

### <a name="train-a-model-with-labels"></a>Treine um modelo com rótulos

Também pode treinar modelos personalizados rotulando manualmente os documentos de treino. Treinar com rótulos leva a um melhor desempenho em alguns cenários. 

> [!IMPORTANT]
> Para treinar com etiquetas, é necessário dispor de ficheiros especiais de informação sobre* \<filename\> etiquetas (.pdf.labels.json*) no seu recipiente de armazenamento blob ao lado dos documentos de treino. A ferramenta de rotulagem da [amostra 'Reconhecimento](../../quickstarts/label-tool.md) de Formulários' fornece um UI para o ajudar a criar estes ficheiros de etiquetas. Uma vez que as tenha, pode ligar para a **função begin_train_model** com o parâmetro *use_labels* definido para `true` .

```python
poller = form_training_client.begin_train_model(self.trainingDataUrl, use_labels=True)
model = poller.result()
```

O **CustomFormModel** devolvido indica os campos que o modelo pode extrair, juntamente com a sua precisão estimada em cada campo. O bloco de código seguinte imprime esta informação para a consola.

```python
# Custom model information
print("Model ID: {}".format(model.model_id))
print("Status: {}".format(model.status))
print("Created on: {}".format(model.created_on))
print("Last modified: {}".format(model.last_modified))

print("Recognized fields:")
# looping through the submodels, which contains the fields they were trained on
# The labels are based on the ones you gave the training document.
for submodel in model.models:
    print("...The submodel with form type {} has accuracy '{}'".format(submodel.form_type, submodel.accuracy))
    for name, field in submodel.fields.items():
        print("...The model found field '{}' to have name '{}' with an accuracy of {}".format(
            name, field.name, field.accuracy
        ))
```

## <a name="analyze-forms-with-a-custom-model"></a>Analisar formulários com um modelo personalizado

Esta secção demonstra como extrair informações chave/valor e outros conteúdos dos seus tipos de formulários personalizados, utilizando modelos treinados com os seus próprios formulários.

> [!IMPORTANT]
> Para implementar este cenário, já deve ter treinado um modelo para que possa passar o seu ID para o método abaixo. Consulte a secção [de modelos do Comboio.](#train-a-model-without-labels)

Vaiusar o método **begin_recognize_custom_forms_from_url.** O valor devolvido é uma coleção de objetos **RecognizedForm:** um para cada página no documento submetido.

```python
# Make sure your form's type is included in the list of form types the custom model can recognize
poller = form_recognizer_client.begin_recognize_custom_forms_from_url(
    model_id=model.model_id, url=formUrl)
forms = poller.result()
```

O código que se segue imprime os resultados da análise para a consola. Imprime cada campo reconhecido e valor correspondente, juntamente com uma pontuação de confiança.

```python
for idx, form in enumerate(forms):
    print("--------Recognizing Form #{}--------".format(idx))
    print("Form {} has type {}".format(idx, form.form_type))
    for name, field in form.fields.items():
        # each field is of type FormField
        # The value of the field can also be a FormField, or a list of FormFields
        # In our sample, it is just a FormField.
        print("...Field '{}' has value '{}' with a confidence score of {}".format(
            name, field.value, field.confidence
        ))
        # label data is populated if you are using a model trained with unlabeled data, since the service needs to make predictions for
        # labels if not explicitly given to it.
        if field.label_data:
            print("...Field '{}' has label '{}' with a confidence score of {}".format(
                name,
                field.label_data.text,
                field.confidence
            ))
    print("-----------------------------------")
```

## <a name="manage-your-custom-models"></a>Gerencie os seus modelos personalizados

Esta secção demonstra como gerir os modelos personalizados armazenados na sua conta. 

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Verifique o número de modelos na conta de recurso FormRecogniser

O seguinte bloco de código verifica quantos modelos guardou na sua conta 'Reconhecimento de Formulários' e compara-o ao limite da conta.

```python
# First, we see how many custom models we have, and what our limit is
account_properties = form_training_client.get_account_properties()
print("Our account has {} custom models, and we can have at most {} custom models".format(
    account_properties.custom_model_count, account_properties.custom_model_limit
))
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Enumerar os modelos atualmente armazenados na conta de recursos

O bloco de código seguinte lista os modelos atuais na sua conta e imprime os seus dados para a consola. Também guarda uma referência ao primeiro modelo.

```python
# Next, we get a paged list of all of our custom models
custom_models = form_training_client.list_model_infos()

print("We have models with the following ids:")

# Let's pull out the first model
first_model = next(custom_models)
print(first_model.model_id)
for model in custom_models:
    print(model.model_id)
```

### <a name="get-a-specific-model-using-the-models-id"></a>Obtenha um modelo específico usando o ID do modelo

O bloco de código seguem o ID do modelo guardado na secção anterior e utiliza-o para obter detalhes sobre o modelo.

```python
# Now we'll get the first custom model in the paged list
custom_model = form_training_client.get_custom_model(model_id=first_model.model_id)
print("Model ID: {}".format(custom_model.model_id))
print("Status: {}".format(custom_model.status))
print("Created on: {}".format(custom_model.created_on))
print("Last modified: {}".format(custom_model.last_modified))
```

### <a name="delete-a-model-from-the-resource-account"></a>Eliminar um modelo da conta de recursos

Também pode eliminar um modelo da sua conta fazendo referência ao seu ID. Este código elimina o modelo utilizado na secção anterior.

```python
form_training_client.delete_model(model_id=custom_model.model_id)

# Confirm deletion:
try:
    form_training_client.get_custom_model(model_id=custom_model.model_id)
except ResourceNotFoundError:
    print("Successfully deleted model with id {}".format(custom_model.model_id))
}
```

## <a name="run-the-application"></a>Executar a aplicação

Execute o pedido com o `python` comando no seu ficheiro de arranque rápido.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos associados ao mesmo.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Resolução de Problemas

### <a name="general"></a>Geral

A biblioteca de clientes Do Reconhecimento de Formulários irá levantar exceções definidas no [Azure Core.](https://aka.ms/azsdk-python-azure-core)

## <a name="logging"></a>Registo

Esta biblioteca usa a [biblioteca de exploração madeireira padrão](https://docs.python.org/3/library/logging.html) para a exploração madeireira. Informações básicas sobre sessões HTTP (URLs, cabeçalhos, e assim por diante) são registadas ao nível de INFO.

A exploração de nível DEBUG detalhada, incluindo organismos de pedido/resposta e cabeçalhos não redigidos, pode ser ativada num cliente com o argumento da `logging_enable` palavra-chave:

```python
import sys
import logging
from azure.ai.formrecognizer import FormRecognizerClient
from azure.core.credentials import AzureKeyCredential

# Create a logger for the 'azure' SDK
logger = logging.getLogger('azure')
logger.setLevel(logging.DEBUG)

# Configure a console output
handler = logging.StreamHandler(stream=sys.stdout)
logger.addHandler(handler)

endpoint = "https://<my-custom-subdomain>.cognitiveservices.azure.com/"
credential = AzureKeyCredential("<api_key>")

# This client will log detailed information about its HTTP sessions, at DEBUG level
form_recognizer_client = FormRecognizerClient(endpoint, credential, logging_enable=True)
```

Da mesma forma, `logging_enable` pode permitir a exploração de sessão de registo detalhado para uma única operação, mesmo quando não está ativada para o cliente:

```python
poller = form_recognizer_client.begin_recognize_receipts(receipt, logging_enable=True)
```

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, usou a biblioteca de clientes Form Recogniser Python para treinar modelos e analisar formas de diferentes maneiras. Em seguida, aprenda dicas para criar um melhor conjunto de dados de treino e produzir modelos mais precisos.

> [!div class="nextstepaction"]
> [Criar um conjunto de dados de preparação](../../build-training-data-set.md)

* [O que é o Reconhecedor de Formato?](../../overview.md)
* O código de amostra deste guia (e muito mais) pode ser encontrado no [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).
