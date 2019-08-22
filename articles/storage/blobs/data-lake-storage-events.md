---
title: 'Tutorial: Usar eventos de Azure Data Lake Storage Gen2 para atualizar uma tabela Delta do databricks | Microsoft Docs'
description: Este tutorial mostra como usar uma assinatura de grade de eventos, uma função do Azure e um trabalho de Azure Databricks para inserir linhas de dados em uma tabela que é armazenada no Azure datalake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: normesta
ms.reviewer: sumameh
ms.openlocfilehash: ce132c6a6859156b209a26b5950eb6a509f446fc
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657598"
---
# <a name="tutorial-use-azure-data-lake-storage-gen2-events-to-update-a-databricks-delta-table"></a>Tutorial: Usar eventos de Azure Data Lake Storage Gen2 para atualizar uma tabela Delta do databricks

Este tutorial mostra como manipular eventos em uma conta de armazenamento que tem um namespace hierárquico.

Você criará uma pequena solução que permite que um usuário Preencha uma tabela Delta do databricks carregando um arquivo CSV (valores separados por vírgula) que descreve um pedido de vendas. Você criará essa solução conectando uma assinatura de grade de eventos, uma função do Azure e um [trabalho](https://docs.azuredatabricks.net/user-guide/jobs.html) no Azure Databricks.

Neste tutorial, irá:

> [!div class="checklist"]
> * Crie uma assinatura de grade de eventos que chama uma função do Azure.
> * Crie uma função do Azure que recebe uma notificação de um evento e, em seguida, executa o trabalho em Azure Databricks.
> * Crie um trabalho do databricks que insere uma ordem de cliente em uma tabela Delta do databricks que está localizada na conta de armazenamento.

Criaremos essa solução em ordem inversa, começando com o espaço de trabalho Azure Databricks.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Crie uma conta de armazenamento que tenha um namespace hierárquico (Azure Data Lake Storage Gen2). Este tutorial usa uma conta de armazenamento `contosoorders`denominada. Certifique-se de que sua conta de usuário tenha a [função de colaborador de dados de blob de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) atribuída a ela.

  Consulte [criar uma conta de Azure data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).

* Crie uma entidade de serviço. Consulte [como: Use o portal para criar um aplicativo do Azure AD e uma entidade de serviço que](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)possa acessar recursos.

  Há algumas coisas específicas que você precisará fazer ao executar as etapas nesse artigo.

  :heavy_check_mark: Ao executar as etapas na seção [atribuir o aplicativo a uma função](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) do artigo, certifique-se de atribuir a função de **colaborador de dados de blob de armazenamento** à entidade de serviço.

  > [!IMPORTANT]
  > Certifique-se de atribuir a função no escopo da conta de armazenamento Data Lake Storage Gen2. Você pode atribuir uma função ao grupo de recursos ou à assinatura pai, mas receberá erros relacionados a permissões até que essas atribuições de função se propaguem para a conta de armazenamento.

  :heavy_check_mark: Ao executar as etapas na seção [obter valores para entrar no](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) artigo, Cole a ID do locatário, a ID do aplicativo e os valores de senha em um arquivo de texto. Você precisará desses valores mais tarde.

## <a name="create-a-sales-order"></a>Criar uma ordem de venda

Primeiro, crie um arquivo CSV que descreva um pedido de vendas e, em seguida, carregue esse arquivo na conta de armazenamento. Posteriormente, você usará os dados desse arquivo para preencher a primeira linha em nossa tabela Delta do databricks.

1. Abra Gerenciador de Armazenamento do Azure. Em seguida, navegue até sua conta de armazenamento e, na seção Contêineres de **blob** , crie um novo contêiner denominado **dados**.

   ![pasta de dados](./media/data-lake-storage-events/data-container.png "pasta de dados")

   Para obter mais informações sobre como usar Gerenciador de Armazenamento, consulte [usar Gerenciador de armazenamento do Azure para gerenciar dados em uma conta de Azure data Lake Storage Gen2](data-lake-storage-explorer.md).

2. No contêiner de **dados** , crie uma pasta chamada **entrada**.

3. Cole o texto a seguir em um editor de texto.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536365,85123A,WHITE HANGING HEART T-LIGHT HOLDER,6,12/1/2010 8:26,2.55,17850,United Kingdom
   ```

4. Salve esse arquivo em seu computador local e dê a ele o nome **Data. csv**.

5. Em Gerenciador de Armazenamento, carregue esse arquivo na pasta de **entrada** .  

## <a name="create-a-job-in-azure-databricks"></a>Criar um trabalho no Azure Databricks

Nesta seção, você executará estas tarefas:

* Crie um espaço de trabalho Azure Databricks.
* Crie um bloco de notas.
* Crie e popule uma tabela Delta do databricks.
* Adicione o código que insere linhas na tabela Delta do databricks.
* Criar um trabalho.

### <a name="create-an-azure-databricks-workspace"></a>Criar uma área de trabalho do Azure Databricks

Nesta secção, vai criar uma área de trabalho do Azure Databricks com o portal do Azure.

1. No Portal do Azure, selecione **Criar um recurso** > **Análise** > **Azure Databricks**.

    ![Databricks no portal do Azure](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Databricks no portal do Azure")

2. Em **Serviço Azure Databricks**, forneça os valores para criar uma área de trabalho do Databricks.

    ![Criar uma área de trabalho do Azure Databricks](./media/data-lake-storage-events/new-databricks-service.png "Criar uma área de trabalho do Azure Databricks")

    A criação da área de trabalho demora alguns minutos. Para monitorar o status da operação, exiba a barra de progresso na parte superior.

### <a name="create-a-spark-cluster-in-databricks"></a>Criar um cluster do Spark no Databricks

1. Na [portal do Azure](https://portal.azure.com), vá para o espaço de trabalho Azure Databricks que você criou e selecione **Iniciar espaço de trabalho**.

2. Será redirecionado para o portal do Azure Databricks. No portal, selecione **Novo** > **Cluster**.

    ![Databricks no Azure](./media/data-lake-storage-events/databricks-on-azure.png "Databricks no Azure")

3. Na página **Novo cluster**, indique os valores para criar um cluster.

    ![Criar um cluster Databricks Spark no Azure](./media/data-lake-storage-events/create-databricks-spark-cluster.png "Criar um cluster Databricks Spark no Azure")

    Aceite todos os outros valores predefinidos que não sejam os seguintes:

    * Introduza um nome para o cluster.
    * Certifique-se de que seleciona a caixa de verificação **Terminar após 120 minutos de inatividade**. Indique uma duração (em minutos) para terminar o cluster, caso não esteja a ser utilizado.

4. Selecione **Criar cluster**. Depois de o cluster estar em execução, pode anexar blocos de notas ao cluster e executar tarefas do Spark.

Para obter mais informações sobre a criação de clusters, veja [Criar um cluster do Spark no Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

### <a name="create-a-notebook"></a>Criar um bloco de anotações

1. No painel esquerdo, selecione **Área de Trabalho**. No menu pendente **Área de Trabalho**, selecione **Criar** > **Bloco de Notas**.

    ![Criar um bloco de notas no Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Criar um bloco de notas no Databricks")

2. Na caixa de diálogo **Criar Bloco de Notas**, introduza um nome para o bloco de notas. Selecione **Python** como o idioma e, em seguida, selecione o cluster Spark que você criou anteriormente.

    ![Criar um bloco de notas no Databricks](./media/data-lake-storage-events/new-databricks-notebook.png "Criar um bloco de notas no Databricks")

    Selecione **Criar**.

### <a name="create-and-populate-a-databricks-delta-table"></a>Criar e popular uma tabela Delta do databricks

1. No bloco de anotações que você criou, copie e cole o bloco de código a seguir na primeira célula, mas não execute esse código ainda.  

   Substitua os `appId` `password` valoresdeespaçoreservadonesteblocodecódigopelosvaloresquevocêcoletouaoconcluiros`tenant` pré-requisitos deste tutorial.

    ```Python
    dbutils.widgets.text('source_file', "", "Source File")

    spark.conf.set("fs.azure.account.auth.type", "OAuth")
    spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
    spark.conf.set("fs.azure.account.oauth2.client.id", "<appId")
    spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")
    spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant>/oauth2/token")
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")

    adlsPath = 'abfss://data@contosoorders.dfs.core.windows.net/'
    inputPath = adlsPath + dbutils.widgets.get('source_file')
    customerTablePath = adlsPath + 'delta-tables/customers'
    ```

    Esse código cria um widget chamado **source_file**. Posteriormente, você criará uma função do Azure que chama esse código e passa um caminho de arquivo para esse widget.  Esse código também autentica sua entidade de serviço com a conta de armazenamento e cria algumas variáveis que você usará em outras células.

2. Pressione as teclas **Shift + Enter** para executar o código neste bloco.

3. Copie e cole o bloco de código a seguir em uma célula diferente e pressione as teclas **Shift + Enter** para executar o código nesse bloco.

   ```Python
   from pyspark.sql.types import StructType, StructField, DoubleType, IntegerType, StringType


   inputSchema = StructType([
   StructField("InvoiceNo", IntegerType(), True),
   StructField("StockCode", StringType(), True),
   StructField("Description", StringType(), True),
   StructField("Quantity", IntegerType(), True),
   StructField("InvoiceDate", StringType(), True),
   StructField("UnitPrice", DoubleType(), True),
   StructField("CustomerID", IntegerType(), True),
   StructField("Country", StringType(), True)
   ])

   rawDataDF = (spark.read
    .option("header", "true")
    .schema(inputSchema)
    .csv(adlsPath + 'input')
   )

   (rawDataDF.write
     .mode("overwrite")
     .format("delta")
     .saveAsTable("customer_data", path=customerTablePath))
   ```

   Esse código cria a tabela Delta do databricks em sua conta de armazenamento e, em seguida, carrega alguns dados iniciais do arquivo CSV que você carregou anteriormente.

4. Depois que esse bloco de código for executado com êxito, remova esse bloco de código do bloco de anotações.

### <a name="add-code-that-inserts-rows-into-the-databricks-delta-table"></a>Adicionar código que insere linhas na tabela Delta do databricks

1. Copie e cole o bloco de código a seguir em uma célula diferente, mas não execute esta célula.

   ```Python
   upsertDataDF = (spark
     .read
     .option("header", "true")
     .csv(inputPath)
   )
   upsertDataDF.createOrReplaceTempView("customer_data_to_upsert")
   ```

   Esse código insere dados em uma exibição de tabela temporária usando dados de um arquivo CSV. O caminho para esse arquivo CSV é proveniente do widget de entrada que você criou em uma etapa anterior.

2. Adicione o código a seguir para mesclar o conteúdo da exibição de tabela temporária com a tabela Delta do databricks.

   ```
   %sql
   MERGE INTO customer_data cd
   USING customer_data_to_upsert cu
   ON cd.CustomerID = cu.CustomerID
   WHEN MATCHED THEN
     UPDATE SET
       cd.StockCode = cu.StockCode,
       cd.Description = cu.Description,
       cd.InvoiceNo = cu.InvoiceNo,
       cd.Quantity = cu.Quantity,
       cd.InvoiceDate = cu.InvoiceDate,
       cd.UnitPrice = cu.UnitPrice,
       cd.Country = cu.Country
   WHEN NOT MATCHED
     THEN INSERT (InvoiceNo, StockCode, Description, Quantity, InvoiceDate, UnitPrice, CustomerID, Country)
     VALUES (
       cu.InvoiceNo,
       cu.StockCode,
       cu.Description,
       cu.Quantity,
       cu.InvoiceDate,
       cu.UnitPrice,
       cu.CustomerID,
       cu.Country)
   ```

### <a name="create-a-job"></a>Criar um trabalho

Crie um trabalho que execute o bloco de anotações que você criou anteriormente. Posteriormente, você criará uma função do Azure que executa esse trabalho quando um evento é gerado.

1. Clique em **trabalhos**.

2. Na página **trabalhos** , clique em **criar trabalho**.

3. Dê um nome ao trabalho e, em seguida, `upsert-order-data` escolha a pasta de trabalho.

   ![Criar um trabalho](./media/data-lake-storage-events/create-spark-job.png "Criar um trabalho")

## <a name="create-an-azure-function"></a>Criar uma Função do Azure

Crie uma função do Azure que executa o trabalho.

1. No canto superior do espaço de trabalho do databricks, escolha o ícone pessoas e, em seguida, escolha **configurações do usuário**.

   ![Gerenciar conta](./media/data-lake-storage-events/generate-token.png "Configurações do usuário")

2. Clique no botão **gerar novo token** e, em seguida, clique no botão **gerar** .

   Certifique-se de copiar o token para um local seguro. Sua função do Azure precisa desse token para autenticar com o databricks para que ele possa executar o trabalho.
  
3. Selecione o botão **criar um recurso** encontrado no canto superior esquerdo da portal do Azure e, em seguida, selecione **> de computação aplicativo de funções**.

   ![Criar uma função do Azure](./media/data-lake-storage-events/function-app-create-flow.png "Criar Azure function")

4. Na página **criar** do aplicativo de funções, certifique-se de selecionar **.NET Core** para a pilha de tempo de execução e certifique-se de configurar uma instância de Application insights.

   ![Configurar o aplicativo de funções](./media/data-lake-storage-events/new-function-app.png "Configurar o aplicativo de funções")

5. Na página **visão geral** do aplicativo de funções, clique em **configuração**.

   ![Configurar o aplicativo de funções](./media/data-lake-storage-events/configure-function-app.png "Configurar o aplicativo de funções")

6. Na página **configurações do aplicativo** , escolha o botão **nova configuração de aplicativo** para adicionar cada configuração.

   ![Adicionar definição de configuração](./media/data-lake-storage-events/add-application-setting.png "Adicionar definição de configuração")

   Adicione as seguintes configurações:

   |Nome da definição | Value |
   |----|----|
   |**DBX_INSTANCE**| A região do seu espaço de trabalho do databricks. Por exemplo: `westus2.azuredatabricks.net`|
   |**DBX_PAT**| O token de acesso pessoal que você gerou anteriormente. |
   |**DBX_JOB_ID**|O identificador do trabalho em execução. Em nosso caso, esse valor é `1`.|
7. Na página Visão geral do aplicativo de funções, clique no botão **nova função** .

   ![Nova função](./media/data-lake-storage-events/new-function.png "Nova função")

8. Escolha o **gatilho de grade de eventos do Azure**.

   Instale a extensão **Microsoft. Azure. webjobs. Extensions. EventGrid** se você for solicitado a fazer isso. Se precisar instalá-lo, você precisará escolher o gatilho de **grade de eventos do Azure** novamente para criar a função.

   O painel **nova função** é exibido.

9. No painel **nova função** , nomeie a função **UpsertOrder**e, em seguida, clique no botão **criar** .

10. Substitua o conteúdo do arquivo de código por esse código e, em seguida, clique no botão **salvar** :

    ```cs
    using "Microsoft.Azure.EventGrid"
    using "Newtonsoft.Json"
    using Microsoft.Azure.EventGrid.Models;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;

    private static HttpClient httpClient = new HttpClient();

    public static async Task Run(EventGridEvent eventGridEvent, ILogger log)
    {
        log.LogInformation("Event Subject: " + eventGridEvent.Subject);
        log.LogInformation("Event Topic: " + eventGridEvent.Topic);
        log.LogInformation("Event Type: " + eventGridEvent.EventType);
        log.LogInformation(eventGridEvent.Data.ToString());

        if (eventGridEvent.EventType == "Microsoft.Storage.BlobCreated" | | eventGridEvent.EventType == "Microsoft.Storage.FileRenamed") {
            var fileData = ((JObject)(eventGridEvent.Data)) .ToObject<StorageBlobCreatedEventData>();
            if (fileData.Api == "FlushWithClose") {
                log.LogInformation("Triggering Databricks Job for file: " + fileData.Url);
                var fileUrl = new Uri(fileData.Url);
                var httpRequestMessage = new HttpRequestMessage {
                    Method = HttpMethod.Post,
                    RequestUri = new Uri(String.Format("https://{0}/api/2.0/jobs/run-now", System.Environment.GetEnvironmentVariable("DBX_INSTANCE", EnvironmentVariableTarget.Process))),
                    Headers = {
                        { System.Net.HttpRequestHeader.Authorization.ToString(), "Bearer " +  System.Environment.GetEnvironmentVariable ("DBX_PAT", EnvironmentVariableTarget.Process)},
                        { System.Net.HttpRequestHeader.ContentType.ToString (), "application/json" }
                    },
                    Content = new StringContent(JsonConvert.SerializeObject(new {
                        job_id = System.Environment.GetEnvironmentVariable ("DBX_JOB_ID", EnvironmentVariableTarget.Process) ,
                        notebook_params = new {
                            source_file = String.Join("", fileUrl.Segments.Skip(2))
                        }
                    }))
                 };
                var response = await httpClient.SendAsync(httpRequestMessage);
                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

   Esse código analisa informações sobre o evento de armazenamento que foi gerado e, em seguida, cria uma mensagem de solicitação com a URL do arquivo que disparou o evento. Como parte da mensagem, a função passa um valor para o widget **source_file** que você criou anteriormente. o código de função envia a mensagem para o trabalho do databricks e usa o token que você obteve anteriormente como autenticação.

## <a name="create-an-event-grid-subscription"></a>Criar uma subscrição do Event Grid

Nesta seção, você criará uma assinatura de grade de eventos que chama a função do Azure quando os arquivos são carregados para a conta de armazenamento.

1. Na página de código da função, clique no botão **Adicionar assinatura da grade de eventos** .

   ![Nova assinatura de evento](./media/data-lake-storage-events/new-event-subscription.png "Nova assinatura de evento")

2. Na página **criar assinatura de evento** , nomeie a assinatura e, em seguida, use os campos na página para selecionar sua conta de armazenamento.

   ![Nova assinatura de evento](./media/data-lake-storage-events/new-event-subscription-2.png "Nova assinatura de evento")

3. Na lista suspensa **filtrar tipos de eventos** , selecione o **blob criado**e os eventos excluídos do **blob** e clique no botão **criar** .

## <a name="test-the-event-grid-subscription"></a>Testar a assinatura da grade de eventos

1. Crie um arquivo chamado `customer-order.csv`, Cole as informações a seguir nesse arquivo e salve-o em seu computador local.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,228,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

2. Em Gerenciador de Armazenamento, carregue esse arquivo na pasta de **entrada** da sua conta de armazenamento.

   O carregamento de um arquivo gera o evento **Microsoft. Storage. BlobCreated** . A grade de eventos notifica todos os assinantes desse evento. Em nosso caso, o Azure function é o único assinante. A função do Azure analisa os parâmetros de evento para determinar qual evento ocorreu. Em seguida, ele passa a URL do arquivo para o trabalho do databricks. O trabalho do databricks lê o arquivo e adiciona uma linha à tabela Delta do databricks que está localizada na sua conta de armazenamento.

3. Para verificar se o trabalho foi bem-sucedido, abra o espaço de trabalho do databricks, clique no botão **trabalhos** e abra seu trabalho.

4. Selecione o trabalho para abrir a página do trabalho.

   ![Trabalho do Spark](./media/data-lake-storage-events/spark-job.png "Trabalho do Spark")

   Quando o trabalho for concluído, você verá um status de conclusão.

   ![Trabalho concluído com êxito](./media/data-lake-storage-events/spark-job-completed.png "Trabalho concluído com êxito")

5. Em uma nova célula da pasta de trabalho, execute esta consulta em uma célula para ver a tabela Delta atualizada.

   ```
   %sql select * from customer_data
   ```

   A tabela retornada mostra o registro mais recente.

   O ![registro mais recente aparece na tabela] O (./media/data-lake-storage-events/final_query.png "registro mais recente aparece na tabela")

## <a name="clean-up-resources"></a>Limpar recursos

Quando eles não forem mais necessários, exclua o grupo de recursos e todos os recursos relacionados. Para fazer isso, selecione o grupo de recursos para a conta de armazenamento e selecione **excluir**.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Reagir aos eventos de armazenamento de Blobs](storage-blob-event-overview.md)
