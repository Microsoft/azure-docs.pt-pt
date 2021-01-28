---
title: 'Quickstart: Apache Hadoop, Apache Hive & portal Azure HDInsight'
description: Neste arranque rápido, você usa o portal Azure para criar um cluster HDInsight Hadoop
keywords: guia de introdução do hadoop, hadoop linux, início rápido do hadoop, introdução do hive, início rápido do hive
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/24/2020
ms.openlocfilehash: cd3e997bf2fda5f586fdb1ee4dcedff1adbf41f3
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946592"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Quickstart: Criar cluster Apache Hadoop em Azure HDInsight usando o portal Azure

Neste artigo, você aprende a criar clusters Apache Hadoop em HDInsight usando o portal Azure, e depois executar trabalhos de Hive Apache em HDInsight. A maioria das tarefas do Hadoop são tarefas de lote. Cria um cluster, executa algumas tarefas e, em seguida, elimina o cluster. Neste artigo, vai realizar as três tarefas. Para obter explicações aprofundadas sobre as configurações disponíveis, consulte [Configurar clusters em HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Para obter mais informações sobre a utilização do portal para criar clusters, consulte [Criar clusters no portal.](../hdinsight-hadoop-create-linux-clusters-portal.md)

Neste guia de início rápido, irá utilizar o portal do Azure para criar um cluster do Hadoop no HDInsight. Também pode criar um cluster através do [modelo Azure Resource Manager](apache-hadoop-linux-tutorial-get-started.md).

Atualmente, o HDInsight vem com [sete tipos diferentes de cluster.](../hdinsight-overview.md#cluster-types-in-hdinsight) Cada tipo de cluster suporta um conjunto diferente de componentes. Todos os tipos de cluster suportam o Hive. Para obter uma lista de componentes suportados em HDInsight, veja [quais as novidades nas versões de cluster Apache Hadoop fornecidas pela HDInsight?](../hdinsight-component-versioning.md)  

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-apache-hadoop-cluster"></a>Criar um aglomerado Apache Hadoop

Nesta secção, irá criar um cluster do Hadoop no HDInsight através do portal do Azure.

1. Inscreva-se no [portal Azure](https://portal.azure.com).

1. A partir do menu superior, selecione **+ Crie um recurso.**

    ![Criar um cluster hdinsight de recurso](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-create-resource.png "Criar um cluster hdinsight de recurso")

1. Selecione **Analytics**  >  **Azure HDInsight** para ir à página de **cluster Create HDInsight.**

1. A partir do separador **Básicos,** forneça as seguintes informações:

    |Propriedade  |Descrição  |
    |---------|---------|
    |Subscrição    |  A partir da lista de drop-down, selecione a subscrição Azure que é usada para o cluster. |
    |Grupo de recursos     | A partir da lista de drop-down, selecione o seu grupo de recursos existente ou selecione **Criar novo**.|
    |Nome do cluster   | Introduza um nome globalmente exclusivo. O nome pode consistir em até 59 caracteres, incluindo letras, números e hífenes. Os primeiros e últimos caracteres do nome não podem ser hífenes. |
    |Region    | A partir da lista de drop-down, selecione uma região onde o cluster é criado.  Selecione uma localização mais próxima de si para obter um melhor desempenho. |
    |Tipo de cluster| **Selecione Selecionar o tipo de cluster**. Em seguida, selecione **Hadoop** como o tipo de cluster.|
    |Versão|A partir da lista de drop-down, selecione uma **versão**. Utilize a versão padrão se não souber o que escolher.|
    |Nome de utilizador e palavra-passe de início de sessão do cluster    | O nome de login predefinido é **administrador**. A palavra-passe deve ter pelo menos 10 caracteres de comprimento e deve conter pelo menos um dígito, uma maiúscula, e uma letra minúscula, um carácter não alfanumérico (exceto os caracteres ' \) . Certifique-se de que **não escolhe** uma palavra-passe comum, tal como "Pass@word1".|
    |Nome de utilizador de Secure Shell (SSH) | O nome de utilizador predefinido é **sshuser**.  Pode indicar outro nome de utilizador SSH. |
    |Use a palavra-passe de login do cluster para SSH| Selecione esta caixa de verificação para utilizar a mesma palavra-passe para o utilizador SSH que forneceu para o utilizador de login do cluster.|

    ![HDInsight Linux começar a fornecer valores básicos de cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-basics.png "Fornecer valores básicos para criar um cluster HDInsight")

    Selecione o **Seguinte: >>de armazenamento** para avançar para as definições de armazenamento.

1. A partir do separador **Armazenamento,** forneça os seguintes valores:

    |Propriedade  |Descrição  |
    |---------|---------|
    |Tipo de armazenamento primário|Utilize o valor predefinido **Azure Storage**.|
    |Método de seleção|Utilizar o valor predefinido **Selecione da lista**.|
    |Conta de armazenamento primária|Utilize a lista de drop-down para selecionar uma conta de armazenamento existente ou selecione **Criar novo**. Se criar uma nova conta, o nome deve ter entre 3 e 24 caracteres de comprimento, e pode incluir apenas números e letras minúsculas|
    |Contentor|Utilize o valor autopovoado.|

    ![HDInsight Linux começa a fornecer valores de armazenamento de cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-storage.png "Fornecer valores de armazenamento para criar um cluster HDInsight")

    Cada cluster tem uma [conta de Armazenamento Azure,](../hdinsight-hadoop-use-blob-storage.md)um [Azure Data Lake Gen1,](../hdinsight-hadoop-use-data-lake-storage-gen1.md)ou uma [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  dependência. É referida como a conta de armazenamento predefinida. O cluster HDInsight e a sua conta de armazenamento predefinida devem ser indicados na mesma região de Azure. Excluir agrupamentos não apaga a conta de armazenamento.

    Selecione o **separador 'Rever +' para criar.**

1. A partir do separador **'Rever +' criar,** verificar os valores selecionados nos passos anteriores.

    ![HDInsight Linux inicia resumo do cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-review-create-hadoop.png "HDInsight Linux inicia resumo do cluster")

1. Selecione **Criar**. A criação de um cluster demora cerca de 20 minutos.

    Após a criação do cluster, verá a página de descrição geral do cluster no portal do Azure.

    ![Introdução às definições do cluster no HDInsight com Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png "Propriedades de cluster HDInsight")

## <a name="run-apache-hive-queries"></a>Executar consultas de Colmeia Apache

O [Apache Hive](hdinsight-use-hive.md) é o componente mais popular utilizado no HDInsight. Existem várias formas de executar tarefas do Hive no HDInsight. Neste arranque rápido, você usa a vista Ambari Hive a partir do portal. Para conhecer outros métodos de submissão de tarefas do Hive, consulte [Utilizar o Hive no HDInsight](hdinsight-use-hive.md).

> [!NOTE]
> Apache Hive View não está disponível em HDInsight 4.0.

1. Para abrir o Ambari, a partir da captura de ecrã anterior, selecione **Dashboard de Clusters**.  Também pode navegar até  `https://ClusterName.azurehdinsight.net` onde `ClusterName` está o cluster que criou na secção anterior.

    ![HDInsight Linux começa painel de cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-open-cluster-dashboard.png "HDInsight Linux começa painel de cluster")

2. Introduza o nome de utilizador e a palavra-passe do Hadoop que especificou ao criar o cluster. O nome de utilizador predefinido é **admin**.

3. Abra a **Vista do Hive** conforme mostrado na captura de ecrã seguinte:

    ![Selecionando vista de colmeia de Ambari](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-select-hive-view.png "Menu hdInsight Hive Viewer")

4. No separador **CONSULTAS**, cole as seguintes declarações HiveQL na folha de cálculo:

    ```sql
    SHOW TABLES;
    ```

    ![HdInsight Hive View Editor de Consulta](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-apache-hive-view1.png "HdInsight Hive View Editor de Consulta")

5. Selecione **Execute** (Executar). O separador **RESULTADOS** aparece por baixo do separador **CONSULTA** e apresenta informações sobre a tarefa. 

    Uma vez terminada a consulta, o separador **QUERY** apresenta os resultados da operação. Deverá ver uma tabela denomizada **hivesampletable**. Esta tabela do Hive de exemplo inclui todos os clusters do HDInsight.

    ![HDInsight Apache Hive ver resultados](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-hive-views.png "HDInsight Apache Hive ver resultados")

6. Repita os passos 4 e 5 para executar a seguinte consulta:

    ```sql
    SELECT * FROM hivesampletable;
    ```

7. Também pode guardar os resultados da consulta. Selecione o botão de menu à direita e especifique se pretende transferir os resultados como um ficheiro CSV ou armazená-los na conta de armazenamento associada ao cluster.

    ![Salve o resultado da consulta da Colmeia Apache](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-hive-view-save-results.png "Salve o resultado da consulta da Colmeia Apache")

Depois de ter concluído um trabalho na Hive, pode [exportar os resultados para a Base de Dados Azure SQL ou base de dados do SQL Server,](apache-hadoop-use-sqoop-mac-linux.md)pode também [visualizar os resultados utilizando o Excel](apache-hadoop-connect-excel-power-query.md). Para obter mais informações sobre a utilização da Colmeia em HDInsight, consulte [Use Apache Hive e HiveQL com Apache Hadoop em HDInsight para analisar uma amostra do ficheiro Log4j apache.](hdinsight-use-hive.md)

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de completar o arranque rápido, é possível que queira eliminar o cluster. Com o HDInsight, os seus dados são armazenados no Azure Storage, para que possa eliminar com segurança um cluster quando este não estiver a ser utilizado. Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Uma vez que as taxas para o cluster são muitas vezes mais do que os encargos de armazenamento, faz sentido económico apagar clusters quando não estão a ser utilizados.

> [!NOTE]  
> Se estiver *imediatamente* a avançar para o próximo artigo para aprender a executar operações ETL usando Hadoop em HDInsight, é melhor manter o cluster em funcionamento. Isto porque no tutorial você tem que criar um cluster Hadoop novamente. No entanto, se não estiver a analisar o próximo artigo imediatamente, deve apagar o cluster agora.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>Para eliminar o cluster e/ou a conta do Storage predefinida

1. Volte ao separador do browser onde tem o portal do Azure. Deverá estar na página de descrição geral do cluster. Se apenas quiser eliminar o cluster, mas quiser manter a conta de armazenamento predefinida, selecione **Eliminar**.

    ![Azure HDInsight eliminar cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-delete-cluster.png "Excluir cluster Azure HDInsight")

2. Se quiser eliminar o cluster e a conta de armazenamento predefinida, selecione o nome do grupo de recursos (realçado na captura de ecrã anterior) para abrir a página do grupo de recursos.

3. Selecione **Eliminar grupo de recursos** para eliminar o grupo de recursos que contém o cluster e a conta de armazenamento predefinida. Tenha em atenção que a eliminação do grupo de recursos elimina a conta de armazenamento. Se pretender manter a conta do Storage, opte por eliminar apenas o cluster.

## <a name="next-steps"></a>Próximos passos

Neste quickstart, você aprendeu a criar um cluster HDInsight baseado em Linux usando um modelo de Gestor de Recursos, e como executar consultas básicas de Hive. No artigo seguinte, irá saber como executar uma operação de ETL (extração, transformação e carregamento) com o Hadoop no HDInsight.

> [!div class="nextstepaction"]
> [Extrair, transformar e carregar dados usando consulta interativa em HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
