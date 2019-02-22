---
title: 'Início rápido: Analisar dados de geração 2 de armazenamento do Azure Data Lake ao utilizar o Azure Databricks | Documentos da Microsoft'
description: Saiba como executar uma tarefa do Spark no Azure Databricks com o portal do Azure e uma conta de armazenamento de geração 2 de armazenamento do Azure Data Lake.
services: storage
author: normesta
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 02/15/2019
ms.openlocfilehash: 1b5f05f3cddea986230327165399b0fe530e361b
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588080"
---
# <a name="quickstart-analyze-data-in-azure-data-lake-storage-gen2-by-using-azure-databricks"></a>Início rápido: Analisar dados de geração 2 de armazenamento do Azure Data Lake ao utilizar o Azure Databricks

Este início rápido mostra como executar uma tarefa do Apache Spark com o Azure Databricks para realizar análises em dados armazenados numa conta de armazenamento com o Azure Data Lake Storage Gen2 ativada.

Como parte da tarefa do Spark, vou analisar um dados de subscrição de canal de rádio para obter informações sobre a utilização gratuita/paga com base nos dados demográficos.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Crie uma conta de armazenamento de geração 2 do Data Lake. Consulte [início rápido: Criar uma conta de armazenamento de geração 2 de armazenamento do Azure Data Lake](data-lake-storage-quickstart-create-account.md)

  Cole o nome da conta de armazenamento num arquivo de texto. Precisará dela em breve.

*  Crie um principal de serviço. Consulte [como: Utilizar o portal para criar um Azure AD principal de aplicações e serviço que pode aceder a recursos](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

   Há duas coisas específicas que terá que fazer à medida que efetua os passos nesse artigo.

   :heavy_check_mark: Quando realizar os passos no [atribuir a aplicação a uma função](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) secção do artigo, lembre-se de que atribuir a **contribuinte de dados de Blob de armazenamento** função ao principal de serviço.

   > [!IMPORTANT]
   > Certifique-se atribuir a função no âmbito da conta de armazenamento de geração 2 de armazenamento do Data Lake. Pode atribuir uma função para o grupo de recursos de principal ou uma subscrição, mas irá receber erros relacionados com as permissões até que essas atribuições de função se propaguem para a conta de armazenamento.

   :heavy_check_mark: Ao realizar os passos a [obter os valores para iniciar sessão](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) secção do artigo, colar o ID de inquilino, ID da aplicação e valores de chave de autenticação para um ficheiro de texto. Precisará aqueles em breve.

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

    Selecione **Afixar ao dashboard** e, em seguida, clique em **Criar**.

3. Demora um pouco de tempo para criar a área de trabalho. Enquanto está a ser criada a área de trabalho, o **submeter a implementação para o Azure Databricks** mosaico é apresentado no lado direito. Para ver o título, poderá ter de deslocar para a direita no seu dashboard. Há também uma barra de progresso que aparece próximo à parte superior do ecrã. Pode ver qualquer área de progresso.

    ![Mosaico de implementação do Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-deployment-tile.png "Mosaico de implementação do Databricks")

## <a name="create-a-spark-cluster-in-databricks"></a>Criar um cluster do Spark no Databricks

1. No portal do Azure, aceda à área de trabalho do Databricks que criou e, em seguida, selecione **Iniciar Área de Trabalho**.

2. Será redirecionado para o portal do Azure Databricks. No portal, selecione **Novo** > **Cluster**.

    ![Databricks no Azure](./media/data-lake-storage-quickstart-create-databricks-account/databricks-on-azure.png "Databricks no Azure")

3. Na página **Novo cluster**, indique os valores para criar um cluster.

    ![Criar um cluster Databricks Spark no Azure](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-spark-cluster.png "Criar um cluster Databricks Spark no Azure")

    Aceite todos os outros valores predefinidos que não sejam os seguintes:

    * Introduza um nome para o cluster.
    * Crie um cluster com **5.1** tempo de execução.
    * Certifique-se de que seleciona a caixa de verificação **Terminar após 120 minutos de inatividade**. Indique uma duração (em minutos) para terminar o cluster, caso não esteja a ser utilizado.

4. Selecione **Criar cluster**. Depois de o cluster estar em execução, pode anexar blocos de notas ao cluster e executar tarefas do Spark.

Para obter mais informações sobre a criação de clusters, veja [Criar um cluster do Spark no Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

## <a name="create-storage-account-file-system"></a>Criar sistema de ficheiro da conta de armazenamento

Nesta secção, vai criar um bloco de notas na área de trabalho do Azure Databricks e, em seguida, executar fragmentos de código para configurar a conta de armazenamento.

1. No [portal do Azure](https://portal.azure.com), aceda à área de trabalho do Azure Databricks que criou e selecione **Iniciar Área de Trabalho**.

2. No painel esquerdo, selecione **Área de Trabalho**. No menu pendente **Área de Trabalho**, selecione **Criar** > **Bloco de Notas**.

    ![Criar um bloco de notas no Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Criar um bloco de notas no Databricks")

3. Na caixa de diálogo **Criar Bloco de Notas**, introduza um nome para o bloco de notas. Selecione **Scala** como a linguagem e selecione o cluster do Spark que criou anteriormente.

    ![Criar um bloco de notas no Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-details.png "Criar um bloco de notas no Databricks")

    Selecione **Criar**.

4. Copie e cole o seguinte bloco de código na primeira célula, mas não executar esse código ainda.

   ```scala
   spark.conf.set("fs.azure.account.auth.type.<storage-account-name>.dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account-name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<application-id>")
   spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<authentication-key>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")

   ```

    > [!NOTE]
    > Este bloco de código acede diretamente o ponto de extremidade de geração 2 do Data Lake ao utilizar OAuth, mas existem outras formas de ligar a área de trabalho do Databricks à sua conta de geração 2 de armazenamento do Data Lake. Por exemplo, pode montar o sistema de ficheiros ao utilizar o OAuth ou utilize um acesso direto com a chave partilhada. <br>Para ver exemplos dessas abordagens, consulte a [geração 2 de armazenamento do Azure Data Lake](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) artigo no Web site do Azure Databricks.

5. Este bloco de código, substitua a `storage-account-name`, `application-id`, `authentication-id`, e `tenant-id` valores de marcador de posição este bloco de código com os valores que recolheu quando criou o principal de serviço. Definir o `file-system-name` valor do marcador de posição para tudo o que nome pretende dar ao sistema de ficheiros.

    > [!NOTE]
    > Num ambiente de produção, considere armazenar a chave de autenticação no Azure Databricks. Em seguida, adicione uma chave de pesquisa ao seu bloco de código em vez da chave de autenticação. Depois de concluir este início rápido, consulte a [geração 2 de armazenamento do Azure Data Lake](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) artigo no site do Azure Databricks para ver exemplos dessa abordagem.

6. Prima a **SHIFT + ENTER** chaves para executar o código nesse bloco.

## <a name="ingest-sample-data"></a>Ingerir dados de exemplo

Antes de começar esta secção, tem de satisfazer os seguintes pré-requisitos:

Introduza o seguinte código numa célula do bloco de notas:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

Na célula, prima **SHIFT + ENTER** para executar o código.

Agora numa nova célula abaixo destes, introduza o código a seguir e substitua os valores que aparecem entre parênteses Retos com os mesmos valores que utilizou anteriormente:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://<file-system>@<account-name>.dfs.core.windows.net/")

Na célula, prima **SHIFT + ENTER** para executar o código.

## <a name="run-a-spark-sql-job"></a>Executar uma tarefa SQL do Spark

Realize as seguintes tarefas para executar uma tarefa SQL do Spark nos dados.

1. Execute uma instrução SQL para criar uma tabela temporária com dados do ficheiro de dados JSON de exemplo, **small_radio_json.json**. No fragmento seguinte, substitua os valores dos marcadores de posição pelo nome do sistema de ficheiros e o nome da conta de armazenamento. Com o bloco de notas que criou anteriormente, cole o fragmento numa nova célula de código no bloco de notas e prima SHIFT + ENTER.

    ```sql
    %sql
    DROP TABLE IF EXISTS radio_sample_data;
    CREATE TABLE radio_sample_data
    USING json
    OPTIONS (
     path  "abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/<PATH>/small_radio_json.json"
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

    Entre outros detalhes, os dados de exemplo capturam o género do público-alvo de um canal de rádio (nome da coluna **género**) e se a sua subscrição é gratuita ou paga (nome da coluna **nível**).

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

Quando tiver terminado com este artigo, pode terminar o cluster. Na área de trabalho do Azure Databricks, selecione **Clusters** e localize o cluster que pretende terminar. Paire o cursor do rato sobre o botão de reticências na coluna **Ações** e selecione o ícone **Terminar**.

![Parar um cluster do Databricks](./media/data-lake-storage-quickstart-create-databricks-account/terminate-databricks-cluster.png "Parar um cluster do Databricks")

Se não terminar manualmente o cluster para automaticamente, desde que selecionou o **terminar após \_ \_ minutos de inatividade** caixa de verificação ao criar o cluster. Se definir esta opção, o cluster para depois de estar inativo durante o período de tempo designado.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, criou um cluster do Spark no Azure Databricks e executou uma tarefa do Spark com dados numa conta de armazenamento com o Data Lake Storage Gen2 ativado. Também pode ver a página [Origens de dados do Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) para saber como importar dados de outras origens de dados para o Azure Databricks. Avance para o artigo seguinte para saber como executar uma operação de ETL (extração, transformação e carregamento de dados) com o Azure Databricks.

> [!div class="nextstepaction"]
>[Extrair, transformar e carregar dados com o Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md)
