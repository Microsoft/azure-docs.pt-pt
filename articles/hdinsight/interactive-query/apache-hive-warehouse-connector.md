---
title: Faça a integração do Apache Spark e Apache Hive com o conector de armazém do Hive
description: Saiba como integrar o Apache Spark e Apache Hive com o conector de armazém do Hive no HDInsight do Azure.
ms.service: hdinsight
author: nakhanha
ms.author: nakhanha
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: b245661ab8f26c1f529a049d326d2c72838c7a17
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056719"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Faça a integração do Apache Spark e Apache Hive com o conector de armazém do Hive

O Apache Hive armazém conector (HWC) é uma biblioteca que lhe permite trabalhar mais facilmente com o Apache Spark e Apache Hive ao suportar tarefas, como mover dados entre o Spark DataFrames e tabelas do Hive e também instruir o Spark streaming de dados para tabelas do Hive. Hive armazém conector funciona como uma ponte entre o Spark e do Hive. Ele oferece suporte a Scala, Java e Python para o desenvolvimento.

O conector de armazém do Hive permite-lhe tirar partido das funcionalidades únicas de Hive e do Spark para criar poderosas aplicações de macrodados. Apache Hive oferece suporte para transações da base de dados que são ocorreu, consistência, Isolated e durável (ACID). Para obter mais informações sobre ACID e transações no Hive, consulte [Hive transações](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Hive também oferece controlos de segurança detalhadas através do Apache Ranger e baixa latência analítico de processamento não está disponível no Apache Spark.

Apache Spark, tem uma API de transmissão em fluxo estruturada que fornece capacidades de transmissão em fluxo não está disponíveis no Apache Hive. A partir do HDInsight 4.0, 2.3.1 do Apache Spark e Apache Hive 3.1.0 têm metastores separado, que pode tornar difícil interoperabilidade. O conector de armazém do Hive torna mais fácil de utilizar o Spark e do Hive em conjunto. A biblioteca HWC carrega dados a partir dos daemons do LLAP para executor do Spark em paralelo, tornando-o mais eficiente e dimensionável do que utilizar uma ligação de JDBC padrão do Spark para Hive.

![Arquitetura](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Algumas das operações suportadas pelo conector de armazém do Hive são:

* Descrevendo uma tabela
* Criar uma tabela de dados formatados ORC
* Selecionar os dados de Hive e a obtenção de um pacote de dados
* Escrever um pacote de dados do Hive no batch
* Executando uma instrução de atualização do Hive
* Leitura de dados de tabela do Hive, transformá-los no Spark e gravando numa nova tabela do Hive
* Escrever um fluxo de pacote de dados ou do Spark, Hive usando HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Configuração do conector de armazém do Hive

Siga estes passos para configurar o conector de armazém do Hive entre um cluster do Spark e Interactive Query no HDInsight do Azure:

1. Crie um cluster do HDInsight Spark 4.0 no portal do Azure com uma conta de armazenamento e uma rede virtual do Azure personalizada. Para obter informações sobre como criar um cluster numa rede virtual do Azure, consulte [HDInsight adicionar a uma rede virtual existente](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md#existingvnet).
1. Crie um cluster de HDInsight Interactive Query (LLAP) 4.0 com o portal do Azure com a mesma conta de armazenamento e rede virtual do Azure como o cluster do Spark.
1. Copie o conteúdo dos `/etc/hosts` ficheiro no headnode0 do cluster do Interactive Query para o `/etc/hosts` ficheiro no headnode0 do cluster do Spark. Este passo permitirá que o cluster do Spark resolver endereços IP de nós no cluster do Interactive Query. Exibir o conteúdo do arquivo atualizado com `cat /etc/hosts`. O resultado deverá ser algo como o mostrado na captura de ecrã abaixo.

    ![visualizar o ficheiro de anfitriões](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

1. Configure as definições de cluster do Spark efetuando os seguintes passos: 
    1. Aceda ao portal do Azure, selecione clusters do HDInsight e, em seguida, clique no seu nome de cluster.
    1. No lado direito, sob **dashboards de clusters**, selecione **Ambari doméstica**.
    1. Na IU web do Ambari, clique em **SPARK2** > **configurações** > **personalizado spark2-predefinições**.

        ![Configuração de Spark2 Ambari](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

    1. Definir `spark.hadoop.hive.llap.daemon.service.hosts` para o mesmo valor que a propriedade **nome da aplicação LLAP** sob **avançadas do hive interativas env**. Por exemplo, `@llap0`

    1. Definir `spark.sql.hive.hiveserver2.jdbc.url` para a cadeia de ligação de JDBC, que liga ao Hiveserver2 no cluster do Interactive Query. A cadeia de ligação para o cluster será semelhante a URI abaixo. `CLUSTERNAME` é o nome do cluster do Spark e o `user` e `password` parâmetros são definidos para os valores corretos para o seu cluster.

        ```
        jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2
        ```

        >[!Note] 
        > O URL de JDBC deve conter as credenciais para ligar ao Hiveserver2 incluindo um nome de utilizador e palavra-passe.

    1. Definir `spark.datasource.hive.warehouse.load.staging.dir` para um diretório de transição compatível com HDFS adequado. Se tiver dois clusters diferentes, o diretório de transição deve ser uma pasta no diretório de transição da conta de armazenamento do cluster LLAP, para que HiveServer2 tenha acesso ao mesmo. Por exemplo, `wasb://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp` em que `STORAGE_ACCOUNT_NAME` é o nome da conta de armazenamento a ser utilizado pelo cluster, e `STORAGE_CONTAINER_NAME` é o nome do contentor de armazenamento.

    1. Definir `spark.datasource.hive.warehouse.metastoreUri` com o valor do metastore URI do cluster do Interactive Query. Para localizar o metastoreUri para o seu cluster LLAP, procure o **hive.metastore.uris** propriedade na IU do Ambari para sua LLAP do cluster sob **Hive** > **avançadas**  >  **Gerais**. O valor será algo parecido com o URI seguinte:

        ```
        thrift://hn0-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083,
        thrift://hn1-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083
        ```

    1. Definir `spark.security.credentials.hiveserver2.enabled` para `false` para cliente do YARN implementar o modo.
    1. Definir `spark.hadoop.hive.zookeeper.quorum` para o quórum do Cluster do LLAP do Zookeeper. Para localizar o quórum Zookeeper para o seu cluster LLAP, procure o **hive.zookeeper.quorum** propriedade na IU do Ambari para sua LLAP do cluster sob **Hive** > **avançadas**  >  **Advanced hive site**. O valor será algo parecido com a seguinte cadeia:

        ```
        zk1-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk4-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk6-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181
        ```

Para testar a configuração do seu conector de armazém do Hive, siga os passos na secção [consultas de ligação em curso e em execução](#connecting-and-running-queries).

## <a name="using-the-hive-warehouse-connector"></a>Utilizar o conector de armazém do Hive

### <a name="connecting-and-running-queries"></a>Ligar e execução de consultas

Pode escolher entre alguns métodos diferentes para se ligar ao seu cluster do Interactive Query e executar consultas através do conector de armazém do Hive. Métodos suportados incluem as seguintes ferramentas:

* [spark-shell](../spark/apache-spark-shell.md)
* PySpark
* spark-submit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Todos os exemplos fornecidos neste artigo serão executados por meio do shell do spark.

Para iniciar uma sessão de shell do spark, siga os passos abaixo:

1. SSH no nó principal para o seu cluster. Para obter mais informações sobre como ligar ao seu cluster com o SSH, veja [ligar ao HDInsight (Apache Hadoop) através de SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
1. Mude para o diretório correto ao escrever `cd /usr/hdp/current/hive_warehouse_connector` ou forneça o caminho completo para todos os ficheiros jar utilizados como parâmetros no comando shell do spark.
1. Introduza o seguinte comando para iniciar o shell do spark:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Verá uma mensagem de boas-vindas e um `scala>` onde pode introduzir os comandos da linha de comandos.

1. Depois de iniciar o shell do spark, uma instância do conector de armazém do Hive pode ser iniciada com os comandos seguintes:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Ligar e executar consultas em clusters de pacote de segurança da empresa (ESP)

O pacote de segurança da empresa (ESP) fornece capacidades de nível empresarial, como a autenticação baseada no Active Directory, o suporte de vários utilizador e o controlo de acesso baseado em funções para os clusters do Apache Hadoop no HDInsight do Azure. Para obter mais informações sobre o ESP, consulte [uma introdução à segurança do Apache Hadoop com o Enterprise Security Package](../domain-joined/apache-domain-joined-introduction.md).

1. Siga as etapas iniciais 1 e 2 dentro [consultas de ligação em curso e em execução](#connecting-and-running-queries).
1. Tipo de `kinit` e inicie sessão com um utilizador de domínio.
1. Início do spark-shell, com a lista completa de parâmetros de configuração, conforme mostrado abaixo. Todos os valores em todas as letras maiúsculas entre colchetes angulares tem de ser especificados com base no seu cluster. Se precisar de saber os valores de entrada para qualquer um dos parâmetros abaixo, consulte a secção sobre [configuração do conector de armazém do Hive](#hive-warehouse-connector-setup).:

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

### <a name="creating-spark-dataframes-from-hive-queries"></a>Criar pacotes do Spark a partir de consultas do Hive

Os resultados de todas as consultas usando a biblioteca HWC são retornados como um pacote de dados. Os exemplos seguintes demonstram como criar uma consulta básica.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Os resultados da consulta são pacotes de Spark, que pode ser usado com bibliotecas de Spark, como MLIB e SparkSQL.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Escrevendo DataFrames do Spark para tabelas do Hive

Spark não suporta nativamente a escrever para tabelas ACID geridas do Hive. Utilizar HWC, no entanto, pode escrever qualquer pacote de dados para uma tabela do Hive. Pode ver esta funcionalidade em funcionamento no exemplo a seguir:

1. Criar uma tabela chamada `sampletable_colorado` e especificar as colunas com o seguinte comando:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

2. Filtre a tabela `hivesampletable` onde a coluna `state` é igual a `Colorado`. Esta consulta de tabela do Hive é retornada como um DataFrame do Spark. Em seguida, o pacote de dados é guardado na tabela do Hive `sampletable_colorado` usando o `write` função.
    
    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

Pode ver a tabela resultante na captura de ecrã abaixo.

![Mostrar tabela resultante](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Escritas de transmissão em fluxo estruturada

Utilizar conector de armazém do Hive, pode utilizar o Spark streaming para escrever dados em tabelas do Hive.

Siga os passos abaixo para criar um exemplo de conector de armazém do Hive que ingere dados de um fluxo do Spark na porta de localhost 9999 para uma tabela do Hive.

1. Abra um terminal no seu cluster do Spark.
1. Iniciar o fluxo do spark com o seguinte comando:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9988).load()
    ```

1. Gere dados para o fluxo do Spark que criou, efetuando os seguintes passos:
    1. Abra outra terminal no mesmo cluster do Spark.
    1. No prompt de comando, digite `nc -lk 9999`. Este comando utiliza o utilitário de netcat para enviar dados a partir da linha de comando para a porta especificada.
    1. Escreva as palavras que gostaria que o fluxo do Spark a ingestão, seguido de retorno de carro.
        ![dados de entrada para o spark stream](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark-stream-data-input.png)
1. Crie uma nova tabela de Hive para manter os dados de transmissão em fluxo. Na shell do spark, escreva os seguintes comandos:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Escreva os dados de transmissão em fluxo para a tabela recém-criada com o seguinte comando:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > O `metastoreUri` e `database` opções atualmente tem de ser definidas manualmente devido a um problema conhecido no Apache Spark. Para obter mais informações sobre este problema, consulte [SPARK 25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Pode ver os dados inseridos na tabela com o seguinte comando:

    ```scala
    hive.table("stream_table").show()
    ```

### <a name="securing-data-on-spark-esp-clusters"></a>Proteção dos dados em clusters do Spark ESP

1. Criar uma tabela `demo` com alguns dados de exemplo, introduzindo os comandos seguintes:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Ver conteúdo da tabela com o seguinte comando. Antes de aplicar a política, o `demo` tabela mostra a coluna completa.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![tabela de demonstração antes de aplicar políticas do ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Aplica uma política que mostra apenas os últimos quatro carateres da coluna de máscara de coluna.  
    1. Aceda à IU do Ranger Admin em `https://CLUSTERNAME.azurehdinsight.net/ranger/`.
    1. Clique no serviço do Hive para o seu cluster sob **Hive**.
        ![tabela de demonstração antes de aplicar políticas do ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Clique no **máscara** separador e, em seguida **Adicionar nova política** ![lista de políticas](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)
    1. Forneça um nome de política pretendida. Selecione a base de dados: **Predefinido**, uma tabela do Hive: **demonstração**, coluna do Hive: **nome**, utilizador: **rsadmin2**, tipos de acesso: **selecione**e **Máscara parcial: Mostrar os últimos 4** partir o **selecionar a opção de máscara** menu. Clique em **Adicionar**.
                ![lista de políticas](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Veja novamente o conteúdo da tabela. Depois de aplicar a política de ranger, podemos ver apenas os últimos quatro carateres da coluna.

    ![tabela de demonstração depois de aplicar políticas do ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Passos Seguintes

* [Utilizar uma Consulta Interativa com o HDInsight](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started).
* [Exemplos de interação com o conector de armazém do Hive com o Zeppelin, Livy, spark-submit e pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
