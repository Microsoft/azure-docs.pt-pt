---
title: Usar o Apache beeline com o Apache Hive-HDInsight do Azure
description: Saiba como usar o cliente beeline para executar consultas do hive com o Hadoop no HDInsight. Beeline é um utilitário para trabalhar com HiveServer2 sobre JDBC.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: hrasheed
ms.openlocfilehash: 7c4af8346b5da20c662b5549284a3540d08908f8
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072931"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Usar o cliente Apache beeline com Apache Hive

Saiba como usar o [Apache beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) para executar Apache Hive consultas no HDInsight.

Beeline é um cliente do hive que está incluído nos nós de cabeçalho do seu cluster HDInsight. O beeline usa JDBC para se conectar ao HiveServer2, um serviço hospedado em seu cluster HDInsight. Você também pode usar o beeline para acessar o hive no HDInsight remotamente pela Internet. Os exemplos a seguir fornecem as cadeias de conexão mais comuns usadas para se conectar ao HDInsight do beeline:

## <a name="types-of-connections"></a>Tipos de conexões

### <a name="from-an-ssh-session"></a>De uma sessão SSH

Ao conectar-se de uma sessão SSH a um cabeçalho de cluster, você pode conectar `headnodehost` -se ao `10001`endereço na porta:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Em uma rede virtual do Azure

Ao conectar-se de um cliente ao HDInsight em uma rede virtual do Azure, você deve fornecer o FQDN (nome de domínio totalmente qualificado) de um nó de cabeçalho do cluster. Como essa conexão é feita diretamente para os nós de cluster, a conexão usa `10001`a porta:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Substitua `<headnode-FQDN>` pelo nome de domínio totalmente qualificado de um cabeçalho de cluster. Para localizar o nome de domínio totalmente qualificado de um cabeçalho, use as informações no documento [gerenciar o HDInsight usando a API REST do Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) .

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster"></a>Para o cluster Enterprise Security Package do HDInsight (ESP)

Ao se conectar de um cliente a um cluster Enterprise Security Package (ESP) ingressado no Azure Active Directory (AAD) em um computador no mesmo realm do cluster, você também deve especificar o `<AAD-Domain>` nome de domínio e o nome de uma conta de usuário de domínio com permissões para acessar o cluster `<username>`:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Substitua `<username>` pelo nome de uma conta no domínio com permissões para acessar o cluster. Substituir `<AAD-DOMAIN>` pelo nome do Azure Active Directory (AAD) ao qual o cluster está associado. Use uma cadeia de caracteres em `<AAD-DOMAIN>` maiúsculas para o valor, caso contrário, a credencial não será encontrada. Verifique `/etc/krb5.conf` os nomes de territórios, se necessário.

---

### <a name="over-public-internet"></a>Pela Internet pública

Ao se conectar a um cluster ESP não ESP ou Azure Active Directory (AAD) ingressado na Internet pública, você deve fornecer o nome da conta de logon do `admin`cluster (padrão) e a senha. Por exemplo, usando o `<clustername>.azurehdinsight.net` beeline de um sistema cliente para se conectar ao endereço. Essa conexão é feita pela porta `443`e é criptografada usando SSL:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
```

Substitua `clustername` pelo nome do seu cluster do HDInsight. Substitua `admin` pela conta de logon do cluster do cluster. Substitua `password` pela senha da conta de logon do cluster.

---

### <a id="sparksql"></a>Usar o beeline com Apache Spark

Apache Spark fornece sua própria implementação de HiveServer2, que às vezes é chamada de servidor Thrift do Spark. Esse serviço usa o Spark SQL para resolver consultas em vez de Hive e pode fornecer melhor desempenho dependendo da sua consulta.

#### <a name="over-public-internet-with-apache-spark"></a>Sobre Internet pública com Apache Spark

A cadeia de conexão usada durante a conexão pela Internet é um pouco diferente. Em vez de `httpPath=/hive2` conter `httpPath/sparkhive2`:

```bash 
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p password
```

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Do cabeçalho do cluster ou dentro da rede virtual do Azure com Apache Spark

Ao se conectar diretamente do nó principal do cluster ou de um recurso dentro da mesma rede virtual do Azure que o cluster HDInsight, `10002` a porta deve ser usada para o servidor Spark `10001`Thrift em vez de. O exemplo a seguir mostra como se conectar diretamente ao nó principal:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a id="prereq"></a>Pré-requisitos

* Um cluster Hadoop no HDInsight. Consulte [introdução ao HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Observe o [esquema de URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) para o armazenamento primário do seu cluster. Por exemplo, `wasb://` para o armazenamento do `abfs://` Azure, por Azure data Lake Storage Gen2 `adl://` ou para Azure data Lake Storage Gen1. Se a transferência segura estiver habilitada para o armazenamento do Azure ou data Lake Storage Gen2 `wasbs://` , `abfss://`o URI será ou, respectivamente. Para obter mais informações, consulte [transferência segura](../../storage/common/storage-require-secure-transfer.md).


* Opção 1: Um cliente SSH. Para obter mais informações, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](../hdinsight-hadoop-linux-use-ssh-unix.md). A maioria das etapas neste documento pressupõe que você está usando o beeline de uma sessão SSH para o cluster.

* Opção 2:  Um cliente beeline local.


## <a id="beeline"></a>Executar uma consulta de Hive

Este exemplo se baseia no uso do cliente beeline de uma conexão SSH.

1. Abra uma conexão SSH para o cluster com o código a seguir. Substitua `sshuser` pelo utilizador SSH do seu cluster e `CLUSTERNAME` pelo nome do seu cluster. Quando solicitado, insira a senha para a conta de usuário SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Conecte-se ao HiveServer2 com o cliente do beeline de sua sessão SSH aberta inserindo o seguinte comando:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Os comandos beeline começam com `!` um caractere, por `!help` exemplo, exibe a ajuda. No entanto, o `!` pode ser omitido para alguns comandos. Por exemplo, `help` também funciona.

    Há um `!sql`, que é usado para executar instruções HiveQL. No entanto, HiveQL é tão comumente usado que você pode omitir o anterior `!sql`. As duas instruções a seguir são equivalentes:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    Em um novo cluster, apenas uma tabela é listada: **hivesampletable**.

4. Use o seguinte comando para exibir o esquema para o hivesampletable:

    ```hiveql
    describe hivesampletable;
    ```

    Esse comando retorna as seguintes informações:

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

    Essas informações descrevem as colunas na tabela.

5. Insira as instruções a seguir para criar uma tabela chamada **log4jLogs** usando os dados de exemplo fornecidos com o cluster HDInsight: (Revisar conforme necessário com base no seu [esquema de URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme).)

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

    Essas instruções executam as seguintes ações:

    * `DROP TABLE`-Se a tabela existir, ela será excluída.

    * `CREATE EXTERNAL TABLE`-Cria uma tabela **externa** no hive. As tabelas externas armazenam apenas a definição de tabela no hive. Os dados são deixados no local original.

    * `ROW FORMAT`-Como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.

    * `STORED AS TEXTFILE LOCATION`-Onde os dados são armazenados e em qual formato de arquivo.

    * `SELECT`– Seleciona uma contagem de todas as linhas em que a coluna **T4** contém o valor **[ERROR]** . Essa consulta retorna um valor de **3** , pois há três linhas que contêm esse valor.

    * `INPUT__FILE__NAME LIKE '%.log'`-O hive tenta aplicar o esquema a todos os arquivos no diretório. Nesse caso, o diretório contém arquivos que não correspondem ao esquema. Para evitar dados de lixo nos resultados, essa instrução informa ao hive que ele só deve retornar dados de arquivos que terminam em. log.

   > [!NOTE]  
   > As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa. Por exemplo, um processo de upload de dados automatizado ou uma operação MapReduce.
   >
   > Descartar uma tabela externa **não** exclui os dados, apenas a definição da tabela.

    A saída desse comando é semelhante ao seguinte texto:

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

6. Para sair do beeline, `!exit`use.

## <a id="file"></a>Executar um arquivo HiveQL

Esta é uma continuação do exemplo anterior. Use as etapas a seguir para criar um arquivo e, em seguida, execute-o usando beeline.

1. Use o comando a seguir para criar um arquivo chamado **Query. HQL**:

    ```bash
    nano query.hql
    ```

2. Use o texto a seguir como o conteúdo do arquivo. Essa consulta cria uma nova tabela ' interna ' chamada **logs de erros**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Essas instruções executam as seguintes ações:

   * **CREATE TABLE se não existir** -se a tabela ainda não existir, ela será criada. Como a palavra-chave **external** não é usada, essa instrução cria uma tabela interna. As tabelas internas são armazenadas no data warehouse do hive e são gerenciadas completamente pelo Hive.
   * **Armazenado como Orc** -armazena os dados no formato de coluna de linha otimizado (ORC). O formato ORC é um formato altamente otimizado e eficiente para armazenar dados do hive.
   * **INSERIR SUBSTITUIÇÃO... Select** – seleciona linhas da tabela **log4jLogs** que contêm **[ERROR]** e insere os dados na tabela de **logs de erros** .

    > [!NOTE]  
    > Ao contrário das tabelas externas, remover uma tabela interna também exclui os dados subjacentes.

3. Para salvar o arquivo, use **Ctrl**+ **_X**, em seguida, digite **Y**e, por fim, **Enter**.

4. Use o seguinte para executar o arquivo usando beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > O `-i` parâmetro inicia beeline e executa as instruções `query.hql` no arquivo. Quando a consulta for concluída, você chegará ao `jdbc:hive2://headnodehost:10001/>` prompt. Você também pode executar um arquivo usando o `-f` parâmetro, que sai do beeline após a conclusão da consulta.

5. Para verificar se a tabela de **logs de erros** foi criada, use a seguinte instrução para retornar todas as linhas de **logs de erros**:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Três linhas de dados devem ser retornadas, todas contendo **[ERROR]** na coluna T4:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)




## <a id="summary"></a><a id="nextsteps"></a>Próximas etapas

Para obter mais informações gerais sobre o hive no HDInsight, consulte o seguinte documento:

* [Usar Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter mais informações sobre outras maneiras que você pode trabalhar com o Hadoop no HDInsight, consulte os seguintes documentos:

* [Usar o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md

[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx
