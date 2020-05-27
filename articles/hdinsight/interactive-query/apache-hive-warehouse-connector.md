---
title: Apache Spark & Hive - Hive Warehouse Connector - Azure HDInsight
description: Saiba como integrar a Apache Spark e a Apache Hive com o Conector de Armazém hive no Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: fdc90ffaf3cef3c594e7d84e32af9ef78fe08b0d
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849455"
---
# <a name="integrate-apache-spark-and-apache-hive-with-hive-warehouse-connector-in-azure-hdinsight"></a>Integre a Apache Spark e a Apache Hive com o Conector de Armazém hive em Azure HDInsight

O Conector apache hive warehouse (HWC) é uma biblioteca que lhe permite trabalhar mais facilmente com Apache Spark e Apache Hive. Suporta tarefas como mover dados entre dataframes de faíscas e tabelas de colmeias. Além disso, direcionando os dados de streaming spark para as tabelas da Colmeia. O Conector hive Warehouse funciona como uma ponte entre A Faísca e a Colmeia. Também apoia Scala, Java e Python como linguagens de programação para o desenvolvimento.

O Conector de Armazém Hive permite-lhe aproveitar as características únicas da Hive e Spark para construir poderosas aplicações de big data.

A Apache Hive oferece suporte para transações de base de dados que são atómicas, consistentes, isoladas e duráveis (ACID). Para obter mais informações sobre acid e transações na Colmeia, consulte [As Transações da Colmeia.](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions) A Hive também oferece controlos de segurança detalhados através de Apache Ranger e De baixa latência analítica processamento (LLAP) não disponíveis em Apache Spark.

Apache Spark, tem um API de streaming estruturado que dá capacidades de streaming não disponíveis na Colmeia Apache. Começando com HDInsight 4.0, Apache Spark 2.3.1 e Apache Hive 3.1.0 têm metastores separados. As metalojas separadas podem dificultar a interoperabilidade. O Conector de Armazém hive facilita a utilização de Faíscae e Colmeia juntos. A biblioteca HWC carrega dados de daemons LLAP a executores de faíscas em paralelo. Este processo torna-o mais eficiente e adaptável do que uma ligação JDBC padrão de Spark à Hive.

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

O Conector hive Warehouse necessita de clusters separados para cargas de trabalho de Faísca e Consulta Interativa. Siga estes passos para configurar estes clusters em Azure HDInsight.

### <a name="create-clusters"></a>Criar clusters

1. Crie um cluster HDInsight Spark **4.0** com uma conta de armazenamento e uma rede virtual Azure personalizada. Para obter informações sobre a criação de um cluster numa rede virtual Azure, consulte [Adicionar HDInsight a uma rede virtual existente](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet).

1. Crie um cluster HDInsight Interactive Query (LLAP) **4.0** com a mesma conta de armazenamento e rede virtual Azure que o cluster Spark.

### <a name="configure-hwc-settings"></a>Configurar as definições de HWC

#### <a name="gather-preliminary-information"></a>Recolher informações preliminares

1. De um navegador web, navegue até `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE` onde LLAPCLUSTERNAME é o nome do seu cluster De consulta interativa.

1. Navegue para **sumário**  >  **HiveServer2 Interactive JDBC URL** e note o valor. O valor pode ser semelhante a: `jdbc:hive2://zk0-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181/;serviceDiscoveryMode=zooKeeper;zooKeeperNamespace=hiveserver2-interactive` .

1. Navegue para **Configs**  >  **Advanced**  >  **Advanced Hive-site**  >  **hive.zookeeper.quorum** e note o valor. O valor pode ser semelhante a: `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181` .

1. Navegue para **Configs**  >  **Advanced**  >  **General**  >  **hive.metastore.uris** e note o valor. O valor pode ser semelhante a: `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083` .

1. Navegue para **Configs**  >  **Advanced**  >  **Advanced hive-interactive-site**  >  **hive.llap.daemon.service.hosts** e note o valor. O valor pode ser semelhante a: `@llap0` .

#### <a name="configure-spark-cluster-settings"></a>Configurar as definições do cluster de faíscas

1. A partir de um navegador web, navegue até `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` onde clusterNAME é o nome do seu cluster Apache Spark.

1. Expandir **padrão de faísca 2 personalizadas**.

    ![Configuração Apache Ambari Spark2](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

1. Selecione **Adicionar Propriedade...** para adicionar as seguintes configurações:

    | Configuração | Valor |
    |----|----|
    |`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. <br> Definir para um diretório de encenação compatível com HDFS adequado. Se tiver dois clusters diferentes, o diretório de encenação deve ser uma pasta no diretório de encenação da conta de armazenamento do cluster LLAP para que o HiveServer2 tenha acesso ao mesmo.  `STORAGE_ACCOUNT_NAME`Substitua-a com o nome da conta de armazenamento utilizada pelo cluster e com o nome do recipiente de `STORAGE_CONTAINER_NAME` armazenamento. |
    |`spark.sql.hive.hiveserver2.jdbc.url`| O valor que obteve anteriormente do **URL JDBC Interativo hiveServer2** |
    |`spark.datasource.hive.warehouse.metastoreUri`| O valor que obteve anteriormente da **hive.metastore.uris**. |
    |`spark.security.credentials.hiveserver2.enabled`|`true`para o modo de cluster YARN e para o `false` modo cliente YARN. |
    |`spark.hadoop.hive.zookeeper.quorum`| O valor que obteve anteriormente da **hive.zookeeper.quorum**. |
    |`spark.hadoop.hive.llap.daemon.service.hosts`| O valor que obteve anteriormente da **hive.llap.daemon.service.hosts**. |

1. Guarde alterações e reinicie todos os componentes afetados.

### <a name="configure-hwc-for-enterprise-security-package-esp-clusters"></a>Configure HWC para clusters do Pacote de Segurança Empresarial (ESP)

O Pacote de Segurança Empresarial (ESP) fornece capacidades de nível empresarial como autenticação baseada em Diretório ativo, suporte multiutilizador e controlo de acesso baseado em papéis para clusters Apache Hadoop em Azure HDInsight. Para obter mais informações sobre esp, consulte [Use Enterprise Security Package in HDInsight](../domain-joined/apache-domain-joined-architecture.md).

Para além das configurações mencionadas na secção anterior, adicione a seguinte configuração para utilizar o HWC nos clusters ESP.

1. A partir da Web UI de **Spark2**Ambari do cluster Spark, navegue até  >  **CONFIGS**  >  **spark2-defaults personalizados Spark2**.

1. Atualize a seguinte propriedade.

    | Configuração | Valor |
    |----|----|
    | `spark.sql.hive.hiveserver2.jdbc.url.principal`    | `hive/<headnode-FQDN>@<AAD-Domain>` |
    
    `<headnode-FQDN>`Substitua-a com o nome de domínio totalmente qualificado do nó de cabeça do cluster De consulta interativa. Substitua `<AAD-DOMAIN>` pelo nome do Diretório Ativo Azure (AAD) a que o cluster está unido. Use uma corda maiúscula para o `<AAD-DOMAIN>` valor, caso contrário a credencial não será encontrada. Verifique /etc/krb5.conf para obter os nomes do reino, se necessário.
    
1. Guarde as alterações e reinicie os componentes conforme necessário.

## <a name="hive-warehouse-connector-usage"></a>Utilização do conector do Armazém hive

Pode escolher entre alguns métodos diferentes para se conectar ao seu cluster de Consulta Interativa e executar consultas utilizando o Conector de Armazém hive. Os métodos suportados incluem as seguintes ferramentas:

* [Faísca-shell / PySpark](../spark/apache-spark-shell.md)
* [Spark-submit](#spark-submit)
* [Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)


Abaixo estão alguns exemplos para ligar ao HWC de Spark.

### <a name="spark-shell"></a>Casca de faísca

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se ligar ao seu aglomerado de Faíscas Apache. Editar o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, introduzir o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A partir da sua sessão ssh, execute o seguinte comando para observar a `hive-warehouse-connector-assembly` versão:

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. Editar o código abaixo com a `hive-warehouse-connector-assembly` versão acima identificada. Em seguida, execute o comando para iniciar a casca de faísca:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Depois de iniciar a casca de faísca, uma instância de Conector de Armazém hive pode ser iniciada usando os seguintes comandos:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="spark-submit"></a>Spark-submit

Assim que construir o código scala/java juntamente com as dependências num frasco de montagem, use o comando abaixo para lançar uma aplicação Spark. `<VERSION>`Substitua, e com os `<APP_JAR_PATH>` valores reais.

* Modo cliente YARN
    
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode client \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

* Modo Cluster YARN
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode cluster \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=true
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

Para Python, adicione também a seguinte configuração. 

    ```python
    --py-files /usr/hdp/current/hive_warehouse_connector/pyspark_hwc-<VERSION>.zip
    ```
    
## <a name="run-queries-on-enterprise-security-package-esp-clusters"></a>Executar consultas em clusters do Pacote de Segurança Empresarial (ESP)

Utilize antes de iniciar a casca de faísca ou a `kinit` apresentação de faíscas. Substitua o USERNAME pelo nome de uma conta de domínio por permissões de acesso ao cluster e, em seguida, execute o seguinte comando:

```bash
kinit USERNAME
```

### <a name="securing-data-on-spark-esp-clusters"></a>Obtenção de dados sobre clusters Spark ESP

1. Crie uma tabela com alguns dados da `demo` amostra, inserindo os seguintes comandos:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Veja o conteúdo da tabela com o seguinte comando. Antes de aplicar a apólice, a `demo` tabela mostra a coluna completa.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![mesa de demonstração antes de aplicar política ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Aplique uma política de máscarade coluna que apenas mostre os últimos quatro caracteres da coluna.  
    1. Vá ao Ranger Admin UI em `https://LLAPCLUSTERNAME.azurehdinsight.net/ranger/` .
    1. Clique no serviço hive para o seu cluster sob **a Colmeia**.
        ![gestor de serviço ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Clique no **separador Mascarar** e, em seguida, **adicione nova política**

        ![hive armazém cone cector ranger lista política política](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    1. Forneça um nome de política desejado. Selecione base de dados: **Predefinido**, Tabela de Colmeias: **demo,** Coluna hive: **nome**, Utilizador: **rsadmin2**, Tipos de acesso: **selecione**- e **máscara parcial: mostrar o último 4** do menu Select **Masking Option.** Clique em **Adicionar**.
                ![criar política](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Veja o conteúdo da mesa novamente. Depois de aplicar a política dos rangers, só podemos ver os últimos quatro caracteres da coluna.

    ![mesa de demonstração após aplicar política ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Passos seguintes

* [Operações de HWC e Apache Spark](./apache-hive-warehouse-connector-operations.md)
* [Utilizar uma Consulta Interativa com o HDInsight](./apache-interactive-query-get-started.md).
* [Integração de HWC com Apache Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)
* [Exemplos de interação com o Conector de Armazém hive usando Zeppelin, Livy, spark-submit, e pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)