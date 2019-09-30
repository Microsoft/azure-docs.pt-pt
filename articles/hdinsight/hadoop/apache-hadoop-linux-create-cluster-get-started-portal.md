---
title: 'Início rápido: Apache Hadoop e Apache Hive no HDInsight usando o portal do Azure'
description: Neste guia de início rápido, você usa o portal do Azure para criar um cluster HDInsight Hadoop
keywords: guia de introdução do hadoop, hadoop linux, início rápido do hadoop, introdução do hive, início rápido do hive
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 09/25/2019
ms.openlocfilehash: 5d87cc7fdcd9c8065c2a9886b970b406df0d8fc8
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677918"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Início rápido: Criar Apache Hadoop cluster no Azure HDInsight usando portal do Azure

Neste artigo, você aprenderá a criar [Apache Hadoop](https://hadoop.apache.org/) clusters no hdinsight usando portal do Azure e, em seguida, executar trabalhos de Apache Hive no hdinsight. A maioria das tarefas do Hadoop são tarefas de lote. Cria um cluster, executa algumas tarefas e, em seguida, elimina o cluster. Neste artigo, irá realizar as três tarefas.

Neste guia de início rápido, irá utilizar o portal do Azure para criar um cluster do Hadoop no HDInsight. Também pode criar um cluster através do [modelo Azure Resource Manager](apache-hadoop-linux-tutorial-get-started.md).

Atualmente, o HDInsight inclui [sete tipos diferentes de cluster](../hdinsight-overview.md#cluster-types-in-hdinsight). Cada tipo de cluster suporta um conjunto diferente de componentes. Todos os tipos de cluster suportam o Hive. Para obter uma lista de componentes com suporte no HDInsight, consulte [novidades nas versões de cluster apache Hadoop fornecidas pelo HDInsight?](../hdinsight-component-versioning.md)  

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-apache-hadoop-cluster"></a>Criar um cluster Apache Hadoop

Nesta secção, irá criar um cluster do Hadoop no HDInsight através do portal do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No portal do Azure, vá para **criar uma** > **análise** > de recursos**HDInsight**.

    ![Criar um recurso de cluster HDInsight](./media/apache-hadoop-linux-create-cluster-get-started-portal/create-hdinsight-cluster.png "Criar um recurso de cluster HDInsight")

1. Em **noções básicas**, insira ou selecione os seguintes valores:

    |Propriedade  |Descrição  |
    |---------|---------|
    |Subscription    |  Selecione a sua subscrição do Azure. |
    |Resource group     | Crie um grupo de recursos ou selecione um existente.  Um grupo de recursos é um contentor de componentes do Azure.  Neste caso, o grupo de recursos contém o cluster do HDInsight e a conta de armazenamento do Azure dependente. |
    |Nome do cluster   | Introduza um nome para o cluster do Hadoop. Uma vez que todos os clusters no HDInsight partilham o mesmo espaço de nomes DNS, este nome tem de ser único. O nome pode conter até 59 caracteres, incluindo letras, números e hifens. O primeiro e último carateres do nome não podem ser hífenes. |
    |Location    | Selecione uma localização do Azure onde quer criar o cluster.  Selecione uma localização mais próxima de si para obter um melhor desempenho. |
    |Tipo de cluster| Selecione **Selecionar tipo de cluster**. Em seguida, selecione **Hadoop** como o tipo de cluster.|
    |Version|A versão padrão para o tipo de cluster será especificada. Selecione na lista suspensa se desejar especificar uma versão diferente.|
    |Nome de usuário e senha de logon do cluster    | O nome de início de sessão predefinido é **admin**. A palavra-passe tem de ter no mínimo 10 carateres e tem de conter, pelo menos, um número, uma letra maiúscula e uma letra minúscula, e um caráter não alfanumérico (exceto os carateres ' " `\). Certifique-se de que **não escolhe** uma palavra-passe comum, tal como "Pass@word1".|
    |Nome de utilizador de Secure Shell (SSH) | O nome de utilizador predefinido é **sshuser**.  Pode indicar outro nome de utilizador SSH. |
    |Usar senha de logon do cluster para SSH| Marque essa caixa de seleção para usar a mesma senha para o usuário SSH como aquela que você forneceu para o usuário de logon do cluster.|

    ![Introdução ao fornecimento de valores básicos do cluster no HDInsight com Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-basics-blank.png "Fornecer valores básicos para criar um cluster do HDInsight")

    Selecione o **Next: > De armazenamento >**  para avançar para as configurações de armazenamento.

1. Na guia **armazenamento** , forneça os seguintes valores:

    |Propriedade  |Descrição  |
    |---------|---------|
    |Tipo de armazenamento primário|Use o valor padrão **armazenamento do Azure**.|
    |Método de seleção|Use o valor padrão **selecionar da lista**.|
    |Conta de armazenamento primário|Use a lista suspensa para selecionar uma conta de armazenamento existente ou selecione **criar nova**. Se você criar uma nova conta, o nome deverá ter entre 3 e 24 caracteres de comprimento e só poderá incluir números e letras minúsculas|
    |Contentor|Use o valor preenchido automaticamente.|

    ![Introdução ao fornecimento de valores de armazenamento do cluster no HDInsight com Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-storage-blank.png "Fornecer valores de armazenamento para criar um cluster no HDInsight")

    Selecione a guia **revisar + criar** .

1. Na guia **revisar + criar** , verifique os valores que você selecionou nas etapas anteriores.

    ![Introdução ao resumo do cluster no HDInsight com Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-review-create-hadoop.png "Introdução ao resumo do cluster no HDInsight com Linux")

1. Selecione **Criar**. A criação de um cluster demora cerca de 20 minutos.

Após a criação do cluster, verá a página de descrição geral do cluster no portal do Azure.

![Introdução às definições do cluster no HDInsight com Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png "Propriedades do cluster no HDInsight")

Cada cluster tem uma dependência de [conta do Armazenamento do Azure](../hdinsight-hadoop-use-blob-storage.md) ou de [conta do Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). É designada de conta de armazenamento predefinida. O cluster HDInsight e sua conta de armazenamento padrão devem estar colocalizados na mesma região do Azure. A eliminação dos clusters não elimina a conta de armazenamento.

> [!NOTE]  
> Para outros métodos de criação de cluster e noções básicas sobre as propriedades usadas neste guia de início rápido, consulte [Criar clusters HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="run-apache-hive-queries"></a>Executar Apache Hive consultas

O [Apache Hive](hdinsight-use-hive.md) é o componente mais popular utilizado no HDInsight. Existem várias formas de executar tarefas do Hive no HDInsight. Neste guia de início rápido, você usa a exibição do Ambari Hive no Portal. Para conhecer outros métodos de submissão de tarefas do Hive, consulte [Utilizar o Hive no HDInsight](hdinsight-use-hive.md).

1. Para abrir o Ambari, a partir da captura de ecrã anterior, selecione **Dashboard de Clusters**.  Você também pode navegar até `https://ClusterName.azurehdinsight.net`, onde `ClusterName` é o cluster que você criou na seção anterior.

    ![Introdução ao dashboard de clusters no HDInsight com Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-open-cluster-dashboard.png "Introdução ao dashboard de clusters no HDInsight com Linux")

2. Introduza o nome de utilizador e a palavra-passe do Hadoop que especificou ao criar o cluster. O nome de utilizador predefinido é **admin**.

3. Abra a **Vista do Hive** conforme mostrado na captura de ecrã seguinte:

    ![Selecionando exibição do hive em Ambari](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-select-hive-view.png "Menu do Visualizador do hive do HDInsight")

4. No separador **CONSULTAS**, cole as seguintes declarações HiveQL na folha de cálculo:

    ```sql
    SHOW TABLES;
    ```

    ![Editor de consultas de exibição do hive do HDInsight](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-apache-hive-view1.png "Editor de consultas de exibição do hive do HDInsight")

5. Selecione **Executar**. O separador **RESULTADOS** aparece por baixo do separador **CONSULTA** e apresenta informações sobre a tarefa. 

    Depois que a consulta for concluída, a guia **consulta** exibirá os resultados da operação. Deverá ver uma tabela denomizada **hivesampletable**. Esta tabela do Hive de exemplo inclui todos os clusters do HDInsight.

    ![Resultados da exibição do HDInsight Apache Hive](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-hive-views.png "Resultados da exibição do HDInsight Apache Hive")

6. Repita os passos 4 e 5 para executar a seguinte consulta:

    ```sql
    SELECT * FROM hivesampletable;
    ```

7. Também pode guardar os resultados da consulta. Selecione o botão de menu à direita e especifique se quer transferir os resultados como um ficheiro CSV ou armazená-los na conta de armazenamento associada ao cluster.

    ![Salvar resultado da consulta de Apache Hive](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-hive-view-save-results.png "Salvar resultado da consulta de Apache Hive")

Depois de concluir uma tarefa deo Hive, pode [exportar os resultados para a Base de Dados SQL do Azure ou a base de dados do SQL Server](apache-hadoop-use-sqoop-mac-linux.md). Do mesmo modo, pode [visualizar os resultados com o Excel](apache-hadoop-connect-excel-power-query.md). Para obter mais informações sobre como usar o hive no HDInsight, consulte [usar Apache Hive e HiveQL com Apache Hadoop no HDInsight para analisar um arquivo Apache Log4J de exemplo](hdinsight-use-hive.md).

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o início rápido, talvez você queira excluir o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

> [!NOTE]  
> Se você estiver *imediatamente* no próximo artigo para saber como executar operações de ETL usando o Hadoop no HDInsight, talvez você queira manter o cluster em execução. Isso ocorre porque, no tutorial, você precisa criar um cluster Hadoop novamente. No entanto, se você não estiver passando pelo próximo artigo imediatamente, deverá excluir o cluster agora.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>Para eliminar o cluster e/ou a conta do Storage predefinida

1. Volte ao separador do browser onde tem o portal do Azure. Deverá estar na página de descrição geral do cluster. Se apenas quiser eliminar o cluster, mas quiser manter a conta de armazenamento predefinida, selecione **Eliminar**.

    ![Excluir cluster do Azure HDInsight](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-delete-cluster.png "Excluir cluster HDInsight do Azure")

2. Se quiser eliminar o cluster e a conta de armazenamento predefinida, selecione o nome do grupo de recursos (realçado na captura de ecrã anterior) para abrir a página do grupo de recursos.

3. Selecione **Eliminar grupo de recursos** para eliminar o grupo de recursos, o qual contém o cluster e a conta de armazenamento predefinida. Tenha em atenção que a eliminação do grupo de recursos elimina a conta de armazenamento. Se pretender manter a conta do Storage, opte por eliminar apenas o cluster.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu como criar um cluster HDInsight baseado em Linux usando um modelo do Resource Manager e como executar consultas básicas do hive. No artigo seguinte, irá saber como executar uma operação de ETL (extração, transformação e carregamento) com o Hadoop no HDInsight.

> [!div class="nextstepaction"]
> [Extrair, transformar e carregar dados usando a consulta interativa no HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
