---
title: Carregamento em massa em Apache Phoenix usando o psql-Azure HDInsight
description: Usar a ferramenta psql para carregar dados de carregamento em massa em tabelas Apache Phoenix no Azure HDInsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ashishth
ms.openlocfilehash: 43465a1c31b953620c45dfe759de7b6e1b4dc3c9
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70917281"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Carregamento de dados em massa para o Apache Phoenix com psql

[Apache Phoenix](https://phoenix.apache.org/) é um banco de dados relacional de software livre, maciçomente paralelo, criado no [Apache HBase](../hbase/apache-hbase-overview.md). O Phoenix fornece consultas semelhantes ao SQL no HBase. O Phoenix usa drivers JDBC para permitir que os usuários criem, excluam e alterem tabelas, índices, exibições e sequências do SQL e Upsert linhas individualmente e em massa. O Phoenix usa a compilação nativa noSQL em vez de usar o MapReduce para compilar consultas, para criar aplicativos de baixa latência sobre o HBase. O Phoenix adiciona coprocessadores para dar suporte à execução de código fornecido pelo cliente no espaço de endereço do servidor, executando o código colocalizado com os dados. Isso minimiza a transferência de dados do cliente/servidor.  Para trabalhar com dados usando Phoenix no HDInsight, primeiro crie tabelas e, em seguida, carregue os dados nelas.

## <a name="bulk-loading-with-apache-phoenix"></a>Carregamento em massa com Apache Phoenix

Há várias maneiras de obter dados no HBase, incluindo o uso de APIs de cliente, um trabalho MapReduce com TableOutputFormat ou a colocação dos dados manualmente usando o Shell do HBase. O Phoenix fornece dois métodos para carregar dados CSV em tabelas Phoenix: uma ferramenta de carregamento `psql`de cliente chamada e uma ferramenta de carregamento em massa baseada em MapReduce.

A `psql` ferramenta é de thread único e é mais adequada para carregar megabytes ou gigabytes de dados. Todos os arquivos CSV a serem carregados devem ter a extensão de arquivo '. csv '.  Você também pode especificar arquivos de script SQL na `psql` linha de comando com a extensão de arquivo '. SQL '.

O carregamento em massa com o MapReduce é usado para volumes de dados muito maiores, normalmente em cenários de produção, já que o MapReduce usa vários threads.

Antes de começar a carregar dados, verifique se Phoenix está habilitado e se as configurações de tempo limite de consulta estão conforme o esperado.  Acesse o painel do [Apache Ambari](https://ambari.apache.org/) do cluster HDInsight, selecione HBase e, em seguida, a guia configuração.  Role para baixo para verificar se Apache Phoenix está definido `enabled` como mostrado:

![Apache Phoenix configurações do cluster HDInsight](./media/apache-hbase-phoenix-psql/apache-ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Usar `psql` para tabelas de carregamento em massa

1. Crie uma nova tabela e salve a consulta com o nome `createCustomersTable.sql`do arquivo.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

2. Copie o arquivo CSV (exemplo de conteúdo mostrado) `customers.csv` como em `/tmp/` um diretório para carregar em sua tabela recém-criada.  Use o `hdfs` comando para copiar o arquivo CSV para o local de origem desejado.

    ```
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,Norway
    ... 4997 more rows 
    ```

    ```bash
    hdfs dfs -copyToLocal /example/data/customers.csv /tmp/
    ```

3. Crie uma consulta SQL SELECT para verificar os dados de entrada carregados corretamente e salve a consulta com o `listCustomers.sql`nome de arquivo. Você pode usar qualquer consulta SQL.
     ```sql
    SELECT Name, Income from Customers group by Country;
    ```

4. Carregue os dados em massa abrindo uma *nova* janela de comando do Hadoop. Primeiro, altere para o local do diretório de `cd` execução com o comando e, `psql` em seguida, `psql.py` use a ferramenta (comando do Python). 

    O exemplo a seguir espera que você copiou `customers.csv` o arquivo de uma conta de armazenamento para seu diretório temporário `hdfs` local usando como na etapa 2 acima.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin

    python psql.py ZookeeperQuorum createCustomersTable.sql /tmp/customers.csv listCustomers.sql
    ```

    > [!NOTE]   
    > Para determinar o `ZookeeperQuorum` nome, localize a cadeia de caracteres de quorum [Apache ZooKeeper](https://zookeeper.apache.org/) no arquivo `/etc/hbase/conf/hbase-site.xml` com `hbase.zookeeper.quorum`o nome da propriedade.

5. Após a `psql` conclusão da operação, você verá uma mensagem na janela de comando:

    ```
    CSV Upsert complete. 5000 rows upserted
    Time: 4.548 sec(s)
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Usar o MapReduce para carregar tabelas em massa

Para o carregamento de taxa de transferência mais alta distribuído pelo cluster, use a ferramenta de carregamento do MapReduce. Esse carregador primeiro converte todos os dados em HFiles e, em seguida, fornece o HFiles criado para o HBase.

1. Inicie o carregador MapReduce do CSV usando o `hadoop` comando com o JAR do cliente Phoenix:

    ```bash
    hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table CUSTOMERS --input /data/customers.csv
    ```

2. Crie uma nova tabela com uma instrução SQL, como `CreateCustomersTable.sql` na etapa 1 anterior.

3. Para verificar o esquema da sua tabela, execute `!describe inputTable`.

4. Determine o caminho do local para os dados de entrada, como o `customers.csv` arquivo de exemplo. Os arquivos de entrada podem estar em sua conta de armazenamento WASB/ADLS. Neste cenário de exemplo, os arquivos de entrada estão no `<storage account parent>/inputFolderBulkLoad` diretório.

5. Altere para o diretório de execução para o comando de carregamento em massa do MapReduce:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

6. Localize seu `ZookeeperQuorum` valor em `/etc/hbase/conf/hbase-site.xml`, com o nome `hbase.zookeeper.quorum`da propriedade.

7. Configure o classpath e execute o comando `CsvBulkLoadTool` da ferramenta:

    ```bash
    /usr/hdp/current/phoenix-client$ HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /inputFolderBulkLoad/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure
    ```

8. Para usar o MapReduce com Azure data Lake Storage, localize o diretório raiz data Lake Storage, que é `hbase.rootdir` o valor `hbase-site.xml`em. No comando a seguir, o diretório raiz Data Lake Storage é `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`. Neste comando, especifique o Data Lake Storage as pastas de entrada e saída como parâmetros:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

## <a name="recommendations"></a>Recomendações

* Use o mesmo meio de armazenamento para as pastas de entrada e saída, o armazenamento do Azure (WASB) ou o Azure Data Lake Storage (ADL). Para transferir dados do armazenamento do Azure para data Lake Storage, você pode usar `distcp` o comando:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Use nós de trabalho de tamanho maior. Os processos de mapa da cópia em massa do MapReduce produzem grandes quantidades de saídas temporárias que preenchem o espaço não DFS disponível. Para uma grande quantidade de carregamento em massa, use nós de trabalho mais amplos e de tamanho maior. O número de nós de trabalho que você aloca para o cluster afeta diretamente a velocidade de processamento.

* Divida os arquivos de entrada em partes de ~ 10 GB. O carregamento em massa é uma operação de uso intensivo de armazenamento, portanto, dividir os arquivos de entrada em várias partes resulta em um melhor desempenho.

* Evite pontos de HotSpot do servidor de região. Se a chave de linha for monotônico aumentando, as gravações sequenciais do HBase poderão induzir a hotspotting do servidor de região. O *Salt* da chave de linha reduz as gravações sequenciais. O Phoenix fornece uma maneira de saltar de forma transparente a chave de linha com um byte de Salt para uma tabela específica, como referenciado abaixo.

## <a name="next-steps"></a>Passos Seguintes

* [Carregamento de dados em massa com Apache Phoenix](https://phoenix.apache.org/bulk_dataload.html)
* [Usar Apache Phoenix com clusters Apache HBase baseados em Linux no HDInsight](../hbase/apache-hbase-phoenix-squirrel-linux.md)
* [Tabelas com Salt](https://phoenix.apache.org/salted.html)
* [Apache Phoenix gramática](https://phoenix.apache.org/language/index.html)
