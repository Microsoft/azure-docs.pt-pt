---
title: 'Quickstart: Apache Hadoop, Apache Hive & Azure HDInsight portal'
description: Neste arranque rápido, você usa o portal Azure para criar um cluster Hadoop HDInsight
keywords: guia de introdução do hadoop, hadoop linux, início rápido do hadoop, introdução do hive, início rápido do hive
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 09/25/2019
ms.openlocfilehash: 35dc4938760ca83a6781d5791c746ee2f74310ab
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031596"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Quickstart: Criar o cluster Apache Hadoop no Azure HDInsight usando o portal Azure

Neste artigo, aprende-se a criar clusters [Apache Hadoop](https://hadoop.apache.org/) no HDInsight utilizando o portal Azure e, em seguida, executa os trabalhos da Apache Hive no HDInsight. A maioria das tarefas do Hadoop são tarefas de lote. Cria um cluster, executa algumas tarefas e, em seguida, elimina o cluster. Neste artigo, irá realizar as três tarefas.

Neste guia de início rápido, irá utilizar o portal do Azure para criar um cluster do Hadoop no HDInsight. Também pode criar um cluster através do [modelo Azure Resource Manager](apache-hadoop-linux-tutorial-get-started.md).

Atualmente, o HDInsight inclui [sete tipos diferentes de cluster](../hdinsight-overview.md#cluster-types-in-hdinsight). Cada tipo de cluster suporta um conjunto diferente de componentes. Todos os tipos de cluster suportam o Hive. Para obter uma lista de componentes suportados no HDInsight, veja [as novidades nas versões de cluster Apache Hadoop fornecidas pela HDInsight?](../hdinsight-component-versioning.md)  

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-apache-hadoop-cluster"></a>Criar um cluster Apache Hadoop

Nesta secção, irá criar um cluster do Hadoop no HDInsight através do portal do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. A partir do portal Azure, vá criar **um recurso** > **Analytics** > **HDInsight**.

    ![Criar um cluster de recursos HDInsight](./media/apache-hadoop-linux-create-cluster-get-started-portal/create-hdinsight-cluster.png "Criar um cluster de recursos HDInsight")

1. Em **Basics,** insira ou selecione os seguintes valores:

    |Propriedade  |Descrição  |
    |---------|---------|
    |Subscrição    |  Selecione a sua subscrição do Azure. |
    |Grupo de recursos     | Crie um grupo de recursos ou selecione um existente.  Um grupo de recursos é um contentor de componentes do Azure.  Neste caso, o grupo de recursos contém o cluster do HDInsight e a conta de armazenamento do Azure dependente. |
    |Nome do cluster   | Introduza um nome para o cluster do Hadoop. Uma vez que todos os clusters no HDInsight partilham o mesmo espaço de nomes DNS, este nome tem de ser único. O nome pode ser composto por até 59 caracteres, incluindo letras, números e hífenes. O primeiro e o último carateres do nome não podem ser hífenes. |
    |Localização    | Selecione uma localização do Azure onde pretende criar o cluster.  Selecione uma localização mais próxima de si para obter um melhor desempenho. |
    |Tipo de cluster| Selecione o tipo de **cluster .** Em seguida, selecione **Hadoop** como o tipo de cluster.|
    |Versão|Será especificada a versão predefinida para o tipo de cluster. Selecione na lista de drop-down se pretender especificar uma versão diferente.|
    |Nome de utilizador de login de cluster e senha    | O nome de login predefinido é **administrador**. A palavra-passe deve ter pelo menos 10 caracteres de comprimento e deve conter pelo menos um dígito, uma maiúscula e uma letra minúscula, um caracteres não alfanuméricos (exceto os caracteres " \). Certifique-se de que **não escolhe** uma palavra-passe comum, tal como "Pass@word1".|
    |Nome de utilizador de Secure Shell (SSH) | O nome de utilizador predefinido é **sshuser**.  Pode indicar outro nome de utilizador SSH. |
    |Utilize senha de login de cluster para SSH| Selecione esta caixa de verificação para utilizar a mesma palavra-passe para o utilizador SSH como a que forneceu para o utilizador de login do cluster.|

    ![HDInsight Linux começar a fornecer valores básicos cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-basics-blank.png "Fornecer valores básicos para a criação de um cluster HDInsight")

    Selecione o **Seguinte: Armazenamento >>** para avançar para as definições de armazenamento.

1. A partir do separador **Armazenamento,** forneça os seguintes valores:

    |Propriedade  |Descrição  |
    |---------|---------|
    |Tipo de armazenamento primário|Utilize o valor predefinido **do Armazenamento Azure**.|
    |Método de seleção|Utilize o valor predefinido **Selecione a partir da lista**.|
    |Conta de armazenamento primária|Utilize a lista de drop-down para selecionar uma conta de armazenamento existente, ou selecione **Criar nova**. Se criar uma nova conta, o nome deve ter entre 3 e 24 caracteres de comprimento, e pode incluir apenas números e letras minúsculas|
    |Contentor|Use o valor autopovoado.|

    ![HDInsight Linux começar a fornecer valores de armazenamento de cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-storage.png "Fornecer valores de armazenamento para a criação de um cluster HDInsight")

    Selecione o **separador Review + criar.**

1. A partir do separador **Review + criar,** verifique os valores selecionados nos passos anteriores.

    ![HDInsight Linux começar o resumo do cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-review-create-hadoop.png "HDInsight Linux começar o resumo do cluster")

1. Selecione **Criar**. A criação de um cluster demora cerca de 20 minutos.

Após a criação do cluster, verá a página de descrição geral do cluster no portal do Azure.

![HDInsight Linux inicia miná-lo configurações de cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png "Propriedades do cluster HDInsight")

Cada cluster tem uma dependência de [conta do Armazenamento do Azure](../hdinsight-hadoop-use-blob-storage.md) ou de [conta do Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). É designada de conta de armazenamento predefinida. O cluster HDInsight e a sua conta de armazenamento predefinido devem ser colocalizados na mesma região de Azure. A eliminação dos clusters não elimina a conta de armazenamento.

> [!NOTE]  
> Para outros métodos de criação de clusters e compreensão das propriedades utilizadas neste arranque rápido, consulte [Create HDInsight clusters](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="run-apache-hive-queries"></a>Executar consultas da Colmeia Apache

O [Apache Hive](hdinsight-use-hive.md) é o componente mais popular utilizado no HDInsight. Existem várias formas de executar tarefas do Hive no HDInsight. Neste arranque rápido, você usa a vista Ambari Hive do portal. Para conhecer outros métodos de submissão de tarefas do Hive, consulte [Utilizar o Hive no HDInsight](hdinsight-use-hive.md).

1. Para abrir o Ambari, a partir da captura de ecrã anterior, selecione **Dashboard de Clusters**.  Também pode navegar para `https://ClusterName.azurehdinsight.net`, onde `ClusterName` é o cluster que criou na secção anterior.

    ![HDInsight Linux começar painel de cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-open-cluster-dashboard.png "HDInsight Linux começar painel de cluster")

2. Introduza o nome de utilizador e a palavra-passe do Hadoop que especificou ao criar o cluster. O nome de utilizador predefinido é **admin**.

3. Abra a **Vista do Hive** conforme mostrado na captura de ecrã seguinte:

    ![Selecionando vista para a colmeia de Ambari](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-select-hive-view.png "Menu do Espectador da Colmeia HDInsight")

4. No separador **CONSULTAS**, cole as seguintes declarações HiveQL na folha de cálculo:

    ```sql
    SHOW TABLES;
    ```

    ![Editor de consulta de vista de colmeia HDInsight](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-apache-hive-view1.png "Editor de consulta de vista de colmeia HDInsight")

5. Selecione **Executar**. O separador **RESULTADOS** aparece por baixo do separador **CONSULTA** e apresenta informações sobre a tarefa. 

    Uma vez terminada a consulta, o **separador QUERY** apresenta os resultados da operação. Deverá ver uma tabela denomizada **hivesampletable**. Esta tabela do Hive de exemplo inclui todos os clusters do HDInsight.

    ![HDInsight Apache Hive visualiza minúce resultados](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-hive-views.png "HDInsight Apache Hive visualiza minúce resultados")

6. Repita os passos 4 e 5 para executar a seguinte consulta:

    ```sql
    SELECT * FROM hivesampletable;
    ```

7. Também pode guardar os resultados da consulta. Selecione o botão de menu à direita e especifique se quer transferir os resultados como um ficheiro CSV ou armazená-los na conta de armazenamento associada ao cluster.

    ![Salvar resultado da consulta da Colmeia Apache](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-hive-view-save-results.png "Salvar resultado da consulta da Colmeia Apache")

Depois de concluir uma tarefa deo Hive, pode [exportar os resultados para a Base de Dados SQL do Azure ou a base de dados do SQL Server](apache-hadoop-use-sqoop-mac-linux.md). Do mesmo modo, pode [visualizar os resultados com o Excel](apache-hadoop-connect-excel-power-query.md). Para obter mais informações sobre a utilização da Hive no HDInsight, consulte [Use Apache Hive e HiveQL com Apache Hadoop em HDInsight para analisar uma amostra de ficheiro Log4j Apache](hdinsight-use-hive.md).

## <a name="clean-up-resources"></a>Limpar recursos

Depois de completar o arranque rápido, poderá querer eliminar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

> [!NOTE]  
> Se estiver *imediatamente* a proceder ao próximo artigo para aprender a executar operações da ETL utilizando Hadoop no HDInsight, pode querer manter o cluster em funcionamento. Isto porque no tutorial tens de criar um aglomerado hadoop outra vez. No entanto, se não estiver a analisar o próximo artigo imediatamente, tem de apagar o cluster agora.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>Para eliminar o cluster e/ou a conta do Storage predefinida

1. Volte ao separador do browser onde tem o portal do Azure. Deverá estar na página de descrição geral do cluster. Se apenas quiser eliminar o cluster, mas quiser manter a conta de armazenamento predefinida, selecione **Eliminar**.

    ![Azure HDInsight apaga cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-delete-cluster.png "Eliminar cluster Azure HDInsight")

2. Se quiser eliminar o cluster e a conta de armazenamento predefinida, selecione o nome do grupo de recursos (realçado na captura de ecrã anterior) para abrir a página do grupo de recursos.

3. Selecione **Eliminar grupo de recursos** para eliminar o grupo de recursos, o qual contém o cluster e a conta de armazenamento predefinida. Tenha em atenção que a eliminação do grupo de recursos elimina a conta de armazenamento. Se pretender manter a conta do Storage, opte por eliminar apenas o cluster.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar um cluster HDInsight baseado em Linux usando um modelo de Gestor de Recursos, e como realizar consultas básicas da Hive. No artigo seguinte, vai aprender a realizar uma operação de ETL (extração, transformação e carregamento) com o Hadoop no HDInsight.

> [!div class="nextstepaction"]
> [Extrair, transformar e carregar dados usando consulta interativa no HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
