---
title: Carregamento a granel em Apache Phoenix usando psql - Azure HDInsight
description: Utilize a ferramenta psql para carregar dados de carga a granel em tabelas Apache Phoenix em Azure HDInsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 845c4a62aee04a8acdc645ba4c41f1f5496537c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75552615"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Carregamento de dados em massa para o Apache Phoenix com psql

[Apache Phoenix](https://phoenix.apache.org/) é uma base de dados relacional massivamente paralela construída em [Apache HBase](../hbase/apache-hbase-overview.md). Phoenix fornece consultas semelhantes a SQL sobre HBase. A Phoenix utiliza os condutores JDBC para permitir que os utilizadores criem, eliminem e alterem tabelas, índices, vistas e sequências de SQL e linhas de subida individual mente e a granel. Phoenix usa compilação nativa noSQL em vez de usar mapReduce para compilar consultas, para criar aplicações de baixa latência em cima de HBase. A Phoenix adiciona coprocessadores para suportar o código fornecido pelo cliente no espaço de endereço do servidor, executando o código co-localizado com os dados. Isto minimiza a transferência de dados cliente/servidor.  Para trabalhar com dados usando phoenix no HDInsight, primeiro crie tabelas e, em seguida, carregue os dados neles.

## <a name="bulk-loading-with-apache-phoenix"></a>Carregamento a granel com Apache Phoenix

Existem várias formas de obter dados para a HBase, incluindo a utilização de APIs do cliente, um trabalho MapReduce com TableOutputFormat ou a inserção dos dados manualmente utilizando a concha HBase. A Phoenix fornece dois métodos para carregar dados de CSV em tabelas Phoenix: uma ferramenta de carregamento de clientes chamada `psql`, e uma ferramenta de carga a granel baseada em MapReduce.

A `psql` ferramenta é de roscar-se e é mais adequada para carregar megabytes ou gigabytes de dados. Todos os ficheiros CSV a carregar devem ter a extensão do ficheiro '.csv'.  Também pode especificar ficheiros de `psql` script SQL na linha de comando com a extensão do ficheiro '.sql'.

O carregamento a granel com mapReduce é usado para volumes de dados muito maiores, tipicamente em cenários de produção, uma vez que mapReduce usa vários fios.

Antes de começar a carregar dados, verifique se a Phoenix está ativada e que as definições de tempo de tempo de consulta são como esperado.  Aceda ao painel [apache Ambari](https://ambari.apache.org/) do cluster HDInsight, selecione HBase e, em seguida, o separador Configuração.  Desça para verificar se apache `enabled` Phoenix está definido como mostrado:

![Configurações do cluster Apache Phoenix HDInsight](./media/apache-hbase-phoenix-psql/apache-ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Utilizar `psql` para carregar tabelas a granel

1. Crie um `createCustomersTable.sql`ficheiro chamado e copie o código abaixo no ficheiro. Em seguida, guarde e feche o ficheiro.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

1. Crie um `listCustomers.sql`ficheiro chamado e copie o código abaixo no ficheiro. Em seguida, guarde e feche o ficheiro.

    ```sql
    SELECT * from Customers;
    ```

1. Crie um `customers.csv`ficheiro chamado e copie o código abaixo no ficheiro. Em seguida, guarde e feche o ficheiro.

    ```txt
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,42,Norway
    ```

1. Crie um `customers2.csv`ficheiro chamado e copie o código abaixo no ficheiro. Em seguida, guarde e feche o ficheiro.

    ```txt
    4,Nicolle,180000.0,22,US
    5,Kate,210000.5,24,Canada
    6,Ben,45000.0,32,Poland
    ```

1. Abra um pedido de comando e mude de diretório para a localização dos ficheiros recém-criados. Substitua o CLUSTERNAME, abaixo, com o nome real do seu cluster HBase. Em seguida, execute o código para fazer o upload dos ficheiros para o headnode do seu cluster:

    ```cmd
    scp customers.csv customers2.csv createCustomersTable.sql listCustomers.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/tmp
    ```

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se ligar ao seu cluster. Editar o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, introduzir o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Da sua sessão de ssh, mude de diretório para a localização da ferramenta **psql.** Executar o comando abaixo:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

1. Carregue os dados em massa. O código abaixo irá criar a tabela **Clientes** e, em seguida, carregar os dados.

    ```bash
    python psql.py /tmp/createCustomersTable.sql /tmp/customers.csv
    ```

    Depois `psql` de concluída a operação, deverá ver uma mensagem semelhante à seguinte:

    ```output
    csv columns from database.
    CSV Upsert complete. 3 rows upserted
    Time: 0.081 sec(s)
    ```

1. Pode continuar a `psql` utilizar para ver o conteúdo da tabela Clientes. Executar o código abaixo:

    ```bash
    python psql.py /tmp/listCustomers.sql
    ```

    Em alternativa, pode utilizar a [concha HBase](./query-hbase-with-hbase-shell.md), ou [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md) para consultar os dados.

1. Faça upload de dados adicionais. Agora que a mesa já existe, o comando especifica a mesa. Executar o comando abaixo:

    ```bash
    python psql.py -t CUSTOMERS /tmp/customers2.csv
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Utilizar mapReduzir para tabelas de carga a granel

Para uma carga de maior carga distribuída sobre o cluster, utilize a ferramenta de carga MapReduce. Este carregador converte primeiro todos os dados em HFiles e, em seguida, fornece os HFiles criados para HBase.

1. Esta secção continua com a sessão ssh, e objetos criados mais cedo. Crie a tabela **Clientes** e o ficheiro **clientes.csv** conforme necessário usando os passos, acima. Se necessário, restabelecer a sua ligação ssh.

1. Trunca o conteúdo da tabela **Clientes.** A partir da sua sessão aberta, execute os comandos abaixo:

    ```bash
    hbase shell
    truncate 'CUSTOMERS'
    exit
    ```

1. Copie `customers.csv` o ficheiro do seu cabeçada para o Armazenamento Azure.

    ```bash
    hdfs dfs -put /tmp/customers.csv wasbs:///tmp/customers.csv
    ```

1. Alterar o diretório de execução do MapReduce comando de carga a granel:

    ```bash
    cd /usr/hdp/current/phoenix-client
    ```

1. Lance o CSV MapReduce loader utilizando o `hadoop` comando com o jarro de cliente Phoenix:

    ```bash
    HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar phoenix-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /tmp/customers.csv
    ```

    Uma vez que o upload esteja concluído, deve ver uma mensagem semelhante à seguinte:

    ```output
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing master protocol: MasterService
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing zookeeper sessionid=0x26f15dcceff02c3
    19/12/18 18:30:57 INFO zookeeper.ZooKeeper: Session: 0x26f15dcceff02c3 closed
    19/12/18 18:30:57 INFO zookeeper.ClientCnxn: EventThread shut down
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Incremental load complete for table=CUSTOMERS
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Removing output directory /tmp/50254426-aba6-400e-88eb-8086d3dddb6
    ```

1. Para utilizar o MapReduce com o Armazenamento do Lago de Dados `hbase.rootdir` Azure, localize o diretório raiz de armazenamento de data lake, que é o valor em `hbase-site.xml`. No comando seguinte, o diretório raiz `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`de armazenamento de data lake é . Neste comando, especifique as pastas de entrada e saída de dados do Lago como parâmetros:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

1. Para consultar e ver os dados, pode usar **o psql** como descrito anteriormente. Também pode utilizar [a concha HBase,](./query-hbase-with-hbase-shell.md)ou [Apache Zeppelin.](./apache-hbase-phoenix-zeppelin.md)

## <a name="recommendations"></a>Recomendações

* Utilize o mesmo meio de armazenamento para pastas de entrada e saída, quer o Armazenamento Azure (WASB) quer o Azure Data Lake Storage (ADL). Para transferir dados do Armazenamento Azure para o `distcp` Armazenamento de Data Lake, pode utilizar o comando:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Use nódeos de trabalho de tamanho maior. Os processos de mapa da cópia MapReduce produzem grandes quantidades de saída temporária que preenchem o espaço não DFS disponível. Para uma grande quantidade de carga a granel, utilize mais e maiores nós de trabalhador. O número de nós de trabalhador que aloca ao seu cluster afeta diretamente a velocidade de processamento.

* Divida os ficheiros de entrada em pedaços de ~10 GB. O carregamento a granel é uma operação intensiva de armazenamento, por isso dividir os seus ficheiros de entrada em vários pedaços resulta num melhor desempenho.

* Evite os hotspots do servidor da região. Se a chave da linha estiver a aumentar monotonicamente, as escritas sequenciais hBase podem induzir o hotspotting do servidor da região. *Salgar* a chave da linha reduz os escritos sequenciais. Phoenix fornece uma maneira de salgar transparentemente a chave da linha com um byte de salga para uma determinada tabela, como referência abaixo.

## <a name="next-steps"></a>Passos seguintes

* [Carregamento de dados a granel com Apache Phoenix](https://phoenix.apache.org/bulk_dataload.html)
* [Use Apache Phoenix com clusters Apache HBase baseados em Linux em HDInsight](../hbase/apache-hbase-query-with-phoenix.md)
* [Mesas salgadas](https://phoenix.apache.org/salted.html)
* [Gramática Apache Phoenix](https://phoenix.apache.org/language/index.html)
