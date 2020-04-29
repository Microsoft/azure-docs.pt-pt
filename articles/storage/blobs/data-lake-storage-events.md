---
title: 'Tutorial: Implementar o padrão de captura de data lake para atualizar uma tabela Delta de Tijolos de Dados Azure [ Microsoft Docs'
description: Este tutorial mostra-lhe como usar uma subscrição da Rede de Eventos, uma Função Azure e um trabalho de Azure Databricks para inserir linhas de dados numa tabela que é armazenada no Azure DataLake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: normesta
ms.reviewer: sumameh
ms.openlocfilehash: 85fad873b6c176d2278ea48709d2892ab515a025
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78303312"
---
# <a name="tutorial-implement-the-data-lake-capture-pattern-to-update-a-databricks-delta-table"></a>Tutorial: Implementar o padrão de captura de dados do lago para atualizar uma tabela Delta databricks

Este tutorial mostra-lhe como lidar com eventos numa conta de armazenamento que tem um espaço de nome hierárquico.

Você vai construir uma pequena solução que permite a um utilizador povoar uma tabela Databricks Delta, carregando um ficheiro de valores separados de vírem (csv) que descreve uma ordem de venda. Você vai construir esta solução conectando uma subscrição de Event Grid, uma Função Azure e um [Trabalho](https://docs.azuredatabricks.net/user-guide/jobs.html) em Tijolos de Dados Azure.

Neste tutorial, irá:

> [!div class="checklist"]
> * Crie uma subscrição da Rede de Eventos que chame uma Função Azure.
> * Crie uma Função Azure que receba uma notificação de um evento, e depois execute o trabalho em Azure Databricks.
> * Crie um trabalho de Databricks que insere uma encomenda de cliente numa tabela Databricks Delta que está localizada na conta de armazenamento.

Construiremos esta solução em ordem inversa, começando pelo espaço de trabalho Azure Databricks.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Crie uma conta de armazenamento que tenha um espaço de nome hierárquico (Azure Data Lake Storage Gen2). Este tutorial usa uma `contosoorders`conta de armazenamento chamada . Certifique-se de que a sua conta de utilizador tem a função de Colaborador de [Dados blob](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) de armazenamento atribuída à sua.

  Consulte Criar uma conta De armazenamento de [lagos De dados Azure](data-lake-storage-quickstart-create-account.md).

* Crie um diretor de serviço. Ver Como: Utilize o portal para criar uma aplicação e um diretor de serviço da [Azure AD que possam aceder a recursos.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

  Há algumas coisas específicas que terás de fazer enquanto fazes os passos nesse artigo.

  :heavy_check_mark: Ao executar os passos na [Atribuição da aplicação a uma](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) secção de papel do artigo, certifique-se de atribuir a função de Contribuinte de Dados **blob** de armazenamento ao diretor de serviço.

  > [!IMPORTANT]
  > Certifique-se de atribuir o papel no âmbito da conta de armazenamento gen2 de armazenamento do Lago de Dados. Pode atribuir uma função ao grupo de recursos-mãe ou subscrição, mas receberá erros relacionados com permissões até que essas atribuições de funções se propaguem na conta de armazenamento.

  :heavy_check_mark: Ao executar os passos nos [valores Get para assinar na](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) secção do artigo, colar o ID do inquilino, id da aplicação e valores de palavra-passe em um ficheiro de texto. Vai precisar desses valores mais tarde.

## <a name="create-a-sales-order"></a>Criar uma ordem de venda

Primeiro, crie um ficheiro CSV que descreva uma ordem de venda e, em seguida, faça upload do ficheiro para a conta de armazenamento. Mais tarde, utilizará os dados deste ficheiro para preencher a primeira linha da nossa tabela Databricks Delta.

1. Open Azure Storage Explorer. Em seguida, navegue para a sua conta de armazenamento e, na secção **Blob Containers,** crie um novo recipiente chamado **dados**.

   ![pasta de dados](./media/data-lake-storage-events/data-container.png "pasta de dados")

   Para obter mais informações sobre como usar o Storage Explorer, consulte [o Use Azure Storage Explorer para gerir dados numa conta Azure Data Lake Storage Gen2](data-lake-storage-explorer.md).

2. No recipiente de **dados,** crie uma pasta chamada **entrada**.

3. Colar o seguinte texto num editor de texto.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536365,85123A,WHITE HANGING HEART T-LIGHT HOLDER,6,12/1/2010 8:26,2.55,17850,United Kingdom
   ```

4. Guarde este ficheiro para o seu computador local e dê-lhe o nome **data.csv**.

5. No Storage Explorer, faça o upload deste ficheiro para a pasta de **entrada.**  

## <a name="create-a-job-in-azure-databricks"></a>Criar um emprego em Azure Databricks

Nesta secção, executará estas tarefas:

* Criar um espaço de trabalho Azure Databricks.
* Crie um bloco de notas.
* Crie e povoeuma mesa Delta de Databricks.
* Adicione código que insere linhas na tabela Databricks Delta.
* Criar um Trabalho.

### <a name="create-an-azure-databricks-workspace"></a>Criar uma área de trabalho do Azure Databricks

Nesta secção, vai criar uma área de trabalho do Azure Databricks com o portal do Azure.

1. No portal Azure, selecione **Criar um recurso** > **Analytics** > **Azure Databricks**.

    ![Tijolos de dados no portal Azure](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Tijolos de dados no portal Azure")

2. Em **Serviço Azure Databricks**, forneça os valores para criar uma área de trabalho do Databricks.

    ![Criar uma área de trabalho do Azure Databricks](./media/data-lake-storage-events/new-databricks-service.png "Criar uma área de trabalho do Azure Databricks")

    A criação da área de trabalho demora alguns minutos. Para monitorizar o estado de funcionamento, veja a barra de progresso no topo.

### <a name="create-a-spark-cluster-in-databricks"></a>Criar um cluster do Spark no Databricks

1. No [portal Azure,](https://portal.azure.com)vá ao espaço de trabalho Azure Databricks que criou e, em seguida, selecione **Launch Workspace**.

2. Será redirecionado para o portal do Azure Databricks. A partir do portal, selecione **New** > **Cluster**.

    ![Tijolos de dados em Azure](./media/data-lake-storage-events/databricks-on-azure.png "Tijolos de dados em Azure")

3. Na página **Novo cluster**, indique os valores para criar um cluster.

    ![Criar cluster de faíscas databricks em Azure](./media/data-lake-storage-events/create-databricks-spark-cluster.png "Criar cluster de faíscas databricks em Azure")

    Aceite todos os outros valores predefinidos que não sejam os seguintes:

    * Introduza um nome para o cluster.
    * Certifique-se de que seleciona a caixa de verificação **Terminar após 120 minutos de inatividade**. Indique uma duração (em minutos) para terminar o cluster, caso não esteja a ser utilizado.

4. Selecione **Criar cluster**. Depois de o cluster estar em execução, pode anexar blocos de notas ao cluster e executar tarefas do Spark.

Para obter mais informações sobre a criação de clusters, veja [Criar um cluster do Spark no Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

### <a name="create-a-notebook"></a>Criar um bloco de notas

1. No painel esquerdo, selecione **Área de Trabalho**. No menu pendente **Área de Trabalho**, selecione **Criar** > **Bloco de Notas**.

    ![Criar caderno em Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Criar caderno em Databricks")

2. Na caixa de diálogo **Criar Bloco de Notas**, introduza um nome para o bloco de notas. Selecione **Python** como o idioma e, em seguida, selecione o cluster Spark que criou anteriormente.

    ![Criar caderno em Databricks](./media/data-lake-storage-events/new-databricks-notebook.png "Criar caderno em Databricks")

    Selecione **Criar**.

### <a name="create-and-populate-a-databricks-delta-table"></a>Criar e povoar uma tabela Delta databricks

1. No caderno que criou, copia e cola o seguinte bloco de código na primeira célula, mas ainda não executa este código.  

   Substitua `appId` `password`os `tenant` valores do espaço reservado neste bloco de código sintetizando os valores recolhidos ao completar os pré-requisitos deste tutorial.

    ```Python
    dbutils.widgets.text('source_file', "", "Source File")

    spark.conf.set("fs.azure.account.auth.type", "OAuth")
    spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
    spark.conf.set("fs.azure.account.oauth2.client.id", "<appId>")
    spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")
    spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant>/oauth2/token")

    adlsPath = 'abfss://data@contosoorders.dfs.core.windows.net/'
    inputPath = adlsPath + dbutils.widgets.get('source_file')
    customerTablePath = adlsPath + 'delta-tables/customers'
    ```

    Este código cria um widget chamado **source_file**. Mais tarde, criará uma Função Azure que chama este código e passa um caminho de ficheiro para esse widget.  Este código também autentica o seu principal de serviço com a conta de armazenamento, e cria algumas variáveis que você vai usar em outras células.

    > [!NOTE]
    > Num ambiente de produção, considere armazenar a sua chave de autenticação em Tijolos de Dados Azure. Em seguida, adicione uma chave de procura para o seu bloco de código em vez da chave de autenticação. <br><br>Por exemplo, em vez de `spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")`utilizar esta linha de código: `spark.conf.set("fs.azure.account.oauth2.client.secret", dbutils.secrets.get(scope = "<scope-name>", key = "<key-name-for-service-credential>"))`utilizaria a seguinte linha de código: . <br><br>Depois de ter concluído este tutorial, consulte o artigo [do Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) no Site azure Databricks para ver exemplos desta abordagem.

2. Prima as teclas **SHIFT + ENTER** para executar o código neste bloco.

3. Copie e cole o seguinte bloco de código numa célula diferente e, em seguida, prima as teclas **SHIFT + ENTER** para executar o código neste bloco.

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

   Este código cria a tabela Databricks Delta na sua conta de armazenamento e, em seguida, carrega alguns dados iniciais do ficheiro csv que você carregou anteriormente.

4. Depois de este bloco de código sacar com sucesso, retire este bloco de código do seu caderno.

### <a name="add-code-that-inserts-rows-into-the-databricks-delta-table"></a>Adicione código que insere linhas na tabela Databricks Delta

1. Copie e cole o seguinte bloco de código numa célula diferente, mas não dirija esta célula.

   ```Python
   upsertDataDF = (spark
     .read
     .option("header", "true")
     .csv(inputPath)
   )
   upsertDataDF.createOrReplaceTempView("customer_data_to_upsert")
   ```

   Este código insere os dados numa vista temporária da tabela utilizando dados de um ficheiro CSV. O caminho para esse ficheiro CSV vem do widget de entrada que criou num passo anterior.

2. Adicione o seguinte código para fundir o conteúdo da vista temporária da tabela com a tabela Databricks Delta.

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

### <a name="create-a-job"></a>Criar um Trabalho

Crie um Trabalho que gere o caderno que criou anteriormente. Mais tarde, criará uma Função Azure que gere este trabalho quando um evento é levantado.

1. Clique em **Jobs**.

2. Na página **Jobs,** clique em **Criar Emprego**.

3. Dê um nome ao trabalho `upsert-order-data` e escolha o livro.

   ![Criar um emprego](./media/data-lake-storage-events/create-spark-job.png "Criar uma tarefa")

## <a name="create-an-azure-function"></a>Criar uma Função do Azure

Crie uma Função Azure que gere o Trabalho.

1. No canto superior do espaço de trabalho databricks, escolha o ícone das pessoas e, em seguida, escolha **as definições do Utilizador**.

   ![Gerir conta](./media/data-lake-storage-events/generate-token.png "Definições do utilizador")

2. Clique no novo botão **de token Generate** e, em seguida, clique no botão **Generate.**

   Certifique-se de copiar o símbolo para um lugar seguro. A função Azure precisa deste símbolo para autenticar com databricks para que possa executar o Trabalho.
  
3. Selecione o botão **Criar um** botão de recurso encontrado no canto superior esquerdo do portal Azure e, em seguida, selecione **Compute > Function App**.

   ![Criar uma função Azure](./media/data-lake-storage-events/function-app-create-flow.png "Criar função Azure")

4. Na página **Criar** da App função, certifique-se de selecionar **.NET Core** para a pilha de tempo de execução e certifique-se de configurar uma instância de Insights de Aplicação.

   ![Configurar a aplicação de funções](./media/data-lake-storage-events/new-function-app.png "Configurar a aplicação de funções")

5. Na página **'Visão Geral** da App função', clique na **Configuração**.

   ![Configurar a aplicação de funções](./media/data-lake-storage-events/configure-function-app.png "Configurar a aplicação de funções")

6. Na página Definições de **Aplicação,** escolha o botão de **definição de nova aplicação** para adicionar cada definição.

   ![Adicionar definição de configuração](./media/data-lake-storage-events/add-application-setting.png "Adicionar definição de configuração")

   Adicione as seguintes definições:

   |Nome da definição | Valor |
   |----|----|
   |**DBX_INSTANCE**| A região do seu espaço de trabalho de databricks. Por exemplo: `westus2.azuredatabricks.net`|
   |**DBX_PAT**| O sinal de acesso pessoal que gerou anteriormente. |
   |**DBX_JOB_ID**|O identificador do trabalho de corrida. No nosso caso, `1`este valor é.|
7. Na página geral da aplicação de funções, clique no botão **de função New.**

   ![Nova função](./media/data-lake-storage-events/new-function.png "Nova função")

8. Escolha o **gatilho da grelha de eventos Azure**.

   Instale a extensão **Microsoft.Azure.WebJobs.Extensions.EventGrid** se for solicitado a fazê-lo. Se tiver de o instalar, terá de escolher novamente o Gatilho da Grelha de **Eventos Azure** para criar a função.

   Aparece o painel **new function.**

9. No painel **New Function,** nomeie a função **UpsertOrder**e, em seguida, clique no botão **Criar.**

10. Substitua o conteúdo do ficheiro de código por este código e, em seguida, clique no botão **Guardar:**

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
            var fileData = ((JObject)(eventGridEvent.Data)).ToObject<StorageBlobCreatedEventData>();
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

   Este código analisa informações sobre o evento de armazenamento que foi levantado, e depois cria uma mensagem de pedido com url do ficheiro que desencadeou o evento. Como parte da mensagem, a função passa um valor para o widget **source_file** que criou anteriormente. o código de função envia a mensagem para o Databricks Job e utiliza o símbolo que obteve anteriormente como autenticação.

## <a name="create-an-event-grid-subscription"></a>Criar uma subscrição do Event Grid

Nesta secção, irá criar uma subscrição da Rede de Eventos que liga para a Função Azure quando os ficheiros são enviados para a conta de armazenamento.

1. Na página do código de função, clique no botão de **subscrição add Event Grid.**

   ![Nova subscrição de eventos](./media/data-lake-storage-events/new-event-subscription.png "Nova subscrição de eventos")

2. Na página **de Subscrição de Eventos Criar,** nomeie a subscrição e, em seguida, use os campos na página para selecionar a sua conta de armazenamento.

   ![Nova subscrição de eventos](./media/data-lake-storage-events/new-event-subscription-2.png "Nova subscrição de eventos")

3. Na lista de **"Filter to Event Types** drop-down", selecione os eventos **Blob Created**e **Blob Deleted** e, em seguida, clique no botão **Criar.**

## <a name="test-the-event-grid-subscription"></a>Teste a subscrição da Grelha de Eventos

1. Crie um `customer-order.csv`ficheiro chamado, repasse as seguintes informações nesse ficheiro e guarde-a para o seu computador local.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,228,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

2. No Storage Explorer, faça o upload deste ficheiro para a pasta de **entrada** da sua conta de armazenamento.

   O upload de um ficheiro eleva o evento **Microsoft.Storage.BlobCreated.** A Event Grid identifica todos os subscritores desse evento. No nosso caso, a Função Azure é o único assinante. A Função Azure analisa os parâmetros do evento para determinar que evento ocorreu. Em seguida, passa o URL do ficheiro para o Databricks Job. O Databricks Job lê o ficheiro e adiciona uma linha à tabela Databricks Delta que está localizada na sua conta de armazenamento.

3. Para verificar se o trabalho foi bem sucedido, abra o espaço de trabalho dos seus databricks, clique no botão **Jobs** e, em seguida, abra o seu trabalho.

4. Selecione o trabalho para abrir a página de trabalho.

   ![Trabalho de faísca](./media/data-lake-storage-events/spark-job.png "Trabalho de faísca")

   Quando o trabalho terminar, verá um estado de conclusão.

   ![Trabalho concluído com sucesso](./media/data-lake-storage-events/spark-job-completed.png "Trabalho concluído com sucesso")

5. Numa nova célula de livro, execute esta consulta numa cela para ver a tabela delta atualizada.

   ```
   %sql select * from customer_data
   ```

   A mesa devolvida mostra o último disco.

   ![O último registo aparece na tabela](./media/data-lake-storage-events/final_query.png "O último registo aparece na tabela")

6. Para atualizar este registo, `customer-order-update.csv`crie um ficheiro chamado , repasse as seguintes informações nesse ficheiro e guarde-a para o seu computador local.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,22,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

   Este ficheiro csv é quase idêntico ao anterior, exceto que `228` `22`a quantidade da encomenda é alterada de .

7. No Storage Explorer, faça o upload deste ficheiro para a pasta de **entrada** da sua conta de armazenamento.

8. Execute `select` a consulta novamente para ver a tabela delta atualizada.

   ```
   %sql select * from customer_data
   ```

   A tabela devolvida mostra o registo atualizado.

   ![Registo atualizado aparece na tabela](./media/data-lake-storage-events/final_query-2.png "Registo atualizado aparece na tabela")

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, apague o grupo de recursos e todos os recursos relacionados. Para isso, selecione o grupo de recursos para a conta de armazenamento e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Reagir aos eventos de armazenamento de Blobs](storage-blob-event-overview.md)
