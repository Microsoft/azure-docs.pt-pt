---
title: Carregamento em Apache Phoenix com psql - Azure HDInsight em massa
description: Utilize a ferramenta de psql para carregar o carregamento em massa para tabelas de Phoenix.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ashishth
ms.openlocfilehash: fe6705dc3dedd521357f924dd433c7eacf88ba84
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718638"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Carregamento de dados em massa para o Apache Phoenix com psql

[O Apache Phoenix](https://phoenix.apache.org/) é uma código-fonte aberto, paralela em massa base de dados relacional, criado nos [Apache HBase](../hbase/apache-hbase-overview.md). Phoenix fornece consultas de tipo SQL através de HBase. Phoenix utiliza controladores JDBC para permitir aos utilizadores criar, eliminar e alterar as tabelas SQL, índices, Exibições e sequências e linhas de upsert individualmente e em massa. Phoenix utiliza compilação nativo do noSQL, em vez de utilizar o MapReduce para compilar consultas, para criar aplicativos de baixa latência sobre o HBase. Phoenix adiciona coprocessadores para suportar a execução do código fornecido pelo cliente no espaço de endereços de servidor, executando o código localizado conjuntamente com os dados. Isso minimiza a transferência de dados de cliente/servidor.  Para trabalhar com dados com o Phoenix no HDInsight, primeiro crie tabelas e, em seguida, carregar dados para eles.

## <a name="bulk-loading-with-apache-phoenix"></a>Carregamento com o Apache Phoenix em massa

Existem várias formas de obter dados no HBase, incluindo através de APIs, uma tarefa de MapReduce com TableOutputFormat, cliente ou inserir os dados manualmente utilizando a shell de HBase. Phoenix fornece dois métodos para carregar dados do CSV para tabelas de Phoenix: um cliente com o nome de ferramenta de carregamento `psql`e uma ferramenta de carregamento em massa baseada em MapReduce.

O `psql` ferramenta é o único thread e é mais adequada para carregar em megabytes ou gigabytes de dados. Todos os ficheiros CSV para ser carregado tem de ter a extensão de ficheiro ". csv".  Também pode especificar ficheiros de script SQL no `psql` linha de comandos com a extensão de arquivo ". SQL".

Em massa ao carregar com o MapReduce é utilizada para muito maiores volumes de dados, normalmente em cenários de produção, como MapReduce usa vários threads.

Antes de iniciar o carregamento de dados, certifique-se de que o Phoenix está ativado e que as definições de tempo limite da consulta são os esperados.  Aceder ao seu cluster do HDInsight [Apache Ambari](https://ambari.apache.org/) dashboard, selecione o HBase e, em seguida, no separador de configuração.  Desloque para baixo para verificar se o Apache Phoenix está definido `enabled` conforme mostrado:

![Definições de Cluster de HDInsight Apache Phoenix](./media/apache-hbase-phoenix-psql/ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Utilize `psql` para tabelas de carregamento em massa

1. Criar uma nova tabela, em seguida, guarde a consulta com nome de ficheiro `createCustomersTable.sql`.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

2. Copiar um ficheiro CSV (conteúdo de exemplo mostrado) como `customers.csv` num `/tmp/` diretório para carregamento na tabela criada recentemente.  Utilize o `hdfs` comandos para copiar o ficheiro CSV para a sua localização de origem pretendido.

    ```
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,Norway
    ... 4997 more rows 
    ```

    ```bash
    hdfs dfs -copyToLocal /example/data/customers.csv /tmp/
    ```

3. Criar uma consulta SQL SELECT para verificar os dados de entrada carregado corretamente, em seguida, guarde a consulta com nome de ficheiro `listCustomers.sql`. Pode usar qualquer consulta SQL.
     ```sql
    SELECT Name, Income from Customers group by Country;
    ```

4. Em massa carregar os dados, abrindo uma *novo* janela de comandos do Hadoop. Em primeiro lugar mude para a localização do diretório de execução com o `cd` de comandos e, em seguida, utilizar o `psql` ferramenta (Python `psql.py` comando). 

    O exemplo seguinte espera que copiou a `customers.csv` ficheiro a partir de uma conta de armazenamento para o seu diretório temporário local com `hdfs` como no passo 2 acima.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin

    python psql.py ZookeeperQuorum createCustomersTable.sql /tmp/customers.csv listCustomers.sql
    ```

    > [!NOTE]   
    > Para determinar a `ZookeeperQuorum` atribua um nome, localize o [Apache ZooKeeper](https://zookeeper.apache.org/) cadeia de quórum no ficheiro `/etc/hbase/conf/hbase-site.xml` com o nome da propriedade `hbase.zookeeper.quorum`.

5. Depois do `psql` operação é concluída, deverá ver uma mensagem na janela de comando:

    ```
    CSV Upsert complete. 5000 rows upserted
    Time: 4.548 sec(s)
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Utilizar o MapReduce para tabelas de carregamento em massa

Para o carregamento de um maior débito distribuído por cluster, utilize a ferramenta de carga do MapReduce. Este carregador primeiro converte todos os dados em HFiles e, em seguida, fornece o HFiles criado para HBase.

1. Inicie o carregador de MapReduce de CSV, utilizando o `hadoop` comando com o jar do cliente de Phoenix:

    ```bash
    hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table CUSTOMERS --input /data/customers.csv
    ```

2. Criar uma nova tabela com uma instrução de SQL, tal como acontece com `CreateCustomersTable.sql` anterior no passo 1.

3. Para verificar o esquema da sua tabela, execute `!describe inputTable`.

4. Determinar o caminho de localização aos seus dados de entrada, como o exemplo `customers.csv` ficheiro. Os ficheiros de entrada podem ser na sua conta de armazenamento WASB/ADLS. Neste cenário de exemplo, os ficheiros de entrada estão no `<storage account parent>/inputFolderBulkLoad` diretório.

5. Mude para o diretório de execução do comando de carga do MapReduce em massa:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

6. Localize seu `ZookeeperQuorum` valor numa `/etc/hbase/conf/hbase-site.xml`, com o nome da propriedade `hbase.zookeeper.quorum`.

7. Configurar o caminho da classe e execute o `CsvBulkLoadTool` ferramenta comando:

    ```bash
    /usr/hdp/current/phoenix-client$ HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /inputFolderBulkLoad/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure
    ```

8. Para utilizar o MapReduce com o armazenamento do Azure Data Lake, localize o diretório de raiz de armazenamento do Data Lake, o que é o `hbase.rootdir` valor em `hbase-site.xml`. No comando seguinte, o diretório de raiz de armazenamento do Data Lake é `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`. Neste comando, especifique a entrada de armazenamento do Data Lake e pastas como parâmetros de saída:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

## <a name="recommendations"></a>Recomendações

* Utilize o mesmo meio de armazenamento para pastas de entrada e saídas, o armazenamento do Azure (WASB) ou o Azure Data Lake Storage (ADL). Para transferir dados do armazenamento do Azure para armazenamento do Data Lake, pode utilizar o `distcp` comando:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Utilize nós de trabalho de tamanho maior. Os processos de mapa da cópia em massa MapReduce produzem grandes quantidades de saída temporária preencher o espaço não DFS disponíveis. Para uma grande quantidade de carregamento em massa, utilize nós de trabalho mais e tamanho maior. O número de nós de trabalho que é atribuir diretamente ao seu cluster afeta a velocidade de processamento.

* Divida ficheiros de entrada em partes de ~ 10 GB. Carregamento em massa é uma operação de armazenamento intensivas, então, dividindo os ficheiros de entrada em vários segmentos resulta num melhor desempenho.

* Evite pontos ativos do servidor de região. Se a sua chave de linha forma monótona está aumentando, escritas sequenciais do HBase podem induza hotspotting de servidor de região. *Inclusão de Salt* a chave de linha reduz escritas sequenciais. Phoenix fornece uma forma de forma transparente salt a chave de linha com um byte de inclusão de Salt para uma tabela específica, conforme referenciado abaixo.

## <a name="next-steps"></a>Passos Seguintes

* [Dados em massa ao carregar com o Apache Phoenix](https://phoenix.apache.org/bulk_dataload.html)
* [Utilizar Apache Phoenix com clusters do Apache HBase baseado em Linux no HDInsight](../hbase/apache-hbase-phoenix-squirrel-linux.md)
* [Tabelas de Salt](https://phoenix.apache.org/salted.html)
* [Apache Phoenix gramática](https://phoenix.apache.org/language/index.html)
