---
title: 'Tutorial: Utilize uma Função Azure para processar documentos armazenados'
titleSuffix: Azure Cognitive Services
description: Este guia mostra-lhe como utilizar uma função Azure para ativar o processamento de documentos que são enviados para um recipiente de armazenamento de bolhas Azure.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 03/19/2021
ms.author: lajanuar
ms.openlocfilehash: bf455d9401593b5c09fa295e492368a2a5bee240
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048697"
---
# <a name="tutorial-use-an-azure-function-to-process-stored-documents"></a>Tutorial: Utilize uma Função Azure para processar documentos armazenados

Pode utilizar o Form Recogniser como parte de um pipeline automatizado de processamento de dados construído com funções Azure. Este guia mostra-lhe como utilizar uma função Azure para processar documentos que são enviados para um recipiente de armazenamento de bolhas Azure. Este fluxo de trabalho extrai dados de tabelas de documentos armazenados utilizando o serviço De layout do Reconhecimento de Formulários e guarda os dados da tabela num ficheiro .csv em Azure. Em seguida, pode exibir os dados utilizando o Microsoft Power BI (não coberto aqui).

> [!div class="mx-imgBorder"]
> ![diagrama de fluxo de trabalho de serviço azul](./media/tutorial-azure-function/workflow-diagram.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma conta de Armazenamento do Azure
> * Criar um projeto das Funções do Azure
> * Extrair dados de layout de formulários carregados
> * Enviar dados de layout para o Azure Storage

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Crie um recurso De Reconhecimento de "  target="_blank"> Formulários crie um recurso de Reconhecimento de <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Formulários no portal Azure para obter a chave e ponto final do Reconhecimento de Formulários. Depois de implementar, clique em **Ir para o recurso**.
  * Necessitará da chave e ponto final do recurso que cria para ligar a sua aplicação à API do Reconhecimento de Formulários. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
  * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.
* Um documento PDF local para analisar. Pode descarregar este [documento de amostra](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout.pdf) para utilizar.
* [Python 3.8.x](https://www.python.org/downloads/) instalado.
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) instalado.
* [Azure Funções Core Tools](../../azure-functions/functions-run-local.md?tabs=windows%2ccsharp%2cbash#install-the-azure-functions-core-tools) instaladas.
* Código do Estúdio Visual com as seguintes extensões instaladas:
  * [Extensão de Funções Azure](/azure/developer/python/tutorial-vs-code-serverless-python-01#visual-studio-code-python-and-the-azure-functions-extension)
  * [Extensão python](https://code.visualstudio.com/docs/python/python-tutorial#_install-visual-studio-code-and-the-python-extension)

## <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure

[Crie uma conta de Armazenamento Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) no portal Azure. Selecione **StorageV2** como o tipo conta.

No painel esquerdo, selecione o **separador CORS** e retire a política CORS existente, se existir alguma.

Uma vez implantado, crie dois recipientes de armazenamento de bolhas vazias, nomeados **teste** e **saída**.

## <a name="create-an-azure-functions-project"></a>Criar um projeto das Funções do Azure

Abra o Visual Studio Code. Se instalou a extensão Azure Functions, deverá ver um logótipo Azure no painel de navegação esquerdo. Selecione-a. Crie um novo projeto, e quando solicitado criar uma pasta local **coa_new** para conter o projeto.

![VSCode criar botão de função](./media/tutorial-azure-function/vs-code-create-function.png)


Ser-lhe-á solicitado que configufique uma série de configurações:
* No **seletor de linguagem Select,** selecione Python.
* Na **solicitação do modelo Selecione um** pedido de modelo, selecione o gatilho de armazenamento Azure Blob. Em seguida, dê ao gatilho padrão um nome.
* No aviso **de definição Select,** opte por criar novas definições de aplicações locais.
* Selecione a sua subscrição Azure com a conta de armazenamento que criou. Em seguida, você precisa introduzir o nome do recipiente de armazenamento (neste caso, `test/{name}` )
* Opte por abrir o projeto na janela atual. 

![VSCode criar exemplo rápido](./media/tutorial-azure-function/vs-code-prompt.png)

Quando tiver concluído estes passos, o VSCode adicionará um novo projeto Azure Function com um script *\_ \_ \_ \_ init .py* Python. Este script será acionado quando um ficheiro é enviado para o recipiente de armazenamento de **teste,** mas não fará nada.

## <a name="test-the-function"></a>Testar a função

Prima F5 para executar a função básica. O VSCode irá solicitar-lhe que selecione uma conta de armazenamento para interagir com. Selecione a conta de armazenamento que criou e continue.

Abra o Azure Storage Explorer e carre fique com um documento PDF de amostra para o recipiente **de teste.** Em seguida, verifique o terminal VSCode. O script deve registar que foi desencadeado pelo upload PDF.

![Teste de terminal VSCode](./media/tutorial-azure-function/vs-code-terminal-test.png)


Pare o guião antes de continuar.

## <a name="add-form-processing-code"></a>Adicionar código de processamento de formulários

Em seguida, você adicionará o seu próprio código ao script Python para ligar para o serviço De Reconhecimento de Formulários e analisar os documentos carregados usando o Layout de Reconhecimento de Formulário [API](concept-layout.md).

No VSCode, navegue para o ficheiro *requirements.txt* da função. Isto define as dependências do seu guião. Adicione os seguintes pacotes Python ao ficheiro:

```
cryptography
azure-functions
azure-storage-blob
azure-identity
requests
pandas
numpy
```

Então, abra o *\_ \_ guião \_ \_ .py.* Adicione as seguintes instruções `import`:

```Python
import logging
from azure.storage.blob import BlobServiceClient
import azure.functions as func
import json
import time
from requests import get, post
import os
from collections import OrderedDict
import numpy as np
import pandas as pd
```

Pode deixar a `main` função gerada como está. Irá adicionar o seu código personalizado dentro desta função.

```python
# This part is automatically generated
def main(myblob: func.InputStream):
    logging.info(f"Python blob trigger function processed blob \n"
    f"Name: {myblob.name}\n"
    f"Blob Size: {myblob.length} bytes")
```

O bloco de código que se segue chama a API [de Layout de Análise](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync) de Reconhecedor de Formulários no documento carregado. Preencha os seus valores finais e chave. 


# <a name="version-20"></a>[versão 2.0](#tab/2-0)

```Python
# This is the call to the Form Recognizer endpoint
    endpoint = r"Your Form Recognizer Endpoint"
    apim_key = "Your Form Recognizer Key"
    post_url = endpoint + "/formrecognizer/v2.0/Layout/analyze"
    source = myblob.read()

    headers = {
    # Request headers
    'Content-Type': 'application/pdf',
    'Ocp-Apim-Subscription-Key': apim_key,
        }

    text1=os.path.basename(myblob.name)
```

# <a name="version-21-preview"></a>[versão 2.1 pré-visualização](#tab/2-1)

```Python
# This is the call to the Form Recognizer endpoint
    endpoint = r"Your Form Recognizer Endpoint"
    apim_key = "Your Form Recognizer Key"
    post_url = endpoint + "/formrecognizer/v2.1-preview.3/Layout/analyze"
    source = myblob.read()

    headers = {
    # Request headers
    'Content-Type': 'application/pdf',
    'Ocp-Apim-Subscription-Key': apim_key,
        }

    text1=os.path.basename(myblob.name)
```
---

> [!IMPORTANT]
> Aceda ao portal do Azure. Se o recurso 'Reconhecimento de Formulários' que criou na secção **Pré-Requisitos** implementado com sucesso, clique no botão **'Ir a Recursos'** nos **Passos Seguintes**. Pode encontrar a sua chave e ponto final na **página chave e ponto final** do recurso, sob **gestão de recursos.** 
>
> Lembre-se de remover a chave do seu código quando terminar, e nunca postá-la publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Para mais informações, consulte o artigo [de segurança dos Serviços Cognitivos.](../cognitive-services-security.md)

Em seguida, adicione código para consultar o serviço e obter os dados devolvidos. 


```Python
resp = requests.post(url = post_url, data = source, headers = headers)
    if resp.status_code != 202:
        print("POST analyze failed:\n%s" % resp.text)
        quit()
    print("POST analyze succeeded:\n%s" % resp.headers)
    get_url = resp.headers["operation-location"]

    wait_sec = 25
    
    time.sleep(wait_sec)
    # The layout API is async therefore the wait statement
    
    resp =requests.get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
    
    resp_json = json.loads(resp.text)
    
    
    status = resp_json["status"]
    
    
    if status == "succeeded":
        print("Layout Analysis succeeded:\n%s")
        results=resp_json
    else:
        print("GET Layout results failed:\n%s")
        quit()

    results=resp_json
```

Em seguida, adicione o seguinte código para ligar ao recipiente **de saída** Azure Storage. Preencha os seus próprios valores para o nome e chave da conta de armazenamento. Pode obter a chave no separador **teclas de acesso** do seu recurso de armazenamento no portal Azure.

```Python
# This is the connection to the blob storage, with the Azure Python SDK
    blob_service_client = BlobServiceClient.from_connection_string("DefaultEndpointsProtocol=https;AccountName="Storage Account Name";AccountKey="storage account key";EndpointSuffix=core.windows.net")
    container_client=blob_service_client.get_container_client("output")
```

O código seguinte analisa a resposta do Reconhecedor de Formulário devolvido, constrói um ficheiro .csv e envia-o para o recipiente **de saída.** 


> [!IMPORTANT]
> Provavelmente terá de editar este código para corresponder à estrutura dos seus próprios documentos de formulário.

```Python
# The code below is how I extract the json format into tabular data 
    # Please note that you need to adjust the code below to your form structure
    # It probably won't work out-of-box for your specific form
    pages = results["analyzeResult"]["pageResults"]

    def make_page(p):
        res=[]
        res_table=[]
        y=0
        page = pages[p]
        for tab in page["tables"]:
            for cell in tab["cells"]:
                res.append(cell)
                res_table.append(y)
            y=y+1
    
        res_table=pd.DataFrame(res_table)
        res=pd.DataFrame(res)
        res["table_num"]=res_table[0]
        h=res.drop(columns=["boundingBox","elements"])
        h.loc[:,"rownum"]=range(0,len(h))
        num_table=max(h["table_num"])
        return h, num_table, p

    h, num_table, p= make_page(0)   

    for k in range(num_table+1):
        new_table=h[h.table_num==k]
        new_table.loc[:,"rownum"]=range(0,len(new_table))
        row_table=pages[p]["tables"][k]["rows"]
        col_table=pages[p]["tables"][k]["columns"]
        b=np.zeros((row_table,col_table))
        b=pd.DataFrame(b)
        s=0
        for i,j in zip(new_table["rowIndex"],new_table["columnIndex"]):
            b.loc[i,j]=new_table.loc[new_table.loc[s,"rownum"],"text"]
            s=s+1

```

Finalmente, o último bloco de código envia a tabela extraída e os dados de texto para o seu elemento de armazenamento de bolhas.

```Python
    # Here is the upload to the blob storage
    tab1_csv=b.to_csv(header=False,index=False,mode='w')
    name1=(os.path.splitext(text1)[0]) +'.csv'
    container_client.upload_blob(name=name1,data=tab1_csv)
```

## <a name="run-the-function"></a>Executar a função

Prima F5 para voltar a executar a função. Utilize o Azure Storage Explorer para enviar um formulário PDF de amostra para o recipiente de armazenamento **de teste.** Esta ação deve desencadear a execução do script e, em seguida, deve ver o ficheiro .csv resultante (apresentado como tabela) no recipiente **de saída.**

Pode ligar este recipiente ao Power BI para criar visualizações ricas dos dados que contém.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar uma Função Azure escrita em Python para processar automaticamente documentos PDF carregados e desempenhá-lo num formato mais amigo dos dados. Em seguida, aprenda a usar o Power BI para exibir os dados.

> [!div class="nextstepaction"]
> [Microsoft Power BI](https://powerbi.microsoft.com/integrations/azure-table-storage/)

* [O que é o Reconhecedor de Formato?](overview.md)
* Saiba mais sobre o [Layout API](concept-layout.md)