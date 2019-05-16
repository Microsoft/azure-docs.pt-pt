---
title: 'Início rápido: Executar uma tarefa do Spark no Azure Databricks com o portal do Azure'
description: O manual de início rápido mostra como utilizar o portal do Azure para criar uma área de trabalho do Azure Databricks, um cluster do Apache Spark e executar uma tarefa do Spark.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: 43133810c6f8b7cb9fdacb2503103e09f345acfc
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/13/2019
ms.locfileid: "65551057"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>Início rápido: Executar uma tarefa do Spark no Azure Databricks com o portal do Azure

Este manual de início rápido mostra como criar uma área de trabalho do Azure Databricks e um cluster do Apache Spark dentro dessa área de trabalho. Por fim, irá aprender a executar uma tarefa do Spark no cluster do Databricks. Para obter mais informações sobre o Azure Databricks, veja [O que é o Azure Databricks?](what-is-azure-databricks.md)

Neste início rápido, como parte da tarefa do Spark, analise os dados de segurança de Boston para obter informações sobre os diferentes métodos de criação de relatórios.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-databricks-workspace"></a>Criar uma área de trabalho do Azure Databricks

Nesta secção, vai criar uma área de trabalho do Azure Databricks com o portal do Azure.

1. No Portal do Azure, selecione **Criar um recurso** > **Análise** > **Azure Databricks**.

    ![Databricks no portal do Azure](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "Databricks no portal do Azure")

2. Em **Serviço Azure Databricks**, forneça os valores para criar uma área de trabalho do Databricks.

    ![Criar uma área de trabalho do Azure Databricks](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "Criar uma área de trabalho do Azure Databricks")

    Forneça os seguintes valores:
    
    |Propriedade  |Descrição  |
    |---------|---------|
    |**Nome da área de trabalho**     | Indique um nome para a sua área de trabalho do Databricks        |
    |**Subscrição**     | Na lista pendente, selecione a sua subscrição do Azure.        |
    |**Grupo de recursos**     | Especifique se quer criar um novo grupo de recursos ou utilizar um existente. Um grupo de recursos é um contentor que mantém recursos relacionados para uma solução do Azure. Para obter mais informações, veja [Descrição geral do Grupo de Recursos do Azure](../azure-resource-manager/resource-group-overview.md). |
    |**Localização**     | Selecione **E.U.A. Oeste 2**. Para outras regiões disponíveis, veja [Serviços do Azure disponíveis por região](https://azure.microsoft.com/regions/services/).        |
    |**Escalão de Preço**     |  Escolha entre **padrão**, **Premium**, ou **avaliação**. Para obter mais informações sobre estes escalões, veja [Página de preços do Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Selecione **Afixar ao dashboard** e, em seguida, clique em **Criar**.

4. A criação da área de trabalho demora alguns minutos. Durante a criação da área de trabalho, pode ver o estado da implementação **notificações**.

    ![Mosaico de implementação do Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-deployment-tile.png "Mosaico de implementação do Databricks")

## <a name="create-a-spark-cluster-in-databricks"></a>Criar um cluster do Spark no Databricks

> [!NOTE]
> Para utilizar uma conta gratuita para criar o cluster do Azure Databricks, antes de criar o cluster, aceda ao seu perfil e altere a subscrição para **pay as you go**. Para obter mais informações, veja [Conta gratuita do Azure](https://azure.microsoft.com/free/).

1. No portal do Azure, aceda à área de trabalho do Databricks que criou e, em seguida, clique em **Iniciar Área de Trabalho**.

2. Será redirecionado para o portal do Azure Databricks. No portal, clique em **Novo Cluster**.

    ![Databricks no Azure](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Databricks no Azure")

3. Na página **Novo cluster**, indique os valores para criar um cluster.

    ![Criar um cluster Databricks Spark no Azure](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Criar um cluster Databricks Spark no Azure")

    Aceite todos os outros valores predefinidos que não sejam os seguintes:

   * Introduza um nome para o cluster.
   * Neste artigo, crie um cluster com **5.2** tempo de execução.
   * Certifique-se de que seleciona a caixa de verificação **Terminar após \_\_ minutos de atividade**. Indique uma duração (em minutos) para terminar o cluster, caso não esteja a ser utilizado.
    
     Selecione **Criar cluster**. Depois de o cluster estar em execução, pode anexar blocos de notas ao cluster e executar tarefas do Spark.

Para obter mais informações sobre a criação de clusters, veja [Criar um cluster do Spark no Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

## <a name="run-a-spark-sql-job"></a>Executar uma tarefa SQL do Spark

Execute as seguintes tarefas para criar um bloco de notas no Databricks, configure o bloco de notas para ler dados a partir de um conjuntos de dados aberto do Azure e, em seguida, execute uma tarefa SQL do Spark nos dados.

1. No painel esquerdo, selecione **Azure Databricks**. Partir do **tarefas comuns**, selecione **novo bloco de notas**.

    ![Criar um bloco de notas no Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Criar um bloco de notas no Databricks")

2. Na **criar bloco de notas** caixa de diálogo caixa, introduza um nome, selecione **Python** como a linguagem e selecione o cluster do Spark que criou anteriormente.

    ![Criar um bloco de notas no Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Criar um bloco de notas no Databricks")

    Selecione **Criar**.

3. Neste passo, crie um DataFrame do Spark com os dados de segurança de Boston da [conjuntos de dados aberto do Azure](https://azure.microsoft.com/services/open-datasets/catalog/boston-safety-data/#AzureDatabricks)e utilizar o SQL para consultar os dados.

   O comando a seguir define as informações de acesso de armazenamento do Azure. Cole este código de PySpark na primeira célula e utilizar **Shift + Enter** para executar o código.

   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "citydatacontainer"
   blob_relative_path = "Safety/Release/city=Boston"
   blob_sas_token = r"?st=2019-02-26T02%3A34%3A32Z&se=2119-02-27T02%3A34%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=XlJVWA7fMXCSxCKqJm8psMOh0W4h7cSYO28coRqF2fs%3D"
   ```

   O seguinte comando permite ao Spark ler a partir do armazenamento de BLOBs remotamente. Cole este código de PySpark para a próxima célula e utilizar **Shift + Enter** para executar o código.

   ```python
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
   print('Remote blob path: ' + wasbs_path)
   ```

   O comando seguinte cria um pacote de dados. Cole este código de PySpark para a próxima célula e utilizar **Shift + Enter** para executar o código.

   ```python
   df = spark.read.parquet(wasbs_path)
   print('Register the DataFrame as a SQL temporary view: source')
   df.createOrReplaceTempView('source')
   ```

4. Execute as primeiras 10 linhas de dados de uma instrução de SQL retorna a partir da vista temporária chamada **origem**. Cole este código de PySpark para a próxima célula e utilizar **Shift + Enter** para executar o código.

   ```python
   print('Displaying top 10 rows: ')
   display(spark.sql('SELECT * FROM source LIMIT 10'))
   ```

5. Verá uma saída tabular, como a apresentada na captura de ecrã seguinte, (apenas são apresentadas algumas colunas):

    ![Dados de exemplo](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "dados JSON de exemplo")

6. Agora, criar uma representação visual destes dados para mostrar o número de eventos de segurança é relatado usando-os cidadãos ligar aplicação e a aplicação de função de trabalho de cidade em vez de outras origens. Na parte inferior da saída tabular, selecione o **gráfico de barras** ícone e clique em **Plot Options**.

    ![Criar um gráfico de barras](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "Criar um gráfico de barras")

8. Em **Personalizar Desenho**, arraste e largue os valores, conforme mostra a captura de ecrã.

    ![Personalizar o gráfico circular](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "personalizar o gráfico de barras")

   * Definir **chaves** ao **origem**.
   * Definir **valores** ao **< \id >**.
   * Defina **Agregação** como **CONTAGEM**.
   * Definir **tipo de apresentação** ao **gráfico de pizza**.

     Clique em **Aplicar**.

## <a name="clean-up-resources"></a>Limpar recursos

Depois de ler o artigo, pode terminar o cluster. Para tal, na área de trabalho do Azure Databricks, no painel esquerdo, selecione **Clusters**. Para o cluster que quer terminar, mova o cursor sobre o botão de reticências na coluna **Ações** e selecione o ícone **Terminar**.

![Parar um cluster do Databricks](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Parar um cluster do Databricks")

Se não terminar manualmente o cluster, este irá parar automaticamente, desde que tenha selecionado a **terminar após \_ \_ minutos de inatividade** caixa de verificação ao criar o cluster. Nesse caso, o cluster para automaticamente se tiver estado inativo durante o período de tempo especificado.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, criou um cluster do Spark no Azure Databricks e executou uma tarefa do Spark com dados a partir de conjuntos de dados aberto do Azure. Também pode ver a página [Origens de dados do Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) para saber como importar dados de outras origens de dados para o Azure Databricks. Avance para o artigo seguinte para saber como executar uma operação de ETL (extração, transformação e carregamento de dados) com o Azure Databricks.

> [!div class="nextstepaction"]
>[Extrair, transformar e carregar dados com o Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
