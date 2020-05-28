---
title: Use Apache Beeline com Apache Hive - Azure HDInsight
description: Aprenda a usar o cliente Beeline para executar consultas da Hive com Hadoop no HDInsight. Beeline é um utilitário para trabalhar com hiveServer2 sobre JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: 3b270b8ae4e9729d2c0f8ae99a3c19c68561df95
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84119260"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Utilizar o cliente do Apache Beeline com o Apache Hive

Aprenda a usar [apache beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) para executar consultas apache hive no HDInsight.

Beeline é um cliente da Colmeia que está incluído nos nós da cabeça do seu cluster HDInsight. Para ligar ao cliente Beeline instalado no seu cluster HDInsight, ou instalar a Beeline localmente, consulte [Connect ou instale a Apache Beeline](connect-install-beeline.md). A Beeline utiliza o JDBC para ligar ao HiveServer2, um serviço hospedado no seu cluster HDInsight. Também pode usar a Beeline para aceder à Hive no HDInsight remotamente através da internet. Os seguintes exemplos fornecem as cordas de ligação mais comuns usadas para ligar ao HDInsight a partir da Beeline.

## <a name="prerequisites-for-examples"></a>Pré-requisitos para exemplos

* Um aglomerado de Hadoop no HDInsight. Ver [Começar com HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Note o esquema URI para o armazenamento primário do seu cluster. Por exemplo, `wasb://` para o Armazenamento Azure, para O Armazenamento de Lagos `abfs://` Azure Data Gen2, ou `adl://` para Azure Data Lake Storage Gen1. Se a transferência segura estiver ativada para o Armazenamento Azure, o URI é `wasbs://` . Para mais informações, consulte [a transferência segura](../../storage/common/storage-require-secure-transfer.md).

* Opção 1: Um cliente SSH. Para mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando O SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). A maioria dos passos deste documento assumem que está a usar a Beeline de uma sessão de SSH para o cluster.

* Opção 2: Cliente local da Beeline.

## <a name="run-a-hive-query"></a>Executar uma consulta do Hive

Este exemplo baseia-se na utilização do cliente Beeline a partir de uma ligação SSH.

1. Abra uma ligação SSH ao cluster com o código abaixo. Substitua `sshuser` pelo utilizador SSH do seu cluster e `CLUSTERNAME` pelo nome do seu cluster. Quando solicitado, introduza a palavra-passe para a conta de utilizador SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Ligue-se ao HiveServer2 com o seu cliente Beeline a partir da sua sessão SSH aberta, inserindo o seguinte comando:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Os comandos beeline começam com um `!` personagem, por `!help` exemplo, mostra ajuda. No entanto, o `!` pode ser omitido para alguns comandos. Por exemplo, `help` também funciona.

    Há, `!sql` que é usado para executar declarações da HiveQL. No entanto, o HiveQL é tão comumente usado que pode omiti-lo antes `!sql` . As duas seguintes declarações são equivalentes:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    Num novo aglomerado, apenas uma tabela está listada: **a colmeia.**

4. Utilize o seguinte comando para exibir o esquema para a colmeia:

    ```hiveql
    describe hivesampletable;
    ```

    Este comando devolve as seguintes informações:

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

    |Declaração |Descrição |
    |---|---|
    |MESA DE LANÇAMENTO|Se a mesa existe, é apagada.|
    |CRIAR TABELA EXTERNA|Cria uma mesa **externa** na Colmeia. As tabelas externas apenas armazenam a definição de mesa na Colmeia. Os dados são deixados no local original.|
    |FORMATO DE LINHA|Como os dados são formatados. Neste caso, os campos em cada tronco são separados por um espaço.|
    |LOCALIZAÇÃO ARMAZENADA COMO FICHEIRO DE TEXTO|Onde os dados são armazenados e em que formato de ficheiro.|
    |SELECIONAR|Seleciona uma contagem de todas as linhas onde a coluna **t4** contém o valor **[ERROR]**. Esta consulta devolve um valor de **3,** uma vez que existem três linhas que contêm este valor.|
    |INPUT__FILE__NAME LIKE '%.log'|A Hive tenta aplicar o esquema a todos os ficheiros do diretório. Neste caso, o diretório contém ficheiros que não correspondem ao esquema. Para evitar dados de lixo nos resultados, esta declaração diz à Hive que só deve devolver dados de ficheiros que terminam em .log.|

   > [!NOTE]  
   > As tabelas externas devem ser utilizadas quando se espera que os dados subjacentes sejam atualizados por uma fonte externa. Por exemplo, um processo automatizado de upload de dados ou uma operação MapReduce.
   >
   > Deixar cair uma tabela externa **não** elimina os dados, apenas a definição de tabela.

    A saída deste comando é semelhante ao seguinte texto:

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

6. Saída Beeline:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>Executar um ficheiro HiveQL

Este exemplo é uma continuação do exemplo anterior. Utilize os seguintes passos para criar um ficheiro e, em seguida, executá-lo utilizando beeline.

1. Utilize o seguinte comando para criar um ficheiro chamado **consulta.hql:**

    ```bash
    nano query.hql
    ```

1. Utilize o seguinte texto como conteúdo do ficheiro. Esta consulta cria uma nova tabela 'interna' chamada **errorLogs:**

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Estas declarações fazem as seguintes ações:

    |Declaração |Descrição |
    |---|---|
    |CRIAR TABELA SE NÃO EXISTIR|Se a mesa já não existe, é criada. Uma vez que a palavra-chave **externa** não é usada, esta declaração cria uma tabela interna. As tabelas internas são armazenadas no armazém de dados da Hive e são geridas completamente pela Hive.|
    |ARMAZENADO COMO ORC|Armazene os dados em formato Colunar de Linhas Otimizada (ORC). O formato ORC é um formato altamente otimizado e eficiente para armazenar dados da Hive.|
    |INSERIR SOBREESCRITA ... SELECIONE|Seleciona linhas da tabela **log4jLogs** que contêm **[ERROR]** e, em seguida, insere os dados na tabela **errorLogs.**|

    > [!NOTE]  
    > Ao contrário das tabelas externas, a queda de uma tabela interna também elimina os dados subjacentes.

1. Para guardar o ficheiro, utilize **ctrl** + **X,** em seguida, **introduza Y**, e finalmente **enter .**

1. Utilize o seguinte para executar o ficheiro utilizando a Beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > O parâmetro inicia a `-i` Beeline e executa as declarações no `query.hql` ficheiro. Assim que a consulta terminar, chegas ao `jdbc:hive2://headnodehost:10001/>` aviso. Também pode executar um ficheiro utilizando o `-f` parâmetro, que sai da Beeline após a consulta estar concluída.

1. Para verificar se a tabela **errorLogs** foi criada, utilize a seguinte declaração para devolver todas as linhas dos **errorLogs:**

    ```hiveql
    SELECT * from errorLogs;
    ```

    Devem ser devolvidas três linhas de dados, todas contendo **[ERROR]** na coluna t4:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (0.813 seconds)

## <a name="next-steps"></a>Próximos passos

* Para obter informações mais gerais sobre a Hive em HDInsight, consulte [Use Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)

* Para obter mais informações sobre outras formas de trabalhar com Hadoop no HDInsight, consulte [Use MapReduce com Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)
