---
title: 'Início rápido: analisar dados em Azure Data Lake Storage Gen2 usando Azure Databricks | Microsoft Docs'
description: Aprenda a executar um trabalho do Spark em Azure Databricks usando a portal do Azure e uma conta de armazenamento Azure Data Lake Storage Gen2.
author: normesta
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 02/15/2019
ms.reviewer: jeking
ms.openlocfilehash: 5badd4aeabd8ec322ea5fb847cf134f302269c27
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330992"
---
# <a name="quickstart-analyze-data-in-azure-data-lake-storage-gen2-by-using-azure-databricks"></a>Início rápido: analisar dados em Azure Data Lake Storage Gen2 usando Azure Databricks

Este guia de início rápido mostra como executar um trabalho de Apache Spark usando Azure Databricks para executar a análise de dados armazenados em uma conta de armazenamento com Azure Data Lake Storage Gen2 habilitado.

Como parte do trabalho do Spark, você analisará uma assinatura de canal de rádio para obter informações sobre o uso gratuito/pago com base em dados demográficos.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Crie uma conta de armazenamento Data Lake Gen2. Consulte [início rápido: criar uma conta de armazenamento Azure data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md)

  Cole o nome da conta de armazenamento em um arquivo de texto. Você precisará dela em breve.

* Crie uma entidade de serviço. Consulte [como: usar o portal para criar um aplicativo do Azure AD e uma entidade de serviço que pode acessar recursos](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

  Há algumas coisas específicas que você precisará fazer ao executar as etapas nesse artigo.

  : heavy_check_mark: ao executar as etapas na seção [atribuir o aplicativo a uma função](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) do artigo, certifique-se de atribuir a função de **colaborador de dados de blob de armazenamento** à entidade de serviço.

  > [!IMPORTANT]
  > Certifique-se de atribuir a função no escopo da conta de armazenamento Data Lake Storage Gen2. Você pode atribuir uma função ao grupo de recursos ou à assinatura pai, mas receberá erros relacionados a permissões até que essas atribuições de função se propaguem para a conta de armazenamento.

  : heavy_check_mark: ao executar as etapas na seção [obter valores para entrar no](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) artigo, Cole a ID do locatário, a ID do aplicativo e os valores de senha em um arquivo de texto. Você precisará delas em breve.

## <a name="create-an-azure-databricks-workspace"></a>Criar uma área de trabalho do Azure Databricks

Nesta secção, vai criar uma área de trabalho do Azure Databricks com o portal do Azure.

1. No Portal do Azure, selecione **Criar um recurso** > **Análise** > **Azure Databricks**.

    ![Databricks no portal do Azure](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Databricks no portal do Azure")

2. Em **Serviço Azure Databricks**, forneça os valores para criar uma área de trabalho do Databricks.

    ![Criar uma área de trabalho do Azure Databricks](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-workspace.png "Criar uma área de trabalho do Azure Databricks")

    Forneça os seguintes valores:

    |Propriedade  |Descrição  |
    |---------|---------|
    |**Nome da área de trabalho**     | Indique um nome para a sua área de trabalho do Databricks        |
    |**Subscrição**     | Na lista pendente, selecione a sua subscrição do Azure.        |
    |**Grupo de recursos**     | Especifique se quer criar um novo grupo de recursos ou utilizar um existente. Um grupo de recursos é um contentor que mantém recursos relacionados para uma solução do Azure. Para obter mais informações, veja [Descrição geral do Grupo de Recursos do Azure](../../azure-resource-manager/resource-group-overview.md). |
    |**Localização**     | Selecione **E.U.A. Oeste 2**. Pode selecionar outra região pública, se preferir.        |
    |**Escalão de Preço**     |  Escolha entre **Standard** ou **Premium**. Para obter mais informações sobre estes escalões, veja [Página de preços do Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

3. A criação da conta demora alguns minutos. Para monitorar o status da operação, exiba a barra de progresso na parte superior.

4. Selecione **Afixar ao dashboard** e, em seguida, selecione **Criar**.

## <a name="create-a-spark-cluster-in-databricks"></a>Criar um cluster do Spark no Databricks

1. No portal do Azure, aceda à área de trabalho do Databricks que criou e, em seguida, selecione **Iniciar Área de Trabalho**.

2. Será redirecionado para o portal do Azure Databricks. No portal, selecione **Novo** > **Cluster**.

    ![Databricks no Azure](./media/data-lake-storage-quickstart-create-databricks-account/databricks-on-azure.png "Databricks no Azure")

3. Na página **Novo cluster**, indique os valores para criar um cluster.

    ![Criar um cluster Databricks Spark no Azure](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-spark-cluster.png "Criar um cluster Databricks Spark no Azure")

    Preencha os valores para os campos seguintes e aceite os valores predefinidos para os outros campos:

    - Introduza um nome para o cluster.
     
    - Certifique-se de que seleciona a caixa de verificação **Terminar após 120 minutos de inatividade**. Indique uma duração (em minutos) para terminar o cluster, caso não esteja a ser utilizado.

4. Selecione **Criar cluster**. Depois de o cluster estar em execução, pode anexar blocos de notas ao cluster e executar tarefas do Spark.

Para obter mais informações sobre a criação de clusters, veja [Criar um cluster do Spark no Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

## <a name="create-storage-account-container"></a>Criar contêiner de conta de armazenamento

Nesta secção, vai criar um bloco de notas na área de trabalho do Azure Databricks e, em seguida, executar fragmentos de código para configurar a conta de armazenamento.

1. No [Portal do Azure](https://portal.azure.com), aceda à área de trabalho do Azure Databricks que criou e selecione **Iniciar Área de Trabalho**.

2. No painel esquerdo, selecione **Área de Trabalho**. No menu pendente **Área de Trabalho**, selecione **Criar** > **Bloco de Notas**.

    ![Criar um bloco de notas no Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Criar um bloco de notas no Databricks")

3. Na caixa de diálogo **Criar Bloco de Notas**, introduza um nome para o bloco de notas. Selecione **Scala** como a linguagem e selecione o cluster do Spark que criou anteriormente.

    ![Criar um bloco de notas no Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-details.png "Criar um bloco de notas no Databricks")

    Selecione **Criar**.

4. Copie e cole o bloco de código a seguir na primeira célula, mas não execute esse código ainda.

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

    > [!NOTE]
    > Esse bloco de código acessa diretamente o Data Lake ponto de extremidade Gen2 usando o OAuth, mas há outras maneiras de conectar o espaço de trabalho do databricks à sua conta de Data Lake Storage Gen2. Por exemplo, você pode montar o contêiner usando o OAuth ou usar um acesso direto com chave compartilhada. <br>Para ver exemplos dessas abordagens, consulte o artigo [Azure data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) no site do Azure Databricks.

5. Nesse bloco de código, substitua os valores de espaço reservado `storage-account-name`, `appID`, `password`e `tenant-id` nesse bloco de código pelos valores que você coletou ao criar a entidade de serviço. Defina o valor do espaço reservado `container-name` como qualquer nome que você deseja dar ao contêiner.

    > [!NOTE]
    > Em uma configuração de produção, considere armazenar sua chave de autenticação no Azure Databricks. Em seguida, adicione uma chave de pesquisa ao bloco de código em vez da chave de autenticação. Depois de concluir este guia de início rápido, consulte o artigo [Azure data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) no site do Azure Databricks para ver exemplos dessa abordagem.

6. Pressione as teclas **Shift + Enter** para executar o código neste bloco.

## <a name="ingest-sample-data"></a>Ingerir dados de exemplo

Antes de começar esta secção, tem de satisfazer os seguintes pré-requisitos:

Introduza o seguinte código numa célula do bloco de notas:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

Na célula, pressione **Shift + Enter** para executar o código.

Agora, em uma nova célula abaixo desta, insira o código a seguir e substitua os valores que aparecem entre colchetes com os mesmos valores que você usou anteriormente:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://<file-system>@<account-name>.dfs.core.windows.net/")

Na célula, pressione **Shift + Enter** para executar o código.

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

    ![Dados JSON de exemplo](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sample-csv-data.png "Dados JSON de exemplo")

    Entre outros detalhes, os dados de exemplo capturam o gênero do público de um canal de rádio (nome da coluna, **sexo**) e se sua assinatura é gratuita ou paga (nome da coluna, **nível**).

4. Agora, vai criar uma representação visual destes dados para mostrar para cada género, quantos utilizadores têm contas gratuitas e quantos têm subscrições pagas. Na parte inferior da saída tabular, clique no ícone de **Gráfico de barras** e, em seguida, clique em **Opções de Desenho**.

    ![Criar um gráfico de barras](./media/data-lake-storage-quickstart-create-databricks-account/create-plots-databricks-notebook.png "Criar um gráfico de barras")

5. Em **Personalizar Desenho**, arraste e largue os valores, conforme mostra a captura de ecrã.

    ![Personalizar um gráfico de barras](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-customize-plot.png "Personalizar um gráfico de barras")

    - Defina **Chaves** como **género**.
    - Defina **Agrupamentos de séries** como **nível**.
    - Defina **Valores** como **nível**.
    - Defina **Agregação** como **CONTAGEM**.

6. Clique em **Aplicar**.

7. A saída mostra a representação visual, tal como ilustrado na captura de ecrã seguinte:

     ![Personalizar um gráfico de barras](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sql-query-output-bar-chart.png "Personalizar um gráfico de barras")

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver concluído este artigo, você poderá encerrar o cluster. Na área de trabalho do Azure Databricks, selecione **Clusters** e localize o cluster que pretende terminar. Paire o cursor do rato sobre o botão de reticências na coluna **Ações** e selecione o ícone **Terminar**.

![Parar um cluster do Databricks](./media/data-lake-storage-quickstart-create-databricks-account/terminate-databricks-cluster.png "Parar um cluster do Databricks")

Se você não encerrar manualmente o cluster, ele será interrompido automaticamente, desde que você tenha selecionado a caixa de seleção **terminar depois de \_\_ minutos de inatividade** ao criar o cluster. Se definir esta opção, o cluster para depois de estar inativo durante o período de tempo designado.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou um cluster do Spark no Azure Databricks e executou uma tarefa do Spark com dados numa conta de armazenamento com o Data Lake Storage Gen2 ativado. Também pode ver a página [Origens de dados do Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) para saber como importar dados de outras origens de dados para o Azure Databricks. Avance para o artigo seguinte para saber como executar uma operação de ETL (extração, transformação e carregamento de dados) com o Azure Databricks.

> [!div class="nextstepaction"]
>[Extrair, transformar e carregar dados com o Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md)
