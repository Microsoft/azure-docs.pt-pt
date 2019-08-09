---
title: Notas de versão do Azure HDInsight
description: Notas de versão mais recentes do Azure HDInsight. Obtenha dicas e detalhes de desenvolvimento para Hadoop, Spark, R Server, Hive e muito mais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: b6e9a340a1fdcbe3ee24b8c81d171ade04c63139
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880024"
---
# <a name="release-notes-for-azure-hdinsight"></a>Notas de versão do Azure HDInsight

Este artigo fornece informações sobre as atualizações de versão **mais recentes** do Azure HDInsight. Para obter informações sobre versões anteriores, consulte [arquivo de notas de versão do HDInsight](hdinsight-release-notes-archive.md).

> [!IMPORTANT]  
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, consulte o [artigo controle de versão do HDInsight](hdinsight-component-versioning.md).

## <a name="summary"></a>Resumo

O Azure HDInsight é um dos serviços mais populares entre clientes corporativos para Apache Hadoop de software livre e análise de Apache Spark no Azure.

## <a name="new-features"></a>Novos recursos

Para obter mais informações sobre alterações importantes com o HDInsight 4,0., consulte [o que há de novo no HDI 4,0?](../hdinsight/hdinsight-version-release.md).

## <a name="component-versions"></a>Versões de componentes

As versões oficiais do Apache de todos os componentes do HDInsight 4,0 são fornecidas abaixo. Os componentes listados são versões das versões estáveis mais recentes disponíveis.

- 2\.7.1 Apache Ambari
- Apache Hadoop 3.1.1
- Apache HBase 2.0.0
- Apache Hive 3.1.0
- Apache Kafka 1.1.1, 2.1.0
- Apache Mahout 0.9.0 +
- 4\.2.0 Apache Oozie
- Apache Phoenix 4.7.0
- 0\.16.0 Apache Pig
- Apache Ranger 0.7.0
- Apache Slider 0.92.0
- Apache Spark 2.3.1, 2.4.0
- 1\.4.7 Apache Sqoop
- 0\.9.1 Apache TEZ
- 0\.8.0 Apache Zeppelin
- Apache ZooKeeper 3.4.6

As versões posteriores dos componentes do Apache, às vezes, são agrupadas na distribuição HDP, além das versões listadas acima. Nesse caso, essas versões posteriores são listadas na tabela de visualizações técnicas e não devem ser substituídas pelas versões do componente do Apache da lista acima em um ambiente de produção.

## <a name="apache-patch-information"></a>Informações de patch do Apache

Para obter mais informações sobre os patches disponíveis no HDInsight 4,0, consulte a lista de patches para cada produto na tabela abaixo.

| Nome do produto | Informações de patch |
|---|---|
| Ambari | [Informações de patch do Ambari](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.1.0/bk_ambari-release-notes/content/ambari_relnotes-2.7.1.0-patch-information.html) |
| Hadoop | [Informações de patch do Hadoop](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hadoop.html) |
| HBase | [Informações de patch do HBase](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hbase.html) |
| Hive  | Esta versão fornece 3.1.0 do hive sem patches adicionais do Apache.  |
| Kafka | Esta versão fornece o Kafka 1.1.1 sem patches adicionais do Apache. |
| Oozie | [Informações de patch do Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_oozie.html) |
| Phoenix | [Informações de patch de Phoenix](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_phoenix.html) |
| Pig | [Informações de patch do Pig](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_pig.html) |
| Ranger | [Informações de patch do Ranger](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_ranger.html) |
| Spark | [Informações de patch do Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_spark.html) |
| Sqoop | Esta versão fornece Sqoop 1.4.7 sem patches adicionais do Apache. |
| Tez | Esta versão fornece tez 0.9.1 sem patches adicionais do Apache. |
| Zeppelin | Esta versão fornece Zeppelin 0.8.0 sem patches adicionais do Apache. |
| Zookeeper | [Informações de patch do Zookeeper](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_zookeeper.html) |

## <a name="fixed-common-vulnerabilities-and-exposures"></a>Correção de vulnerabilidades e exposições comuns

Para obter mais informações sobre os problemas de segurança resolvidos nesta versão, consulte Hortonworks ' unfixed [Common Vulnerabilities and Exposures for HDP 3.0.1](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/cve.html).

## <a name="known-issues"></a>Problemas conhecidos

### <a name="replication-is-broken-for-secure-hbase-with-default-installation"></a>A replicação foi interrompida para o HBase seguro com instalação padrão

Para o HDInsight 4,0, execute as seguintes etapas:

1. Habilite a comunicação entre clusters.
1. Entre no cabeçalho ativo.
1. Baixe um script para habilitar a replicação com o seguinte comando:

    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Digite o comando `sudo kinit <domainuser>`.
1. Digite o seguinte comando para executar o script:

    ```
    sudo bash hdi_enable_replication.sh -m <hn0> -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```
Para o HDInsight 3,6, faça o seguinte:

1. Entre no Active HMaster ZK.
1. Baixe um script para habilitar a replicação com o seguinte comando:
    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Digite o comando `sudo kinit -k -t /etc/security/keytabs/hbase.service.keytab hbase/<FQDN>@<DOMAIN>`.
1. Escreva o seguinte comando:

    ```bash
    sudo bash hdi_enable_replication.sh -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```

### <a name="phoenix-sqlline-stops-working-after-migrating-hbase-cluster-to-hdinsight-40"></a>O Phoenix sqlline para de funcionar após a migração do cluster HBase para o HDInsight 4,0

Execute as seguintes etapas:

1. Descarte as seguintes tabelas de Phoenix:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.MUTEX`
    1. `SYSTEM.CATALOG`
1. Se você não puder excluir nenhuma das tabelas, reinicie o HBase para limpar todas as conexões com as tabelas.
1. Execute novamente `sqlline.py`. O Phoenix recriará todas as tabelas que foram excluídas na etapa 1.
1. Regenerar tabelas e exibições Phoenix para seus dados do HBase.

### <a name="phoenix-sqlline-stops-working-after-replicating-hbase-phoenix-metadata-from-hdinsight-36-to-40"></a>O Phoenix sqlline para de funcionar após a replicação de metadados do HBase Phoenix do HDInsight 3,6 para o 4,0

Execute as seguintes etapas:

1. Antes de fazer a replicação, vá para o cluster de destino 4,0 `sqlline.py`e execute. Esse comando gerará tabelas Phoenix, `SYSTEM.MUTEX` como `SYSTEM.LOG` e que só existem em 4,0.
1. Descartar as seguintes tabelas:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.CATALOG`
1. Iniciar a replicação do HBase

## <a name="deprecation"></a>Preterição

Os serviços Apache Storm e ML não estão disponíveis no HDInsight 4,0.
