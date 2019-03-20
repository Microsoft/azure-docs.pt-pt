---
title: 'Início rápido: Criar um cluster do Spark no HDInsight com o portal do Azure'
description: Este guia de início rápido mostra como pode utilizar o portal do Azure para criar um cluster do Apache Spark no Azure HDInsight e executar uma consulta do Spark SQL.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 11/06/2018
ms.custom: mvc
ms.openlocfilehash: 6f96195197daf026cddee10785e0cadae8096031
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58111774"
---
# <a name="quickstart-create-an-apache-spark-cluster-in-hdinsight-using-the-azure-portal"></a>Início rápido: Criar um cluster do Apache Spark no HDInsight com o portal do Azure
Saiba como criar um cluster do Apache Spark no Azure HDInsight e como executar consultas do Spark SQL relativamente a tabelas do Hive. O Apache Spark permite uma análise de dados e computação de clusters rápidas através do processamento dentro da memória. Para obter informações sobre o Spark no HDInsight, consulte [descrição geral: Apache Spark no HDInsight do Azure](apache-spark-overview.md).

Neste guia de início rápido, irá utilizar o portal do Azure para criar um cluster do Apache Spark no HDInsight. O cluster utiliza o Azure Storage Blob como a ferramenta de armazenamento de clusters. Para obter mais informações sobre como utilizar a geração 2 de armazenamento do Data Lake, veja [início rápido: Configurar clusters no HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

> [!IMPORTANT]  
> A faturação dos clusters do HDInsight é rateada ao minuto, quer esteja a utilizá-los ou não. Confirme que elimina o cluster depois de o utilizar. Para obter mais informações, consulte a secção [Limpar recursos](#clean-up-resources) deste artigo.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-hdinsight-spark-cluster"></a>Criar um cluster do Spark no HDInsight

1. No portal do Azure, selecione **criar um recurso** > **análise** > **HDInsight**. 

    ![HDInsight no portal do Azure](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-hdinsight-spark-cluster.png "HDInsight no portal do Azure")
2. Em **Básico**, forneça os seguintes valores:
     
    |Propriedade  |Descrição  |
    |---------|---------|
    |**Nome do cluster**     | Dê um nome ao cluster do Apache Spark no HDInsight. O nome do cluster utilizado neste guia de início rápido é **myspark20180403**.|
    |**Subscrição**     | Selecione uma subscrição do Azure utilizada para este cluster no menu pendente. A subscrição utilizada neste guia de início rápido é **&lt;subscrição do Azure**. |
    |**Tipo de cluster**| Expanda o item e, em seguida, selecione **Spark** como o tipo de cluster e especifique a versão do cluster do Apache Spark. <br/> <img src="./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-hdinsight-spark-cluster-type.png" alt = "Select HDInsight cluster type" /> |
    |**Nome de utilizador de início de sessão do cluster**| Introduza o nome de utilizador de início de sessão do cluster.  O nome predefinido é *admin*. Irá utilizar esta conta para iniciar sessão no bloco de notas do Jupyter mais adiante no guia de início rápido. |
    |**Palavra-passe de início de sessão do cluster**| Introduza a palavra-passe de início de sessão do cluster. |
    |**Nome de utilizador de Secure Shell (SSH)**| Introduza o nome de utilizador SSH. O nome de utilizador SSH utilizado neste guia de início rápido é **sshuser**. Por predefinição, esta conta tem a mesma palavra-passe que a conta *Nome de utilizador de início de sessão do cluster*. |
    |**Grupo de recursos**     | Especifique se quer criar um novo grupo de recursos ou utilizar um existente. Um grupo de recursos é um contentor que mantém recursos relacionados para uma solução do Azure. O nome do grupo de recursos utilizado neste guia de início rápido é **myspark20180403rg**. |
    |**Localização**     | Selecione uma localização para o grupo de recursos. O modelo utiliza esta localização para criar o cluster, bem como para o armazenamento predefinido do mesmo. A localização utilizada neste guia de início rápido é **E.U.A. Leste 2**. |

    ![Criar configurações básicas do cluster do Apache Spark no HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-hdinsight-spark-cluster-basic2.png "Criar configurações básicas do cluster do Apache Spark no HDInsight")

    Selecione **Seguinte** para avançar para a página **Armazenamento**.
3. Em **Armazenamento**, forneça os seguintes valores:

   - **Selecionar uma conta de armazenamento**: selecione **Criar novo** e, em seguida, atribua um nome à nova conta de armazenamento. O nome da conta de armazenamento utilizado neste guia de início rápido é **myspark20180403store**.

     ![Criar configurações de armazenamento de cluster do Apache Spark no HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-hdinsight-spark-cluster-storage.png "Criar configurações de armazenamento de cluster do Apache Spark no HDInsight")

     > [!NOTE]  
     > Na captura de ecrã, é mostrada a opção **Selecionar existente**. A ligação alterna entre **Criar novo** e **Selecionar existente**.

     O **Contentor predefinido** tem um nome predefinido.  Pode alterar o nome se quiser.

     Selecione **Seguinte** para avançar para a página **Resumo**. 


3. Em **Resumo**, selecione **Criar**. A criação do cluster demora cerca de 20 minutos. Tem de criar o cluster antes de poder avançar para a sessão seguinte.

Caso se depare com um problema ao criar clusters do HDInsight, estes poderão dever-se ao facto de não ter as permissões certas para o fazer. Para obter mais informações, veja [Access control requirements](../hdinsight-hadoop-create-linux-clusters-portal.md) (Requisitos do controlo de acesso).

## <a name="create-a-jupyter-notebook"></a>Criar um bloco de notas do Jupyter

O Jupyter Notebook é um ambiente de bloco de notas interativo que suporta várias linguagens de programação. O bloco de notas permite-lhe interagir com os seus dados, combinar código com texto markdown e realizar visualizações simples. 

1. Abra o [Portal do Azure](https://portal.azure.com).
2. Selecione **Clusters do HDInsight** e, em seguida, selecione o cluster que criou.

    ![Abrir o cluster do HDInsight no portal do Azure](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. No portal, selecione **Dashboards de cluster** e, em seguida, selecione **Jupyter Notebook**. Se lhe for pedido, introduza as credenciais de início de sessão do cluster.

   ![Abra o Bloco de Notas do Jupyter para executar a consulta interativa do Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Open Jupyter Notebook to run interactive Spark SQL query")

4. Selecione **New (Novo)** > **PySpark** para criar um bloco de notas. 

   ![Crie um Bloco de Notas Jupyter para executar a consulta interativa do Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Create a Jupyter Notebook to run interactive Spark SQL query")

   É criado e aberto um novo bloco de notas com o nome Untitled (Untitled.pynb).


## <a name="run-spark-sql-statements"></a>Executar instruções SQL do Spark

SQL (Structured Query Language) é a linguagem mais comum e mais utilizada para consultar e definir dados. O Spark SQL funciona como uma extensão do Apache Spark para o processamento de dados estruturados e utiliza a sintaxe familiar do SQL Server.

1. Verifique se o kernel está pronto. O kernel está pronto quando vir um círculo hollow junto ao nome do kernel no bloco de notas. O círculo sólido indica que o kernel está ocupado.

    ![Consulta do Hive no HDInsight Spark](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Consulta do Hive no HDInsight Spark")

    Quando inicia o bloco de notas pela primeira vez, o kernel efetua algumas tarefas em segundo plano. Aguarde que o kernel esteja preparado. 
2. Cole o seguinte código numa célula vazia e, em seguida, prima **SHIFT + ENTER** para o executar. O comando lista as tabelas do Hive no cluster:

    ```PySpark
    %%sql
    SHOW TABLES
    ```
    Quando utiliza um Bloco de Notas do Jupyter com o seu cluster do HDInsight Spark, obtém um `sqlContext` predefinido que pode utilizar para executar consultas do Hive com o Spark SQL. `%%sql` indica ao Bloco de Notas do Jupyter que utilize o `sqlContext` predefinido para executar a consulta do Hive. A consulta devolve as primeiras dez linhas de uma tabela do Hive (**hivesampletable**) que vem em todos os clusters do HDInsight por predefinição. São necessários cerca de 30 segundos para receber os resultados. O resultado tem o seguinte aspeto: 

    ![Consulta do Hive no HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Consulta do Hive no HDInsight Spark")

    Sempre que executar uma consulta no Jupyter, o título da janela do browser apresenta o estado **(Ocupado)** juntamente com o título do bloco de notas. Também vê um círculo sólido junto ao texto do **PySpark** no canto superior direito.
    
2. Execute outra consulta para ver os dados no `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```
    
    O ecrã deve atualizar-se e mostrar o resultado da consulta.

    ![Hive query output in HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "(Saída de consulta do Hive no HDInsight Spark)")

2. No menu **File (Ficheiro)** do bloco de notas, selecione **Close and Halt (Fechar e Parar)**. Encerrar o bloco de notas liberta os recursos do cluster.

## <a name="clean-up-resources"></a>Limpar recursos
O HDInsight permite guardar os seus dados no Armazenamento do Azure ou no Armazenamento do Azure Data Lake, pelo que pode eliminar um cluster em segurança quando este não estiver a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados. Se tenciona trabalhar imediatamente no tutorial listado nos [Passos seguintes](#next-steps), convém manter o cluster.

Regresse ao portal do Azure e selecione **Eliminar**.

![Eliminar um cluster do HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "Eliminar um cluster do HDInsight")

Também pode selecionar o nome do grupo de recursos para abrir a página do grupo de recursos e, em seguida, selecionar **Eliminar grupo de recursos**. Ao eliminar o grupo de recursos, está a eliminar o cluster do Spark no HDInsight e a conta de armazenamento predefinida.

## <a name="next-steps"></a>Passos Seguintes 

Neste guia de início rápido, aprendeu a criar um cluster do Apache Spark no HDInsight e a executar uma consulta do Spark SQL básica. Prossiga para o tutorial seguinte para saber como utilizar um cluster do Spark no HDInsight para executar consultas interativas nos dados de exemplo.

> [!div class="nextstepaction"]
>[Executar consultas interativas no Apache Spark](./apache-spark-load-data-run-query.md)

