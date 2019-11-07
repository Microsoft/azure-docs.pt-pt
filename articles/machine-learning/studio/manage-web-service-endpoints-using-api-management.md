---
title: Gerenciar serviços Web usando o gerenciamento de API
titleSuffix: ML Studio (classic) Azure
description: Um guia que mostra como gerenciar os serviços Web do AzureML usando o gerenciamento de API. Gerencie seus pontos de extremidade da API REST definindo o acesso do usuário, a limitação de uso e o monitoramento do painel.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 11/03/2017
ms.openlocfilehash: 227a7205788194cc507dcd9dab7e5cad57abc7f9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73671582"
---
# <a name="manage-azure-machine-learning-studio-classic-web-services-using-api-management"></a>Gerenciar Azure Machine Learning Studio (clássico) serviços Web usando o gerenciamento de API
## <a name="overview"></a>Descrição geral
Este guia mostra como começar rapidamente a usar o gerenciamento de API para gerenciar seus serviços da Web Azure Machine Learning Studio (clássico).

## <a name="what-is-azure-api-management"></a>O que é a API Management do Azure?
O gerenciamento de API do Azure é um serviço do Azure que permite que você gerencie seus pontos de extremidade da API REST definindo o acesso do usuário, a limitação de uso e o monitoramento do painel. Consulte o [site de gerenciamento de API do Azure](https://azure.microsoft.com/services/api-management/) para obter mais detalhes. Para começar a usar o gerenciamento de API do Azure, consulte [o guia de importação e publicação](/azure/api-management/import-and-publish). Este outro guia, em que este guia se baseia, aborda mais tópicos, incluindo configurações de notificação, preços de camada, tratamento de resposta, autenticação de usuário, criação de produtos, assinaturas de desenvolvedor e painel de uso.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia, você precisará de:

* Uma conta do Azure.
* Uma conta do AzureML.
* O espaço de trabalho, o serviço e o api_key para um experimento do AzureML implantado como um serviço Web. Para obter detalhes sobre como criar um experimento do AzureML, consulte o guia de [início rápido do estúdio](create-experiment.md). Para obter informações sobre como implantar um experimento do Studio (clássico) como um serviço Web, consulte o [instruções de implantação do estúdio](deploy-a-machine-learning-web-service.md) para obter detalhes sobre como implantar um experimento do AzureML como um serviço Web. Como alternativa, o apêndice A tem instruções sobre como criar e testar um experimento simples do AzureML e implantá-lo como um serviço Web.

## <a name="create-an-api-management-instance"></a>Criar uma instância da API Management

Você pode gerenciar seu serviço Web do Azure Machine Learning com uma instância de gerenciamento de API.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **+ Criar um recurso**.
3. Na caixa de pesquisa, digite "gerenciamento de API" e, em seguida, selecione o recurso "gerenciamento de API".
4. Clique em **Criar**.
5. O valor de **nome** será usado para criar uma URL exclusiva (Este exemplo usa "demoazureml").
6. Selecione uma **assinatura**, um **grupo de recursos**e um **local** para sua instância de serviço.
7. Especifique um valor para **nome da organização** (Este exemplo usa "demoazureml").
8. Insira seu **email do administrador** -este email será usado para notificações do sistema de gerenciamento de API.
9. Clique em **Criar**.

Pode levar até 30 minutos para que um novo serviço seja criado.

![Criar serviço](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>Criar a API
Depois que a instância de serviço é criada, a próxima etapa é criar a API. Uma API consiste num conjunto de operações que podem ser invocadas a partir de uma aplicação cliente. As operações de API são suportadas com um proxy para serviços Web existentes. Este guia cria APIs que o proxy para os RRS do AzureML existentes e os serviços Web BES.

Para criar a API:

1. Na portal do Azure, abra a instância de serviço que você criou.
2. No painel de navegação à esquerda, selecione **APIs**.

   ![API-gerenciamento-menu](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. Clique em **Adicionar API**.
2. Insira um **nome de API Web** (Este exemplo usa "API de demonstração do AzureML").
3. Para **URL do serviço Web**, insira "`https://ussouthcentral.services.azureml.net`".
4. Insira um * * sufixo de URL da API Web ". Isso se tornará a última parte da URL que os clientes usarão para enviar solicitações para a instância de serviço (Este exemplo usa "azureml-demo").
5. Para **esquema de URL da API Web**, selecione **https**.
6. Para **produtos**, selecione **iniciador**.
7. Clique em **Guardar**.


## <a name="add-the-operations"></a>Adicionar as operações

As operações são adicionadas e configuradas para uma API no portal do Publicador. Para acessar o portal do editor, clique em **portal do editor** no portal do Azure para o serviço de gerenciamento de API, selecione **APIs**, **operações**e clique em **Adicionar operação**.

![Adicionar operação](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

A janela **nova operação** será exibida e a guia **assinatura** será selecionada por padrão.

## <a name="add-rrs-operation"></a>Adicionar operação RRS
Primeiro, crie uma operação para o serviço de RRS do AzureML:

1. Para o **verbo http**, selecione **post**.
2. Para o **modelo de URL**, digite "`/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}`".
3. Insira um **nome de exibição** (Este exemplo usa "RRs execute").

   ![Adicionar-RRs-operação-assinatura](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Clique em **respostas** > **Adicionar** à esquerda e selecione **200 OK**.
5. Clique em **salvar** para salvar esta operação.

   ![Adicionar-RRs-operação-resposta](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>Adicionar operações BES

> [!NOTE]
> As capturas de tela não são incluídas aqui para as operações BES, pois são muito semelhantes àquelas para adicionar a operação RRS.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Enviar (mas não iniciar) um trabalho de execução em lotes

1. Clique em **Adicionar operação** para adicionar uma operação BES à API.
2. Para o **verbo http**, selecione **post**.
3. Para o **modelo de URL**, digite "`/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}`".
4. Insira um **nome de exibição** (Este exemplo usa "BES Submit").
5. Clique em **respostas** > **Adicionar** à esquerda e selecione **200 OK**.
6. Clique em **Guardar**.

### <a name="start-a-batch-execution-job"></a>Iniciar um trabalho de execução em lotes

1. Clique em **Adicionar operação** para adicionar uma operação BES à API.
2. Para o **verbo http**, selecione **post**.
3. Para o **verbo http**, digite "`/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}`".
4. Insira um **nome de exibição** (Este exemplo usa "BES Start").
6. Clique em **respostas** > **Adicionar** à esquerda e selecione **200 OK**.
7. Clique em **Guardar**.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Obter o status ou o resultado de um trabalho de execução em lotes

1. Clique em **Adicionar operação** para adicionar uma operação BES à API.
2. Para o **verbo http**, selecione **Get**.
3. Para o **modelo de URL**, digite "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`".
4. Insira um **nome de exibição** (Este exemplo usa "BES status").
6. Clique em **respostas** > **Adicionar** à esquerda e selecione **200 OK**.
7. Clique em **Guardar**.

### <a name="delete-a-batch-execution-job"></a>Excluir um trabalho de execução em lotes

1. Clique em **Adicionar operação** para adicionar uma operação BES à API.
2. Para o **verbo http**, selecione **excluir**.
3. Para o **modelo de URL**, digite "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`".
4. Insira um **nome de exibição** (Este exemplo usa "BES Delete").
5. Clique em **respostas** > **Adicionar** à esquerda e selecione **200 OK**.
6. Clique em **Guardar**.

## <a name="call-an-operation-from-the-developer-portal"></a>Chamar uma operação no portal do desenvolvedor

As operações podem ser chamadas diretamente do portal do desenvolvedor, que fornece uma maneira conveniente de exibir e testar as operações de uma API. Nesta etapa, você chamará o método **RRs execute** que foi adicionado à API de **demonstração do AzureML**. 

1. Clique em **portal do desenvolvedor**.

   ![desenvolvedor-Portal](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. Clique em **APIs** no menu superior e clique em **API de demonstração do AzureML** para ver as operações disponíveis.

   ![demoazureml-API](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. Selecione **RRs executar** para a operação. Clique em **experimentar**.

   ![teste-ti](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. Para **parâmetros de solicitação**, digite seu **espaço de trabalho** e **serviço**, digite "2,0 para **apiversion**e" true "para obter os **detalhes**. Você pode encontrar seu **espaço de trabalho** e **serviço** no painel do serviço Web do AzureML (consulte **testar o serviço Web** no apêndice A).

   Para **cabeçalhos de solicitação**, clique em **Adicionar cabeçalho** e digite "Content-Type" e "Application/JSON". Clique em **Adicionar cabeçalho** novamente e digite "autorização" e "portador *\<seu Service API-Key\>* ". Você pode encontrar sua chave de API no painel do serviço Web do AzureML (consulte **testar o serviço Web** no apêndice A).

   Para o **corpo da solicitação**, digite `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}`.

   ![azureml-demonstração-API](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. Clique em **Enviar**.

   ![Enviar](./media/manage-web-service-endpoints-using-api-management/send.png)

Depois que uma operação é invocada, o portal do desenvolvedor exibe a **URL solicitada** do serviço de back-end, o **status da resposta**, os **cabeçalhos de resposta**e qualquer conteúdo de **resposta**.

![status da resposta](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Apêndice A-criando e testando um serviço Web do AzureML simples
### <a name="creating-the-experiment"></a>Criando o experimento
Abaixo estão as etapas para criar um experimento simples do AzureML e implantá-lo como um serviço Web. O serviço Web usa como entrada uma coluna de texto arbitrário e retorna um conjunto de recursos representados como inteiros. Por exemplo:

| Texto | Texto com hash |
| --- | --- |
| Este é um bom dia |1 1 2 2 0 2 0 1 |

Primeiro, usando um navegador de sua escolha, navegue até: [https://studio.azureml.net/](https://studio.azureml.net/) e insira suas credenciais para fazer logon. Em seguida, crie um novo experimento em branco.

![Pesquisar-experimento-modelos](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Renomeie-o como **SimpleFeatureHashingExperiment**. Expanda conjuntos de itens **salvos** e arraste as **revisões de livros do Amazon** para seu experimento.

![simples-Feature-hash-experimento](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Expanda a **transformação** e a **manipulação** de dados e arraste **selecionar colunas no DataSet** para seu experimento. Conecte as **revisões do livro da Amazon** para **selecionar colunas no conjunto**de registros.

![Conectar o módulo conjunto de registros de análises de livros a um módulo de colunas de projeto](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Clique em **selecionar colunas no conjunto** de e, em seguida, clique em **Iniciar seletor de coluna** e selecione **Col2**. Clique na marca de seleção para aplicar essas alterações.

![Selecionar colunas usando nomes de coluna](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Expanda **análise de texto** e arraste o **hash de recurso** para o experimento. Conectar **selecionar colunas no conjunto** de **recursos para hash de recurso**.

![conectar-projeto-colunas](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Digite **3** para o **bits de hash**. Isso criará 8 (23) colunas.

![hash-bitsize](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

Neste ponto, talvez você queira clicar em **executar** para testar o experimento.

![funcionam](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Criar um serviço Web
Agora, crie um serviço Web. Expanda **serviço Web** e arraste **entrada** para seu experimento. Conecte a **entrada** ao **hash de recurso**. Além disso, arraste a **saída** para o experimento. Conecte a **saída** ao **hash de recurso**.

![saída-para-recurso-hashing](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Clique em **publicar serviço Web**.

![publicar-Web-Service](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Clique em **Sim** para publicar o experimento.

![Sim para publicar](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>Testar o serviço Web
Um serviço Web do AzureML consiste em RSS (serviço de solicitação/resposta) e pontos de extremidade BES (serviço de execução em lote). O RSS é para execução síncrona. BES é para a execução de trabalhos assíncronos. Para testar seu serviço Web com a fonte Python de exemplo abaixo, talvez seja necessário baixar e instalar o SDK do Azure para Python (consulte: [como instalar o Python](/azure/python/python-sdk-azure-install)).

Você também precisará do **espaço de trabalho**, **serviço**e **api_key** de seu experimento para a fonte de exemplo abaixo. Você pode encontrar o espaço de trabalho e o serviço clicando em **solicitação/resposta** ou **em execução em lote** para seu experimento no painel do serviço Web.

![Localizar-espaço de trabalho e serviço](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Você pode encontrar o **api_key** clicando em seu experimento no painel do serviço Web.

![Localizar chave-API](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Ponto de extremidade RRS de teste
##### <a name="test-button"></a>Botão Testar
Uma maneira fácil de testar o ponto de extremidade RRS é clicar em **testar** no painel do serviço Web.

![test](./media/manage-web-service-endpoints-using-api-management/test.png)

Digite **este é um bom dia** para **Col2**. Clique na marca de seleção.

![inserir dados](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Você verá algo como

![exemplo-saída](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Código de exemplo
Outra maneira de testar os RRS é do seu código de cliente. Se você clicar em **solicitação/resposta** no painel e rolar para a parte inferior, verá o código de exemplo C#para, Python e R. Você também verá a sintaxe da solicitação RRS, incluindo o URI de solicitação, os cabeçalhos e o corpo.

Este guia mostra um exemplo funcional de Python. Você precisará modificá-lo com o **espaço de trabalho**, o **serviço**e o **api_key** do seu experimento.

    import urllib2
    import json
    workspace = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE WORKSPACE ID>"
    service = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE SERVICE ID>"
    api_key = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE API KEY>"
    data = {
    "Inputs": {
        "input1": {
            "ColumnNames": ["Col2"],
            "Values": [ [ "This is a good day" ] ]
        },
    },
    "GlobalParameters": { }
    }
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
    headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
    body = str.encode(json.dumps(data))
    try:
        req = urllib2.Request(url, body, headers)
        response = urllib2.urlopen(req)
        result = response.read()
        print "result:" + result
            except urllib2.HTTPError, error:
        print("The request failed with status code: " + str(error.code))
        print(error.info())
        print(json.loads(error.read()))

#### <a name="test-bes-endpoint"></a>Testar ponto de extremidade BES
Clique em **execução em lote** no painel e role até a parte inferior. Você verá o código de exemplo C#para o, Python e R. Você também verá a sintaxe das solicitações BES para enviar um trabalho, iniciar um trabalho, obter o status ou os resultados de um trabalho e excluir um trabalho.

Este guia mostra um exemplo funcional de Python. Você precisa modificá-lo com o **espaço de trabalho**, o **serviço**e o **api_key** de seu experimento. Além disso, você precisa modificar o **nome da conta de armazenamento**, a **chave da conta de armazenamento**e o nome do contêiner de **armazenamento**. Por fim, você precisará modificar o local do arquivo de **entrada** e o local do arquivo de **saída**.

    import urllib2
    import json
    import time
    from azure.storage import *
    workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
    service = "<REPLACE WITH YOUR SERVICE ID>"
    api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
    storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
    storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
    storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
    input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
    output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
    input_blob_name = "mydatablob.csv"
    output_blob_name = "myresultsblob.csv"
    def printHttpError(httpError):
    print("The request failed with status code: " + str(httpError.code))
    print(httpError.info())
    print(json.loads(httpError.read()))
    return
    def saveBlobToFile(blobUrl, resultsLabel):
    print("Reading the result from " + blobUrl)
    try:
        response = urllib2.urlopen(blobUrl)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    with open(output_file, "wb+") as f:
        f.write(response.read())
    print(resultsLabel + " have been written to the file " + output_file)
    return
    def processResults(result):
    first = True
    results = result["Results"]
    for outputName in results:
        result_blob_location = results[outputName]
        sas_token = result_blob_location["SasBlobToken"]
        base_url = result_blob_location["BaseLocation"]
        relative_url = result_blob_location["RelativeLocation"]
        print("The results for " + outputName + " are available at the following Azure Storage location:")
        print("BaseLocation: " + base_url)
        print("RelativeLocation: " + relative_url)
        print("SasBlobToken: " + sas_token)
        if (first):
            first = False
            url3 = base_url + relative_url + sas_token
            saveBlobToFile(url3, "The results for " + outputName)
    return

    def invokeBatchExecutionService():
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
    blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
    print("Uploading the input to blob storage...")
    data_to_upload = open(input_file, "r").read()
    blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
    print "Uploaded the input to blob storage"
    input_blob_path = "/" + storage_container_name + "/" + input_blob_name
    connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
    payload =  {
        "Input": {
            "ConnectionString": connection_string,
            "RelativeLocation": input_blob_path
        },
        "Outputs": {
            "output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
        },
        "GlobalParameters": {
        }
    }
        body = str.encode(json.dumps(payload))
    headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
    print("Submitting the job...")
    # submit the job
    req = urllib2.Request(url + "?api-version=2.0", body, headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    result = response.read()
    job_id = result[1:-1] # remove the enclosing double-quotes
    print("Job ID: " + job_id)
    # start the job
    print("Starting the job...")
    req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    url2 = url + "/" + job_id + "?api-version=2.0"

    while True:
        print("Checking the job status...")
        # If you are using Python 3+, replace urllib2 with urllib.request in the following code
        req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
        try:
            response = urllib2.urlopen(req)
        except urllib2.HTTPError, error:
            printHttpError(error)
            return
        result = json.loads(response.read())
        status = result["StatusCode"]
        print "status:" + status
        if (status == 0 or status == "NotStarted"):
            print("Job " + job_id + " not yet started...")
        elif (status == 1 or status == "Running"):
            print("Job " + job_id + " running...")
        elif (status == 2 or status == "Failed"):
            print("Job " + job_id + " failed!")
            print("Error details: " + result["Details"])
            break
        elif (status == 3 or status == "Cancelled"):
            print("Job " + job_id + " cancelled!")
            break
        elif (status == 4 or status == "Finished"):
            print("Job " + job_id + " finished!")
            processResults(result)
            break
        time.sleep(1) # wait one second
    return
    invokeBatchExecutionService()
