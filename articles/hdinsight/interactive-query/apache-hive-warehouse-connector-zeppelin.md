---
title: Hive Warehouse Connector - Apache Zeppelin usando Livy - Azure HDInsight
description: Saiba como integrar o Conector de Armazém hive com Apache Zeppelin no Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 1f9d2d9bd2a58fa4c6f14db8ffd067bb39fc1553
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853814"
---
# <a name="integrate-apache-zeppelin-with-hive-warehouse-connector-in-azure-hdinsight"></a>Integrar O Apache Zeppelin com o Conector de Armazém hive em Azure HDInsight

Os clusters HDInsight Spark incluem cadernos Apache Zeppelin com diferentes intérpretes. Neste artigo, vamos focar-nos apenas no intérprete livy para aceder às mesas da Colmeia da Spark usando o Conector hive Warehouse.

## <a name="prerequisite"></a>Pré-requisito

Complete os passos de configuração do [Conector do Armazém hive.](apache-hive-warehouse-connector.md#hive-warehouse-connector-setup)

## <a name="getting-started"></a>Introdução

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se ligar ao seu aglomerado de Faíscas Apache. Editar o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, introduzir o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A partir da sua sessão ssh, execute o seguinte comando para observar as versões para `hive-warehouse-connector-assembly` `pyspark_hwc` e:

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

    Guarde a saída para posterior utilização ao configurar o Apache Zeppelin.

## <a name="configure-livy"></a>Configure Livy

As seguintes configurações são necessárias para aceder às mesas de colmeia de Zeppelin com o intérprete Livy.

### <a name="interactive-query-cluster"></a>Cluster de consulta interativa

1. De um navegador web, navegue até `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HDFS/configs` onde LLAPCLUSTERNAME é o nome do seu cluster De consulta interativa.

1. Navegue **Advanced**para o  >  **núcleo do site personalizado**avançado. Selecione **Adicionar Propriedade...** para adicionar as seguintes configurações:

    | Configuração                 | Valor |
    | ----------------------------- |-------|
    | hadoop.proxyuser.livy.groups  | *     |
    | hadoop.proxyuser.livy.hosts   | *     |

1. Guarde alterações e reinicie todos os componentes afetados.

### <a name="spark-cluster"></a>Aglomerado de Faíscas

1. A partir de um navegador web, navegue até `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` onde clusterNAME é o nome do seu cluster Apache Spark.

1. Expandir **custom livy2-conf**. Selecione **Adicionar Propriedade...** para adicionar a seguinte configuração:

    | Configuração                 | Valor                                      |
    | ----------------------------- |------------------------------------------  |
    | livy.file.local-dir-whitelist | /usr/hdp/current/hive_warehouse_connector/ |

1. Guarde alterações e reinicie todos os componentes afetados.

### <a name="configure-livy-interpreter-in-zeppelin-ui-spark-cluster"></a>Configure Livy Interpreter em Zeppelin UI (Cluster de Faíscas)

1. De um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net/zeppelin/#/interpreter` até, onde `CLUSTERNAME` está o nome do seu cluster Apache Spark.

1. Navegue até **livy2**.

1. Adicione as seguintes configurações:

    | Configuração                 | Valor                                      |
    | ----------------------------- |:------------------------------------------:|
    | livy.spark.hadoop.hive.llap.daemon.service.hosts | @llap0 |
    | livy.spark.security.credentials.hiveserver2.enabled | true |
    | livy.spark.sql.hive.llap | true |
    | livy.spark.yarn.security.credentials.hiveserver2.enabled | true |
    | livy.superusers | livy,zeppelin |
    | livy.spark.jars | `file:///usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-VERSION.jar`.<br>Substitua a VERSÃO pelo valor obtido de [Getting started,](#getting-started)mais cedo. |
    | livy.spark.submit.pyFiles | `file:///usr/hdp/current/hive_warehouse_connector/pyspark_hwc-VERSION.zip`.<br>Substitua a VERSÃO pelo valor obtido de [Getting started,](#getting-started)mais cedo. |
    | livy.spark.sql.hive.hiveserver2.jdbc.url | Delineie-o para o URL JDBC Interativo HiveServer2 do cluster De consulta interativa. |
    | spark.security.credentials.hiveserver2.enabled | true |

1. Apenas para clusters ESP, adicione a seguinte configuração:

    | Configuração| Valor|
    |---|---|
    | livy.spark.sql.hive.hiveserver2.jdbc.url.principal | `hive/<headnode-FQDN>@<AAD-Domain>` |

    `<headnode-FQDN>`Substitua-a com o nome de domínio totalmente qualificado do nó de cabeça do cluster De consulta interativa.
    Substitua `<AAD-DOMAIN>` pelo nome do Diretório Ativo Azure (AAD) a que o cluster está unido. Use uma corda maiúscula para o `<AAD-DOMAIN>` valor, caso contrário a credencial não será encontrada. Verifique `/etc/krb5.conf` se é necessário o nome do reino.

1. Guarde as alterações e reinicie o intérprete Livy.

Se o intérprete da Livy não estiver acessível, modifique o ficheiro presente dentro do `shiro.ini` componente Zeppelin em Ambari. Para mais informações, consulte [Configurar a Segurança Apache Zeppelin](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.0.1/configuring-zeppelin-security/content/enabling_access_control_for_interpreter__configuration__and_credential_settings.html).  


## <a name="running-queries-in-zeppelin"></a>Perguntas de execução em Zeppelin 

Lance um caderno Zeppelin usando o intérprete da Livy e execute o seguinte

```python
%livy2

import com.hortonworks.hwc.HiveWarehouseSession
import com.hortonworks.hwc.HiveWarehouseSession._
import org.apache.spark.sql.SaveMode

# Initialize the hive context
val hive = HiveWarehouseSession.session(spark).build()

# Create a database
hive.createDatabase("hwc_db",true)
hive.setDatabase("hwc_db")

# Create a Hive table
hive.createTable("testers").ifNotExists().column("id", "bigint").column("name", "string").create()

val dataDF = Seq( (1, "foo"), (2, "bar"), (8, "john")).toDF("id", "name")

# Validate writes to the table
dataDF.write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table", "hwc_db.testers").save()

# Validate reads
hive.executeQuery("select * from testers").show()

```

## <a name="next-steps"></a>Passos seguintes

* [Operações de HWC e Apache Spark](./apache-hive-warehouse-connector-operations.md)
* [Integração de HWC com Apache Spark e Apache Hive](./apache-hive-warehouse-connector.md)
* [Utilizar uma Consulta Interativa com o HDInsight](./apache-interactive-query-get-started.md).