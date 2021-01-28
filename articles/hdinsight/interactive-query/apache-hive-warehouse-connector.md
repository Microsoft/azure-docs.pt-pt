---
title: Apache Spark & Colmeia - Conector do Armazém da Colmeia - Azure HDInsight
description: Saiba como integrar a Apache Spark e a Apache Hive com o Conector do Armazém da Colmeia no Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 39eb007c85d9f0623b4a5611e36d4ed7a75423e0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941185"
---
# <a name="integrate-apache-spark-and-apache-hive-with-hive-warehouse-connector-in-azure-hdinsight"></a>Integre a Faísca Apache e a Colmeia Apache com o Conector do Armazém da Colmeia em Azure HDInsight

O Apache Hive Warehouse Connector (HWC) é uma biblioteca que lhe permite trabalhar mais facilmente com Apache Spark e Apache Hive. Suporta tarefas como a movimentação de dados entre datas spark e tabelas de Colmeia. Além disso, ao direcionar os dados de streaming spark para as tabelas da Hive. O Conector do Armazém da Colmeia funciona como uma ponte entre a Spark e a Hive. Também apoia Scala, Java e Python como linguagens de programação para o desenvolvimento.

O Conector do Armazém da Colmeia permite-lhe tirar partido das características únicas da Colmeia e da Spark para construir poderosas aplicações de big data.

A Apache Hive oferece suporte para transações de base de dados que são atómicas, consistentes, isoladas e duráveis (ACID). Para obter mais informações sobre ACID e transações em Colmeia, consulte [As Transações da Colmeia.](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions) A Hive também oferece controlos de segurança detalhados através do Apache Ranger e do Baixo Processamento Analítico de Latência (LLAP) não disponíveis em Apache Spark.

Apache Spark, tem uma API de streaming estruturada que dá capacidades de streaming não disponíveis na Colmeia Apache. Começando com HDInsight 4.0, Apache Spark 2.3.1 e Apache Hive 3.1.0 têm metastões separadas. As metástases separadas podem dificultar a interoperabilidade. O Conector do Armazém da Colmeia facilita a utilização de faíscas e colmeias juntos. A biblioteca HWC carrega dados de daemons LLAP para executores spark em paralelo. Este processo torna-o mais eficiente e adaptável do que uma ligação JDBC padrão da Spark à Colmeia.

![arquitetura de conector de armazém de colmeia](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Algumas das operações apoiadas pelo Conector do Armazém da Colmeia são:

* Descrevendo uma mesa
* Criação de uma tabela para dados formatados pela ORC
* Selecionando dados da Colmeia e recuperando um DataFrame
* Escrever um DataFrame para Colmeia em lote
* Executar uma declaração de atualização da Colmeia
* Ler dados de tabela da Colmeia, transformá-lo em Faísca, e escrevê-lo para uma nova tabela de Colmeias
* Escrever um dataframe ou fluxo de faísca para colmeia usando HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Configuração do conector do armazém da colmeia

> [!IMPORTANT]
> A instância Interativa HiveServer2 instalada nos clusters spark 2.4 Enterprise Security Package não é suportada para utilização com o Conector do Armazém da Colmeia. Em vez disso, deve configurar um cluster interativo HiveServer2 para acolher as suas cargas de trabalho Interativas HiveServer2. Não é suportada uma configuração do Conector do Armazém de Colmeia que utilize um único cluster Spark 2.4.

O Conector do Armazém da Colmeia necessita de aglomerados separados para cargas de trabalho de faísca e consulta interativa. Siga estes passos para configurar estes clusters em Azure HDInsight.

### <a name="create-clusters"></a>Criar clusters

1. Crie um cluster HDInsight Spark **4.0** com uma conta de armazenamento e uma rede virtual Azure personalizada. Para obter informações sobre a criação de um cluster numa rede virtual Azure, consulte [Add HDInsight a uma rede virtual existente.](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet)

1. Crie um cluster HDInsight Interactive Query (LLAP) **4.0** com a mesma conta de armazenamento e rede virtual Azure que o cluster Spark.

### <a name="configure-hwc-settings"></a>Configurar configurações HWC

#### <a name="gather-preliminary-information"></a>Recolher informações preliminares

1. A partir de um navegador web, navegue `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE` até onde LLAPCLUSTERNAME é o nome do seu cluster de consultainterna.

1. Navegue para **o**  >  **URL JDBC Interativo HiveServer2 e** note o valor. O valor pode ser semelhante a: `jdbc:hive2://zk0-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181/;serviceDiscoveryMode=zooKeeper;zooKeeperNamespace=hiveserver2-interactive` .

1. Navegue para **Configs**  >  **Advanced**  >  **Hive-site**  >  **hive.zookeeper.qurum** e note o valor. O valor pode ser semelhante a: `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181` .

1. Navegue para **Configs**  >  **Advanced**  >  **General**  >  **hive.metastore.uris** e note o valor. O valor pode ser semelhante a: `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083` .

1. Navegue para **Configs**  >  **Advanced**  >  **hive-interactive-site**  >  **hive.llap.daemon.service.hosts** e note o valor. O valor pode ser semelhante a: `@llap0` .

#### <a name="configure-spark-cluster-settings"></a>Configurar configurações de cluster spark

1. A partir de um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` até onde CLUSTERNAME é o nome do seu cluster Apache Spark.

1. Expandir **os padrão de faíscas personalizados**.

    ![Configuração Apache Ambari Spark2](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

1. Selecione **Adicionar Propriedade...** para adicionar as seguintes configurações:

    | Configuração | Valor |
    |----|----|
    |`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. <br> Coloque-se num diretório adequado compatível com HDFS. Se tiver dois clusters diferentes, o diretório de encenação deve ser uma pasta no diretório de encenação da conta de armazenamento do cluster LLAP para que a HiveServer2 tenha acesso ao mesmo.  `STORAGE_ACCOUNT_NAME`Substitua-o pelo nome da conta de armazenamento utilizada pelo cluster e `STORAGE_CONTAINER_NAME` pelo nome do recipiente de armazenamento. |
    |`spark.sql.hive.hiveserver2.jdbc.url`| O valor que obteve anteriormente da **HiveServer2 Interactive JDBC URL** |
    |`spark.datasource.hive.warehouse.metastoreUri`| O valor que obteve anteriormente da **hive.metastore.uris**. |
    |`spark.security.credentials.hiveserver2.enabled`|`true` para o modo de cluster YARN e `false` para o modo cliente YARN. |
    |`spark.hadoop.hive.zookeeper.quorum`| O valor que obteve anteriormente de **hive.zookeeper.qurum**. |
    |`spark.hadoop.hive.llap.daemon.service.hosts`| O valor que obteve anteriormente de **hive.llap.daemon.service.hosts**. |

1. Guarde as alterações e reinicie todos os componentes afetados.

### <a name="configure-hwc-for-enterprise-security-package-esp-clusters"></a>Configure os clusters HWC para pacotes de segurança empresarial (ESP)

O Pacote de Segurança Empresarial (ESP) fornece capacidades de qualidade empresarial como a autenticação baseada no Ative Directory, suporte a vários utilizadores e controlo de acesso baseado em funções para clusters Apache Hadoop em Azure HDInsight. Para obter mais informações sobre esp, consulte [Use Enterprise Security Package in HDInsight](../domain-joined/apache-domain-joined-architecture.md).

Para além das configurações mencionadas na secção anterior, adicione a seguinte configuração para utilizar hWC nos clusters ESP.

1. A partir da UI web Ambari do cluster Spark, navegue até **Spark2**  >  **CONFIGS**  >  **Custom spark2-defaults**.

1. Atualize a seguinte propriedade.

    | Configuração | Valor |
    |----|----|
    | `spark.sql.hive.hiveserver2.jdbc.url.principal`    | `hive/<llap-headnode>@<AAD-Domain>` |
    
    * A partir de um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` até onde CLUSTERNAME é o nome do seu cluster de consultainterna. Clique na **HiveServer2 Interactive**. Verá o Nome de Domínio Totalmente Qualificado (FQDN) do nó de cabeça no qual llap está em execução como mostrado na imagem. `<llap-headnode>`Substitua-o por este valor.

        ![Nó de cabeça do conector do armazém de colmeia](./media/apache-hive-warehouse-connector/head-node-hive-server-interactive.png)

    * Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para ligar ao seu cluster de consultas interativos. Procure `default_realm` por um parâmetro no `/etc/krb5.conf` ficheiro. `<AAD-DOMAIN>`Substitua-o por este valor como uma corda maiúscula, caso contrário a credencial não será encontrada.

        ![conector de armazém de colmeiaS Domínio AAD](./media/apache-hive-warehouse-connector/aad-domain.png)

    * Por exemplo, `hive/hn0-ng36ll.mjry42ikpruuxgs2qy2kpg4q5e.cx.internal.cloudapp.net@PKRSRVUQVMAE6J85.D2.INTERNAL.CLOUDAPP.NET` . .
    
1. Guarde as alterações e reinicie os componentes conforme necessário.

## <a name="hive-warehouse-connector-usage"></a>Utilização do conector do armazém da colmeia

Pode escolher entre alguns métodos diferentes para ligar ao seu cluster de consultas interativas e executar consultas usando o Conector do Armazém da Colmeia. Os métodos suportados incluem as seguintes ferramentas:

* [Spark-shell / PySpark](../spark/apache-spark-shell.md)
* [Submissão de faíscas](#spark-submit)
* [Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)


Abaixo estão alguns exemplos para ligar ao HWC da Spark.

### <a name="spark-shell"></a>Concha de faísca

1. Use [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para ligar ao seu aglomerado de faíscas Apache. Edite o comando abaixo substituindo o CLUSTERNAME pelo nome do seu cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A partir da sua sessão de sessão, execute o seguinte comando para notar a `hive-warehouse-connector-assembly` versão:

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. Edite o código abaixo com a `hive-warehouse-connector-assembly` versão acima identificada. Em seguida, execute o comando para iniciar a concha de faísca:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Depois de iniciar a casca de faísca, uma instância do Conector do Armazém da Colmeia pode ser iniciada utilizando os seguintes comandos:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="spark-submit"></a>Submissão de faíscas

Assim que construir o código scala/java juntamente com as dependências num frasco de montagem, utilize o comando abaixo para lançar uma aplicação Spark. Substitua `<VERSION>` , e `<APP_JAR_PATH>` pelos valores reais.

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

* Modo cluster YARN
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
    
## <a name="run-queries-on-enterprise-security-package-esp-clusters"></a>Executar consultas sobre clusters do Pacote de Segurança Empresarial (ESP)

Utilize `kinit` antes de iniciar a faísca ou a faísca. Substitua o USERNAME pelo nome de uma conta de domínio por permissões de acesso ao cluster e, em seguida, execute o seguinte comando:

```bash
kinit USERNAME
```

### <a name="securing-data-on-spark-esp-clusters"></a>Obtenção de dados sobre clusters Spark ESP

1. Criar uma tabela `demo` com alguns dados de amostra, introduzindo os seguintes comandos:

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

    ![mesa de demonstração antes de aplicar a política ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Aplique uma política de mascaramento de coluna que apenas mostre os últimos quatro caracteres da coluna.  
    1. Vá ao Ranger Admin UI em `https://LLAPCLUSTERNAME.azurehdinsight.net/ranger/` .
    1. Clique no serviço Hive para o seu cluster sob **a Colmeia.**
        ![ranger gerente de serviço](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Clique no **separador Mascaramento** e, em seguida, **Adicione Nova Política**

        ![lista de política de ligação de colmeia de armazém de colmeias de colmeias](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    1. Fornecer um nome de política desejado. Selecione base de dados: **Predefinição,** tabela De Colmeia: **demo,** coluna Hive: **nome,** Utilizador: **rsadmin2**, Tipos de acesso: **selecione**, e **máscara parcial: apareça no último 4** do menu **Select Masking Option.** Clique em **Adicionar**.
                ![criar política](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Volte a ver o conteúdo da tabela. Depois de aplicar a política dos rangers, só podemos ver os últimos quatro caracteres da coluna.

    ![mesa de demonstração depois de aplicar a política ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Próximos passos

* [Operações do HWC e do Apache Spark](./apache-hive-warehouse-connector-operations.md)
* [Utilizar uma Consulta Interativa com o HDInsight](./apache-interactive-query-get-started.md).
* [Integração do HWC no Apache Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)
* [Exemplos de interação com o Conector do Armazém da Colmeia utilizando Zeppelin, Livy, spark-submit e pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
