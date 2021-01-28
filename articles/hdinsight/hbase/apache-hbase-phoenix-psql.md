---
title: Carregamento a granel em Apache Phoenix usando psql - Azure HDInsight
description: Utilize a ferramenta psql para carregar dados de carga a granel nas tabelas Apache Phoenix em Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: d56c9b33d53be0e30769aef8934a2dbf43d06867
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944841"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Carregamento de dados em massa para o Apache Phoenix com psql

[Apache Phoenix](https://phoenix.apache.org/) é uma base de dados relacional massivamente paralela construída na [Apache HBase](../hbase/apache-hbase-overview.md). Phoenix fornece consultas semelhantes ao SQL sobre a HBase. A Phoenix utiliza controladores JDBC para permitir que os utilizadores criem, apaguem e alterem tabelas SQL, índices, vistas e sequências, e filas de subidas individual e a granel. Phoenix usa compilação nativa noSQL em vez de usar MapReduce para compilar consultas, para criar aplicações de baixa latência em cima da HBase. A Phoenix adiciona coprocessadores para suportar o código fornecido pelo cliente no espaço de endereço do servidor, executando o código indicado com os dados. Isto minimiza a transferência de dados cliente/servidor.  Para trabalhar com dados usando Phoenix em HDInsight, primeiro criar tabelas e depois carregar dados neles.

## <a name="bulk-loading-with-apache-phoenix"></a>Carregamento a granel com Apache Phoenix

Existem várias formas de obter dados em HBase, incluindo a utilização de APIs do cliente, um trabalho MapReduce com TableOutputFormat, ou inserir os dados manualmente utilizando a concha HBase. A Phoenix fornece dois métodos para carregar dados de CSV nas tabelas Phoenix: uma ferramenta de carregamento de clientes chamada `psql` , e uma ferramenta de carga a granel baseada no MapReduce.

A `psql` ferramenta é de rosca única e é mais adequada para carregar megabytes ou gigabytes de dados. Todos os ficheiros CSV a serem carregados devem ter a extensão de ficheiro ".csv".  Também pode especificar ficheiros de script SQL na linha de `psql` comando com a extensão de ficheiro '.sql'.

O carregamento a granel com o MapReduce é usado para volumes de dados muito maiores, tipicamente em cenários de produção, uma vez que o MapReduce utiliza vários fios.

Antes de começar a carregar dados, verifique se a Phoenix está ativada e que as definições de tempo de consulta são como esperado.  Aceda ao seu painel de instrumentos [Apache Ambari,](https://ambari.apache.org/) selecione HBase e, em seguida, o separador Configuração.  Desça para verificar se Apache Phoenix está definido como `enabled` mostrado:

![Configurações do cluster Apache Phoenix HDInsight](./media/apache-hbase-phoenix-psql/apache-ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Use `psql` para mesas de carga a granel

1. Crie um ficheiro chamado `createCustomersTable.sql` , e copie o código abaixo no ficheiro. Em seguida, guarde e feche o ficheiro.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

1. Crie um ficheiro chamado `listCustomers.sql` , e copie o código abaixo no ficheiro. Em seguida, guarde e feche o ficheiro.

    ```sql
    SELECT * from Customers;
    ```

1. Crie um ficheiro chamado `customers.csv` , e copie o código abaixo no ficheiro. Em seguida, guarde e feche o ficheiro.

    ```txt
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,42,Norway
    ```

1. Crie um ficheiro chamado `customers2.csv` , e copie o código abaixo no ficheiro. Em seguida, guarde e feche o ficheiro.

    ```txt
    4,Nicolle,180000.0,22,US
    5,Kate,210000.5,24,Canada
    6,Ben,45000.0,32,Poland
    ```

1. Abra um pedido de comando e altere o diretório para a localização dos ficheiros recém-criados. Substitua o CLUSTERNAME, abaixo, com o nome real do seu cluster HBase. Em seguida, execute o código para enviar os ficheiros para o headnode do seu cluster:

    ```cmd
    scp customers.csv customers2.csv createCustomersTable.sql listCustomers.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/tmp
    ```

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para ligar ao seu cluster. Edite o comando abaixo substituindo o CLUSTERNAME pelo nome do seu cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Da sua sessão de ssh, mude o diretório para a localização da ferramenta **psql.** Execute o comando abaixo:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

1. A granel carregue os dados. O código abaixo irá criar a tabela **Clientes** e, em seguida, carregar os dados.

    ```bash
    python psql.py /tmp/createCustomersTable.sql /tmp/customers.csv
    ```

    Após a conclusão da `psql` operação, deverá ver uma mensagem semelhante:

    ```output
    csv columns from database.
    CSV Upsert complete. 3 rows upserted
    Time: 0.081 sec(s)
    ```

1. Pode continuar a usar `psql` para ver o conteúdo da tabela Clientes. Execute o código abaixo:

    ```bash
    python psql.py /tmp/listCustomers.sql
    ```

    Em alternativa, pode utilizar [a concha HBase](./query-hbase-with-hbase-shell.md)ou [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md) para consultar os dados.

1. Faça upload de dados adicionais. Agora que a mesa já existe, o comando especifica a tabela. Execute o comando abaixo:

    ```bash
    python psql.py -t CUSTOMERS /tmp/customers2.csv
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Use mapReduce para mesas de carga em massa

Para uma carga de maior produção distribuída sobre o cluster, utilize a ferramenta de carga MapReduce. Este carregador converte primeiro todos os dados em HFiles e, em seguida, fornece os HFiles criados para HBase.

1. Esta secção continua com a sessão ssh, e objetos criados anteriormente. Crie a tabela **Clientes** e **customers.csv** ficheiro conforme necessário usando os passos, acima. Se necessário, restabelece a sua ligação.

1. Truncar o conteúdo da tabela **Clientes.** A partir da sua sessão aberta, execute os comandos abaixo:

    ```bash
    hbase shell
    truncate 'CUSTOMERS'
    exit
    ```

1. Copie o `customers.csv` ficheiro da sua cabeça para o Azure Storage.

    ```bash
    hdfs dfs -put /tmp/customers.csv wasbs:///tmp/customers.csv
    ```

1. Alteração para o diretório de execução para o comando de carga a granel MapReduce:

    ```bash
    cd /usr/hdp/current/phoenix-client
    ```

1. Lance o carregador CSV MapReduce utilizando o `hadoop` comando com o frasco do cliente Phoenix:

    ```bash
    HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar phoenix-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /tmp/customers.csv
    ```

    Uma vez concluído o upload, deverá ver uma mensagem semelhante:

    ```output
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing master protocol: MasterService
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing zookeeper sessionid=0x26f15dcceff02c3
    19/12/18 18:30:57 INFO zookeeper.ZooKeeper: Session: 0x26f15dcceff02c3 closed
    19/12/18 18:30:57 INFO zookeeper.ClientCnxn: EventThread shut down
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Incremental load complete for table=CUSTOMERS
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Removing output directory /tmp/50254426-aba6-400e-88eb-8086d3dddb6
    ```

1. Para utilizar o MapReduce com o Azure Data Lake Storage, localize o diretório de raiz de armazenamento de data lake, que é o `hbase.rootdir` valor em `hbase-site.xml` . No seguinte comando, o diretório de raiz de armazenamento de data lake é `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1` . Neste comando, especifique a entrada e as pastas de saída do Data Lake como parâmetros:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

1. Para consultar e visualizar os dados, pode utilizar **o psql** como descrito anteriormente. Também pode utilizar [a concha HBase,](./query-hbase-with-hbase-shell.md)ou [Apache Zeppelin.](./apache-hbase-phoenix-zeppelin.md)

## <a name="recommendations"></a>Recomendações

* Utilize o mesmo meio de armazenamento tanto para as pastas de entrada como para a saída, quer para o armazenamento de Azure Storage (WASB) quer para o Armazenamento do Lago de Dados Azure (ADL). Para transferir dados do Azure Storage para o Data Lake Storage, pode utilizar o `distcp` comando:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Use nós de trabalhador de maior dimensão. Os processos de mapa da cópia a granel MapReduce produzem grandes quantidades de saída temporária que preenchem o espaço não-DFS disponível. Para uma grande quantidade de carregamento a granel, utilize nós de trabalhador de maior dimensão. O número de nós de trabalhadores que atribui ao seu cluster afeta diretamente a velocidade de processamento.

* Divida os ficheiros de entrada em pedaços ~10-GB. O carregamento a granel é uma operação intensiva de armazenamento, por isso dividir os seus ficheiros de entrada em vários pedaços resulta num melhor desempenho.

* Evite pontos de acesso ao servidor da região. Se a tecla de linha estiver a aumentar monotonicamente, as gravações sequenciais da HBase podem induzir o hotspotting do servidor da região. *Salgar* a tecla de linha reduz as gravações sequenciais. Phoenix fornece uma maneira de salgar transparentemente a chave da linha com um byte de salga para uma determinada tabela, como referenciado abaixo.

## <a name="next-steps"></a>Próximos passos

* [Carregamento de dados em massa com Apache Phoenix](https://phoenix.apache.org/bulk_dataload.html)
* [Use Apache Phoenix com clusters Apache HBase baseados em Linux em HDInsight](../hbase/apache-hbase-query-with-phoenix.md)
* [Mesas Salgadas](https://phoenix.apache.org/salted.html)
* [Gramática apache fénix](https://phoenix.apache.org/language/index.html)
