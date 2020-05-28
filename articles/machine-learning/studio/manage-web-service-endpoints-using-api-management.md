---
title: Gerir serviços web utilizando a API Management
titleSuffix: ML Studio (classic) - Azure
description: Um guia que mostra como gerir os serviços web da AzureML utilizando a API Management. Gerencie os seus pontos finais REST API definindo o acesso ao utilizador, o estrangulamento de utilização e a monitorização do dashboard.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/03/2017
ms.openlocfilehash: a5814113906aadad01821f78863f5053b8082892
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84114831"
---
# <a name="manage-azure-machine-learning-studio-classic-web-services-using-api-management"></a>Gerir o Azure Machine Learning Studio (clássico) serviços web utilizando a API Management

## <a name="overview"></a>Descrição geral
Este guia mostra-lhe como começar rapidamente a usar a API Management para gerir os seus serviços web Azure Machine Learning Studio (clássicos).

## <a name="what-is-azure-api-management"></a>O que é a API Management do Azure?
A Azure API Management é um serviço Azure que lhe permite gerir os seus pontos finais REST API, definindo o acesso ao utilizador, o estrangulamento de utilização e a monitorização do dashboard. Consulte o site de gestão da [API Azure](https://azure.microsoft.com/services/api-management/) para mais detalhes. Para começar com a Azure API Management, consulte [o guia de importação e publicação.](/azure/api-management/import-and-publish) Este outro guia, no qual este guia se baseia, aborda mais tópicos, incluindo configurações de notificação, preços de nível, manuseamento de respostas, autenticação do utilizador, criação de produtos, subscrições de desenvolvedores e dashboarding de uso.

## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia, precisa de:

* Uma conta do Azure.
* Uma conta AzureML.
* O espaço de trabalho, o serviço e o api_key para uma experiência AzureML implementada como um serviço web. Para mais detalhes sobre como criar uma experiência AzureML, consulte o [Studio quickstart](create-experiment.md). Para obter informações sobre como implementar uma experiência studio (clássica) como um serviço web, consulte o [Studio implementando como](deploy-a-machine-learning-web-service.md) obter detalhes sobre como implementar uma experiência AzureML como um serviço web. Alternadamente, o Apêndice A tem instruções para como criar e testar uma experiência simples do AzureML e implantá-la como um serviço web.

## <a name="create-an-api-management-instance"></a>Criar uma instância da API Management

Pode gerir o seu serviço web Azure Machine Learning com uma instância de Gestão API.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **+ Criar um recurso**.
3. Na caixa de pesquisa, digite "Gestão aPI", em seguida, selecione o recurso "Gestão aPi".
4. Clique em **Criar**.
5. O valor **nome** será usado para criar um URL único (este exemplo usa "demoazureml").
6. Selecione uma **Subscrição,** **grupo de recursos**e **localização** para a sua instância de serviço.
7. Especifique um valor para o **nome da Organização** (este exemplo utiliza "demoazureml").
8. Insira o seu **email do Administrador** - este email será utilizado para notificações do sistema de Gestão API.
9. Clique em **Criar**.

Pode levar até 30 minutos para que um novo serviço seja criado.

![criar serviço](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>Criar a API
Uma vez criada a instância de serviço, o próximo passo é criar a API. Uma API consiste num conjunto de operações que podem ser invocadas a partir de uma aplicação cliente. As operações de API são suportadas com um proxy para serviços Web existentes. Este guia cria APIs que proxy para os serviços web AzureML RRS e BES existentes.

Para criar a API:

1. No portal Azure, abra a instância de serviço que criou.
2. No painel de navegação esquerdo, selecione **APIs**.

   ![api-management-menu](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. Clique em **adicionar API**.
2. Introduza um **nome Web API** (este exemplo utiliza "AzureML Demo API").
3. Para URL de **serviço Web,** introduza `https://ussouthcentral.services.azureml.net` " ".
4. Introduza um sufixo URL aPI **Web API". Esta será a última parte do URL que os clientes usarão para enviar pedidos para a instância de serviço (este exemplo utiliza "azureml-demo").
5. Para **o esquema de URL Web API,** selecione **HTTPS**.
6. Para **produtos,** selecione **Starter**.
7. Clique em **Guardar**.


## <a name="add-the-operations"></a>Adicione as operações

As operações são adicionadas e configuradas a uma API no portal da editora. Para aceder ao portal da editora, clique no **portal Publisher** no portal Azure para o seu serviço de Gestão API, selecione **APIs,** **Operações,** em seguida, clique em **Adicionar operação**.

![add-operation](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

A nova janela de **funcionamento** será exibida e o separador **Signature** será selecionado por defeito.

## <a name="add-rrs-operation"></a>Adicionar operação RRS
Primeiro crie uma operação para o serviço AzureML RRS:

1. Para o **verbo HTTP,** selecione **POST**.
2. Para o **modelo de URL,** escreva `/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}` " ".
3. Introduza um **nome display** (este exemplo utiliza "RRS Executar").

   ![add-rrs-operação-assinatura](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Clique em **Respostas**  >  **ADD** à esquerda e selecione **200 OK**.
5. Clique em **Guardar** para salvar esta operação.

   ![add-rrs-operação-resposta](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>Adicionar operações do BES

> [!NOTE]
> As imagens não estão incluídas aqui para as operações do BES, uma vez que são muito semelhantes às que se destinam à operação RRS.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Submeter (mas não começar) um trabalho de execução de lote

1. Clique em **adicionar operação** para adicionar uma operação BES à API.
2. Para o **verbo HTTP,** selecione **POST**.
3. Para o **modelo de URL,** escreva `/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}` " ".
4. Introduza um **nome de exibição** (este exemplo utiliza "BES Submit").
5. Clique em **Respostas**  >  **ADD** à esquerda e selecione **200 OK**.
6. Clique em **Guardar**.

### <a name="start-a-batch-execution-job"></a>Inicie um trabalho de execução de lote

1. Clique em **adicionar operação** para adicionar uma operação BES à API.
2. Para o **verbo HTTP,** selecione **POST**.
3. Para o **verbo HTTP,** escreva `/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}` " ".
4. Introduza um **nome display** (este exemplo utiliza "BES Start").
6. Clique em **Respostas**  >  **ADD** à esquerda e selecione **200 OK**.
7. Clique em **Guardar**.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Obtenha o estado ou resultado de um trabalho de execução de lote

1. Clique em **adicionar operação** para adicionar uma operação BES à API.
2. Para o **verbo HTTP,** selecione **GET**.
3. Para o **modelo de URL,** escreva `/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}` " ".
4. Introduza um **nome de exibição** (este exemplo utiliza "Estado DO BES").
6. Clique em **Respostas**  >  **ADD** à esquerda e selecione **200 OK**.
7. Clique em **Guardar**.

### <a name="delete-a-batch-execution-job"></a>Eliminar um trabalho de execução de lote

1. Clique em **adicionar operação** para adicionar uma operação BES à API.
2. Para o **verbo HTTP,** selecione **DELETE**.
3. Para o **modelo de URL,** escreva `/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}` " ".
4. Introduza um **nome de exibição** (este exemplo utiliza "BES Delete").
5. Clique em **Respostas**  >  **ADD** à esquerda e selecione **200 OK**.
6. Clique em **Guardar**.

## <a name="call-an-operation-from-the-developer-portal"></a>Ligue para uma operação do portal Developer

As operações podem ser chamadas diretamente a partir do portal Developer, que fornece uma forma conveniente de visualizar e testar as operações de uma API. Neste passo, você vai chamar o método **RRS Execute** que foi adicionado à **API de demonstração AzureML**. 

1. Clique no **portal Developer**.

   ![developer-portal](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. Clique em **APIs** no menu superior e, em seguida, clique em **AzureML Demo API** para ver as operações disponíveis.

   ![demoazureml-api](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. Selecione **RRS Executar** para a operação. Clique em **experimentá-lo**.

   ![tentar](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. Para **os parâmetros Request**, digite o seu espaço de **trabalho** e **serviço,** escreva "2.0 para a **apiversão**" e "verdadeiro" para os **detalhes**. Pode encontrar o seu espaço de **trabalho** e **serviço** no painel de instrumentos de serviço web AzureML (ver Testar o **serviço web** no Apêndice A).

   Para **obter cabeçalhos,** clique em **adicionar cabeçalho** e digite "Content-Type" e "application/json". Clique em **adicionar cabeçalho** novamente e digite "Autorização" e "Portador". *\<your service API-KEY\>* Pode encontrar o seu API-KEY no painel de instrumentos de serviço web AzureML (ver **Testar o serviço web** no Apêndice A).

   Para **solicitar corpo,** escreva `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}` .

   ![azureml-demo-api](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. Clique em **Enviar**.

   ![enviar](./media/manage-web-service-endpoints-using-api-management/send.png)

Após a invocação de uma operação, o portal de desenvolvimento apresenta o **URL solicitado** do serviço back-end, o **estado de resposta,** os **cabeçalhos de resposta**e qualquer conteúdo de **Resposta**.

![estado de resposta](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Apêndice A - Criação e teste de um simples serviço web AzureML
### <a name="creating-the-experiment"></a>Criando a experiência
Abaixo estão os passos para criar uma experiência simples azureML e implementá-la como um serviço web. O serviço web toma como entrada uma coluna de texto arbitrário e devolve um conjunto de funcionalidades representadas como inteiros. Por exemplo:

| Texto | Texto hashed |
| --- | --- |
| Este é um bom dia. |1 1 2 2 0 2 0 1 |

Primeiro, usando um navegador à sua escolha, navegue para: [https://studio.azureml.net/](https://studio.azureml.net/) e insira as suas credenciais para iniciar sessão. Em seguida, criar uma nova experiência em branco.

![modelos de pesquisa-experiência](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Mude o nome para **SimpleFeatureHashingExperiment**. Expanda **datasets de dados guardados** e arraste **as avaliações de livros da Amazon** para a sua experiência.

![simples-característica-hashing-experiment](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Expanda a **transformação** e **manipulação** de dados e arraste **colunas selecionadas em Dataset** para a sua experiência. Conecte **comentários de livros da Amazon** para selecionar **colunas no Dataset**.

![Ligue o módulo de dataset de comentários do livro a um módulo de Colunas de Projeto](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Clique **em Selecionar Colunas no Dataset** e, em seguida, clique no seletor de **colunas de lançamento** e selecione **Col2**. Clique na marca de verificação para aplicar estas alterações.

![Selecione colunas usando nomes de colunas](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Expanda **o Text Analytics** e arraste o **Hashing** para a experiência. Ligue **as colunas seletas no conjunto** de dados à **hashing**feature .

![ligar colunas de projetos](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Tipo **3** para o tamanho do **hashing**. Isto criará 8 (23) colunas.

![hashing-bitsize](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

Neste ponto, é melhor clicar em **Correr** para testar a experiência.

![correr](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Criar um serviço Web
Agora crie um serviço web. Expanda **o Serviço Web** e arraste a **Entrada** para a sua experiência. Ligue a **entrada** ao **hashing feature**. Também arraste **a saída** para a sua experiência. Ligue a **saída** ao **hashing feature**.

![output-to-feature-hashing](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Clique **em Publicar o serviço web**.

![serviço web de publicação](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Clique em **Sim** para publicar a experiência.

![sim-publicar](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>Testar o serviço Web
Um serviço web AzureML é composto por pontos finais RSS (serviço de pedido/resposta) e BES (serviço de execução de lote). RSS é para execução sincronizada. O BES é para uma execução de emprego assíncrono. Para testar o seu serviço web com a fonte da amostra Python abaixo, poderá ser necessário descarregar e instalar o Azure SDK para Python (ver: [Como instalar python).](/azure/developer/python/azure-sdk-install)

Você também precisará do espaço de **trabalho,** **serviço,** e **api_key** da sua experiência para a fonte de amostra abaixo. Pode encontrar o espaço de trabalho e o serviço clicando em **Solicitar/Resposta** ou **executar lote** para a sua experiência no painel de instrumentos do serviço web.

![encontrar espaço de trabalho-e-serviço](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Pode encontrar o **api_key** clicando na sua experiência no painel de instrumentos do serviço web.

![find-api-key](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Ponto final do teste RRS
##### <a name="test-button"></a>Botão Testar
Uma maneira fácil de testar o ponto final rrs é clicar em **Teste** no painel de instrumentos do serviço web.

![test](./media/manage-web-service-endpoints-using-api-management/test.png)

Tipo **Este é um bom dia** para o **col2**. Clique na marca de verificação.

![dados de entrada](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Verá algo como.

![amostra-saída](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Código de Exemplo
Outra forma de testar o seu RRS é do seu código de cliente. Se clicar em **Pedido/resposta** no painel de instrumentos e rolar para baixo, verá o código da amostra para C#, Python e R. Você também verá a sintaxe do pedido RRS, incluindo o pedido URI, cabeçalhos e corpo.

Este guia mostra um exemplo de Python em funcionamento. Terá de o modificar com o espaço de **trabalho,** **serviço**e **api_key** da sua experiência.

    import urllib2
    import json
    workspace = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE WORKSPACE ID>"
    service = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE SERVICE ID>"
    api_key = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE API KEY>"
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

#### <a name="test-bes-endpoint"></a>Ponto final do TESTE BES
Clique na **execução** do Lote no painel de instrumentos e percorra para baixo. Verá o código de amostra para C#, Python e R. Verá também a sintaxe dos pedidos do BES para apresentar um emprego, iniciar um emprego, obter o estatuto ou os resultados de um emprego, e apagar um emprego.

Este guia mostra um exemplo de Python em funcionamento. Você precisa modificá-lo com o espaço de **trabalho,** **serviço,** e **api_key** da sua experiência. Além disso, é necessário modificar o nome da conta de **armazenamento,** **a chave da conta**de armazenamento e o nome do recipiente de **armazenamento.** Por último, terá de modificar a localização do ficheiro de **entrada** e a localização do ficheiro de **saída**.

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
