---
title: Carregamento em massa em Apache Phoenix usando o psql-Azure HDInsight
description: Usar a ferramenta psql para carregar dados de carregamento em massa em tabelas Apache Phoenix no Azure HDInsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 845c4a62aee04a8acdc645ba4c41f1f5496537c3
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552615"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Carregamento de dados em massa para o Apache Phoenix com psql

[Apache Phoenix](https://phoenix.apache.org/) é um banco de dados relacional de software livre, maciçomente paralelo, criado no [Apache HBase](../hbase/apache-hbase-overview.md). O Phoenix fornece consultas semelhantes ao SQL no HBase. O Phoenix usa drivers JDBC para permitir que os usuários criem, excluam e alterem tabelas, índices, exibições e sequências do SQL e Upsert linhas individualmente e em massa. O Phoenix usa a compilação nativa noSQL em vez de usar o MapReduce para compilar consultas, para criar aplicativos de baixa latência sobre o HBase. O Phoenix adiciona coprocessadores para dar suporte à execução de código fornecido pelo cliente no espaço de endereço do servidor, executando o código colocado com os dados. Isso minimiza a transferência de dados do cliente/servidor.  Para trabalhar com dados usando Phoenix no HDInsight, primeiro crie tabelas e, em seguida, carregue os dados nelas.

## <a name="bulk-loading-with-apache-phoenix"></a>Carregamento em massa com Apache Phoenix

Há várias maneiras de obter dados no HBase, incluindo o uso de APIs de cliente, um trabalho MapReduce com TableOutputFormat ou a colocação dos dados manualmente usando o Shell do HBase. O Phoenix fornece dois métodos para carregar dados CSV em tabelas Phoenix: uma ferramenta de carregamento de cliente chamada `psql`e uma ferramenta de carregamento em massa baseada em MapReduce.

A ferramenta de `psql` é de thread único e é mais adequada para carregar megabytes ou gigabytes de dados. Todos os arquivos CSV a serem carregados devem ter a extensão de arquivo '. csv '.  Você também pode especificar arquivos de script SQL na linha de comando `psql` com a extensão de arquivo '. SQL '.

O carregamento em massa com o MapReduce é usado para volumes de dados muito maiores, normalmente em cenários de produção, já que o MapReduce usa vários threads.

Antes de começar a carregar dados, verifique se Phoenix está habilitado e se as configurações de tempo limite de consulta estão conforme o esperado.  Acesse o painel do [Apache Ambari](https://ambari.apache.org/) do cluster HDInsight, selecione HBase e, em seguida, a guia configuração.  Role para baixo para verificar se Apache Phoenix está definido como `enabled` conforme mostrado:

![Apache Phoenix configurações do cluster HDInsight](./media/apache-hbase-phoenix-psql/apache-ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Usar `psql` para carregar tabelas em massa

1. Crie um arquivo chamado `createCustomersTable.sql`e copie o código abaixo para o arquivo. Em seguida, guarde e feche o ficheiro.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

1. Crie um arquivo chamado `listCustomers.sql`e copie o código abaixo para o arquivo. Em seguida, guarde e feche o ficheiro.

    ```sql
    SELECT * from Customers;
    ```

1. Crie um arquivo chamado `customers.csv`e copie o código abaixo para o arquivo. Em seguida, guarde e feche o ficheiro.

    ```txt
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,42,Norway
    ```

1. Crie um arquivo chamado `customers2.csv`e copie o código abaixo para o arquivo. Em seguida, guarde e feche o ficheiro.

    ```txt
    4,Nicolle,180000.0,22,US
    5,Kate,210000.5,24,Canada
    6,Ben,45000.0,32,Poland
    ```

1. Abra um prompt de comando e altere o diretório para o local dos arquivos recém-criados. Substitua CLUSTERname, abaixo, pelo nome real do cluster HBase. Em seguida, execute o código para carregar os arquivos para o cabeçalho do cluster:

    ```cmd
    scp customers.csv customers2.csv createCustomersTable.sql listCustomers.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/tmp
    ```

1. Use o [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster. Edite o comando a seguir substituindo CLUSTERname pelo nome do cluster e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Na sua sessão SSH, altere o diretório para o local da ferramenta **psql** . Execute o comando a seguir:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

1. Carregar os dados em massa. O código a seguir criará a tabela **Customers** e, em seguida, carregará os dados.

    ```bash
    python psql.py /tmp/createCustomersTable.sql /tmp/customers.csv
    ```

    Após a conclusão da operação de `psql`, você deverá ver uma mensagem semelhante à seguinte:

    ```output
    csv columns from database.
    CSV Upsert complete. 3 rows upserted
    Time: 0.081 sec(s)
    ```

1. Você pode continuar a usar `psql` para exibir o conteúdo da tabela Customers. Execute o código abaixo:

    ```bash
    python psql.py /tmp/listCustomers.sql
    ```

    Como alternativa, você pode usar o [shell do HBase](./query-hbase-with-hbase-shell.md)ou o [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md) para consultar os dados.

1. Carregar dados adicionais. Agora que a tabela já existe, o comando especifica a tabela. Execute o comando a seguir:

    ```bash
    python psql.py -t CUSTOMERS /tmp/customers2.csv
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Usar o MapReduce para carregar tabelas em massa

Para o carregamento de taxa de transferência mais alta distribuído pelo cluster, use a ferramenta de carregamento do MapReduce. Esse carregador primeiro converte todos os dados em HFiles e, em seguida, fornece o HFiles criado para o HBase.

1. Esta seção continua com a sessão SSH e os objetos criados anteriormente. Crie a tabela **Customers** e o arquivo **Customers. csv** conforme necessário usando as etapas acima. Se necessário, restabeleça a conexão SSH.

1. Truncar o conteúdo da tabela **Customers** . Em sua sessão SSH aberta, execute os comandos abaixo:

    ```bash
    hbase shell
    truncate 'CUSTOMERS'
    exit
    ```

1. Copie o arquivo de `customers.csv` do seu cabeçalho para o armazenamento do Azure.

    ```bash
    hdfs dfs -put /tmp/customers.csv wasbs:///tmp/customers.csv
    ```

1. Altere para o diretório de execução para o comando de carregamento em massa do MapReduce:

    ```bash
    cd /usr/hdp/current/phoenix-client
    ```

1. Inicie o carregador MapReduce do CSV usando o comando `hadoop` com o JAR do cliente Phoenix:

    ```bash
    HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar phoenix-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /tmp/customers.csv
    ```

    Quando o carregamento for concluído, você verá uma mensagem semelhante à seguinte:

    ```output
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing master protocol: MasterService
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing zookeeper sessionid=0x26f15dcceff02c3
    19/12/18 18:30:57 INFO zookeeper.ZooKeeper: Session: 0x26f15dcceff02c3 closed
    19/12/18 18:30:57 INFO zookeeper.ClientCnxn: EventThread shut down
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Incremental load complete for table=CUSTOMERS
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Removing output directory /tmp/50254426-aba6-400e-88eb-8086d3dddb6
    ```

1. Para usar o MapReduce com Azure Data Lake Storage, localize o diretório raiz Data Lake Storage, que é o valor de `hbase.rootdir` em `hbase-site.xml`. No comando a seguir, o diretório raiz Data Lake Storage é `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`. Neste comando, especifique o Data Lake Storage as pastas de entrada e saída como parâmetros:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

1. Para consultar e exibir os dados, você pode usar **psql** conforme descrito anteriormente. Você também pode usar o [shell do HBase](./query-hbase-with-hbase-shell.md)ou o [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md).

## <a name="recommendations"></a>Recomendações

* Use o mesmo meio de armazenamento para as pastas de entrada e saída, o armazenamento do Azure (WASB) ou o Azure Data Lake Storage (ADL). Para transferir dados do armazenamento do Azure para Data Lake Storage, você pode usar o comando `distcp`:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Use nós de trabalho de tamanho maior. Os processos de mapa da cópia em massa do MapReduce produzem grandes quantidades de saídas temporárias que preenchem o espaço não DFS disponível. Para uma grande quantidade de carregamento em massa, use nós de trabalho mais amplos e de tamanho maior. O número de nós de trabalho que você aloca para o cluster afeta diretamente a velocidade de processamento.

* Divida os arquivos de entrada em partes de ~ 10 GB. O carregamento em massa é uma operação de uso intensivo de armazenamento, portanto, dividir os arquivos de entrada em várias partes resulta em um melhor desempenho.

* Evite pontos de HotSpot do servidor de região. Se a chave de linha for monotônico aumentando, as gravações sequenciais do HBase poderão induzir a hotspotting do servidor de região. O *Salt* da chave de linha reduz as gravações sequenciais. O Phoenix fornece uma maneira de saltar de forma transparente a chave de linha com um byte de Salt para uma tabela específica, como referenciado abaixo.

## <a name="next-steps"></a>Passos seguintes

* [Carregamento de dados em massa com Apache Phoenix](https://phoenix.apache.org/bulk_dataload.html)
* [Usar Apache Phoenix com clusters Apache HBase baseados em Linux no HDInsight](../hbase/apache-hbase-query-with-phoenix.md)
* [Tabelas com Salt](https://phoenix.apache.org/salted.html)
* [Apache Phoenix gramática](https://phoenix.apache.org/language/index.html)
