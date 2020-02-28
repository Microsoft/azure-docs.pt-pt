---
title: Use Apache Beeline com Apache Hive - Azure HDInsight
description: Aprenda a usar o cliente Beeline para executar consultas da Hive com Hadoop no HDInsight. Beeline é um utilitário para trabalhar com hiveServer2 sobre JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 13c51f0db468c1591ca29de17f1744752589a1c8
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663750"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Use o cliente Apache Beeline com Apache Hive

Aprenda a usar [apache beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) para executar consultas apache hive no HDInsight.

Beeline é um cliente da Colmeia que está incluído nos nós da cabeça do seu cluster HDInsight. Para instalar a Beeline localmente, consulte [instalar o cliente beeline,](#install-beeline-client)abaixo. A Beeline utiliza o JDBC para ligar ao HiveServer2, um serviço hospedado no seu cluster HDInsight. Também pode usar a Beeline para aceder à Hive no HDInsight remotamente através da internet. Os seguintes exemplos fornecem as cordas de ligação mais comuns usadas para ligar ao HDInsight a partir da Beeline.

## <a name="types-of-connections"></a>Tipos de ligações

### <a name="from-an-ssh-session"></a>A partir de uma sessão de SSH

Ao ligar de uma sessão SSH a um nído de cluster, pode então ligar-se ao endereço `headnodehost` na porta `10001`:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Sobre uma Rede Virtual Azure

Ao ligar-se de um cliente ao HDInsight sobre uma Rede Virtual Azure, deve fornecer o nome de domínio totalmente qualificado (FQDN) de um nó de cabeça de cluster. Uma vez que esta ligação é feita diretamente aos nós do cluster, a ligação utiliza porta `10001`:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Substitua `<headnode-FQDN>` com o nome de domínio totalmente qualificado de um headnode de cluster. Para encontrar o nome de domínio totalmente qualificado de um headnode, utilize a informação no [Manage HDInsight utilizando o documento Apache Ambari REST API.](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes)

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Para o cluster HDInsight Enterprise Security Package (ESP) utilizando kerberos

Ao ligar-se de um cliente a um cluster do Enterprise Security Package (ESP) ligado ao Azure Ative Directory (AAD)-DS numa máquina no mesmo domínio do cluster, deve também especificar o nome de domínio `<AAD-Domain>` e o nome de uma conta de utilizador de domínio com permissões para aceder ao cluster `<username>`:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Substitua `<username>` com o nome de uma conta no domínio por permissões de acesso ao cluster. Substitua `<AAD-DOMAIN>` pelo nome do Diretório Ativo Azure (AAD) a que o cluster está associado. Use uma corda maiúscula para o valor `<AAD-DOMAIN>`, caso contrário a credencial não será encontrada. Verifique `/etc/krb5.conf` os nomes do reino, se necessário.

---

### <a name="over-public-or-private-endpoints"></a>Sobre pontos finais públicos ou privados

Ao ligar-se a um cluster utilizando os pontos finais públicos ou privados, deve fornecer o nome da conta de login do cluster (`admin`padrão) e a palavra-passe. Por exemplo, usar a Beeline a partir de um sistema de cliente para se ligar ao endereço `clustername.azurehdinsight.net`. Esta ligação é feita sobre `443`da porta , e é encriptada usando SSL.

Substitua `clustername` pelo nome do seu cluster do HDInsight. Substitua `admin` com a conta de login do cluster para o seu cluster. Para os clusters ESP, utilize a UPN completa (por exemplo, user@domain.com). Substitua `password` com a palavra-passe para a conta de login do cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

ou para fins finais privados:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Os pontos finais privados apontam para um equilíbrio de carga básico, que só pode ser acedido a partir dos VNETs espreitados na mesma região. Consulte [constrangimentos no vnet global e equilibradores](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) de carga para mais informações. Pode utilizar o comando `curl` com `-v` opção de resolver qualquer problema de conectividade com pontos finais públicos ou privados antes de utilizar a linha de apição.

---

### <a name="use-beeline-with-apache-spark"></a>Use beeline com Apache Spark

A Apache Spark fornece a sua própria implementação do HiveServer2, que às vezes é referido como o servidor Spark Thrift. Este serviço utiliza o Spark SQL para resolver consultas em vez da Hive, e pode proporcionar um melhor desempenho dependendo da sua consulta.

#### <a name="through-public-or-private-endpoints"></a>Através de pontos finais públicos ou privados

A corda de ligação utilizada é ligeiramente diferente. Em vez de conter `httpPath=/hive2` é `httpPath/sparkhive2`. Substitua `clustername` pelo nome do seu cluster do HDInsight. Substitua `admin` com a conta de login do cluster para o seu cluster. Para os clusters ESP, utilize a UPN completa (por exemplo, user@domain.com). Substitua `password` com a palavra-passe para a conta de login do cluster.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

ou para fins finais privados:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Os pontos finais privados apontam para um equilíbrio de carga básico, que só pode ser acedido a partir dos VNETs espreitados na mesma região. Consulte [constrangimentos no vnet global e equilibradores](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) de carga para mais informações. Pode utilizar o comando `curl` com `-v` opção de resolver qualquer problema de conectividade com pontos finais públicos ou privados antes de utilizar a linha de apição.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Da cabeça de cluster ou dentro da Rede Virtual Azure com Apache Spark

Ao ligar diretamente a partir do nó da cabeça do cluster, ou de um recurso dentro da mesma Rede Virtual Azure que o cluster HDInsight, os `10002` de porta devem ser utilizados para o servidor Spark Thrift em vez de `10001`. O exemplo que se segue mostra como ligar diretamente ao nó da cabeça:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a name="prerequisites-for-examples"></a>Pré-requisitos para exemplos

* Um aglomerado de Hadoop no HDInsight. Ver [Começar com HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Note o [esquema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) para o armazenamento primário do seu cluster. Por exemplo, `wasb://` para o Armazenamento Azure, `abfs://` para O Armazenamento de Lagos Azure Data Gen2, ou `adl://` para o Azure Data Lake Storage Gen1. Se a transferência segura estiver ativada para o Armazenamento Azure, o URI é `wasbs://`. Para mais informações, consulte [a transferência segura](../../storage/common/storage-require-secure-transfer.md).

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

3. Os comandos beeline começam com um personagem `!`, por exemplo, `!help` demonstra ajuda. No entanto, o `!` pode ser omitido para alguns comandos. Por exemplo, `help` também funciona.

    Há `!sql`, que é usado para executar declarações da HiveQL. No entanto, o HiveQL é tão comumente usado que pode omiti-lo `!sql`anteriores. As duas seguintes declarações são equivalentes:

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

5. Introduza as seguintes declarações para criar uma tabela chamada **log4jLogs** utilizando dados de amostra fornecidos com o cluster HDInsight: (Revise conforme necessário com base no seu [esquema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme).)

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
    |SELECIONAR|Seleciona uma contagem de todas as linhas onde a coluna **t4** contém o valor **[ERROR]** . Esta consulta devolve um valor de **3,** uma vez que existem três linhas que contêm este valor.|
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

Esta é uma continuação do exemplo anterior. Utilize os seguintes passos para criar um ficheiro e, em seguida, executá-lo utilizando beeline.

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

1. Para guardar o ficheiro, utilize **o Ctrl**+**X,** depois introduza **Y**, e finalmente **enter .**

1. Utilize o seguinte para executar o ficheiro utilizando a Beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > O parâmetro `-i` inicia a Beeline e executa as declarações no ficheiro `query.hql`. Assim que a consulta terminar, chega satisfaça o `jdbc:hive2://headnodehost:10001/>` pronta. Também pode executar um ficheiro utilizando o parâmetro `-f`, que sai da Beeline após a consulta estar concluída.

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

## <a name="install-beeline-client"></a>Instalar cliente de beeline

Embora a Beeline esteja incluída nos nós da cabeça do seu cluster HDInsight, pode querer instalá-lo numa máquina local.  Os passos abaixo para instalar a Beeline numa máquina local baseiam-se num [subsistema windows para linux](https://docs.microsoft.com/windows/wsl/install-win10).

1. Atualizar listas de pacotes. Introduza o seguinte comando na sua concha de bash:

    ```bash
    sudo apt-get update
    ```

1. Instale java se não estiver instalado. Pode verificar com o comando `which java`.

    1. Se não for instalado nenhum pacote java, introduza o seguinte comando:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Abra o ficheiro bashrc (geralmente encontrado em ~/.bashrc): `nano ~/.bashrc`.

    1. Altere o ficheiro bashrc. Adicione a seguinte linha no final do ficheiro:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Em seguida, prima **Ctrl+X**, em **seguida, Y**, em seguida, entrar.

1. Baixe os arquivos Hadoop e Beeline, insira os seguintes comandos:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Desfaça as malas, introduza os seguintes comandos:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Altere ainda mais o ficheiro bashrc. Tens de identificar o caminho para onde os arquivos foram desembalados. Se utilizar o [Subsistema Windows para o Linux](https://docs.microsoft.com/windows/wsl/install-win10), e seguir exatamente os passos, o seu caminho seria `/mnt/c/Users/user/`, onde `user` é o seu nome de utilizador.

    1. Abra o ficheiro: `nano ~/.bashrc`

    1. Modifique os comandos abaixo com o caminho adequado e, em seguida, introduza-os no final do ficheiro bashrc:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Em seguida, prima **Ctrl+X**, em **seguida, Y**, em seguida, entrar.

1. Feche e reabra a sessão de bash.

1. Teste a sua ligação. Utilize o formato de ligação de [pontos finais públicos ou privados,](#over-public-or-private-endpoints)acima.

## <a name="next-steps"></a>Passos seguintes

* Para obter informações mais gerais sobre a Hive em HDInsight, consulte [Use Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)

* Para obter mais informações sobre outras formas de trabalhar com Hadoop no HDInsight, consulte [Use MapReduce com Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)
