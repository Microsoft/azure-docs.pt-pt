---
title: Usar exibições do Apache Ambari para trabalhar com o hive no HDInsight (Apache Hadoop) – Azure
description: Saiba como usar o modo de exibição do hive do seu navegador da Web para enviar consultas do hive. A exibição do hive faz parte da interface do usuário da Web do amAmbari fornecida com o cluster HDInsight baseado em Linux.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: hrasheed
ms.openlocfilehash: 3ab2bf0334b58f3a5ac8ad4abacfcc45e0366240
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70917754"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Usar a exibição do Ambari hive do Apache com o Apache Hadoop no HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Saiba como executar consultas do hive usando o modo de exibição do Ambari hive do Apache. A exibição do hive permite que você crie, otimize e execute consultas do hive a partir do seu navegador da Web.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Hadoop no HDInsight. Consulte [introdução ao HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).
* Um navegador da Web

## <a name="run-a-hive-query"></a>Executar uma consulta do Hive

1. No [portal do Azure](https://portal.azure.com/), selecione o cluster.  Consulte [listar e mostrar clusters](../hdinsight-administer-use-portal-linux.md#showClusters) para obter instruções. O cluster é aberto em uma folha de novo Portal.

2. Em **painéis do cluster**, selecione **exibições do Ambari**. Quando for solicitado a autenticar, use o nome da conta `admin`de logon do cluster (padrão) e a senha que você forneceu ao criar o cluster.

3. Na lista de exibições, selecione __exibição do hive__.

    ![A exibição do hive selecionada](./media/apache-hadoop-use-hive-ambari-view/select-apache-hive-view.png)

    A página de exibição do hive é semelhante à imagem a seguir:

    ![Imagem da planilha de consulta para a exibição do hive](./media/apache-hadoop-use-hive-ambari-view/ambari-worksheet-view.png)

4. Na guia __consulta__ , Cole as seguintes instruções HiveQL na planilha:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS loglevel, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' 
        GROUP BY t4;
    ```

    Essas instruções executam as seguintes ações:

   * `DROP TABLE`: Exclui a tabela e o arquivo de dados, caso a tabela já exista.

   * `CREATE EXTERNAL TABLE`: Cria uma nova tabela "externa" no hive.
     As tabelas externas armazenam apenas a definição de tabela no hive. Os dados são deixados no local original.

   * `ROW FORMAT`: Mostra como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.

   * `STORED AS TEXTFILE LOCATION`: Mostra onde os dados são armazenados e que são armazenados como texto.

   * `SELECT`: Seleciona uma contagem de todas as linhas em que a coluna T4 contém o valor [ERROR].

   > [!IMPORTANT]  
   > Deixe a seleção de __banco de dados__ em __padrão__. Os exemplos neste documento usam o banco de dados padrão incluído com o HDInsight.

5. Para iniciar a consulta, selecione **executar** abaixo da planilha. O botão gira laranja e o texto é alterado para **parar**.

6. Depois que a consulta for concluída, a guia **resultados** exibirá os resultados da operação. O texto a seguir é o resultado da consulta:

        loglevel       count
        [ERROR]        3

    Você pode usar a guia **log** para exibir as informações de log criadas pelo trabalho.

   > [!TIP]  
   > Baixe ou salve os resultados na caixa de diálogo suspensa **ações** na guia **resultados** .

### <a name="visual-explain"></a>Explicação Visual

Para exibir uma visualização do plano de consulta, selecione a guia **explicação Visual** abaixo da planilha.

A exibição de **explicação Visual** da consulta pode ser útil para entender o fluxo de consultas complexas.

### <a name="tez-ui"></a>Interface do usuário do amTez

Para exibir a interface do usuário do amTez para a consulta, selecione a guia **interface do usuário do tez** abaixo da planilha.

> [!IMPORTANT]  
> Tez não é usado para resolver todas as consultas. Você pode resolver muitas consultas sem usar o tez. 

## <a name="view-job-history"></a>Ver histórico de tarefas

A guia __trabalhos__ exibe um histórico de consultas de Hive.

![Imagem do histórico de trabalhos](./media/apache-hadoop-use-hive-ambari-view/apache-hive-job-history.png)

## <a name="database-tables"></a>Tabelas de banco de dados

Você pode usar a guia __tabelas__ para trabalhar com tabelas em um banco de dados do hive.

![Imagem da guia tabelas](./media/apache-hadoop-use-hive-ambari-view/hdinsight-tables-tab.png)

## <a name="saved-queries"></a>Consultas guardadas

Na guia **consulta** , você pode, opcionalmente, salvar consultas. Depois de salvar uma consulta, você pode reutilizá-la na guia __consultas salvas__ .

![Imagem da guia consultas salvas](./media/apache-hadoop-use-hive-ambari-view/ambari-saved-queries.png)

> [!TIP]  
> As consultas salvas são armazenadas no armazenamento de cluster padrão. Você pode encontrar as consultas salvas no caminho `/user/<username>/hive/scripts`. Eles são armazenados como arquivos de texto `.hql` sem formatação.
>
> Se você excluir o cluster, mas mantiver o armazenamento, poderá usar um utilitário como [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) ou data Lake Storage Explorer (no [portal do Azure](https://portal.azure.com)) para recuperar as consultas.

## <a name="user-defined-functions"></a>Funções definidas pelo utilizador

Você pode estender o hive por meio de UDF (funções definidas pelo usuário). Use um UDF para implementar a funcionalidade ou lógica que não é modelada facilmente em HiveQL.

Declare e salve um conjunto de UDFs usando a guia **UDF** na parte superior da exibição do hive. Essas UDFs podem ser usadas com o **Editor de consultas**.

![Imagem da guia UDF](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Depois de adicionar um UDF à exibição do hive, um botão **Inserir UDFs** aparece na parte inferior do editor de **consultas**. A seleção dessa entrada exibe uma lista suspensa dos UDFs definidos na exibição do hive. A seleção de um UDF adiciona instruções HiveQL à sua consulta para habilitar o UDF.

Por exemplo, se você tiver definido um UDF com as seguintes propriedades:

* Nome do recurso: myudfs

* Caminho do recurso:/myudfs.jar

* Nome do UDF: myawesomeudf

* Nome da classe UDF: com. myudfs. awesome

O uso do botão **Inserir UDFs** exibe uma entrada chamada **myudfs**, com outra lista suspensa para cada UDF definido para esse recurso. Nesse caso, é **myawesomeudf**. A seleção dessa entrada adiciona o seguinte ao início da consulta:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Em seguida, você pode usar o UDF em sua consulta. Por exemplo, `SELECT myawesomeudf(name) FROM people;`.

Para obter mais informações sobre como usar UDFs com o hive no HDInsight, consulte os seguintes artigos:

* [Usando o Python com o Apache Hive e o Apache Pig no HDInsight](python-udf-hdinsight.md)
* [Como adicionar um UDF de Apache Hive personalizado ao HDInsight](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Configurações do hive

Você pode alterar várias configurações do hive, como alterar o mecanismo de execução do hive de tez (o padrão) para MapReduce.

## <a id="nextsteps"></a>Passos seguintes

Para obter informações gerais sobre o hive no HDInsight:

* [Usar Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras maneiras que você pode trabalhar com o Hadoop no HDInsight:

* [Usar o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)
