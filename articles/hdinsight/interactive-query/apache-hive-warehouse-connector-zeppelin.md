---
title: Conector do Armazém hive - Apache Zeppelin usando Livy - Azure HDInsight
description: Saiba como integrar o Conector do Armazém da Hive com o Apache Zeppelin no Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 4859ff098bf3fdffbecc70608cb147d17d9d7f59
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941163"
---
# <a name="integrate-apache-zeppelin-with-hive-warehouse-connector-in-azure-hdinsight"></a>Integre Apache Zeppelin com Conector de Armazém de Colmeia em Azure HDInsight

Os clusters HDInsight Spark incluem cadernos Apache Zeppelin com diferentes intérpretes. Neste artigo, vamos focar-nos apenas no intérprete Livy para aceder às mesas de Colmeia da Spark usando o Conector do Armazém da Colmeia.

> [!NOTE]
> Este artigo contém referências ao termo *whitelist*, um termo que a Microsoft já não utiliza. Quando o termo for removido do software, vamos removê-lo deste artigo.

## <a name="prerequisite"></a>Pré-requisito

Complete os passos de [configuração do Conector do Armazém da Colmeia.](apache-hive-warehouse-connector.md#hive-warehouse-connector-setup)

## <a name="getting-started"></a>Introdução

1. Use [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para ligar ao seu aglomerado de faíscas Apache. Edite o comando abaixo substituindo o CLUSTERNAME pelo nome do seu cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A partir da sua sessão de sessão, execute o seguinte comando para anotar as versões `hive-warehouse-connector-assembly` `pyspark_hwc` para:

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

    Guarde a saída para utilização posterior ao configurar o Apache Zeppelin.

## <a name="configure-livy"></a>Configure Livy

As configurações seguintes são necessárias para aceder a mesas de colmeia de Zeppelin com o intérprete Livy.

### <a name="interactive-query-cluster"></a>Cluster de Consulta Interativa

1. A partir de um navegador web, navegue `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HDFS/configs` até onde LLAPCLUSTERNAME é o nome do seu cluster de consultainterna.

1. Navegue para o  >  **núcleo avançado personalizado.** Selecione **Adicionar Propriedade...** para adicionar as seguintes configurações:

    | Configuração                 | Valor |
    | ----------------------------- |-------|
    | hadoop.proxyuser.livy.groups  | *     |
    | hadoop.proxyuser.livy.hosts   | *     |

1. Guarde as alterações e reinicie todos os componentes afetados.

### <a name="spark-cluster"></a>Aglomerado de faíscas

1. A partir de um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` até onde CLUSTERNAME é o nome do seu cluster Apache Spark.

1. Expandir **livy2-conf personalizado.** Selecione **Adicionar Propriedade...** para adicionar a seguinte configuração:

    | Configuração                 | Valor                                      |
    | ----------------------------- |------------------------------------------  |
    | livy.file.local-dir-whitelist | /usr/hdp/current/hive_warehouse_connector/ |

1. Guarde as alterações e reinicie todos os componentes afetados.

### <a name="configure-livy-interpreter-in-zeppelin-ui-spark-cluster"></a>Configure o intérprete livy em Zeppelin UI (Spark Cluster)

1. A partir de um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net/zeppelin/#/interpreter` para, onde `CLUSTERNAME` está o nome do seu cluster Apache Spark.

1. Navegue até **livy2.**

1. Adicione as seguintes configurações:

    | Configuração                 | Valor                                      |
    | ----------------------------- |:------------------------------------------:|
    | livy.spark.hadoop.hive.llap.daemon.service.hosts | @llap0 |
    | livy.spark.security.credentials.hiveserver2.enabled | true |
    | livy.spark.sql.hive.llap | true |
    | livy.spark.yarn.security.credentials.hiveserver2.enabled | true |
    | livy.superusers | livy,zeppelin |
    | livy.spark.jars | `file:///usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-VERSION.jar`.<br>Substitua a VERSÃO pelo valor obtido a partir de [Iniciar,](#getting-started)mais cedo. |
    | livy.spark.submit.pyFiles | `file:///usr/hdp/current/hive_warehouse_connector/pyspark_hwc-VERSION.zip`.<br>Substitua a VERSÃO pelo valor obtido a partir de [Iniciar,](#getting-started)mais cedo. |
    | livy.spark.sql.hive.hiveserver2.jdbc.url | Coloque-o no URL JDBC Interativo HiveServer2 do cluster de consultas interativas. |
    | spark.security.credentials.hiveserver2.enabled | true |

1. Apenas para clusters ESP, adicione a seguinte configuração:

    | Configuração| Valor|
    |---|---|
    | livy.spark.sql.hive.hiveserver2.jdbc.url.principal | `hive/<llap-headnode>@<AAD-Domain>` |

    * A partir de um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` até onde CLUSTERNAME é o nome do seu cluster de consultainterna. Clique na **HiveServer2 Interactive**. Verá o Nome de Domínio Totalmente Qualificado (FQDN) do nó de cabeça no qual llap está em execução como mostrado na imagem. `<llap-headnode>`Substitua-o por este valor.

        ![Nó de cabeça do conector do armazém de colmeia](./media/apache-hive-warehouse-connector/head-node-hive-server-interactive.png)

    * Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para ligar ao seu cluster de consultas interativos. Procure `default_realm` por um parâmetro no `/etc/krb5.conf` ficheiro. `<AAD-DOMAIN>`Substitua-o por este valor como uma corda maiúscula, caso contrário a credencial não será encontrada.

        ![conector de armazém de colmeiaS Domínio AAD](./media/apache-hive-warehouse-connector/aad-domain.png)

    * Por exemplo, `hive/hn0-ng36ll.mjry42ikpruuxgs2qy2kpg4q5e.cx.internal.cloudapp.net@PKRSRVUQVMAE6J85.D2.INTERNAL.CLOUDAPP.NET` . .

1. Guarde as alterações e reinicie o intérprete Livy.

Se o intérprete Livy não estiver acessível, modifique o `shiro.ini` ficheiro presente no componente Zeppelin em Ambari. Para mais informações, consulte [a Configuração da Segurança Apache Zeppelin.](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.0.1/configuring-zeppelin-security/content/enabling_access_control_for_interpreter__configuration__and_credential_settings.html)  


## <a name="running-queries-in-zeppelin"></a>Consultas em execução em Zeppelin 

Lançar um caderno Zeppelin usando o intérprete Livy e executar o seguinte

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

## <a name="next-steps"></a>Próximos passos

* [Operações do HWC e do Apache Spark](./apache-hive-warehouse-connector-operations.md)
* [Integração do HWC no Apache Spark e no Apache Hive](./apache-hive-warehouse-connector.md)
* [Utilizar uma Consulta Interativa com o HDInsight](./apache-interactive-query-get-started.md).
