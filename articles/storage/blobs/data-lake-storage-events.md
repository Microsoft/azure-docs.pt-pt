---
title: 'Tutorial: Implementar o padrão de captura do lago de dados para atualizar uma tabela Delta do Azure Databricks Microsoft Docs'
description: Este tutorial mostra-lhe como usar uma subscrição de Grade de Eventos, uma Função Azure e um trabalho de Azure Databricks para inserir linhas de dados numa tabela que é armazenada no Azure DataLake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: normesta
ms.reviewer: sumameh
ms.custom: devx-track-csharp
ms.openlocfilehash: 8df4de01750de92222bfa9021b66828927804e85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89005484"
---
# <a name="tutorial-implement-the-data-lake-capture-pattern-to-update-a-databricks-delta-table"></a>Tutorial: Implementar o padrão de captura do lago de dados para atualizar uma tabela Delta databricks

Este tutorial mostra-lhe como lidar com eventos numa conta de armazenamento que tem um espaço hierárquico de nomes.

Você vai construir uma pequena solução que permite a um utilizador povoar uma tabela Databricks Delta carregando um ficheiro de valores separados por vírgula (csv) que descreve uma ordem de venda. Você vai construir esta solução conectando uma subscrição de Grade de Eventos, uma Função Azure e um [Job](https://docs.azuredatabricks.net/user-guide/jobs.html) in Azure Databricks.

Neste tutorial, irá:

> [!div class="checklist"]
> * Crie uma subscrição de Grade de Eventos que chame uma Função Azure.
> * Crie uma Função Azure que receba uma notificação de um evento e, em seguida, executa o trabalho em Azure Databricks.
> * Crie um trabalho databricks que insere uma encomenda de clientes numa tabela Databricks Delta que está localizada na conta de armazenamento.

Vamos construir esta solução em ordem inversa, a começar pelo espaço de trabalho da Azure Databricks.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Crie uma conta de armazenamento que tenha um espaço hierárquico de nomes (Azure Data Lake Storage Gen2). Este tutorial usa uma conta de armazenamento chamada `contosoorders` . Certifique-se de que a sua conta de utilizador tem a [função de Contribuinte de Dados de Armazenamento Blob](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) que lhe é atribuída.

  Consulte [criar uma conta Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).

* Criar um diretor de serviço. Ver [Como: Utilizar o portal para criar uma aplicação AD Azure e um responsável de serviço que possa aceder aos recursos.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

  Há algumas coisas específicas que terás de fazer enquanto executas os passos nesse artigo.

  :heavy_check_mark: Ao executar os passos na [atribuição da aplicação a uma](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) secção de função do artigo, certifique-se de atribuir a função de Contribuinte de **Dados de Armazenamento** ao titular do serviço.

  > [!IMPORTANT]
  > Certifique-se de atribuir a função no âmbito da conta de armazenamento do Data Lake Gen2. Pode atribuir uma função ao grupo de recursos-mãe ou subscrição, mas receberá erros relacionados com permissões até que essas atribuições de funções se propaguem na conta de armazenamento.

  :heavy_check_mark: Ao executar os passos nos [valores get para a assinatura na](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) secção do artigo, cole o ID do inquilino, iD da aplicação e valores de senha num ficheiro de texto. Vai precisar desses valores mais tarde.

## <a name="create-a-sales-order"></a>Criar uma ordem de venda

Primeiro, crie um ficheiro CSV que descreva uma ordem de venda e, em seguida, carrequiva esse ficheiro para a conta de armazenamento. Mais tarde, usará os dados deste ficheiro para preencher a primeira linha da nossa tabela Databricks Delta.

1. Abra o Explorador de Armazenamento Azure. Em seguida, navegue para a sua conta de armazenamento, e na secção **Blob Containers,** crie um novo recipiente chamado **dados**.

   ![pasta de dados](./media/data-lake-storage-events/data-container.png "pasta de dados")

   Para obter mais informações sobre como utilizar o Storage Explorer, consulte [o Use Azure Storage Explorer para gerir dados numa conta Azure Data Lake Storage Gen2](data-lake-storage-explorer.md).

2. No recipiente de **dados,** crie uma pasta chamada **entrada**.

3. Cole o texto seguinte num editor de texto.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536365,85123A,WHITE HANGING HEART T-LIGHT HOLDER,6,12/1/2010 8:26,2.55,17850,United Kingdom
   ```

4. Guarde este ficheiro para o seu computador local e dê-lhe o nome **data.csv**.

5. No Storage Explorer, faça o upload deste ficheiro para a pasta **de entrada.**  

## <a name="create-a-job-in-azure-databricks"></a>Criar um emprego na Azure Databricks

Nesta secção, executará estas tarefas:

* Crie um espaço de trabalho Azure Databricks.
* Crie um bloco de notas.
* Crie e povoe uma tabela Delta Databricks.
* Adicione código que insere linhas na tabela Databricks Delta.
* Criar um trabalho.

### <a name="create-an-azure-databricks-workspace"></a>Criar uma área de trabalho do Azure Databricks

Nesta secção, vai criar uma área de trabalho do Azure Databricks com o portal do Azure.

1. No portal Azure, **selecione Criar um recurso**  >  **Analytics**  >  **Azure Databricks**.

    ![Databricks no portal Azure](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Databricks no portal Azure")

2. Em **Serviço Azure Databricks**, forneça os valores para criar uma área de trabalho do Databricks.

    ![Criar uma área de trabalho do Azure Databricks](./media/data-lake-storage-events/new-databricks-service.png "Criar uma área de trabalho do Azure Databricks")

    A criação da área de trabalho demora alguns minutos. Para monitorizar o estado de funcionamento, veja a barra de progresso no topo.

### <a name="create-a-spark-cluster-in-databricks"></a>Criar um cluster do Spark no Databricks

1. No [portal Azure,](https://portal.azure.com)vá ao espaço de trabalho Azure Databricks que criou e, em seguida, selecione **Launch Workspace**.

2. Será redirecionado para o portal do Azure Databricks. A partir do portal, selecione **Novo**  >  **Cluster**.

    ![Databricks em Azure](./media/data-lake-storage-events/databricks-on-azure.png "Databricks em Azure")

3. Na página **Novo cluster**, indique os valores para criar um cluster.

    ![Criar conjunto de faíscas de dados no Azure](./media/data-lake-storage-events/create-databricks-spark-cluster.png "Criar conjunto de faíscas de dados no Azure")

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

### <a name="create-and-populate-a-databricks-delta-table"></a>Criar e povoar uma tabela Delta Databricks

1. No caderno que criou, copie e cole o seguinte bloco de código na primeira célula, mas ainda não execute este código.  

   Substitua os valores , `appId` , espaço reservado neste bloco de `password` `tenant` códigos pelos valores que recolheu ao completar os pré-requisitos deste tutorial.

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

    Este código cria um widget chamado **source_file**. Mais tarde, criará uma Função Azure que chama este código e passa um caminho de ficheiro para esse widget.  Este código também autentica o seu principal de serviço com a conta de armazenamento, e cria algumas variáveis que irá utilizar noutras células.

    > [!NOTE]
    > Numa definição de produção, considere guardar a sua chave de autenticação em Azure Databricks. Em seguida, adicione uma chave de procura ao seu bloco de código em vez da chave de autenticação. <br><br>Por exemplo, em vez de utilizar esta linha de código: `spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")` , utilizaria a seguinte linha de código: `spark.conf.set("fs.azure.account.oauth2.client.secret", dbutils.secrets.get(scope = "<scope-name>", key = "<key-name-for-service-credential>"))` . <br><br>Depois de ter concluído este tutorial, consulte o artigo da [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) no Site Azure Databricks para ver exemplos desta abordagem.

2. Prima as teclas **SHIFT + ENTER** para executar o código neste bloco.

3. Copie e cole o bloco de código que se segue para uma célula diferente e, em seguida, prima as teclas **SHIFT + ENTER** para executar o código neste bloco.

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

   Este código cria a tabela Databricks Delta na sua conta de armazenamento e, em seguida, carrega alguns dados iniciais a partir do ficheiro csv que fez o upload anterior.

4. Depois de este bloco de códigos ser executado com sucesso, retire este bloco de código do seu caderno.

### <a name="add-code-that-inserts-rows-into-the-databricks-delta-table"></a>Adicione código que insere linhas na tabela Databricks Delta

1. Copie e cole o seguinte bloco de código numa célula diferente, mas não passe esta célula.

   ```Python
   upsertDataDF = (spark
     .read
     .option("header", "true")
     .csv(inputPath)
   )
   upsertDataDF.createOrReplaceTempView("customer_data_to_upsert")
   ```

   Este código insere dados numa visão temporária da tabela utilizando dados de um ficheiro CSV. O caminho para esse ficheiro csv provém do widget de entrada que criou num passo anterior.

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

### <a name="create-a-job"></a>Criar um trabalho

Crie um Jó que executa o caderno que criou anteriormente. Mais tarde, criará uma Função Azure que gere este trabalho quando um evento é levantado.

1. Clique **em Jobs**.

2. Na página **Jobs,** clique em **Criar Trabalho.**

3. Dê um nome ao trabalho e depois escolha o `upsert-order-data` livro.

   ![Criar uma tarefa](./media/data-lake-storage-events/create-spark-job.png "Criar uma tarefa")

## <a name="create-an-azure-function"></a>Criar uma Função do Azure

Criar uma Função Azure que gere o Trabalho.

1. No canto superior do espaço de trabalho Databricks, escolha o ícone das pessoas e, em seguida, escolha **as definições do Utilizador**.

   ![Gerir conta](./media/data-lake-storage-events/generate-token.png "Definições do utilizador")

2. Clique no botão **'Gerar novo token'** e, em seguida, clique no botão **Gerar.**

   Certifique-se de copiar o símbolo para um lugar seguro. A sua Função Azure necessita deste token para autenticar com databricks para que possa executar o Trabalho.
  
3. Selecione o botão **de recurso** encontrado no canto superior esquerdo do portal Azure e, em seguida, selecione **Computação > App de função**.

   ![Criar uma função Azure](./media/data-lake-storage-events/function-app-create-flow.png "Criar função Azure")

4. Na página **Criar** a App 'Função', certifique-se de selecionar **.NET Core** para a pilha de tempo de execução e certifique-se de configurar uma instância de Insights de Aplicação.

   ![Configurar a aplicação de funções](./media/data-lake-storage-events/new-function-app.png "Configurar a aplicação de funções")

5. Na página **geral** da App de Função, clique em **Configuração**.

   ![Configurar a aplicação de funções](./media/data-lake-storage-events/configure-function-app.png "Configurar a aplicação de funções")

6. Na página Definições de **Aplicação,** escolha o novo botão **de definição de aplicação** para adicionar cada definição.

   ![Adicionar configuração](./media/data-lake-storage-events/add-application-setting.png "Adicionar configuração")

   Adicione as seguintes definições:

   |Nome da definição | Valor |
   |----|----|
   |**DBX_INSTANCE**| A região do seu espaço de trabalho databricks. Por exemplo: `westus2.azuredatabricks.net`|
   |**DBX_PAT**| O sinal de acesso pessoal que gerou anteriormente. |
   |**DBX_JOB_ID**|O identificador do trabalho de corrida. No nosso caso, este valor `1` é.|
7. Na página geral da aplicação de função, clique no botão **Nova função.**

   ![Nova função](./media/data-lake-storage-events/new-function.png "Nova função")

8. Escolha **o gatilho da grelha de eventos Azure**.

   Instale a extensão **Microsoft.Azure.WebJobs.Extensions.EventGrid** se for solicitado para o fazer. Se tiver de o instalar, terá de escolher **novamente o Azure Event Grid Trigger** para criar a função.

   Aparece o painel new **function.**

9. No painel **'Nova Função',** nomeie a função **UpsertOrder**e, em seguida, clique no botão **Criar.**

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

   Este código analisa informações sobre o evento de armazenamento que foi levantado e, em seguida, cria uma mensagem de pedido com url do ficheiro que desencadeou o evento. Como parte da mensagem, a função transmite um valor ao **widget source_file** que criou anteriormente. o código de função envia a mensagem para o Databricks Job e utiliza o token que obteve anteriormente como autenticação.

## <a name="create-an-event-grid-subscription"></a>Criar uma subscrição do Event Grid

Nesta secção, irá criar uma subscrição de Grade de Eventos que ligue para a Função Azure quando os ficheiros são enviados para a conta de armazenamento.

1. Na página de código de função, clique no botão **de subscrição 'Adicionar Grelha de** Eventos'.

   ![Nova subscrição de eventos](./media/data-lake-storage-events/new-event-subscription.png "Nova subscrição de eventos")

2. Na página **'Criar Subscrição de** Eventos', nomeie a subscrição e, em seguida, utilize os campos na página para selecionar a sua conta de armazenamento.

   ![Nova subscrição de eventos](./media/data-lake-storage-events/new-event-subscription-2.png "Nova subscrição de eventos")

3. Na lista de casos de "Filtro para Tipos de **Eventos",** selecione os eventos **Blob Created**e **Blob Deleted** e, em seguida, clique no botão **Criar.**

## <a name="test-the-event-grid-subscription"></a>Teste a subscrição da Grade de Eventos

1. Crie um ficheiro chamado `customer-order.csv` , cole as seguintes informações nesse ficheiro e guarde-as para o computador local.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,228,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

2. No Storage Explorer, faça o upload deste ficheiro para a pasta de **entrada** da sua conta de armazenamento.

   O upload de um ficheiro eleva o evento **Microsoft.Storage.BlobCreated.** A Grade de Eventos notifica todos os subscritores desse evento. No nosso caso, a Função Azure é o único subscritor. A Função Azure analisa os parâmetros do evento para determinar que evento ocorreu. Em seguida, passa o URL do ficheiro para o Databricks Job. O Databricks Job lê o ficheiro e adiciona uma linha à tabela Databricks Delta que está localizada na sua conta de armazenamento.

3. Para verificar se o trabalho foi bem sucedido, abra o espaço de trabalho dos databricks, clique no botão **Jobs** e, em seguida, abra o seu trabalho.

4. Selecione o trabalho para abrir a página de trabalho.

   ![Trabalho de faísca](./media/data-lake-storage-events/spark-job.png "Trabalho de faísca")

   Quando o trabalho terminar, verá um estado de conclusão.

   ![Trabalho concluído com sucesso](./media/data-lake-storage-events/spark-job-completed.png "Trabalho concluído com sucesso")

5. Numa nova célula de livro, faça esta consulta numa célula para ver a tabela delta atualizada.

   ```
   %sql select * from customer_data
   ```

   A mesa de regresso mostra o último registo.

   ![O último registo aparece na tabela](./media/data-lake-storage-events/final_query.png "O último registo aparece na tabela")

6. Para atualizar este registo, crie um ficheiro chamado `customer-order-update.csv` , cole as seguintes informações nesse ficheiro e guarde-as para o computador local.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,22,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

   Este ficheiro csv é quase idêntico ao anterior, exceto que a quantidade da encomenda é alterada de `228` `22` .

7. No Storage Explorer, faça o upload deste ficheiro para a pasta de **entrada** da sua conta de armazenamento.

8. Volte a fazer `select` a consulta para ver a tabela delta atualizada.

   ```
   %sql select * from customer_data
   ```

   A tabela devolvida mostra o registo atualizado.

   ![O registo atualizado aparece na tabela](./media/data-lake-storage-events/final_query-2.png "O registo atualizado aparece na tabela")

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não forem necessários, apague o grupo de recursos e todos os recursos relacionados. Para tal, selecione o grupo de recursos para a conta de armazenamento e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Reagir aos eventos de armazenamento de Blobs](storage-blob-event-overview.md)
