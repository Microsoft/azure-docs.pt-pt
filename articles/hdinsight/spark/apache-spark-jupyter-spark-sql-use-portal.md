---
title: 'Quickstart: Criar cluster Spark em HDInsight usando o portal Azure'
description: Este quickstart mostra como usar o portal Azure para criar um cluster Apache Spark em Azure HDInsight, e executar uma consulta Spark SQL.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/25/2020
ms.openlocfilehash: 145dffea50040c86a4af9d77ba8f68cccc8d2958
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866051"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-portal"></a>Quickstart: Criar cluster Apache Spark em Azure HDInsight usando o portal Azure

Neste arranque rápido, você usa o portal Azure para criar um cluster Apache Spark em Azure HDInsight. Em seguida, você cria um Caderno Jupyter, e usá-lo para executar consultas Spark SQL contra as mesas apache hive. O Azure HDInsight é um serviço de análise gerido, de espectro completo e de código aberto para empresas. A estrutura Apache Spark para HDInsight permite uma análise rápida de dados e computação de cluster utilizando o processamento na memória. O Jupyter Notebook permite interagir com os seus dados, combinar código com texto de marcação e fazer visualizações simples.

Para obter explicações aprofundadas sobre as configurações disponíveis, consulte [Configurar clusters em HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Para obter mais informações sobre a utilização do portal para criar clusters, consulte [Criar clusters no portal.](../hdinsight-hadoop-create-linux-clusters-portal.md)

Se estiver a utilizar vários clusters em conjunto, vai querer criar uma rede virtual, e se estiver a usar um cluster Spark também vai querer usar o Conector do Armazém da Colmeia. Para obter mais informações, consulte [Plan a virtual network for Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md) and Integrate Apache Spark and Apache [Hive with the Hive Warehouse Connector](../interactive-query/apache-hive-warehouse-connector.md).

> [!IMPORTANT]  
> A faturação dos clusters do HDInsight é rateada ao minuto, quer esteja a utilizá-los ou não. Confirme que elimina o cluster depois de o utilizar. Para obter mais informações, consulte a secção [Limpar recursos](#clean-up-resources) deste artigo.

## <a name="prerequisites"></a>Pré-requisitos

Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

## <a name="create-an-apache-spark-cluster-in-hdinsight"></a>Criar um cluster Apache Spark em HDInsight

Você usa o portal Azure para criar um cluster HDInsight que usa Azure Storage Blobs como armazenamento de cluster. Para obter mais informações sobre como utilizar o Armazenamento do Data Lake Ger2, veja [Início Rápido: Configurar clusters no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. A partir do menu superior, selecione **+ Crie um recurso.**

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-resource.png " alt-text="Portal Azure criar uma" border="true":::urce de recurso" border="true"::

1. Selecione **Analytics**  >  **Azure HDInsight** para ir à página de **cluster Create HDInsight.**

1. A partir do separador **Básicos,** forneça as seguintes informações:

    |Propriedade  |Descrição  |
    |---------|---------|
    |Subscrição  | A partir da lista de drop-down, selecione a subscrição Azure que é usada para o cluster. |
    |Grupo de recursos | A partir da lista de drop-down, selecione o seu grupo de recursos existente ou selecione **Criar novo**.|
    |Nome do cluster | Introduza um nome globalmente exclusivo.|
    |Region   | A partir da lista de drop-down, selecione uma região onde o cluster é criado. |
    |Tipo de cluster| Selecione Selecionar o tipo de cluster para abrir uma lista. Na lista, selecione **Spark**.|
    |Versão cluster|Este campo irá preencher automaticamente a versão predefinitiva uma vez selecionado o tipo de cluster.|
    |Nome de utilizador de início de sessão do cluster| Introduza o nome de utilizador de início de sessão do cluster.  O nome predefinido é **administrador.** Utilize esta conta para iniciar sessão no Jupyter Notebook mais tarde no quickstart. |
    |Palavra-passe de início de sessão do cluster| Introduza a palavra-passe de início de sessão do cluster. |
    |Nome de utilizador de Secure Shell (SSH)| Introduza o nome de utilizador SSH. O nome de utilizador SSH utilizado neste guia de início rápido é **sshuser**. Por predefinição, esta conta tem a mesma palavra-passe que a conta *Nome de utilizador de início de sessão do cluster*. |

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-basics-spark.png " alt-text="Screenshot mostra Criar o cluster De Insight H D com o separador Basics selecionado." border="true":::

    Selecione **Seguinte: >>de armazenamento** para continuar na página **de Armazenamento.**

1. Em **Armazenamento**, forneça os seguintes valores:

    |Propriedade  |Descrição  |
    |---------|---------|
    |Tipo de armazenamento primário|Utilize o valor predefinido **Azure Storage**.|
    |Método de seleção|Utilizar o valor predefinido **Selecione da lista**.|
    |Conta de armazenamento primária|Utilize o valor auto-povoado.|
    |Contentor|Utilize o valor auto-povoado.|

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-storage.png " alt-text="Screenshot mostra Criar O cluster De Insight H D com o separador de armazenamento selecionado." border="true":::

    Selecione **Review + criar** para continuar.

1. Em **'Rever + criar'** **(criar),** selecione Criar . A criação do cluster demora cerca de 20 minutos. Tem de criar o cluster antes de poder avançar para a sessão seguinte.

Se tiver um problema com a criação de clusters HDInsight, pode ser que não tenha as permissões certas para o fazer. Para obter mais informações, veja [Access control requirements](../hdinsight-hadoop-customize-cluster-linux.md#access-control) (Requisitos do controlo de acesso).

## <a name="create-a-jupyter-notebook"></a>Criar um Bloco de Notas do Jupyter Notebook

O Jupyter Notebook é um ambiente de bloco de notas interativo que suporta várias linguagens de programação. O bloco de notas permite-lhe interagir com os seus dados, combinar código com texto markdown e realizar visualizações simples.

1. A partir de um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net/jupyter` para, onde `CLUSTERNAME` está o nome do seu cluster. Se lhe for pedido, introduza as credenciais de início de sessão do cluster.

1. Selecione **New**  >  **PySpark** para criar um caderno.

   :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png " alt-text="Crie um Caderno Jupyter para executar consulta interativa spark SQL" border="true":::

   É criado e aberto um novo bloco de notas com o nome Untitled (Untitled.pynb).

## <a name="run-apache-spark-sql-statements"></a>Executar declarações de Apache Spark SQL

SQL (Structured Query Language) é a linguagem mais comum e mais utilizada para consultar e definir dados. O Spark SQL funciona como uma extensão do Apache Spark para o processamento de dados estruturados e utiliza a sintaxe familiar do SQL Server.

1. Verifique se o kernel está pronto. O kernel está pronto quando vir um círculo hollow junto ao nome do kernel no bloco de notas. O círculo sólido indica que o kernel está ocupado.

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png " alt-text="A screenshot mostra uma janela Jupyter com um indicador PySpark." border="true"::: indicador arca. border="true":::

    Quando inicia o bloco de notas pela primeira vez, o kernel efetua algumas tarefas em segundo plano. Aguarde que o kernel esteja preparado.

1. Cole o seguinte código numa célula vazia e, em seguida, prima **SHIFT + ENTER** para o executar. O comando lista as tabelas do Hive no cluster:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Quando utilizar um Bloco de Notas Jupyter com o seu cluster HDInsight, obtém-se uma predefinição `sqlContext` que pode utilizar para executar consultas de Hive utilizando o Spark SQL. `%%sql` indica ao Bloco de Notas do Jupyter que utilize o `sqlContext` predefinido para executar a consulta do Hive. A consulta devolve as primeiras dez linhas de uma tabela do Hive (**hivesampletable**) que vem em todos os clusters do HDInsight por predefinição. São necessários cerca de 30 segundos para receber os resultados. O resultado tem o seguinte aspeto:

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-get-started-hive-query.png " alt-text="A screenshot mostra uma janela Jupyter para o caderno criado neste arranque rápido." border="true"::: é quickstart. border="true":::

    Sempre que executar uma consulta no Jupyter, o título da janela do browser apresenta o estado **(Ocupado)** juntamente com o título do bloco de notas. Também vê um círculo sólido junto ao texto do **PySpark** no canto superior direito.

1. Execute outra consulta para ver os dados no `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    O ecrã deve atualizar-se e mostrar o resultado da consulta.

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-get-started-hive-query-output.png " alt-text="Saída de consulta de colmeia em HDInsight" border="true"::: Insight" border="true":::

1. No menu **File (Ficheiro)** do bloco de notas, selecione **Close and Halt (Fechar e Parar)**. Encerrar o bloco de notas liberta os recursos do cluster.

## <a name="clean-up-resources"></a>Limpar os recursos

O HDInsight guarda os seus dados no Azure Storage ou no Azure Data Lake Storage, para que possa eliminar com segurança um cluster quando este não estiver a ser utilizado. Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Uma vez que as taxas para o cluster são muitas vezes mais do que os encargos de armazenamento, faz sentido económico apagar clusters quando não estão a ser utilizados. Se tenciona trabalhar imediatamente no tutorial listado nos [Passos seguintes](#next-steps), convém manter o cluster.

Regresse ao portal do Azure e selecione **Eliminar**.

:::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-azure-portal-delete-cluster.png " alt-text="Portal Azure elimina um" border="true":::cluster de visão hdInsight" border="true"::

Também pode selecionar o nome do grupo de recursos para abrir a página do grupo de recursos e, em seguida, selecionar **Eliminar grupo de recursos**. Ao eliminar o grupo de recursos, elimina tanto o cluster HDInsight como a conta de armazenamento predefinido.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você aprendeu a criar um cluster Apache Spark em HDInsight e executar uma consulta básica spark SQL. Avance para o próximo tutorial para aprender a usar um cluster HDInsight para executar consultas interativas em dados de amostras.

> [!div class="nextstepaction"]
> [Executar consultas interativas sobre Apache Spark](./apache-spark-load-data-run-query.md)