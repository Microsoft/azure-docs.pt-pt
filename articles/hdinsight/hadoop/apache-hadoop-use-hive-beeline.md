---
title: Utilizar Beeline do Apache com o Apache Hive - Azure HDInsight
description: Saiba como utilizar o cliente de Beeline para executar consultas do Hive com o Hadoop no HDInsight. Beeline é um utilitário para trabalhar com HiveServer2 através de JDBC.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: hrasheed
ms.openlocfilehash: 89303e5c827fc24540d345a9a2b9a0743e453a4d
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59056865"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Utilizar o cliente do Apache Beeline com Apache Hive

Aprenda a usar [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) para executar consultas do Apache Hive no HDInsight.

Beeline é um cliente de ramo de registo que está incluído nos nós principais do cluster do HDInsight. Beeline utiliza JDBC para ligar ao HiveServer2, um serviço hospedado no seu cluster do HDInsight. Também pode utilizar Beeline para acessar o Hive no HDInsight remotamente através da internet. Os exemplos seguintes mostram as cadeias de ligação mais comuns utilizadas para ligar ao HDInsight a partir Beeline:

## <a name="types-of-connections"></a>Tipos de ligações

### <a name="from-an-ssh-session"></a>Numa sessão SSH

Ao ligar-se de uma sessão SSH para um nó principal do cluster, em seguida, pode ligar para o `headnodehost` endereço na porta `10001`:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Através de uma rede Virtual do Azure

Ao ligar de um cliente para o HDInsight, através de uma rede Virtual do Azure, tem de fornecer o nome de domínio completamente qualificado (FQDN) de um nó principal do cluster. Uma vez que esta ligação é efetuada diretamente para os nós do cluster, a ligação utiliza a porta `10001`:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Substitua `<headnode-FQDN>` com o nome de domínio completamente qualificado de um nó principal do cluster. Para localizar o nome de domínio completamente qualificado de um nó principal, utilize as informações a [gerir o HDInsight com a API de REST do Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) documento.

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster"></a>Cluster do HDInsight pacote de segurança da empresa (ESP)

Quando ligar a partir de um cliente a um cluster de pacote de segurança da empresa (ESP) associado ao Azure Active Directory (AAD), também tem de especificar o nome de domínio `<AAD-Domain>` e o nome de uma conta de utilizador de domínio com permissões para aceder ao cluster `<username>`:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Substitua `<username>` com o nome de uma conta no domínio com permissões para aceder ao cluster. Substitua `<AAD-DOMAIN>` com o nome do Azure Active Directory (AAD) que o cluster está associado. Utilize uma cadeia em maiúsculas para o `<AAD-DOMAIN>` valor, caso contrário, a credencial não será encontrada. Verificar `/etc/krb5.conf` para os nomes de realm se for necessário.

---

### <a name="over-public-internet"></a>Internet pública

Ao ligar através da internet pública, tem de fornecer o nome de conta de início de sessão do cluster (predefinido `admin`) e palavra-passe. Por exemplo, utilizar Beeline de um sistema de cliente para ligar ao `<clustername>.azurehdinsight.net` endereço. Esta ligação é efetuada através da porta `443`e são criptografadas com SSL:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
```

Substitua `clustername` pelo nome do seu cluster do HDInsight. Substitua `admin` com a conta de início de sessão do cluster para o seu cluster. Substitua `password` com a palavra-passe para a conta de início de sessão do cluster.

---

### <a id="sparksql"></a>Utilizar Beeline com o Apache Spark

Apache Spark fornece sua própria implementação de HiveServer2, o que é por vezes referido como o servidor Spark Thrift. Este serviço utiliza o Spark SQL para resolver a consultas em vez do Hive e pode fornecer um melhor desempenho, dependendo da sua consulta.

#### <a name="over-public-internet-with-apache-spark"></a>A Internet pública com o Apache Spark

A cadeia de ligação utilizada ao ligar-se através da internet é ligeiramente diferente. Em vez de conter `httpPath=/hive2` é `httpPath/sparkhive2`:

```bash 
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p password
```

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Do cluster principal ou inside rede Virtual do Azure com o Apache Spark

Ao ligar diretamente a partir do nó principal do cluster, ou a partir de um recurso dentro da mesma rede Virtual do Azure que o cluster do HDInsight, a porta `10002` deve ser utilizado para o servidor Spark Thrift, em vez de `10001`. O exemplo seguinte mostra como ligar diretamente ao nó principal:

```bash
beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a id="prereq"></a>Pré-requisitos

* Um cluster do Hadoop no HDInsight. Ver [introdução ao HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Observe que o [esquema de URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) para armazenamento primário do seu cluster. Por exemplo, `wasb://` armazenamento do Azure, `abfs://` para o ger2 de armazenamento do Azure Data Lake, ou `adl://` para geração 1 de armazenamento do Azure Data Lake. Se a transferência segura é ativada para o armazenamento do Azure ou de geração 2 de armazenamento do Data Lake, o URI é `wasbs://` ou `abfss://`, respectivamente. Para obter mais informações, consulte [transferência segura](../../storage/common/storage-require-secure-transfer.md).


* Opção 1: Um cliente SSH. Para obter mais informações, consulte [ligar ao HDInsight (Apache Hadoop) através de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). A maioria dos passos neste documento partem do princípio de que está a utilizar Beeline numa sessão SSH para o cluster.

* Opção 2:  Um cliente Beeline local.


## <a id="beeline"></a>Executar uma consulta do Hive

Este exemplo baseia-se sobre como utilizar o cliente de Beeline a partir de uma ligação de SSH.

1. Abra uma ligação SSH ao cluster com o código abaixo. Substitua `sshuser` pelo utilizador SSH do seu cluster e `CLUSTERNAME` pelo nome do seu cluster. Quando lhe for pedido, introduza a palavra-passe da conta de utilizador SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Ligar ao HiveServer2 com seu cliente Beeline da sua sessão SSH aberto introduzindo o seguinte comando:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Comandos de beeline começam com um `!` por exemplo, de caracteres `!help` apresenta a ajuda. No entanto, a `!` podem ser omitidas para alguns comandos. Por exemplo, `help` também funciona.

    Existe um `!sql`, que é utilizado para executar declarações HiveQL. No entanto, HiveQL é muito comum usado que pode omitir anterior `!sql`. As duas instruções seguintes são equivalentes:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    Num cluster novo, apenas uma tabela está listada: **hivesampletable**.

4. Utilize o seguinte comando para apresentar o esquema para o hivesampletable:

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

    Estas informações descrevem as colunas na tabela.

5. Introduza as seguintes instruções para criar uma tabela chamada **log4jLogs** utilizando dados de exemplo fornecidos com o cluster do HDInsight: (Correção conforme necessário com base no seu [esquema de URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme).)

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

    * `DROP TABLE` -Se a tabela existe, é eliminado.

    * `CREATE EXTERNAL TABLE` -Cria um **externo** tabela do Hive. Tabelas externas apenas armazenam a definição da tabela no Hive. Os dados são deixados na localização original.

    * `ROW FORMAT` -Como os dados estiverem formatados. Neste caso, os campos em cada registo são separados por um espaço.

    * `STORED AS TEXTFILE LOCATION` -Onde os dados são armazenados e, em que formato de ficheiro.

    * `SELECT` -Seleciona uma contagem de todas as linhas em que coluna **t4** contém o valor **[erro]**. Esta consulta retorna um valor de **3** porque há três linhas que contêm este valor.

    * `INPUT__FILE__NAME LIKE '%.log'` -Hive tenta aplicar o esquema a todos os ficheiros no diretório. Neste caso, o diretório contém ficheiros que não corresponde ao esquema. Para impedir que os dados de lixo nos resultados, essa instrução diz ao ramo de registo que este deve devolver apenas dados de ficheiros terminados em. log.

   > [!NOTE]  
   > Tabelas externas devem ser usadas quando espera que os dados subjacentes ser atualizados por uma origem externa. Por exemplo, um processo de carregamento de dados automatizada ou uma operação de MapReduce.
   >
   > Remover uma tabela externa faz **não** eliminar os dados, apenas a definição da tabela.

    O resultado deste comando é semelhante ao seguinte texto:

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

6. Para sair do Beeline, utilize `!exit`.

## <a id="file"></a>Executar um arquivo de HiveQL

Esta é uma continuação do exemplo anterior. Utilize os seguintes passos para criar um ficheiro, em seguida, executá-la com o Beeline.

1. Utilize o seguinte comando para criar um ficheiro denominado **query.hql**:

    ```bash
    nano query.hql
    ```

2. Utilize o seguinte texto como o conteúdo do ficheiro. Esta consulta cria uma nova tabela de "interna" com o nome **registos de erros**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Essas instruções executam as seguintes ações:

   * **Criar tabela se não existe** -se a tabela ainda não existir, será criada. Uma vez que o **externo** palavra-chave não é utilizado, esta instrução cria uma tabela interna. Tabelas internas são armazenadas no armazém de dados de Hive e são geridas completamente do Hive.
   * **ARMAZENADOS ORC de AS** -armazena os dados no formato otimizado linhas em colunas (ORC). Formato ORC é um formato altamente otimizado e eficiente para armazenar os dados de Hive.
   * **SUBSTITUIR INSERT... SELECIONE** -seleciona linhas do **log4jLogs** tabela que contêm **[erro]**, em seguida, insere os dados no **registos de erros** tabela.

    > [!NOTE]  
    > Ao contrário das tabelas externas, remover uma tabela interna elimina também os dados subjacentes.

3. Para guardar o ficheiro, utilize **Ctrl**+**_X**, em seguida, introduza **Y**e finalmente **Enter**.

4. Utilize o seguinte para executar o ficheiro com o Beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > O `-i` parâmetro inicia o Beeline e executa as instruções no `query.hql` ficheiro. Assim que a consulta é concluída, vai deparar-se no `jdbc:hive2://headnodehost:10001/>` prompt. Também pode executar um ficheiro com o `-f` parâmetro, que é encerrado Beeline depois da consulta é concluída.

5. Para verificar se o **registos de erros** tabela foi criada, utilize a seguinte instrução para retornar todas as linhas da **registos de erros**:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Três linhas de dados devem ser devolvidas, tudo que contêm **[erro]** na coluna t4:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)




## <a id="summary"></a><a id="nextsteps"></a>Próximas etapas

Para obter informações mais gerais sobre o Hive no HDInsight, consulte o documento seguinte:

* [Utilizar o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter mais informações sobre outras formas pode trabalhar com o Hadoop no HDInsight, consulte os seguintes documentos:

* [Utilizar o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)
* [Utilizar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)

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
