---
title: 'Quickstart: Analise dados no Azure Data Lake Storage Gen2 utilizando tijolos de dados azure  Microsoft Docs'
description: Aprenda a executar um trabalho de Spark em Azure Databricks utilizando o portal Azure e uma conta de armazenamento de Armazenamento de Lago De dados Azure Gen2.
author: normesta
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 02/17/2020
ms.reviewer: jeking
ms.openlocfilehash: 346795b79a78589d949b035a803a67a9e5a2e8e5
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470739"
---
# <a name="quickstart-analyze-data-with-databricks"></a>Quickstart: Analisar dados com Databricks

Neste arranque rápido, executa um trabalho da Apache Spark utilizando os Azure Databricks para realizar análises em dados armazenados numa conta de armazenamento. Como parte do trabalho da Spark, irá analisar os dados de subscrição de um canal de rádio para obter informações sobre o uso gratuito/pago com base na demografia.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* O nome da sua conta de armazenamento do Lago De dados Azure Gen2. Crie uma conta de armazenamento de Gen2 de armazenamento de lago de [dados Azure.](data-lake-storage-quickstart-create-account.md)

* O ID do inquilino, o ID da aplicação e a palavra-passe de um diretor de serviço Azure com um papel atribuído de **Storage Blob Data Contributor**. [Criar um diretor de serviço.](../../active-directory/develop/howto-create-service-principal-portal.md)

  > [!IMPORTANT]
  > Atribuir o papel no âmbito da conta de armazenamento gen2 de armazenamento do Lago de Dados. Pode atribuir uma função ao grupo de recursos-mãe ou subscrição, mas receberá erros relacionados com permissões até que essas atribuições de funções se propaguem na conta de armazenamento.

## <a name="create-an-azure-databricks-workspace"></a>Criar uma área de trabalho do Azure Databricks

Nesta secção, vai criar uma área de trabalho do Azure Databricks com o portal do Azure.

1. No Portal do Azure, selecione **Criar um recurso** > **Análise** > **Azure Databricks**.

    ![Tijolos de dados no portal Azure](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Tijolos de dados no portal Azure")

2. Em **Serviço Azure Databricks**, forneça os valores para criar uma área de trabalho do Databricks.

    ![Criar um espaço de trabalho Azure Databricks](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-workspace.png "Criar uma área de trabalho do Azure Databricks")

    Forneça os seguintes valores:

    |Propriedade  |Descrição  |
    |---------|---------|
    |**Nome da área de trabalho**     | Indique um nome para a sua área de trabalho do Databricks        |
    |**Subscrição**     | Na lista pendente, selecione a sua subscrição do Azure.        |
    |**Grupo de recursos**     | Especifique se quer criar um novo grupo de recursos ou utilizar um existente. Um grupo de recursos é um contentor que mantém recursos relacionados para uma solução do Azure. Para obter mais informações, veja [Descrição geral do Grupo de Recursos do Azure](../../azure-resource-manager/management/overview.md). |
    |**Localização**     | Selecione **E.U.A. Oeste 2**. Pode selecionar outra região pública, se preferir.        |
    |**Escalão de Preço**     |  Escolha entre **Standard** ou **Premium**. Para obter mais informações sobre estes escalões, veja [Página de preços do Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

3. A criação da conta demora alguns minutos. Para monitorizar o estado de funcionamento, veja a barra de progresso no topo.

4. Selecione **Afixar ao dashboard** e, em seguida, selecione **Criar**.

## <a name="create-a-spark-cluster-in-databricks"></a>Criar um cluster do Spark no Databricks

1. No portal do Azure, aceda à área de trabalho do Databricks que criou e, em seguida, selecione **Iniciar Área de Trabalho**.

2. Será redirecionado para o portal do Azure Databricks. No portal, selecione **Novo** > **Cluster**.

    ![Tijolos de dados em Azure](./media/data-lake-storage-quickstart-create-databricks-account/databricks-on-azure.png "Tijolos de dados em Azure")

3. Na página **Novo cluster**, indique os valores para criar um cluster.

    ![Criar cluster de faíscas databricks em Azure](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-spark-cluster.png "Criar cluster de faíscas databricks em Azure")

    Preencha os valores para os campos seguintes e aceite os valores predefinidos para os outros campos:

    - Introduza um nome para o cluster.
     
    - Certifique-se de que seleciona a caixa de verificação **Terminar após 120 minutos de inatividade**. Indique uma duração (em minutos) para terminar o cluster, caso não esteja a ser utilizado.

4. Selecione **Criar cluster**. Depois de o cluster estar em execução, pode anexar blocos de notas ao cluster e executar tarefas do Spark.

Para obter mais informações sobre a criação de clusters, veja [Criar um cluster do Spark no Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

## <a name="create-storage-account-container"></a>Criar recipiente de conta de armazenamento

Nesta secção, vai criar um bloco de notas na área de trabalho do Azure Databricks e, em seguida, executar fragmentos de código para configurar a conta de armazenamento.

1. No [Portal do Azure](https://portal.azure.com), aceda à área de trabalho do Azure Databricks que criou e selecione **Iniciar Área de Trabalho**.

2. No painel esquerdo, selecione **Área de Trabalho**. No menu pendente **Área de Trabalho**, selecione **Criar** > **Bloco de Notas**.

    ![Criar caderno em Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Criar caderno em Databricks")

3. Na caixa de diálogo **Criar Bloco de Notas**, introduza um nome para o bloco de notas. Selecione **Scala** como a linguagem e selecione o cluster do Spark que criou anteriormente.

    ![Criar caderno em Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-details.png "Criar caderno em Databricks")

    Selecione **Criar**.

4. Copie e cole o seguinte bloco de código na primeira célula, mas não execute este código ainda.

   ```scala
   spark.conf.set("fs.azure.account.auth.type.<storage-account-name>.dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account-name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<appID>")
   spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<password>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")

   ```
5. Neste bloco de código, substitua os `storage-account-name`, `appID`, `password`e `tenant-id` valores de espaço reservado neste bloco de código supor os valores que recolheu quando criou o diretor de serviço. Detete o valor do espaço reservado `container-name` para qualquer nome que queira dar ao recipiente.

6. Prima as teclas **SHIFT + ENTER** para executar o código neste bloco.

## <a name="ingest-sample-data"></a>Ingerir dados de exemplo

Antes de começar esta secção, tem de satisfazer os seguintes pré-requisitos:

Introduza o seguinte código numa célula do bloco de notas:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

Na célula, prima **SHIFT + ENTER** para executar o código.

Agora, numa nova célula abaixo desta, introduza o seguinte código e substitua os valores que aparecem nos suportes com os mesmos valores que utilizou anteriormente:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/")

Na célula, prima **SHIFT + ENTER** para executar o código.

## <a name="run-a-spark-sql-job"></a>Executar uma tarefa SQL do Spark

Realize as seguintes tarefas para executar uma tarefa SQL do Spark nos dados.

1. Execute uma instrução SQL para criar uma tabela temporária com dados do ficheiro de dados JSON de exemplo, **small_radio_json.json**. No fragmento seguinte, substitua os valores dos marcadores de posição pelo nome do contentor e o nome da conta de armazenamento. Com o bloco de notas que criou anteriormente, cole o fragmento numa nova célula de código no bloco de notas e prima SHIFT + ENTER.

    ```sql
    %sql
    DROP TABLE IF EXISTS radio_sample_data;
    CREATE TABLE radio_sample_data
    USING json
    OPTIONS (
     path  "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json"
    )
    ```

    Depois de o comando ser concluído com êxito, terá todos os dados do ficheiro JSON como uma tabela num cluster do Databricks.

    O comando mágico de linguagem `%sql` permite executar um código SQL a partir do bloco de notas, mesmo se o bloco de notas for de outro tipo. Para obter mais informações, veja [Misturar linguagens num bloco de notas](https://docs.azuredatabricks.net/user-guide/notebooks/index.html#mixing-languages-in-a-notebook).

2. Vamos ver um instantâneo dos dados JSON de exemplo para compreender melhor a consulta que irá executar. Cole o fragmento seguinte na célula de código e prima **SHIFT + ENTER**.

    ```sql
    %sql
    SELECT * from radio_sample_data
    ```

3. Verá uma saída tabular, como a apresentada na captura de ecrã seguinte, (apenas são apresentadas algumas colunas):

    ![Dados da Amostra JSON](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sample-csv-data.png "Dados da Amostra JSON")

    Entre outros detalhes, os dados da amostra captam o sexo da audiência de um canal de rádio (nome de coluna, **género)** e se a sua subscrição é gratuita ou paga (nome de coluna, **nível).**

4. Agora, vai criar uma representação visual destes dados para mostrar para cada género, quantos utilizadores têm contas gratuitas e quantos têm subscrições pagas. Na parte inferior da saída tabular, clique no ícone de **Gráfico de barras** e, em seguida, clique em **Opções de Desenho**.

    ![Criar gráfico de barras](./media/data-lake-storage-quickstart-create-databricks-account/create-plots-databricks-notebook.png "Criar gráfico de barras")

5. Em **Personalizar Desenho**, arraste e largue os valores, conforme mostra a captura de ecrã.

    ![Personalizar gráfico de barras](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-customize-plot.png "Personalizar gráfico de barras")

    - Defina **Chaves** como **género**.
    - Defina **Agrupamentos de séries** como **nível**.
    - Defina **Valores** como **nível**.
    - Defina **Agregação** como **CONTAGEM**.

6. Clique em **Aplicar**.

7. A saída mostra a representação visual, tal como ilustrado na captura de ecrã seguinte:

     ![Personalizar gráfico de barras](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sql-query-output-bar-chart.png "Personalizar gráfico de barras")

## <a name="clean-up-resources"></a>Limpar recursos

Assim que terminar este artigo, pode terminar o agrupamento. Na área de trabalho do Azure Databricks, selecione **Clusters** e localize o cluster que pretende terminar. Paire o cursor do rato sobre o botão de reticências na coluna **Ações** e selecione o ícone **Terminar**.

![Parar um cluster de Databricks](./media/data-lake-storage-quickstart-create-databricks-account/terminate-databricks-cluster.png "Parar um cluster de Databricks")

Se não encerrar manualmente o cluster que para automaticamente, desde que selecione o **'Terminate' após \_\_ minutos de** caixa de verificação de inatividade enquanto cria o cluster. Se definir esta opção, o cluster para depois de estar inativo durante o período de tempo designado.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou um cluster do Spark no Azure Databricks e executou uma tarefa do Spark com dados numa conta de armazenamento com o Data Lake Storage Gen2 ativado.

Avance para o artigo seguinte para saber como executar uma operação de ETL (extração, transformação e carregamento de dados) com o Azure Databricks.

> [!div class="nextstepaction"]
>[Extrair, transformar e carregar dados utilizando tijolos de dados Azure.](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md)

- Para aprender a importar dados de outras fontes de dados para Os Databricks Do Azure, consulte [as fontes](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html)de dados da Spark .

- Para saber mais sobre outras formas de aceder ao Azure Data Lake Storage Gen2 a partir de um espaço de trabalho Azure Databricks, consulte [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html).
