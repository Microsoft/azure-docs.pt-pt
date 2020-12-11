---
title: Use Apache Beeline com Apache Hive - Azure HDInsight
description: Aprenda a usar o cliente Beeline para executar consultas de Colmeia com Hadoop em HDInsight. Beeline é uma utilidade para trabalhar com a HiveServer2 em vez de JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 10/28/2020
ms.custom: contperf-fy21q1, contperf-fy21q2
ms.openlocfilehash: 70fbbdfc5d8f1bac5fa27175ea25be1503a77594
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031900"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Utilizar o cliente do Apache Beeline com o Apache Hive

Este artigo descreve como usar o cliente [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) da linha de comando para criar e executar consultas apache Hive sobre uma ligação SSH.

## <a name="background"></a>Fundo

Beeline é um cliente da Hive que está incluído nos nós da cabeça do seu cluster HDInsight. Para ligar ao cliente Beeline instalado no seu cluster HDInsight, ou instalar a Beeline localmente, consulte [Connect to ou instale Apache Beeline](connect-install-beeline.md). A Beeline utiliza o JDBC para ligar ao HiveServer2, um serviço alojado no seu cluster HDInsight. Também pode utilizar o Beeline para aceder à Hive em HDInsight remotamente através da internet. Os exemplos seguintes fornecem as cadeias de ligação mais comuns usadas para ligar ao HDInsight da Beeline.

## <a name="prerequisites-for-examples"></a>Pré-requisitos para exemplos

* Um aglomerado hadoop em HDInsight. Ver [Começar com HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Reparem no esquema URI para o armazenamento primário do seu cluster. Por exemplo,  `wasb://` para o Azure Storage, `abfs://` para Azure Data Lake Storage Gen2, ou `adl://` para Azure Data Lake Storage Gen1. Se a transferência segura estiver ativada para o Armazenamento Azure, o URI é `wasbs://` . Para mais informações, consulte [a transferência segura.](../../storage/common/storage-require-secure-transfer.md)

* Um cliente SSH. Para obter mais informações, veja [Ligar ao HDInsight (Apache Hadoop) através de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). A maioria dos passos deste documento assume que está a usar a Beeline de uma sessão de SSH para o cluster. Você também pode usar um cliente Beeline local, mas esses passos não estão cobertos neste artigo.

## <a name="run-a-hive-query"></a>Executar uma consulta do Hive

Este exemplo baseia-se na utilização do cliente Beeline a partir de uma ligação SSH.

1. Abra uma ligação SSH ao cluster com o código abaixo. Substitua `sshuser` pelo utilizador SSH do seu cluster e `CLUSTERNAME` pelo nome do seu cluster. Quando solicitado, introduza a palavra-passe para a conta de utilizador SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Conecte-se ao HiveServer2 com o seu cliente Beeline a partir da sua sessão SSH aberta, introduzindo o seguinte comando:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Os comandos beeline começam com um `!` personagem, por exemplo, `!help` mostra ajuda. No entanto, o `!` pode ser omitido para alguns comandos. Por exemplo, `help` também funciona.

    Há, `!sql` que é usado para executar declarações de HiveQL. No entanto, o HiveQL é tão comumente usado que pode omitir o `!sql` anterior. As duas declarações seguintes são equivalentes:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    Num novo aglomerado, apenas uma tabela é listada: **hivesmpletable**.

4. Utilize o seguinte comando para visualizar o esquema para o hivesmpletable:

    ```hiveql
    describe hivesampletable;
    ```

    Este comando devolve as seguintes informações:

    ```output
    +-----------------------+------------+----------+--+
    |       col_name        | data_type  | comment  |
    +-----------------------+------------+----------+--+
    | clientid              | string     |          |
    | querytime             | string     |          |
    | market                | string     |          |
    | deviceplatform        | string     |          |
    | devicemake            | string     |          |
    | devicemodel           | string     |          |
    | state                 | string     |          |
    | country               | string     |          |
    | querydwelltime        | double     |          |
    | sessionid             | bigint     |          |
    | sessionpagevieworder  | bigint     |          |
    +-----------------------+------------+----------+--+
    ```

    Esta informação descreve as colunas na tabela.

5. Introduza as seguintes declarações para criar uma tabela chamada **log4jLogs** utilizando dados de amostra fornecidos com o cluster HDInsight: (Revise conforme necessário com base no seu esquema URI.)

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
        GROUP BY t4;
    ```

    Estas declarações fazem as seguintes ações:

    |Instrução |Descrição |
    |---|---|
    |DROP TABLE|Se a mesa existe, é apagada.|
    |CRIAR TABELA EXTERNA|Cria uma mesa **externa** na Colmeia. As mesas externas armazenam apenas a definição de tabela na Colmeia. Os dados são deixados no local original.|
    |FORMATO DE LINHA|Como os dados são formatados. Neste caso, os campos em cada tronco são separados por um espaço.|
    |ARMAZENADO COMO LOCALIZAÇÃO DE FICHEIRO DE TEXTO|Onde os dados são armazenados e em que formato de ficheiro.|
    |SELECIONAR|Seleciona uma contagem de todas as linhas onde a coluna **t4** contém o valor **[ERROR]**. Esta consulta devolve um valor de **3,** uma vez que existem três linhas que contêm este valor.|
    |INPUT__FILE__NAME como '%.log'|A Colmeia tenta aplicar o esquema a todos os ficheiros do diretório. Neste caso, o diretório contém ficheiros que não correspondem ao esquema. Para prevenir dados de lixo nos resultados, esta declaração diz à Hive que só deve devolver dados de ficheiros que terminam em .log.|

   > [!NOTE]  
   > As tabelas externas devem ser utilizadas quando se espera que os dados subjacentes sejam atualizados por uma fonte externa. Por exemplo, um processo automatizado de upload de dados ou uma operação MapReduce.
   >
   > Deixar cair uma tabela externa **não** apaga os dados, apenas a definição de tabela.

    A saída deste comando é semelhante ao seguinte texto:

    ```output
    INFO  : Tez session hasn't been created yet. Opening session
    INFO  :

    INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

    INFO  : Map 1: -/-      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
    INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
    INFO  : Map 1: 1/1      Reducer 2: 0/1
    INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
    INFO  : Map 1: 1/1      Reducer 2: 1/1
    +----------+--------+--+
    |   sev    | count  |
    +----------+--------+--+
    | [ERROR]  | 3      |
    +----------+--------+--+
    1 row selected (47.351 seconds)
    ```

6. Saída Beeline:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>Executar um ficheiro HiveQL

Este exemplo é uma continuação do exemplo anterior. Utilize os seguintes passos para criar um ficheiro e, em seguida, executá-lo usando Beeline.

1. Utilize o seguinte comando para criar um ficheiro chamado **consulta.hql**:

    ```bash
    nano query.hql
    ```

1. Utilize o seguinte texto como o conteúdo do ficheiro. Esta consulta cria uma nova tabela 'interna' denominada **ErrorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Estas declarações fazem as seguintes ações:

    |Instrução |Descrição |
    |---|---|
    |CRIAR TABELA SE NÃO EXISTIR|Se a mesa já não existe, é criada. Uma vez que a palavra-chave **EXTERNA** não é utilizada, esta afirmação cria uma tabela interna. As tabelas internas são armazenadas no armazém de dados da Hive e são geridas completamente pela Hive.|
    |ARMAZENADO COMO ORC|Armazena os dados no formato Colunar de Linha Otimizada (ORC). O formato ORC é um formato altamente otimizado e eficiente para armazenar dados da Hive.|
    |INSIRA A SOBRESSAR ... SELECIONE|Seleciona linhas da tabela **log4jLogs** que contêm **[ERROR]** e, em seguida, insere os dados na tabela **ErrorLogs.**|

    > [!NOTE]  
    > Ao contrário das tabelas externas, deixar cair uma tabela interna também elimina os dados subjacentes.

1. Para guardar o ficheiro, use **ctrl** + **X,** em seguida, insira **Y**, e finalmente **insira**.

1. Utilize o seguinte para executar o ficheiro utilizando a Beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > O `-i` parâmetro inicia a Beeline e executa as declarações no `query.hql` ficheiro. Assim que a consulta terminar, chega-se ao `jdbc:hive2://headnodehost:10001/>` pedido. Também pode executar um ficheiro utilizando o `-f` parâmetro, que sai da Beeline após a conclusão da consulta.

1. Para verificar se a tabela **ErrorLogs** foi criada, utilize a seguinte declaração para retornar todas as linhas de **errorLogs**:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Devem ser devolvidas três linhas de dados, todas contendo **[ERROR]** na coluna t4:

    ```output
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    3 rows selected (0.813 seconds)
    ```

## <a name="next-steps"></a>Passos seguintes

* Para obter informações mais gerais sobre a Colmeia em HDInsight, consulte [Use Apache Hive com Apache Hadoop em HDInsight](hdinsight-use-hive.md)

* Para obter mais informações sobre outras formas de trabalhar com Hadoop em HDInsight, consulte [Use MapReduce com Apache Hadoop em HDInsight](hdinsight-use-mapreduce.md)
