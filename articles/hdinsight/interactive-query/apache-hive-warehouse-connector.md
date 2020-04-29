---
title: Apache Spark & Hive - Hive Warehouse Connector - Azure HDInsight
description: Saiba como integrar a Apache Spark e a Apache Hive com o Conector de Armazém hive no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: 77623a89e52a5e15fbb4159ff49d9377e53e7d4c
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509538"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Integre a Apache Spark e a Apache Hive com o Conector de Armazém da Colmeia

O Conector apache hive warehouse (HWC) é uma biblioteca que lhe permite trabalhar mais facilmente com Apache Spark e Apache Hive. Mais fácil suportando tarefas como mover dados entre dataframes de faíscas e tabelas de colmeias. E direcionando os dados de streaming de Faíscas para as mesas da Colmeia. O Conector hive Warehouse funciona como uma ponte entre A Faísca e a Colmeia. Apoia Scala, Java e Python para o desenvolvimento.

O Conector de Armazém hive permite-lhe aproveitar as características únicas da Hive e Spark. Funcionalidades usadas para construir poderosas aplicações de big data. A Apache Hive oferece suporte para transações de base de dados que são atómicas, consistentes, isoladas e duráveis (ACID). Para obter mais informações sobre acid e transações na Colmeia, consulte [As Transações da Colmeia.](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions) A Hive também oferece controlos de segurança detalhados através de Apache Ranger e De baixa latência analítica processamento não disponível em Apache Spark.

Apache Spark, tem um API de streaming estruturado que dá capacidades de streaming não disponíveis na Colmeia Apache. Começando com HDInsight 4.0, Apache Spark 2.3.1 e Apache Hive 3.1.0 têm metastores separados. Estas metastores separadas podem dificultar a interoperabilidade. O Conector de Armazém hive facilita a utilização de Faíscae e Colmeia juntos. A biblioteca HWC carrega dados de daemons LLAP (Low Latency Analytical Processing) a executores de faíscas em paralelo. Esta ação torna-a mais eficiente e adaptável do que usar uma ligação JDBC padrão de Spark a Hive.

![arquitetura de conector de armazém de colmeia](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Algumas das operações apoiadas pelo Conector de Armazém hive são:

* Descrevendo uma mesa
* Criação de uma tabela para dados formatados ORC
* Selecionar dados da Colmeia e recuperar um DataFrame
* Escrever um DataFrame para a Colmeia em lote
* Executar uma declaração de atualização da Colmeia
* Ler dados de tabela da Hive, transformá-lo em Faísca, e escrevê-lo para uma nova mesa da Colmeia
* Escrever um dataframe ou fluxo de faíscas para a Colmeia usando hivestreaming

## <a name="hive-warehouse-connector-setup"></a>Configuração do conector do Armazém hive

Siga estes passos para configurar o Conector de Armazém hive entre um cluster de faíscas e consultas interativas em Azure HDInsight:

### <a name="create-clusters"></a>Criar clusters

1. Crie um cluster HDInsight Spark **4.0** com uma conta de armazenamento e uma rede virtual Azure personalizada. Para obter informações sobre a criação de um cluster numa rede virtual Azure, consulte [Adicionar HDInsight a uma rede virtual existente](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet).

1. Crie um cluster HDInsight Interactive Query (LLAP) **4.0** com a mesma conta de armazenamento e rede virtual Azure que o cluster Spark.

### <a name="modify-hosts-file"></a>Modificar ficheiro de anfitriões

Copie as informações `/etc/hosts` do nó do ficheiro no headnode0 do seu cluster `/etc/hosts` Deconsulta Interativa e concatena a informação para o ficheiro no headnode0 do seu cluster Spark. Este passo permitirá ao seu cluster Spark resolver endereços IP dos nós no cluster De consulta interativa. Ver o conteúdo do `cat /etc/hosts`ficheiro atualizado com . A saída final deve parecer algo parecido com o que é mostrado na imagem abaixo.

![arquivo de conector de armazém de colmeia](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

### <a name="gather-preliminary-information"></a>Recolher informações preliminares

#### <a name="from-your-interactive-query-cluster"></a>Do seu cluster de consulta interativa

1. Navegue para a página apache Ambari `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` `LLAPCLUSTERNAME` Hive do cluster usando onde está o nome do seu cluster de consulta interativa.

1. Navegue para **Advanced** > **General** > **hive.metastore.uris** e note o valor. O valor pode ser `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`semelhante a: .

1. Navegue para **o local** > avançado de**Advanced hive-site** > **colmeia.zookeeper.quorum** e note o valor. O valor pode ser `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`semelhante a: .

#### <a name="from-your-apache-spark-cluster"></a>Do seu aglomerado de Faíscas Apache

1. Navegue para a página da Colmeia Apache `https://SPARKCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` `SPARKCLUSTERNAME` Ambari do cluster usando onde está o nome do seu cluster Apache Spark.

1. Navegue para **advanced Advanced** > **hive-interactive-site** > **hive.llap.daemon.service.hosts** e note o valor. O valor pode ser `@llap0`semelhante a: .

### <a name="configure-spark-cluster-settings"></a>Configurar as definições do cluster de faíscas

A partir da sua Web UI Spark Ambari, navegue até **Spark2** > **CONFIGS** > **Custom spark2-defaults**.

![Configuração Apache Ambari Spark2](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

Selecione **Adicionar Propriedade...** conforme necessário para adicionar/atualizar o seguinte valor:

| Chave | Valor |
|----|----|
|`spark.hadoop.hive.llap.daemon.service.hosts`|O valor que obteve anteriormente da **hive.llap.daemon.service.hosts**.|
|`spark.sql.hive.hiveserver2.jdbc.url`|`jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2`. Configurado para a cadeia de ligação JDBC, que se conecta ao Hiveserver2 no cluster De consulta interativa. SUBSTITUA `LLAPCLUSTERNAME` com o nome do seu cluster De consulta interativa. Substitua-a `PWD` com a senha real.|
|`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. Definir para um diretório de encenação compatível com HDFS adequado. Se tiver dois clusters diferentes, o diretório de encenação deve ser uma pasta no diretório de encenação da conta de armazenamento do cluster LLAP para que o HiveServer2 tenha acesso ao mesmo.  Substitua-a `STORAGE_ACCOUNT_NAME` com o nome da conta `STORAGE_CONTAINER_NAME` de armazenamento utilizada pelo cluster e com o nome do recipiente de armazenamento.|
|`spark.datasource.hive.warehouse.metastoreUri`|O valor que obteve anteriormente da **hive.metastore.uris**.|
|`spark.security.credentials.hiveserver2.enabled`|`false`para o modo de implantação do cliente YARN.|
|`spark.hadoop.hive.zookeeper.quorum`|O valor que obteve anteriormente da **hive.zookeeper.quorum**.|

Guarde as alterações e reinicie os componentes conforme necessário.

## <a name="using-the-hive-warehouse-connector"></a>Usando o Conector de Armazém da Colmeia

### <a name="connecting-and-running-queries"></a>Consultas de ligação e execução

Pode escolher entre alguns métodos diferentes para se conectar ao seu cluster de Consulta Interativa e executar consultas utilizando o Conector de Armazém hive. Os métodos suportados incluem as seguintes ferramentas:

* [faísca shell](../spark/apache-spark-shell.md)
* PySpark
* spark-submit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Todos os exemplos fornecidos neste artigo serão executados através de uma casca de faísca.

Para iniciar uma sessão de spark-shell, faça os seguintes passos:

1. SSH no nó do cabeça para o seu aglomerado de Faíscas Apache. Para obter mais informações sobre a ligação ao seu cluster com o SSH, consulte [Connect to HDInsight (Apache Hadoop) utilizando SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Introduza o seguinte comando para iniciar a casca de faísca:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

    Verá uma mensagem de boas-vindas e uma `scala>` solicitação onde pode entrar nos comandos.

1. Depois de iniciar a casca de faísca, uma instância de Conector de Armazém hive pode ser iniciada usando os seguintes comandos:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Consultas de ligação e execução em clusters do Pacote de Segurança Empresarial (ESP)

O Pacote de Segurança Empresarial (ESP) fornece capacidades de nível empresarial como a autenticação baseada em Diretório ativo. E suporte multiutilizador, e controlo de acesso baseado em papéis para clusters Apache Hadoop em Azure HDInsight. Para obter mais informações sobre esp, consulte [Use Enterprise Security Package in HDInsight](../domain-joined/apache-domain-joined-architecture.md).

1. SSH no nó do cabeça para o seu aglomerado de Faíscas Apache.

1. Digite `kinit` e faça login com um utilizador de domínio.

1. Inicie a casca de faísca com a lista completa dos parâmetros de configuração, como mostrado abaixo. Todos os valores em todas as letras maiúsculas entre parênteses angulares devem ser especificados com base no seu cluster. Se precisar de descobrir os valores a inserir para qualquer um dos parâmetros abaixo, consulte a secção do [Conector](#hive-warehouse-connector-setup)do Armazém hive .

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>Criação de DataFrames de Faíscas a partir de consultas da Colmeia

Os resultados de todas as consultas que utilizam a biblioteca HWC são devolvidos como DataFrame. Os seguintes exemplos demonstram como criar uma consulta básica.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Os resultados da consulta são Spark DataFrames, que podem ser usados com bibliotecas spark como MLIB e SparkSQL.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Escrever Spark DataFrames para as mesas da Colmeia

A Faísca não apoia a escrita nas mesas acid geridas da Hive. Usando o HWC, no entanto, pode escrever qualquer DataFrame para uma tabela Hive. Pode ver esta funcionalidade a trabalhar no seguinte exemplo:

1. Crie uma `sampletable_colorado` tabela chamada e especifique as suas colunas utilizando o seguinte comando:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Filtrar `hivesampletable` a tabela `state` onde `Colorado`a coluna é igual . Esta consulta da tabela Hive é devolvida como um Spark DataFrame. Em seguida, o DataFrame é `sampletable_colorado` guardado `write` na tabela Hive utilizando a função.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

1. Veja os resultados com o seguinte comando:

    ```scala
    hive.table("sampletable_colorado").show()
    ```

    ![conector de armazém de colmeia mostrar mesa de colmeia](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Streaming estruturado escreve

Utilizando o Conector de Armazém Hive, pode utilizar o streaming Spark para escrever dados em tabelas da Hive.

Siga os passos abaixo para criar um Conector de Armazém da Colmeia. O exemplo ingere dados de um fluxo de faíscas no porto 9999 local numa mesa da Colmeia.

1. Siga os passos sob consultas de [ligação e execução](#connecting-and-running-queries).

1. Inicie o fluxo de faíscas com o seguinte comando:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Gere dados para o fluxo Spark que criou, fazendo os seguintes passos:
    1. Abra uma segunda sessão de SSH no mesmo cluster Spark.
    1. No pedido de `nc -lk 9999`comando, escreva . Este comando `netcat` utiliza o utilitário para enviar dados da linha de comando para a porta especificada.

1. Volte à primeira sessão de SSH e crie uma nova tabela Hive para segurar os dados de streaming. Na casca de faísca, insira o seguinte comando:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Em seguida, escreva os dados de streaming para a tabela recém-criada usando o seguinte comando:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > As `metastoreUri` `database` opções e opções devem ser definidas manualmente devido a um problema conhecido em Apache Spark. Para mais informações sobre este assunto, consulte [SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Volte à segunda sessão de SSH e introduza os seguintes valores:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Volte à primeira sessão de SSH e note a breve atividade. Utilize o seguinte comando para visualizar os dados:

    ```scala
    hive.table("stream_table").show()
    ```

Utilize **ctrl +** `netcat` C para parar na segunda sessão SSH. Utilize `:q` para sair de uma casca de faísca na primeira sessão sHH.

### <a name="securing-data-on-spark-esp-clusters"></a>Obtenção de dados sobre clusters Spark ESP

1. Crie `demo` uma tabela com alguns dados da amostra, inserindo os seguintes comandos:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Veja o conteúdo da tabela com o seguinte comando. Antes de aplicar a `demo` apólice, a tabela mostra a coluna completa.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![mesa de demonstração antes de aplicar política ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Aplique uma política de máscarade coluna que apenas mostre os últimos quatro caracteres da coluna.  
    1. Vá ao Ranger Admin `https://CLUSTERNAME.azurehdinsight.net/ranger/`UI em .
    1. Clique no serviço hive para o seu cluster sob **a Colmeia**.
        ![gestor de serviço ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Clique no **separador Mascarar** e, em seguida, **adicione nova política**

        ![hive armazém cone cector ranger lista política política](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    a. Forneça um nome político. Selecione base de dados: **Predefinido**, Tabela de Colmeias: **demo,** Coluna hive: **nome**, Utilizador: **rsadmin2**, Tipos de acesso: **selecione**- e **máscara parcial: mostrar o último 4** do menu Select **Masking Option.** Clique em **Adicionar**.
                ![criar política](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Veja o conteúdo da mesa novamente. Depois de aplicar a política dos rangers, só podemos ver os últimos quatro caracteres da coluna.

    ![mesa de demonstração após aplicar política ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Passos seguintes

* [Utilizar uma Consulta Interativa com o HDInsight](./apache-interactive-query-get-started.md).
* [Exemplos de interação com o Conector de Armazém hive usando Zeppelin, Livy, spark-submit, e pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
