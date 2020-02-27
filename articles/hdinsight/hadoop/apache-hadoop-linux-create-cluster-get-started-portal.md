---
title: 'Quickstart: Apache Hadoop, Apache Hive & Azure HDInsight portal'
description: Neste arranque rápido, você usa o portal Azure para criar um cluster Hadoop HDInsight
keywords: guia de introdução do hadoop, hadoop linux, início rápido do hadoop, introdução do hive, início rápido do hive
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/24/2020
ms.openlocfilehash: 7d2dd65224aad231db9574aa5a1fa4a00b328da8
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623536"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Quickstart: Criar o cluster Apache Hadoop no Azure HDInsight usando o portal Azure

Neste artigo, aprende-se a criar clusters Apache Hadoop no HDInsight utilizando o portal Azure e, em seguida, executa os trabalhos da Apache Hive no HDInsight. A maioria das tarefas do Hadoop são tarefas de lote. Cria um cluster, executa algumas tarefas e, em seguida, elimina o cluster. Neste artigo, irá realizar as três tarefas. Para explicações aprofundadas das configurações disponíveis, consulte [Conjunto de clusters em HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Para obter mais informações sobre a utilização do portal para criar clusters, consulte [Criar clusters no portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

Neste guia de início rápido, irá utilizar o portal do Azure para criar um cluster do Hadoop no HDInsight. Também pode criar um cluster através do [modelo Azure Resource Manager](apache-hadoop-linux-tutorial-get-started.md).

Atualmente, o HDInsight vem com [sete tipos diferentes](../hdinsight-overview.md#cluster-types-in-hdinsight)de cluster . Cada tipo de cluster suporta um conjunto diferente de componentes. Todos os tipos de cluster suportam o Hive. Para obter uma lista de componentes suportados no HDInsight, veja [as novidades nas versões de cluster Apache Hadoop fornecidas pela HDInsight?](../hdinsight-component-versioning.md)  

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-apache-hadoop-cluster"></a>Criar um aglomerado de Hadoop Apache

Nesta secção, irá criar um cluster do Hadoop no HDInsight através do portal do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. A partir do menu superior, selecione **+ Criar um recurso**.

    ![Criar um cluster de recursos HDInsight](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-create-resource.png "Criar um cluster de recursos HDInsight")

1. Selecione **Analytics** > **Azure HDInsight** para ir à página de **cluster Create HDInsight.**

1. A partir do separador **Basics,** forneça as seguintes informações:

    |Propriedade  |Descrição  |
    |---------|---------|
    |Subscrição    |  A partir da lista de lançamentos, selecione a subscrição Azure que é usada para o cluster. |
    |Grupo de recursos     | A partir da lista de drop-down, selecione o seu grupo de recursos existente, ou selecione **Criar novo**.|
    |Nome do cluster   | Introduza um nome globalmente único. O nome pode ser composto por até 59 caracteres, incluindo letras, números e hífenes. O primeiro e último caracteres do nome não podem ser hífenes. |
    |Região    | A partir da lista de abandono, selecione uma região onde o cluster é criado.  Selecione uma localização mais próxima de si para obter um melhor desempenho. |
    |Tipo de cluster| Selecione o tipo de **cluster .** Em seguida, selecione **Hadoop** como o tipo de cluster.|
    |Versão|A partir da lista de lançamentos, selecione uma **versão**. Utilize a versão predefinida se não souber o que escolher.|
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

    Cada cluster tem uma dependência de [conta do Armazenamento do Azure](../hdinsight-hadoop-use-blob-storage.md) ou de [conta do Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). É referida como a conta de armazenamento padrão. O cluster HDInsight e a sua conta de armazenamento predefinido devem ser colocalizados na mesma região de Azure. Eliminar aglomerados não apaga a conta de armazenamento.

    Selecione o **separador Review + criar.**

1. A partir do separador **Review + criar,** verifique os valores selecionados nos passos anteriores.

    ![HDInsight Linux começar o resumo do cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-review-create-hadoop.png "HDInsight Linux começar o resumo do cluster")

1. Selecione **Criar**. A criação de um cluster demora cerca de 20 minutos.

    Após a criação do cluster, verá a página de descrição geral do cluster no portal do Azure.

    ![HDInsight Linux inicia miná-lo configurações de cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png "Propriedades do cluster HDInsight")

## <a name="run-apache-hive-queries"></a>Executar consultas da Colmeia Apache

O [Apache Hive](hdinsight-use-hive.md) é o componente mais popular utilizado no HDInsight. Existem várias formas de executar tarefas do Hive no HDInsight. Neste arranque rápido, você usa a vista Ambari Hive do portal. Para conhecer outros métodos de submissão de tarefas do Hive, consulte [Utilizar o Hive no HDInsight](hdinsight-use-hive.md).

> [!NOTE]
> Apache Hive View não está disponível em HDInsight 4.0.

1. Para abrir o Ambari, a partir da captura de ecrã anterior, selecione **Dashboard de Clusters**.  Também pode navegar para `https://ClusterName.azurehdinsight.net` onde `ClusterName` é o cluster que criou na secção anterior.

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

Depois de ter concluído um trabalho na Hive, pode exportar os resultados para a Base de [Dados Azure SQL ou para](apache-hadoop-use-sqoop-mac-linux.md)a base de dados Do Servidor SQL, pode também [visualizar os resultados utilizando](apache-hadoop-connect-excel-power-query.md)o Excel . Para obter mais informações sobre a utilização da Hive no HDInsight, consulte [Use Apache Hive e HiveQL com Apache Hadoop em HDInsight para analisar uma amostra de ficheiro Log4j Apache](hdinsight-use-hive.md).

## <a name="clean-up-resources"></a>Limpar recursos

Depois de completar o arranque rápido, poderá querer eliminar o cluster. Com o HDInsight, os seus dados são armazenados no Armazenamento Azure, para que possa eliminar com segurança um cluster quando não estiver a ser utilizado. Também é cobrado por um cluster HDInsight, mesmo quando não está a ser utilizado. Uma vez que as taxas para o cluster são muitas vezes mais do que as taxas de armazenamento, faz sentido económico apagar clusters quando não estão em uso.

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
