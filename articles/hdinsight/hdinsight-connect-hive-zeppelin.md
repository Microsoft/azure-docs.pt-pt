---
title: 'Início rápido: Executar consultas do Apache Hive no HDInsight do Azure - Apache Zeppelin'
description: Saiba como utilizar o Apache Zeppelin para executar consultas do Apache Hive.
keywords: o hdinsight, hadoop, hive, a consulta interativa, o LLAP
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 05/06/2019
ms.author: hrasheed
ms.openlocfilehash: 2aee59f8b040122bf10377cbb6763d771eaa9f02
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65511136"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Início rápido: Executar consultas do Apache Hive no HDInsight do Azure com o Apache Zeppelin

Neste início rápido, irá aprender a utilizar o Apache Zeppelin para executar [Apache Hive](https://hive.apache.org/) consultas no Azure HDInsight. Os clusters do Interactive Query do HDInsight incluem [Apache Zeppelin](https://zeppelin.apache.org/) consultas do Hive de blocos de notas que pode utilizar para executar interativo.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Um cluster do Interactive Query do HDInsight. Ver [criar um cluster](./hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) para criar um cluster do HDInsight.  Certifique-se de escolher a **Interactive Query** tipo de cluster.

## <a name="create-an-apache-zeppelin-note"></a>Criar uma nota de Zeppelin Apache

1. Substitua `CLUSTERNAME` com o nome do seu cluster no seguinte URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin`. Em seguida, introduza o URL num navegador da web.

2. Introduza o nome de utilizador de início de sessão de cluster e a palavra-passe. Na página Zeppelin, pode criar uma nova nota ou abra notas existentes. **HiveSample** contém alguns exemplos de consultas do Hive.  

    ![Zeppelin Interactive Query do HDInsight](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. Selecione **criar nova nota**.

4. Partir do **criar nova nota** caixa de diálogo, escreva ou selecione os seguintes valores:

    - Nome da observação: Introduza um nome para a nota.
    - Interpretador padrão: Selecione **jdbc** na lista pendente.

5. Selecione **criar nota**.

6. Introduza a seguinte consulta do Hive na seção de código e, em seguida, prima **Shift + Enter**:

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![Consulta de execuções do Interactive Query do HDInsight zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    O **%jdbc(hive)** instrução na primeira linha informa o bloco de notas para utilizar o interpretador de JDBC do Hive.

    A consulta deverá devolver uma tabela do Hive chamada **hivesampletable**.

    Seguem-se duas consultas do Hive adicionais que podem ser executadas para **hivesampletable**:

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    Comparando com o Hive tradicional, os resultados da consulta voltar tem mais rapidamente.

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir o início rápido, pode querer eliminar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

Para eliminar um cluster, veja [eliminar um cluster do HDInsight com o seu browser, o PowerShell ou a CLI do Azure](./hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu como utilizar o Apache Zeppelin para executar consultas do Apache Hive no HDInsight do Azure. Para saber mais sobre consultas do Hive, o próximo artigo mostrarei como executar consultas com o Visual Studio.

> [!div class="nextstepaction"]
> [Ligar ao Azure HDInsight e executar consultas do Apache Hive com o Data Lake Tools para Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md)

## <a name="see-also"></a>Consulte também

* [Visualize os dados do Apache Hive com o Microsoft Power BI no Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md).
* [Visualizar dados de Interactive Query Apache Hive com o Power BI no Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Ligar o Excel ao HDInsight com o controlador Microsoft Hive ODBC](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Ligar o Excel para Apache Hadoop com o Power Query](hadoop/apache-hadoop-connect-excel-power-query.md).
* [Utilize a ferramenta do Azure HDInsight para Visual Studio Code](hdinsight-for-vscode.md).
* [Carregar dados para o HDInsight](./hdinsight-upload-data.md).
