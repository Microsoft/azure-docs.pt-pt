---
title: Apache Spark & Hive-conector de depósito do hive – Azure HDInsight
description: Saiba como integrar Apache Spark e Apache Hive com o conector do depósito do hive no Azure HDInsight.
author: nakhanha
ms.author: nakhanha
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2019
ms.openlocfilehash: 2448550cf35f92bc8d91bc6ad9d5b22cc90b5ae0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494302"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Integrar Apache Spark e Apache Hive com o conector do depósito do hive

O conector do Apache Hive Warehouse (HWC) é uma biblioteca que permite que você trabalhe mais facilmente com Apache Spark e Apache Hive ao dar suporte a tarefas como a movimentação de dados entre os dataframes do Spark e as tabelas do hive, além de direcionar dados de streaming do Spark para tabelas do hive. O conector do depósito do hive funciona como uma ponte entre o Spark e o hive. Ele dá suporte a escalabilidade, Java e Python para desenvolvimento.

O conector do depósito do hive permite que você aproveite os recursos exclusivos do hive e do Spark para criar aplicativos de Big data poderosos. O Apache Hive oferece suporte para transações de banco de dados atômicas, consistentes, isoladas e duráveis (ACID). Para obter mais informações sobre ACID e transações no hive, consulte [transações do hive](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). O hive também oferece controles de segurança detalhados por meio do Apache Ranger e o processamento analítico de baixa latência não disponível no Apache Spark.

Apache Spark, tem uma API de streaming estruturada que fornece recursos de streaming não disponíveis no Apache Hive. A partir do HDInsight 4,0, Apache Spark 2.3.1 e Apache Hive 3.1.0 têm metastores separados, o que pode dificultar a interoperabilidade. O conector do depósito do hive torna mais fácil usar o Spark e o hive juntos. A biblioteca HWC carrega dados de daemons do LLAP para o Spark executores em paralelo, tornando-o mais eficiente e escalonável do que usar uma conexão JDBC padrão do Spark para o hive.

![arquitetura do conector de depósito do hive](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Algumas das operações com suporte do conector de depósito do hive são:

* Descrevendo uma tabela
* Criando uma tabela para dados formatados por ORC
* Selecionando dados do hive e recuperando um dataframe
* Gravando um dataframe no hive no lote
* Executando uma instrução Update do hive
* Lendo dados de tabela do hive, transformando-os no Spark e gravando-os em uma nova tabela do hive
* Escrevendo um dataframe ou um fluxo do Spark para o hive usando HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Instalação do conector do depósito do hive

Siga estas etapas para configurar o conector de depósito do hive entre um Spark e um cluster de consulta interativa no Azure HDInsight:

### <a name="create-clusters"></a>Criar clusters

1. Crie um cluster HDInsight Spark **4,0** com uma conta de armazenamento e uma rede virtual do Azure personalizada. Para obter informações sobre como criar um cluster em uma rede virtual do Azure, consulte [Adicionar o HDInsight a uma rede virtual existente](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet).

1. Crie um cluster LLAP (consulta interativa do HDInsight) **4,0** com a mesma conta de armazenamento e rede virtual do Azure que o cluster Spark.

### <a name="modify-hosts-file"></a>Modificar arquivo de hosts

Copie as informações de nó do arquivo `/etc/hosts` em headnode0 do seu cluster de consulta interativa e concatene as informações para o arquivo de `/etc/hosts` no headnode0 do cluster Spark. Esta etapa permitirá que o cluster Spark resolva os endereços IP dos nós no cluster de consulta interativa. Exiba o conteúdo do arquivo atualizado com `cat /etc/hosts`. A saída final deve ter uma aparência semelhante à mostrada na captura de tela abaixo.

![arquivo de hosts do conector de depósito do hive](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

### <a name="gather-preliminary-information"></a>Reunir informações preliminares

#### <a name="from-your-interactive-query-cluster"></a>Do seu cluster de consulta interativa

1. Navegue até o Apache Ambari do cluster home page usando `https://LLAPCLUSTERNAME.azurehdinsight.net` em que `LLAPCLUSTERNAME` é o nome do seu cluster de consulta interativa.

1. Navegue até **hive** > **configurações** > **avançado** > **Hive avançado-site** > **Hive. Zookeeper. quorum** e anote o valor. O valor pode ser semelhante a: `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`.

1. Navegue até **hive** > **configurações** > **avançado** > **geral** > **Hive. metastore. URIs** e observe o valor. O valor pode ser semelhante a: `thrift://hn0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`.

#### <a name="from-your-apache-spark-cluster"></a>Do seu cluster Apache Spark

1. Navegue até o Apache Ambari do cluster home page usando `https://SPARKCLUSTERNAME.azurehdinsight.net` em que `SPARKCLUSTERNAME` é o nome do seu cluster Apache Spark.

1. Navegue até **hive** > **configurações** > **avançado** > **Hive-Interactive-site** > **Hive. LLAP. daemon. Service. hosts** e anote o valor. O valor pode ser semelhante a: `@llap0`.

### <a name="configure-spark-cluster-settings"></a>Definir configurações de cluster do Spark

Em sua interface do usuário da Web do Spark Ambari, navegue até **Spark2** > **configs** > **Personalizar Spark2-padrões**.

![Configuração do Apache Ambari Spark2](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

Selecione **Adicionar Propriedade...** conforme necessário para adicionar/atualizar o seguinte:

| Chave | Valor |
|----|----|
|`spark.hadoop.hive.llap.daemon.service.hosts`|O valor obtido anteriormente de **Hive. LLAP. daemon. Service. hosts**.|
|`spark.sql.hive.hiveserver2.jdbc.url`|`jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2`. Defina como a cadeia de conexão JDBC, que se conecta ao Hiveserver2 no cluster de consulta interativa. Substitua `LLAPCLUSTERNAME` pelo nome do seu cluster de consulta interativa. Substitua `PWD` pela senha real.|
|`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. Defina como um diretório de preparo adequado compatível com HDFS. Se você tiver dois clusters diferentes, o diretório de preparo deverá ser uma pasta no diretório de preparo da conta de armazenamento do cluster LLAP para que HiveServer2 tenha acesso a ela.  Substitua `STORAGE_ACCOUNT_NAME` pelo nome da conta de armazenamento que está sendo usada pelo cluster e `STORAGE_CONTAINER_NAME` pelo nome do contêiner de armazenamento.|
|`spark.datasource.hive.warehouse.metastoreUri`|O valor obtido anteriormente de **Hive. metastore. URIs**.|
|`spark.security.credentials.hiveserver2.enabled`|`false` para o modo de implantação do cliente YARN.|
|`spark.hadoop.hive.zookeeper.quorum`|O valor obtido anteriormente de **Hive. Zookeeper. quorum**.|

Salve as alterações e reinicie os componentes conforme necessário.

## <a name="using-the-hive-warehouse-connector"></a>Usando o conector do depósito do hive

### <a name="connecting-and-running-queries"></a>Conectando e executando consultas

Você pode escolher entre alguns métodos diferentes para se conectar ao seu cluster de consulta interativa e executar consultas usando o conector do depósito do hive. Os métodos com suporte incluem as seguintes ferramentas:

* [Spark-Shell](../spark/apache-spark-shell.md)
* PySpark
* Spark – enviar
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Todos os exemplos fornecidos neste artigo serão executados por meio do Spark-Shell.

Para iniciar uma sessão do Spark-Shell, execute as seguintes etapas:

1. SSH no cabeçalho para o cluster de Apache Spark. Para obter mais informações sobre como se conectar ao cluster com o SSH, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Digite o seguinte comando para iniciar o Shell do Spark:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.2.1-8.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

    Você verá uma mensagem de boas-vindas e um `scala>` prompt em que você pode inserir comandos.

1. Depois de iniciar o Spark-Shell, uma instância do conector do depósito do hive pode ser iniciada usando os seguintes comandos:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Conectando e executando consultas em clusters Enterprise Security Package (ESP)

O Enterprise Security Package (ESP) fornece recursos de nível corporativo como autenticação baseada em Active Directory, suporte a vários usuários e controle de acesso baseado em função para clusters Apache Hadoop no Azure HDInsight. Para obter mais informações sobre o ESP, consulte [usar o Enterprise Security Package no HDInsight](../domain-joined/apache-domain-joined-architecture.md).

1. SSH no cabeçalho para o cluster de Apache Spark. Para obter mais informações sobre como se conectar ao cluster com o SSH, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Digite `kinit` e faça logon com um usuário de domínio.

1. Inicie o Spark-shell com a lista completa de parâmetros de configuração, conforme mostrado abaixo. Todos os valores em letras maiúsculas entre colchetes angulares devem ser especificados com base no seu cluster. Se você precisar descobrir os valores a serem inseridos para qualquer um dos parâmetros abaixo, consulte a seção sobre [instalação do conector do depósito do hive](#hive-warehouse-connector-setup).:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>Criando dataframes do Spark de consultas do hive

Os resultados de todas as consultas que usam a biblioteca HWC são retornados como um dataframe. Os exemplos a seguir demonstram como criar uma consulta básica.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Os resultados da consulta são os quadros de no Spark, que podem ser usados com bibliotecas do Spark como MLIB e SparkSQL.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Gravando quadros de tabela do Spark em tabelas do hive

O Spark não dá suporte nativo à gravação em tabelas ACID gerenciadas do hive. No entanto, usando HWC, você pode gravar qualquer dataframe em uma tabela do hive. Você pode ver essa funcionalidade em trabalho no exemplo a seguir:

1. Crie uma tabela chamada `sampletable_colorado` e especifique suas colunas usando o seguinte comando:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Filtre a tabela `hivesampletable` em que a coluna `state` é igual a `Colorado`. Essa consulta da tabela hive é retornada como um dataframe do Spark. Em seguida, o dataframe é salvo na tabela Hive `sampletable_colorado` usando a função `write`.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

1. Exiba os resultados com o seguinte comando:

    ```scala
    hive.table("sampletable_colorado").show()
    ```
    
    ![conector do depósito do hive Mostrar tabela Hive](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Gravações de streaming estruturado

Usando o conector do depósito do hive, você pode usar o streaming do Spark para gravar dados em tabelas do hive.

Siga as etapas abaixo para criar um exemplo de conector de depósito do hive que ingere dados de um fluxo do Spark na porta localhost 9999 em uma tabela do hive.

1. Siga as etapas em [conectando e executando consultas](#connecting-and-running-queries).

1. Inicie o fluxo do Spark com o seguinte comando:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Gere dados para o fluxo do Spark que você criou, executando as seguintes etapas:
    1. Abra uma segunda sessão SSH no mesmo cluster do Spark.
    1. No prompt de comando, digite `nc -lk 9999`. Esse comando usa o utilitário netcat para enviar dados da linha de comando para a porta especificada.

1. Retorne à primeira sessão SSH e crie uma nova tabela do hive para armazenar os dados de streaming. No Spark-Shell, digite o seguinte comando:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Em seguida, grave os dados de streaming na tabela recém-criada usando o seguinte comando:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > As opções `metastoreUri` e `database` devem ser definidas manualmente no momento devido a um problema conhecido no Apache Spark. Para obter mais informações sobre esse problema, consulte [Spark-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Retorne à segunda sessão SSH e insira os seguintes valores:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Retorne à primeira sessão SSH e observe a atividade Brief. Use o seguinte comando para exibir os dados:

    ```scala
    hive.table("stream_table").show()
    ```

Use **Ctrl + C** para interromper o netcat na segunda sessão SSH. Use `:q` para sair do Spark-Shell na primeira sessão SSH.

### <a name="securing-data-on-spark-esp-clusters"></a>Protegendo dados em clusters do Spark

1. Crie uma tabela `demo` com alguns dados de exemplo inserindo os seguintes comandos:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Exiba o conteúdo da tabela com o comando a seguir. Antes de aplicar a política, a tabela `demo` mostra a coluna completa.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![tabela de demonstração antes de aplicar a política de Ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Aplique uma política de mascaramento de coluna que mostre apenas os últimos quatro caracteres da coluna.  
    1. Acesse a interface do usuário do administrador do Ranger em `https://CLUSTERNAME.azurehdinsight.net/ranger/`.
    1. Clique no serviço de Hive para o cluster em **Hive**.
        ![Ranger Service Manager](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Clique na guia **mascaramento** e **adicione nova política**

        ![lista de políticas do hive do conector do depósito do hive Ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    a. Forneça um nome de política desejado. Selecione banco de dados: **padrão**, tabela Hive: **demonstração**, coluna Hive: **nome**, usuário: **rsadmin2**, tipos de acesso: **Select**e **máscara parcial: mostrar últimos 4** no menu de **opção Selecionar mascaramento** . Clique em **Adicionar**.
                ![criar](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png) de política
1. Exiba o conteúdo da tabela novamente. Depois de aplicar a política de Ranger, podemos ver apenas os últimos quatro caracteres da coluna.

    ![tabela de demonstração após a aplicação da política de Ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Passos seguintes

* [Utilizar uma Consulta Interativa com o HDInsight](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started).
* [Exemplos de interação com o conector de depósito do hive usando Zeppelin, Livy, Spark-Submit e pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
