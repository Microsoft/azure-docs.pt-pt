---
title: Quickstart - Executar um trabalho de Spark em Azure Databricks usando o portal Azure
description: Este quickstart mostra como usar o portal Azure para criar um espaço de trabalho Azure Databricks, um cluster Apache Spark, e executar um trabalho spark.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 03/23/2020
ms.custom: mvc, tracking-python
ms.openlocfilehash: 6c7226a56d5811438ec9d0703e0b8242df13e17b
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84559308"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>Início rápido: Executar uma tarefa do Spark no Azure Databricks com o portal do Azure

Neste arranque rápido, você usa o portal Azure para criar um espaço de trabalho Azure Databricks com um cluster Apache Spark. Você dirige um trabalho no cluster e usa gráficos personalizados para produzir relatórios em tempo real a partir de dados de segurança de Boston.

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

> [!Note]
> Este tutorial não pode ser realizado utilizando **a assinatura de ensaio livre do Azure.**
> Se tiver uma conta gratuita, vá ao seu perfil e altere a sua subscrição para **pay-as-you-go**. Para obter mais informações, veja [Conta gratuita do Azure](https://azure.microsoft.com/free/). Em seguida, [retire o limite de gastos](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)e [solicite um aumento de quota](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) para vCPUs na sua região. Quando criar o seu espaço de trabalho Azure Databricks, pode selecionar o nível de preços **Do Trial (Premium - 14 Dias Free DBUs)** para dar ao espaço de trabalho acesso a DBUs premium Azure Databricks premium durante 14 dias.

## <a name="create-an-azure-databricks-workspace"></a>Criar uma área de trabalho do Azure Databricks

Nesta secção, vai criar uma área de trabalho do Azure Databricks com o portal do Azure.

1. No portal Azure, **selecione Criar um recurso**  >  **Analytics**  >  **Azure Databricks**.

    ![Databricks no portal Azure](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "Databricks no portal Azure")

2. Em **Serviço Azure Databricks**, forneça os valores para criar uma área de trabalho do Databricks.

    ![Criar uma área de trabalho do Azure Databricks](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "Criar uma área de trabalho do Azure Databricks")

    Forneça os seguintes valores:
    
    |Propriedade  |Descrição  |
    |---------|---------|
    |**Nome da área de trabalho**     | Indique um nome para a sua área de trabalho do Databricks        |
    |**Subscrição**     | Na lista pendente, selecione a sua subscrição do Azure.        |
    |**Grupo de recursos**     | Especifique se quer criar um novo grupo de recursos ou utilizar um existente. Um grupo de recursos é um contentor que detém recursos relacionados para uma solução do Azure. Para obter mais informações, veja [Descrição geral do Grupo de Recursos do Azure](../azure-resource-manager/management/overview.md). |
    |**Localização**     | Selecione **E.U.A. Oeste 2**. Para outras regiões disponíveis, veja [Serviços do Azure disponíveis por região](https://azure.microsoft.com/regions/services/).        |
    |**Nível de preços**     |  Escolha entre **Standard,** **Premium**ou **Trial**. Para obter mais informações sobre estes escalões, veja [Página de preços do Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

3. Selecione **Rever + Criar**e, em seguida, **Criar**. A criação da área de trabalho demora alguns minutos. Durante a criação do espaço de trabalho, pode visualizar o estado de implantação em **Notificações**. Uma vez concluído este processo, a sua conta de utilizador é automaticamente adicionada como um utilizador administrativo no espaço de trabalho.

    ![Azulejo de implantação de dados](./media/quickstart-create-databricks-workspace-portal/databricks-deployment-tile.png "Azulejo de implantação de dados")

    Quando uma implantação do espaço de trabalho falha, o espaço de trabalho ainda é criado num estado falhado. Elimine o espaço de trabalho falhado e crie um novo espaço de trabalho que resolva os erros de implantação. Quando elimina o espaço de trabalho falhado, o grupo de recursos geridos e quaisquer recursos implantados com sucesso também são eliminados.

## <a name="create-a-spark-cluster-in-databricks"></a>Criar um cluster do Spark no Databricks

> [!NOTE]
> Para utilizar uma conta gratuita para criar o cluster do Azure Databricks, antes de criar o cluster, aceda ao seu perfil e altere a subscrição para **pay as you go**. Para obter mais informações, veja [Conta gratuita do Azure](https://azure.microsoft.com/free/).

1. No portal do Azure, aceda à área de trabalho do Databricks que criou e, em seguida, clique em **Iniciar Área de Trabalho**.

2. Será redirecionado para o portal do Azure Databricks. A partir do portal, clique em **Novo Cluster.**

    ![Databricks em Azure](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Databricks em Azure")

3. Na página **Novo cluster**, indique os valores para criar um cluster.

    ![Criar conjunto de faíscas de dados no Azure](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Criar conjunto de faíscas de dados no Azure")

    Aceite todos os outros valores predefinidos que não sejam os seguintes:

   * Introduza um nome para o cluster.
   * Para este artigo, crie um cluster com **5.3** tempo de execução.
   * Certifique-se de que **seleciona a Caixa de Verificação Terminate after minutes of \_ \_ inactivactivity.** Indique uma duração (em minutos) para terminar o cluster, caso não esteja a ser utilizado.
    
     Selecione **Criar cluster**. Depois de o cluster estar em execução, pode anexar blocos de notas ao cluster e executar tarefas do Spark.

Para obter mais informações sobre a criação de clusters, veja [Criar um cluster do Spark no Azure Databricks](/azure/databricks/clusters/create).

## <a name="run-a-spark-sql-job"></a>Executar uma tarefa SQL do Spark

Execute as seguintes tarefas para criar um caderno em Databricks, configurar o caderno para ler dados de um Azure Open Datasets e, em seguida, executar um trabalho Spark SQL nos dados.

1. No painel esquerdo, selecione **Azure Databricks**. A partir das **Tarefas Comuns**, selecione **Novo Caderno.**

    ![Criar caderno em Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Criar caderno em Databricks")

2. Na caixa de diálogo **Create Notebook,** insira um nome, selecione **Python** como o idioma e selecione o cluster Spark que criou anteriormente.

    ![Criar caderno em Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Criar caderno em Databricks")

    Selecione **Criar**.

3. Neste passo, crie um Spark DataFrame com dados de segurança de Boston a partir de [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/boston-safety-data/#AzureDatabricks), e use o SQL para consultar os dados.

   O seguinte comando define as informações de acesso ao armazenamento Azure. Cole este código PySpark na primeira célula e utilize o **Shift+Enter** para executar o código.

   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "citydatacontainer"
   blob_relative_path = "Safety/Release/city=Boston"
   blob_sas_token = r"?st=2019-02-26T02%3A34%3A32Z&se=2119-02-27T02%3A34%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=XlJVWA7fMXCSxCKqJm8psMOh0W4h7cSYO28coRqF2fs%3D"
   ```

   O seguinte comando permite que a Spark leia remotamente a partir do armazenamento blob. Cole este código PySpark na célula seguinte e use **o Shift+Enter** para executar o código.

   ```python
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
   print('Remote blob path: ' + wasbs_path)
   ```

   O seguinte comando cria um DataFrame. Cole este código PySpark na célula seguinte e use **o Shift+Enter** para executar o código.

   ```python
   df = spark.read.parquet(wasbs_path)
   print('Register the DataFrame as a SQL temporary view: source')
   df.createOrReplaceTempView('source')
   ```

4. Executar uma declaração SQL devolva as 10 linhas de dados mais de 10 linhas da vista temporária chamada **fonte**. Cole este código PySpark na célula seguinte e use **o Shift+Enter** para executar o código.

   ```python
   print('Displaying top 10 rows: ')
   display(spark.sql('SELECT * FROM source LIMIT 10'))
   ```

5. Verá uma saída tabular, como a apresentada na captura de ecrã seguinte, (apenas são apresentadas algumas colunas):

    ![Dados de exemplo](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "Dados do JSON da amostra")

6. Agora cria uma representação visual destes dados para mostrar quantos eventos de segurança são relatados usando a App Citizens Connect e a City Worker App em vez de outras fontes. A partir da parte inferior da saída tabular, selecione o ícone **do gráfico de Barras** e, em seguida, clique em **Opções de Enredo**.

    ![Criar gráfico de barras](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "Criar gráfico de barras")

8. Em **Personalizar Desenho**, arraste e largue os valores, conforme mostra a captura de ecrã.

    ![Personalizar gráfico de tortas](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "Personalizar gráfico de barras")

   * Definir **teclas** para **a fonte**.
   * Definir **Valores** para **<\id>**.
   * Defina **Agregação** como **CONTAGEM**.
   * Definir **Tipo de exibição** para **gráfico de torta**.

     Clique em **Aplicar**.

## <a name="clean-up-resources"></a>Limpar recursos

Depois de ler o artigo, pode terminar o cluster. Para tal, na área de trabalho do Azure Databricks, no painel esquerdo, selecione **Clusters**. Para o cluster que quer terminar, mova o cursor sobre o botão de reticências na coluna **Ações** e selecione o ícone **Terminar**.

![Pare um cluster Databricks](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Pare um cluster Databricks")

Se não encerrar manualmente o cluster, este para automaticamente, desde que tenha selecionado a **caixa de verificação Terminate after \_ \_ minutes of inactivity** enquanto cria o cluster. Nesse caso, o cluster para automaticamente se tiver estado inativo durante o período de tempo especificado.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou um cluster Spark em Azure Databricks e executou um trabalho de Spark usando dados de Azure Open Datasets. Também pode ver a página [Origens de dados do Spark](/azure/databricks/data/data-sources/index) para saber como importar dados de outras origens de dados para o Azure Databricks. Avance para o artigo seguinte para saber como executar uma operação de ETL (extração, transformação e carregamento de dados) com o Azure Databricks.

> [!div class="nextstepaction"]
>[Extrair, transformar e carregar dados com o Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
