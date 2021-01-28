---
title: 'Quickstart: Criar cluster Apache Spark usando o modelo - Azure HDInsight'
description: Este quickstart mostra como usar o modelo de Gestor de Recursos para criar um cluster Apache Spark em Azure HDInsight, e executar uma consulta Spark SQL.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: 1bbf3cbffe2ab51f10e86dc3b9fe4ea6040a7911
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930268"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-arm-template"></a>Quickstart: Criar cluster Apache Spark em Azure HDInsight usando o modelo ARM

Neste arranque rápido, você usa um modelo de Gestor de Recursos Azure (modelo ARM) para criar um cluster [Apache Spark](./apache-spark-overview.md) em Azure HDInsight. Em seguida, você cria um arquivo Jupyter Notebook, e usá-lo para executar consultas Spark SQL contra as mesas apache Hive. O Azure HDInsight é um serviço de análise gerido, de espectro completo e de código aberto para empresas. A estrutura Apache Spark para HDInsight permite uma análise rápida de dados e computação de cluster utilizando o processamento na memória. O Jupyter Notebook permite interagir com os seus dados, combinar código com texto de marcação e fazer visualizações simples.

Se estiver a utilizar vários clusters em conjunto, vai querer criar uma rede virtual, e se estiver a usar um cluster Spark também vai querer usar o Conector do Armazém da Colmeia. Para obter mais informações, consulte [Plan a virtual network for Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md) and Integrate Apache Spark and Apache [Hive with the Hive Warehouse Connector](../interactive-query/apache-hive-warehouse-connector.md).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux).

:::code language="json" source="~/quickstart-templates/101-hdinsight-spark-linux/azuredeploy.json":::

Dois recursos Azure são definidos no modelo:

* [Microsoft.Storage/storageAstas:](/azure/templates/microsoft.storage/storageaccounts)criar uma conta de armazenamento Azure.
* [Microsoft.HDInsight/cluster:](/azure/templates/microsoft.hdinsight/clusters)crie um cluster HDInsight.

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione o botão **'Implementar para azul'** abaixo para iniciar súb9 no Azure e abrir o modelo ARM.

    [![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

1. Introduza ou selecione os seguintes valores:

    |Propriedade |Descrição |
    |---|---|
    |Subscrição|A partir da lista de drop-down, selecione a subscrição Azure que é usada para o cluster.|
    |Grupo de recursos|A partir da lista de drop-down, selecione o seu grupo de recursos existente ou selecione **Criar novo**.|
    |Localização|O valor irá autopopular com a localização utilizada para o grupo de recursos.|
    |Nome do Cluster|Introduza um nome globalmente exclusivo. Para este modelo, utilize apenas letras minúsculas e números.|
    |Nome de Utilizador de Início de Sessão do Cluster|Forneça o nome de utilizador, o padrão é **administrador**.|
    |Palavra-passe de Início de Sessão do Cluster|Forneça uma senha. A palavra-passe deve ter pelo menos 10 caracteres de comprimento e deve conter pelo menos um dígito, uma maiúscula e uma letra minúscula, um carácter não alfanumérico (exceto os caracteres ' ). |
    |Nome do utilizador Ssh|Forneça o nome de utilizador, o padrão é **sshuser**|
    |Ssh Password|Forneça a senha.|

    ![Crie cluster Spark em HDInsight usando o modelo de gestor de recursos Azure](./media/apache-spark-jupyter-spark-sql/resource-manager-template-spark.png "Crie cluster Spark em HDInsight usando um modelo de gestor de recursos Azure")

1. Para mais **uma análise dos termos e condições.** Em seguida, **selecione Eu concordo com os termos e condições acima indicados,** em seguida, **Comprar**. Receberá uma notificação de que a sua implantação está em andamento. A criação de um cluster demora cerca de 20 minutos.

Se tiver um problema com a criação de clusters HDInsight, pode ser que não tenha as permissões certas para o fazer. Para obter mais informações, veja [Access control requirements](../hdinsight-hadoop-customize-cluster-linux.md#access-control) (Requisitos do controlo de acesso).

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Assim que o cluster for criado, receberá uma notificação **conseguida da Implementação** com uma ligação **de recursos Para Go.** A sua página de grupo de recursos irá listar o seu novo cluster HDInsight e o armazenamento predefinido associado ao cluster. Cada cluster tem um [Azure Storage](../hdinsight-hadoop-use-blob-storage.md), um [Azure Data Lake Storage Gen1,](../hdinsight-hadoop-use-data-lake-storage-gen1.md)ou uma [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) dependência. É referida como a conta de armazenamento predefinida. O cluster HDInsight e a sua conta de armazenamento predefinida devem ser indicados na mesma região de Azure. Eliminar agrupamentos não apaga a dependência da conta de armazenamento. É referida como a conta de armazenamento predefinida. O cluster HDInsight e a sua conta de armazenamento predefinida devem ser indicados na mesma região de Azure. Excluir agrupamentos não apaga a conta de armazenamento.

## <a name="create-a-jupyter-notebook-file"></a>Criar um ficheiro do Jupyter Notebook

[Jupyter Notebook](https://jupyter.org/) é um ambiente de caderno interativo que suporta várias linguagens de programação. Pode utilizar um ficheiro Jupyter Notebook para interagir com os seus dados, combinar código com texto de marcação e realizar visualizações simples.

1. Abra o [portal do Azure](https://portal.azure.com).

2. Selecione **Clusters do HDInsight** e, em seguida, selecione o cluster que criou.

    ![Open HDInsight cluster no portal Azure](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. A partir do portal, na secção **de dashboards Cluster,** selecione **Jupyter Notebook**. Se lhe for pedido, introduza as credenciais de início de sessão do cluster.

   ![Open Jupyter Notebook para executar consulta interativa Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Open Jupyter Notebook para executar consulta interativa Spark SQL")

4. Selecione **New**  >  **PySpark** para criar um caderno.

   ![Crie um ficheiro Jupyter Notebook para executar consulta interativa spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Crie um Caderno Jupyter para executar consulta interativa spark SQL")

   É criado e aberto um novo bloco de notas com o nome Untitled (Untitled.pynb).

## <a name="run-apache-spark-sql-statements"></a>Executar declarações de Apache Spark SQL

SQL (Structured Query Language) é a linguagem mais comum e mais utilizada para consultar e transformar dados. O Spark SQL funciona como uma extensão do Apache Spark para o processamento de dados estruturados e utiliza a sintaxe familiar do SQL Server.

1. Verifique se o kernel está pronto. O kernel está pronto quando vir um círculo hollow junto ao nome do kernel no bloco de notas. O círculo sólido indica que o kernel está ocupado.

    ![Estatuto de kernel](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Estatuto de kernel")

    Quando inicia o bloco de notas pela primeira vez, o kernel efetua algumas tarefas em segundo plano. Aguarde que o kernel esteja preparado.

1. Cole o seguinte código numa célula vazia e, em seguida, prima **SHIFT + ENTER** para o executar. O comando lista as tabelas do Hive no cluster:

    ```sql
    %%sql
    SHOW TABLES
    ```

    Quando utilizar um ficheiro Jupyter Notebook com o seu cluster HDInsight, obtém-se uma sessão pré-estadutiva `spark` que pode utilizar para executar consultas de Hive utilizando o Spark SQL. `%%sql` indica ao Bloco de Notas do Jupyter que utilize a sessão `spark` predefinida para executar a consulta do Hive. A consulta devolve as primeiras dez linhas de uma tabela do Hive (**hivesampletable**) que vem em todos os clusters do HDInsight por predefinição. A primeira vez que submeter a consulta, o Jupyter criará uma aplicação Spark para o caderno. São precisos cerca de 30 segundos para concluir. Uma vez que a aplicação Spark esteja pronta, a consulta é executada em cerca de um segundo e produz os resultados. O resultado tem o seguinte aspeto:

    ![Consulta de Colmeia Apache em HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Consulta de colmeia em HDInsight")

    Sempre que executar uma consulta no Jupyter, o título da janela do browser apresenta o estado **(Ocupado)** juntamente com o título do bloco de notas. Também vê um círculo sólido junto ao texto do **PySpark** no canto superior direito.

1. Execute outra consulta para ver os dados no `hivesampletable`.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    O ecrã deve atualizar-se e mostrar o resultado da consulta.

    ![Saída de consulta de colmeia em HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Saída de consulta de colmeia em HDInsight")

1. No menu **File (Ficheiro)** do bloco de notas, selecione **Close and Halt (Fechar e Parar)**. Desligar o caderno liberta os recursos do cluster, incluindo a aplicação Spark.

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de completar o arranque rápido, é possível que queira eliminar o cluster. Com o HDInsight, os seus dados são armazenados no Azure Storage, para que possa eliminar com segurança um cluster quando este não estiver a ser utilizado. Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Uma vez que as taxas para o cluster são muitas vezes mais do que os encargos de armazenamento, faz sentido económico apagar clusters quando não estão a ser utilizados.

A partir do portal Azure, navegue até ao seu cluster e selecione **Delete**.

![Portal Azure elimina um cluster HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "Excluir o cluster HDInsight")

Também pode selecionar o nome do grupo de recursos para abrir a página do grupo de recursos e, em seguida, selecionar **Eliminar grupo de recursos**. Ao eliminar o grupo de recursos, elimina tanto o cluster HDInsight como a conta de armazenamento predefinido.

## <a name="next-steps"></a>Próximos passos

Neste quickstart, você aprendeu a criar um cluster Apache Spark em HDInsight e executar uma consulta básica spark SQL. Avance para o próximo tutorial para aprender a usar um cluster HDInsight para executar consultas interativas em dados de amostras.

> [!div class="nextstepaction"]
> [Executar consultas interativas sobre Apache Spark](./apache-spark-load-data-run-query.md)
