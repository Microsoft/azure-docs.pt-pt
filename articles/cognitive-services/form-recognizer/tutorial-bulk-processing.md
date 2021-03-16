---
title: 'Tutorial: Extrair dados de formulário a granel usando Azure Data Factory - Reconhecimento de Formulários'
titleSuffix: Azure Cognitive Services
description: Crie atividades da Azure Data Factory para desencadear a formação e execução de modelos de Reconhecimento de Formulários e digitalizar um grande atraso de documentos.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/04/2021
ms.author: lajanuar
ms.openlocfilehash: 0c009a87a5834997cdc489efc75ebb16f9459754
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467107"
---
# <a name="tutorial-extract-form-data-in-bulk-by-using-azure-data-factory"></a>Tutorial: Extrair dados de formulário a granel utilizando a Azure Data Factory

Neste tutorial, vamos ver como usar os serviços da Azure para ingerir um grande lote de formas em meios digitais. O tutorial mostrará como automatizar a ingestão de dados de um lago de dados Azure de documentos numa base de dados Azure SQL. Você será capaz de treinar rapidamente modelos e processar novos documentos com alguns cliques.

## <a name="business-need"></a>Necessidade de negócio

A maioria das organizações está agora ciente do valor dos dados que têm em vários formatos (PDF, imagens, vídeos). Procuram as melhores práticas e formas mais económicas de digitalizar esses ativos.

Além disso, os nossos clientes têm frequentemente vários tipos de formas que vêm dos seus muitos clientes e clientes. Ao contrário dos [quickstarts,](./quickstarts/client-library.md)este tutorial mostra-lhe como treinar automaticamente um modelo com novos e diferentes tipos de formas utilizando uma abordagem orientada por metadados. Se não tiver um modelo existente para o tipo de formulário dado, o sistema criará um e dará-lhe o ID do modelo. 

Ao extrair os dados de formulários e aliá-lo aos sistemas operacionais e armazéns de dados existentes, as empresas podem obter insights e entregar valor aos seus clientes e utilizadores empresariais.

O Azure Form Recogniser ajuda as organizações a usar os seus dados, automatizar processos (pagamentos de faturas, processamento de impostos, e assim por diante), economizar dinheiro e tempo e usufruir de uma melhor precisão de dados.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Confiem do Lago Azure para armazenar os seus formulários.
> * Utilize uma base de dados Azure para criar uma tabela de parametrização.
> * Utilize o Cofre de Chaves Azure para armazenar credenciais sensíveis.
> * Treine o seu modelo De Reconhecimento de Formulários num caderno Azure Databricks.
> * Extraia os dados do formulário utilizando um caderno Databricks.
> * Automatizar formação e extração utilizando a Azure Data Factory.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. [Crie um de graça.](https://azure.microsoft.com/free/cognitive-services/)
* Depois de ter a sua subscrição Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" Crie um recurso De Reconhecimento de "  target="_blank"> Formulários crie um recurso De Reconhecimento de </a> Formulários no portal Azure para obter a sua chave e ponto final. Após o recurso ser implementado, selecione **Ir para recurso**.
    * Necessitará da chave e ponto final do recurso que cria para ligar a sua aplicação à API do Reconhecimento de Formulários. Vai colar a chave e o ponto final no seu código mais tarde neste arranque rápido.
    * Pode utilizar o nível de preços gratuitos (F0) para experimentar o serviço. Em seguida, pode atualizar mais tarde para um nível pago para produção.
* Um conjunto de pelo menos cinco formas do mesmo tipo. Idealmente, este fluxo de trabalho destina-se a suportar grandes conjuntos de documentos. Consulte [construir um conjunto de dados de treino](./build-training-data-set.md) para obter dicas e opções para reunir o conjunto de dados de treino. Para este tutorial, pode utilizar os ficheiros na pasta Train do conjunto de [dados](https://go.microsoft.com/fwlink/?linkid=2128080)da amostra .


## <a name="project-architecture"></a>Arquitetura do projeto 

Este projeto ergue um conjunto de oleodutos Azure Data Factory para desencadear cadernos Python que treinam, analisam e extraem dados de documentos numa conta de armazenamento do Azure Data Lake.

A API do Reconhecimento de Formulários REST requer alguns parâmetros como entrada. Por razões de segurança, alguns destes parâmetros serão guardados num cofre de chaves Azure. Outros parâmetros menos sensíveis, como o nome da pasta blob de armazenamento, serão armazenados numa tabela de parametrização numa base de dados Azure SQL.

Para que cada tipo de forma seja analisada, os engenheiros de dados ou cientistas de dados irão povoar uma linha da tabela de parâmetros. Em seguida, utilizarão a Azure Data Factory para iterar sobre a lista de tipos de formulários detetados e passar os parâmetros relevantes para um caderno Databricks para treinar ou treinar os modelos De Reconhecimento de Formulários. Uma função Azure também pode ser usada aqui.

O caderno Azure Databricks utiliza então os modelos treinados para extrair dados de formulário. Exporta esses dados para uma base de dados Azure SQL.

:::image type="content" source="./media/tutorial-bulk-processing/architecture.png" alt-text="Diagrama que mostra a arquitetura do projeto.":::


## <a name="set-up-azure-data-lake"></a>Configurar o Lago de Dados Azure

O seu atraso de formulários pode estar no seu ambiente no local ou num (s)FTP servidor. Este tutorial utiliza formulários numa conta Azure Data Lake Storage Gen2. Pode transferir os seus ficheiros para lá utilizando a Azure Data Factory, Azure Storage Explorer ou AzCopy. Os conjuntos de dados de treino e pontuação podem ser em diferentes recipientes, mas os conjuntos de dados de formação para todos os tipos de formulários devem estar no mesmo recipiente. (Podem estar em pastas diferentes.)

Para criar um novo lago de dados, siga as instruções na [Criar uma conta de armazenamento para utilizar com a Azure Data Lake Storage Gen2](../../storage/blobs/create-data-lake-storage-account.md).

## <a name="create-a-parameterization-table"></a>Criar uma tabela de parametrização

Em seguida, criaremos uma tabela de metadados numa base de dados Azure SQL. Esta tabela conterá os dados não sensíveis exigidos pela API do Reconhecimento de Formulários REST. Sempre que houver um novo tipo de forma no conjunto de dados, vamos inserir um novo recorde nesta tabela e desencadear os gasodutos de treino e pontuação. (Implementaremos os oleodutos mais tarde.)

Estes campos serão utilizados na tabela:

* **form_description.** Não é necessário como parte do treino. Este campo fornece uma descrição do tipo de formas para as quais estamos treinando o modelo (por exemplo, "cliente A formas", "Formulários do Hotel B").
* **training_container_name.** O nome do contentor da conta de armazenamento onde armazenamos o conjunto de dados de treino. Pode ser o mesmo recipiente **que scoring_container_name.**
* **training_blob_root_folder.** A pasta dentro da conta de armazenamento onde guardaremos os ficheiros para o treino do modelo.
* **scoring_container_name.** O nome do recipiente da conta de armazenamento onde armazenamos os ficheiros que queremos extrair os pares de chaves/valor. Pode ser o mesmo recipiente **que training_container_name.**
* **scoring_input_blob_folder.** A pasta na conta de armazenamento onde armazenaremos os ficheiros para extrair dados.
* **model_id.** A identificação do modelo que queremos retreinar. Para a primeira execução, o valor deve ser definido para -1, o que fará com que o caderno de treino crie um novo modelo personalizado para treinar. O caderno de formação devolverá o novo modelo ID à instância da Azure Data Factory. Utilizando uma atividade de procedimento armazenado, atualizaremos este valor na base de dados Azure SQL.

  Sempre que quiser ingerir um novo tipo de formulário, tem de redefinir manualmente o ID do modelo para -1 antes de treinar o modelo.

* **file_type.** Os tipos de formulários suportados `application/pdf` `image/jpeg` `image/png` são, `image/tif` e.

  Se tiver formulários em outros tipos de ficheiros, terá de alterar este valor e **model_id** quando treina um novo tipo de formulário.
* **form_batch_group_id.** Com o tempo, pode ter vários tipos de forma que treina contra o mesmo modelo. O **campo form_batch_group_id** permitir-lhe-á especificar todos os tipos de formulários que têm vindo a treinar com um modelo específico.

### <a name="create-the-table"></a>Criar a mesa


[Crie uma base de dados Azure SQL](https://ms.portal.azure.com/#create/Microsoft.SQLDatabase)e, em seguida, execute este script SQL no [editor de consulta](../../azure-sql/database/connect-query-portal.md) para criar a tabela necessária:


```sql
CREATE TABLE dbo.ParamFormRecogniser(
    form_description varchar(50) NULL,
  training_container_name varchar(50) NOT NULL,
    training_blob_root_folder varchar(50) NULL,
    scoring_container_name varchar(50) NOT NULL,
    scoring_input_blob_folder varchar(50) NOT NULL,
    scoring_output_blob_folder varchar(50) NOT NULL,
    model_id varchar(50) NULL,
    file_type varchar(50) NULL
) ON PRIMARY
GO
```

Execute este script para criar o procedimento que atualiza automaticamente **model_id** depois de treinado.

```SQL
CREATE PROCEDURE [dbo].[update_model_id] ( @form_batch_group_id  varchar(50),@model_id varchar(50))
AS
BEGIN 
    UPDATE [dbo].[ParamFormRecogniser]   
        SET [model_id] = @model_id  
    WHERE form_batch_group_id =@form_batch_group_id
END
```

## <a name="use-azure-key-vault-to-store-sensitive-credentials"></a>Use o cofre da chave Azure para armazenar credenciais sensíveis

Por razões de segurança, não queremos armazenar certas informações sensíveis na tabela de parametrização na base de dados Azure SQL. Armazenaremos parâmetros sensíveis como segredos do Azure Key Vault.

### <a name="create-an-azure-key-vault"></a>Criar um cofre de chaves Azure

[Crie um recurso Key Vault](https://ms.portal.azure.com/#create/Microsoft.KeyVault). Em seguida, vá ao recurso Key Vault depois de ser criado e, na secção **de definições,** selecione **segredos** para adicionar os parâmetros.

Será apresentada uma nova janela. **Selecione Gerar/importar.** Introduza o nome do parâmetro e o seu valor e, em seguida, selecione **criar**. Complete estes passos para os seguintes parâmetros:

* **CognitiveServiceEndpoint**. O URL de ponto final do seu Formulário Reconhecedor API.
* **CognitiveServiceSubscriptionKey**. A chave de acesso para o seu serviço Desemaçador de Formulários. 
* **ArmazenamentoAme de contagem**. A conta de armazenamento onde o conjunto de dados de treino e os formulários que pretende extrair são armazenados pares de chaves/valor. Se estes itens estiverem em contas diferentes, insira cada nome de conta como um segredo separado. Lembre-se que os conjuntos de dados de treino devem estar no mesmo recipiente para todos os tipos de formulários. Podem estar em pastas diferentes.
* **ArmazenamentoCotasAsKey**. A assinatura de acesso partilhado (SAS) da conta de armazenamento. Para recuperar o URL SAS, vá ao seu recurso de armazenamento. No separador **Storage Explorer,** vá ao seu recipiente, clique à direita e selecione **Obtenha a assinatura de acesso partilhado**. É importante obter o SAS para o seu contentor, não para a conta de armazenamento em si. Certifique-se de que as permissões **de Leitura** e **Lista** estão selecionadas e, em seguida, selecione **Criar**. Em seguida, copie o valor na secção **URL.** Deve ter este formulário: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .

## <a name="train-your-form-recognizer-model-in-a-databricks-notebook"></a>Treine o seu modelo de Reconhecimento de Formulários num caderno Databricks

Você usará Azure Databricks para armazenar e executar o código Python que interage com o serviço De Reconhecimento de Formulários.

### <a name="create-a-notebook-in-databricks"></a>Criar um caderno em Databricks

[Crie um recurso Azure Databricks](https://ms.portal.azure.com/#create/Microsoft.Databricks) no portal Azure. Vá ao recurso depois de criado, e abra o espaço de trabalho.

### <a name="create-a-secret-scope-backed-by-azure-key-vault"></a>Criar uma área secreta apoiada por Azure Key Vault


Para fazer referência aos segredos no cofre de chaves Azure que criou acima, terá de criar uma mira secreta em Databricks. Siga os passos aqui: [Crie um âmbito secreto apoiado por Azure Key Vault](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope).

### <a name="create-a-databricks-cluster"></a>Criar um cluster do Databricks

Um cluster é uma coleção de recursos computacional databricks. Para criar um cluster:

1. No painel esquerdo, selecione o **botão Clusters.**
1. Na página **Clusters,** selecione **Criar Cluster**.
1. Na página **'Criar Cluster',** especifique um nome de cluster e, em seguida, selecione **7.2 (Scala 2.12, Spark 3.0.0)** na lista de **versão runtime databricks.**
1. Selecione **Create Cluster** (Criar Cluster).

### <a name="write-a-settings-notebook"></a>Escreva um caderno de definições

Agora está pronto para adicionar cadernos Python. Primeiro, crie um caderno chamado **Definições**. Este caderno atribuirá os valores da sua tabela de parametrização a variáveis no script. A Azure Data Factory passará mais tarde os valores como parâmetros. Também atribuiremos valores dos segredos no cofre chave para variáveis. 

1. Para criar o portátil **Definições,** selecione o botão **Espaço de Trabalho.** No novo separador, selecione a lista de drop-down e selecione **criar** e, em seguida, **o caderno**.
1. Na janela pop-up, insira um nome para o caderno e, em seguida, selecione **Python** como linguagem predefinitiva. Selecione o seu cluster Databricks e, em seguida, **selecione Criar**.
1. Na primeira célula portátil, recuperamos os parâmetros passados pela Azure Data Factory:

    ```python 
    dbutils.widgets.text("form_batch_group_id", "","")
    dbutils.widgets.get("form_batch_group_id")
    form_batch_group_id = getArgument("form_batch_group_id")
    
    dbutils.widgets.text("model_id", "","")
    dbutils.widgets.get("model_id")
    model_id = getArgument("model_id")
    
    dbutils.widgets.text("training_container_name", "","")
    dbutils.widgets.get("training_container_name")
    training_container_name = getArgument("training_container_name")
    
    dbutils.widgets.text("training_blob_root_folder", "","")
    dbutils.widgets.get("training_blob_root_folder")
    training_blob_root_folder=  getArgument("training_blob_root_folder")
    
    dbutils.widgets.text("scoring_container_name", "","")
    dbutils.widgets.get("scoring_container_name")
    scoring_container_name=  getArgument("scoring_container_name")
    
    dbutils.widgets.text("scoring_input_blob_folder", "","")
    dbutils.widgets.get("scoring_input_blob_folder")
    scoring_input_blob_folder=  getArgument("scoring_input_blob_folder")
    
    
    dbutils.widgets.text("file_type", "","")
    dbutils.widgets.get("file_type")
    file_type = getArgument("file_type")
    
    dbutils.widgets.text("file_to_score_name", "","")
    dbutils.widgets.get("file_to_score_name")
    file_to_score_name=  getArgument("file_to_score_name")
    ```

1. Na segunda célula, recuperamos segredos do Key Vault e atribuímo-los a variáveis:

    ```python 
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    training_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    ScoredFile = file_to_score_name+ "_output.json"
    training_storage_account_url="https://"+training_storage_account_name+".blob.core.windows.net/"+training_container_name+storage_account_sas_key 
    ```

### <a name="write-a-training-notebook"></a>Escreva um caderno de treino

Agora que completamos o caderno **Definições, podemos** criar um caderno para treinar o modelo. Como mencionado acima, usaremos ficheiros armazenados numa pasta numa conta Azure Data Lake Storage Gen2 **(training_blob_root_folder).** O nome da pasta foi passado como variável. Cada conjunto de tipos de formulários estará na mesma pasta. À medida que nos dermos sobre a tabela de parâmetros, treinamos o modelo usando todos os tipos de formulários. 

1. Crie um caderno chamado **TrainFormRecognizer.** 
1. Na primeira célula, executar o caderno **Definições:**

    ```python
    %run "./Settings"
    ```

1. Na célula seguinte, atribua variáveis do ficheiro Definições e treine dinamicamente o modelo para cada tipo de formulário, aplicando o código no [arranque rápido](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md#get-training-results%20)REST .

    ```python
    import json
    import time
    from requests import get, post
    
    post_url = cognitive_service_endpoint + r"/formrecognizer/v2.0/custom/models"
    source = training_storage_account_url
    prefix= training_blob_root_folder
    
    includeSubFolders=True
    useLabelFile=False
    headers = {
        # Request headers.
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    body =     {
        "source": source
        ,"sourceFilter": {
            "prefix": prefix,
            "includeSubFolders": includeSubFolders
       },
    }
    if model_id=="-1": # If you don't already have a model you want to retrain. In this case, we create a model and use it to extract the key/value pairs.
      try:
          resp = post(url = post_url, json = body, headers = headers)
          if resp.status_code != 201:
              print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
              quit()
          print("POST model succeeded:\n%s" % resp.headers)
          get_url = resp.headers["location"]
          model_id=get_url[get_url.index('models/')+len('models/'):]     
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    else :# If you already have a model you want to retrain, we reuse it and (re)train with the new form types.  
      try:
        get_url =post_url+r"/"+model_id
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    ```

1. O passo final no processo de formação é obter o resultado da formação num formato JSON:

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
        try:
            resp = get(url = get_url, headers = headers)
            resp_json = resp.json()
            print (resp.status_code)
            if resp.status_code != 200:
                print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
                n_try += 1
                quit()
            model_status = resp_json["modelInfo"]["status"]
            print (model_status)
            if model_status == "ready":
                print("Training succeeded:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            if model_status == "invalid":
                print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            # Training still running. Wait and retry.
            time.sleep(wait_sec)
            n_try += 1
            wait_sec = min(2*wait_sec, max_wait_sec)     
            print (n_try)
        except Exception as e:
            msg = "GET model failed:\n%s" % str(e)
            print(msg)
            quit()
    print("Train operation did not complete within the allocated time.")
    ```

## <a name="extract-form-data-by-using-a-notebook"></a>Extrair dados de formulário usando um caderno

### <a name="mount-the-azure-data-lake-storage"></a>Monte o armazenamento do Lago Azure Data

O próximo passo é marcar as várias formas que temos utilizando o modelo treinado. Vamos montar a conta de armazenamento do Lago de Dados Azure em Databricks e referir-nos ao monte durante o processo de ingestão.

Como fizemos na fase de treino, usaremos a Azure Data Factory para invocar a extração dos pares chave/valor dos formulários. Vamos ligar as formas nas pastas que estão especificadas na tabela de parâmetros.

1. Crie o caderno para montar a conta de armazenamento em Databricks. Chame-lhe **MountDataLake**. 
1. Primeiro, terá de ligar para o bloco de **notas de Definições:**

    ```python
    %run "./Settings"
    ```

1. Na segunda célula, defina variáveis para os parâmetros sensíveis, que vamos recuperar dos nossos segredos do Cofre Chave:

    ```python
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    scoring_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    scoring_storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    scoring_mount_point = "/mnt/"+scoring_container_name 
    scoring_source_str = "wasbs://{container}@{storage_acct}.blob.core.windows.net/".format(container=scoring_container_name, storage_acct=scoring_storage_account_name) 
    scoring_conf_key = "fs.azure.sas.{container}.{storage_acct}.blob.core.windows.net".format(container=scoring_container_name, storage_acct=scoring_storage_account_name)
    
    ```

1. Tente desmontar a conta de armazenamento no caso de ter sido previamente montada:

    ```python
    try:
      dbutils.fs.unmount(scoring_mount_point) # Use this to unmount as needed
    except:
      print("{} already unmounted".format(scoring_mount_point))
    
    ```

1. Monte a conta de armazenamento:


    ```python
    try: 
      dbutils.fs.mount( 
        source = scoring_source_str, 
        mount_point = scoring_mount_point, 
        extra_configs = {scoring_conf_key: scoring_storage_account_sas_key} 
      ) 
    except Exception as e: 
      print("ERROR: {} already mounted. Run previous cells to unmount first".format(scoring_mount_point))
    
    ```

    > [!NOTE]
    > Montámos apenas a conta de armazenamento de treino. Neste caso, os ficheiros de formação e os ficheiros que queremos extrair pares de chaves/valor estão na mesma conta de armazenamento. Se as suas contas de armazenamento de pontuação e treino forem diferentes, terá de montar ambas as contas de armazenamento. 

### <a name="write-the-scoring-notebook"></a>Escreva o caderno de pontuação

Agora podemos criar um caderno de pontuação. Faremos algo semelhante ao que fizemos no caderno de treino: usaremos ficheiros armazenados em pastas na conta de armazenamento de datas do Azure que acabamos de montar. O nome da pasta é passado como uma variável. Vamos ligar todas as formas na pasta especificada e extrair os pares de chave/valor deles. 

1. Crie um caderno e chame-lhe **ScoreFormRecognizer**. 
1. Executar as **definições** e os cadernos **MountDataLake:**

    ```python
    %run "./Settings"
    %run "./MountDataLake"
    ```

1. Adicione este código, que chama a [API de Análise:](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post 
    
    
    #prefix= TrainingBlobFolder
    post_url = cognitive_service_endpoint + "/formrecognizer/v2.0/custom/models/%s/analyze" % model_id
    source = r"/dbfs/mnt/"+scoring_container_name+"/"+scoring_input_blob_folder+"/"+file_to_score_name
    output = r"/dbfs/mnt/"+scoring_container_name+"/scoringforms/ExtractionResult/"+os.path.splitext(os.path.basename(source))[0]+"_output.json"
    
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers.
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```

1. Na próxima célula, obteremos os resultados da extração de par chave/valor. Esta célula vai fazer o resultado. Queremos o resultado no formato JSON para que possamos processá-lo ainda mais na Base de Dados Azure SQL ou na Azure Cosmos DB. Então vamos escrever o resultado para um ficheiro .json. O nome do ficheiro de saída será o nome do ficheiro pontuado concatenado com "_output.jsligado". O ficheiro será armazenado na mesma pasta que o ficheiro de origem.

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
       try:
           resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": cognitive_service_subscription_key})
           resp_json = resp.json()
           if resp.status_code != 200:
               print("GET analyze results failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           status = resp_json["status"]
           if status == "succeeded":
               print("Analysis succeeded:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           if status == "failed":
               print("Analysis failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           # Analysis still running. Wait and retry.
           time.sleep(wait_sec)
           n_try += 1
           wait_sec = min(2*wait_sec, max_wait_sec)     
       except Exception as e:
           msg = "GET analyze results failed:\n%s" % str(e)
           print(msg)
           n_try += 1
           print("Analyze operation did not complete within the allocated time.")
           quit()
    
    ```
1. Faça o procedimento de escrita de ficheiros numa nova célula:

    ```python
    import requests
    file = open(output, "w")
    file.write(str(resp_json))
    file.close()
    ```

## <a name="automate-training-and-scoring-by-using-azure-data-factory"></a>Automatizar a formação e a pontuação utilizando a Azure Data Factory

O único passo que resta é criar o serviço Azure Data Factory para automatizar os processos de formação e pontuação. Primeiro, siga os passos sob [a criação de uma fábrica de dados.](../../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory) Depois de criar o recurso Azure Data Factory, terá de criar três oleodutos: um para treino e dois para pontuação. (Explicaremos mais tarde.)

### <a name="training-pipeline"></a>Gasoduto de formação

A primeira atividade no pipeline de treino é uma Procuração para ler e devolver os valores na tabela de parametrização na base de dados Azure SQL. Todos os conjuntos de dados de formação estarão na mesma conta de armazenamento e contentor (mas pastas potencialmente diferentes). Assim, manteremos o valor de atributo padrão **A primeira linha apenas** nas definições de atividade do Lookup. Para cada tipo de forma de treinar o modelo contra, vamos treinar o modelo usando todos os ficheiros em **training_blob_root_folder**.

:::image type="content" source="./media/tutorial-bulk-processing/training-pipeline.png" alt-text="Screenshot que mostra um oleoduto de treino na Fábrica de Dados.":::

O procedimento armazenado tem dois parâmetros: **model_id** e **form_batch_group_id**. O código para devolver o ID do modelo do caderno Databricks é `dbutils.notebook.exit(model_id)` . O código para ler o código na atividade de procedimento armazenado na Data Factory é `@activity('GetParametersFromMetadaTable').output.firstRow.form_batch_group_id` .

### <a name="scoring-pipelines"></a>Gasodutos de pontuação

Para extrair os pares chave/valor, vamos digitalizar todas as pastas na tabela de parametrização. Para cada pasta, extrairemos os pares de chaves/valor de todos os ficheiros que estão nele. A Azure Data Factory não suporta atualmente loops ForEach aninhados. Em vez disso, vamos criar dois oleodutos. O primeiro oleoduto fará a procura a partir da tabela de parametrização e passará a lista de pastas como parâmetro para o segundo oleoduto.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1a.png" alt-text="Screenshot que mostra o primeiro pipeline de pontuação na Data Factory.":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1b.png" alt-text="Screenshot que mostra os detalhes para o primeiro pipeline de pontuação em Data Factory.":::

O segundo oleoduto utilizará uma atividade GetMeta para obter a lista de ficheiros na pasta e passá-la como parâmetro para o portátil databricks de pontuação.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2a.png" alt-text="Screenshot que mostra o segundo pipeline de pontuação em Data Factory.":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2b.png" alt-text="Screenshot que mostra os detalhes para o segundo pipeline de pontuação em Data Factory.":::

### <a name="specify-a-degree-of-parallelism"></a>Especificar um grau de paralelismo

Tanto nos gasodutos de treino como na pontuação, pode especificar o grau de paralelismo para que possa processar várias formas simultaneamente.

Para definir o grau de paralelismo no oleoduto Azure Data Factory:

1. Selecione a atividade **ForEach.**
1. Limpe a caixa **sequencial.**
1. Desa cimente o grau de paralelismo na caixa **de contagem de lote.** Recomendamos uma contagem máxima de 15 para a pontuação.

:::image type="content" source="./media/tutorial-bulk-processing/parallelism.png" alt-text="Screenshot que mostra a configuração do paralelismo para a atividade de pontuação na Azure Data Factory.":::

## <a name="how-to-use-the-pipeline"></a>Como usar o oleoduto

Tem agora um oleoduto automatizado para digitalizar o seu atraso de formulários e executar algumas análises em cima dele. Quando adicionar novas formas de tipo familiar a uma pasta de armazenamento existente, basta executar novamente os gasodutos de pontuação. Eles atualizarão todos os seus ficheiros de saída, incluindo ficheiros de saída para os novos formulários. 

Se adicionar novas formas de um novo tipo, também terá de enviar um conjunto de dados de treino para o recipiente apropriado. Em seguida, você adicionará uma nova linha na tabela de parametrização, inserindo as localizações dos novos documentos e seu conjunto de dados de formação. Introduza um valor de -1 para **model_ID** indicando que um novo modelo precisa de ser treinado para os formulários. Em seguida, executar o gasoduto de treino na Azure Data Factory. O oleoduto irá ler a partir da mesa, treinar um modelo e substituir o modelo ID na mesa. Em seguida, pode ligar para os gasodutos de pontuação para começar a escrever os ficheiros de saída.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você configura os oleodutos Azure Data Factory para desencadear o treino e execução de modelos de Reconhecimento de Formulários e digitalizar um grande atraso de ficheiros. Em seguida, explore a API do Reconhecimento de Formulários para ver o que mais pode fazer com ele.

* [API de Reconhecimento de Formulários](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeBusinessCardAsync)
