---
title: Integrar Apache Spark e Apache Hive com o conector do depósito do hive
description: Saiba como integrar Apache Spark e Apache Hive com o conector do depósito do hive no Azure HDInsight.
ms.service: hdinsight
author: nakhanha
ms.author: nakhanha
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: 98b97abf9fe0d8e71f93022dcbfeff0a5339d5a1
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69983073"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Integrar Apache Spark e Apache Hive com o conector do depósito do hive

O conector do Apache Hive Warehouse (HWC) é uma biblioteca que permite que você trabalhe mais facilmente com Apache Spark e Apache Hive ao dar suporte a tarefas como a movimentação de dados entre os dataframes do Spark e as tabelas do hive, além de direcionar dados de streaming do Spark para tabelas do hive. O conector do depósito do hive funciona como uma ponte entre o Spark e o hive. Ele dá suporte a escalabilidade, Java e Python para desenvolvimento.

O conector do depósito do hive permite que você aproveite os recursos exclusivos do hive e do Spark para criar aplicativos de Big data poderosos. O Apache Hive oferece suporte para transações de banco de dados atômicas, consistentes, isoladas e duráveis (ACID). Para obter mais informações sobre ACID e transações no hive, consulte [transações do hive](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). O hive também oferece controles de segurança detalhados por meio do Apache Ranger e o processamento analítico de baixa latência não disponível no Apache Spark.

Apache Spark, tem uma API de streaming estruturada que fornece recursos de streaming não disponíveis no Apache Hive. A partir do HDInsight 4,0, Apache Spark 2.3.1 e Apache Hive 3.1.0 têm metastores separados, o que pode dificultar a interoperabilidade. O conector do depósito do hive torna mais fácil usar o Spark e o hive juntos. A biblioteca HWC carrega dados de daemons do LLAP para o Spark executores em paralelo, tornando-o mais eficiente e escalonável do que usar uma conexão JDBC padrão do Spark para o hive.

![Arquitetura](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

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

1. Crie um cluster HDInsight Spark 4,0 usando o portal do Azure com uma conta de armazenamento e uma rede virtual do Azure personalizada. Para obter informações sobre como criar um cluster em uma rede virtual do Azure, consulte [Adicionar o HDInsight a uma rede virtual existente](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet).
1. Crie um cluster LLAP (consulta interativa do HDInsight) 4,0 usando o portal do Azure com a mesma conta de armazenamento e a rede virtual do Azure que o cluster Spark.
1. Copie o conteúdo do `/etc/hosts` arquivo em headnode0 do seu cluster de consulta interativa para o `/etc/hosts` arquivo no headnode0 do cluster Spark. Esta etapa permitirá que o cluster Spark resolva os endereços IP dos nós no cluster de consulta interativa. Exiba o conteúdo do arquivo atualizado com `cat /etc/hosts`. A saída deve ser semelhante ao que é mostrado na captura de tela abaixo.

    ![exibindo o arquivo de hosts](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

1. Defina as configurações do cluster Spark executando as seguintes etapas: 
    1. Vá para portal do Azure, selecione clusters HDInsight e, em seguida, clique no nome do cluster.
    1. No lado direito, em **painéis de cluster**, selecione **Ambari página inicial**.
    1. Na interface do usuário da Web do amAmbari, clique em**configurações** > do **SPARK2** > **personalizado SPARK2-padrões**.

        ![Configuração do Spark2 Ambari](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

    1. Defina `spark.hadoop.hive.llap.daemon.service.hosts` com o mesmo valor da propriedade **Hive. LLAP. daemon. Service. hosts** em * * Hive avançado-interativo-site * *. Por exemplo, `@llap0`

    1. Defina `spark.sql.hive.hiveserver2.jdbc.url` como a cadeia de conexão JDBC, que se conecta ao Hiveserver2 no cluster de consulta interativa. A cadeia de conexão para o cluster se parecerá com o URI abaixo. `CLUSTERNAME`é o nome do cluster Spark e os `user` parâmetros e `password` são definidos com os valores corretos para o cluster.

        ```
        jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2
        ```

        >[!Note] 
        > A URL JDBC deve conter credenciais para se conectar ao Hiveserver2, incluindo um nome de usuário e senha.

    1. Defina `spark.datasource.hive.warehouse.load.staging.dir` como um diretório de preparo adequado compatível com HDFS. Se você tiver dois clusters diferentes, o diretório de preparo deverá ser uma pasta no diretório de preparo da conta de armazenamento do cluster LLAP para que HiveServer2 tenha acesso a ela. Por exemplo, `wasb://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp` em `STORAGE_ACCOUNT_NAME` que é o nome da conta de armazenamento que está sendo usada pelo cluster `STORAGE_CONTAINER_NAME` e é o nome do contêiner de armazenamento.

    1. Defina `spark.datasource.hive.warehouse.metastoreUri` com o valor do URI do metastore do cluster de consulta interativa. Para localizar o metastoreUri para o cluster LLAP, procure a propriedade **Hive. metastore. URIs** na interface do usuário do Ambari para o cluster LLAP em **Hive** > **avançado** > **geral**. O valor será semelhante ao seguinte URI:

        ```
        thrift://hn0-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083,
        thrift://hn1-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083
        ```

    1. Defina `spark.security.credentials.hiveserver2.enabled` como`false` para o modo de implantação de cliente yarn.
    1. Defina `spark.hadoop.hive.zookeeper.quorum` como o quorum Zookeeper do seu cluster LLAP. Para localizar o quorum Zookeeper para o cluster do LLAP, procure a propriedade **Hive. Zookeeper. quorum** na interface do usuário do Ambari para o cluster > LLAP em > Hive avançado avançado**Hive-site**. O valor será semelhante à seguinte cadeia de caracteres:

        ```
        zk1-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk4-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk6-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181
        ```

Para testar a configuração do seu conector de depósito do hive, siga as etapas na seção [conectando e executando consultas](#connecting-and-running-queries).

## <a name="using-the-hive-warehouse-connector"></a>Usando o conector do depósito do hive

### <a name="connecting-and-running-queries"></a>Conectando e executando consultas

Você pode escolher entre alguns métodos diferentes para se conectar ao seu cluster de consulta interativa e executar consultas usando o conector do depósito do hive. Os métodos com suporte incluem as seguintes ferramentas:

* [spark-shell](../spark/apache-spark-shell.md)
* PySpark
* Spark – enviar
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Todos os exemplos fornecidos neste artigo serão executados por meio do Spark-Shell.

Para iniciar uma sessão do Spark-Shell, execute as seguintes etapas:

1. SSH no cabeçalho para o cluster. Para obter mais informações sobre como se conectar ao cluster com o SSH, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
1. Altere para o diretório correto digitando `cd /usr/hdp/current/hive_warehouse_connector` ou forneça o caminho completo para todos os arquivos jar usados como parâmetros no comando Spark-Shell.
1. Digite o seguinte comando para iniciar o Shell do Spark:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Você verá uma mensagem de boas-vindas e um `scala>` prompt onde você pode inserir comandos.

1. Depois de iniciar o Spark-Shell, uma instância do conector do depósito do hive pode ser iniciada usando os seguintes comandos:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Conectando e executando consultas em clusters Enterprise Security Package (ESP)

O Enterprise Security Package (ESP) fornece recursos de nível corporativo como autenticação baseada em Active Directory, suporte a vários usuários e controle de acesso baseado em função para clusters Apache Hadoop no Azure HDInsight. Para obter mais informações sobre o ESP, consulte [usar o Enterprise Security Package no HDInsight](../domain-joined/apache-domain-joined-architecture.md).

1. Siga as etapas 1 e 2 iniciais em [conectando e executando consultas](#connecting-and-running-queries).
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

2. Filtre a tabela `hivesampletable` na qual a `state` coluna `Colorado`é igual a. Essa consulta da tabela hive é retornada como um dataframe do Spark. Em seguida, o dataframe é salvo na tabela `sampletable_colorado` Hive usando `write` a função.
    
    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

Você pode ver a tabela resultante na captura de tela abaixo.

![Mostrar tabela resultante](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Gravações de streaming estruturado

Usando o conector do depósito do hive, você pode usar o streaming do Spark para gravar dados em tabelas do hive.

Siga as etapas abaixo para criar um exemplo de conector de depósito do hive que ingere dados de um fluxo do Spark na porta localhost 9999 em uma tabela do hive.

1. Abra um terminal no cluster Spark.
1. Inicie o fluxo do Spark com o seguinte comando:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9988).load()
    ```

1. Gere dados para o fluxo do Spark que você criou, executando as seguintes etapas:
    1. Abra outro terminal no mesmo cluster do Spark.
    1. No prompt de comando, digite `nc -lk 9999`. Esse comando usa o utilitário netcat para enviar dados da linha de comando para a porta especificada.
    1. Digite as palavras que você deseja que o fluxo do Spark ingerir, seguido pelo retorno de carro.
        ![dados de entrada para o fluxo do Spark](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark-stream-data-input.png)
1. Crie uma nova tabela do hive para manter os dados de streaming. No Spark-Shell, digite os seguintes comandos:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Grave os dados de streaming na tabela recém-criada usando o seguinte comando:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > As `metastoreUri` opções `database` e devem ser definidas manualmente por causa de um problema conhecido no Apache Spark. Para obter mais informações sobre esse problema, consulte [Spark-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Você pode exibir os dados inseridos na tabela com o seguinte comando:

    ```scala
    hive.table("stream_table").show()
    ```

### <a name="securing-data-on-spark-esp-clusters"></a>Protegendo dados em clusters do Spark

1. Crie uma tabela `demo` com alguns dados de exemplo inserindo os seguintes comandos:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Exiba o conteúdo da tabela com o comando a seguir. Antes de aplicar a política, `demo` a tabela mostra a coluna completa.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![tabela de demonstração antes de aplicar a política de Ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Aplique uma política de mascaramento de coluna que mostre apenas os últimos quatro caracteres da coluna.  
    1. Acesse a interface do usuário do `https://CLUSTERNAME.azurehdinsight.net/ranger/`administrador do Ranger em.
    1. Clique no serviço de Hive para o cluster em **Hive**.
        ![tabela de demonstração antes de aplicar a política de Ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Clique na guia **mascaramento** e **adicione nova** ![lista de políticas de política](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)
    1. Forneça um nome de política desejado. Selecionar Banco de dados: **Padrão**, tabela do hive: **demonstração**, coluna do hive: **nome**, usuário: **rsadmin2**, tipos de acesso: **Select**e **máscara parcial: Mostre os últimos 4** no menu de **opção Selecionar mascaramento** . Clique em **Adicionar**.
                ![lista de políticas](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Exiba o conteúdo da tabela novamente. Depois de aplicar a política de Ranger, podemos ver apenas os últimos quatro caracteres da coluna.

    ![tabela de demonstração após a aplicação da política de Ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Passos Seguintes

* [Utilizar uma Consulta Interativa com o HDInsight](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started).
* [Exemplos de interação com o conector de depósito do hive usando Zeppelin, Livy, Spark-Submit e pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
