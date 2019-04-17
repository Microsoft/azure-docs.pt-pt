---
title: Notas de versão do Azure HDInsight
description: Notas de versão mais recente do Azure HDInsight. Obtenha dicas de desenvolvimento e detalhes para Hadoop, Spark, R Server, do Hive e muito mais.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 6ba9e77faeb297f9862b39384d397b478dc7cf36
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617738"
---
# <a name="release-notes-for-azure-hdinsight"></a>Notas de versão do Azure HDInsight

Este artigo fornece informações sobre o **mais recente** atualizações de versões do Azure HDInsight. Para obter informações sobre versões anteriores, consulte [arquivamento de notas de versão do HDInsight](hdinsight-release-notes-archive.md).

> [!IMPORTANT]  
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, consulte [artigo de controle de versão do HDInsight](hdinsight-component-versioning.md).

## <a name="summary"></a>Resumo

O Azure HDInsight é um dos serviços mais populares entre os clientes empresariais para análise de Apache Hadoop e do Apache Spark de código aberto no Azure.

## <a name="new-features"></a>Novos recursos

Para obter mais informações sobre alterações importantes com o HDInsight 4.0., consulte [o que há de novo no HDI 4.0?](../hdinsight/hdinsight-version-release.md#whats-new-in-hdi-40).

## <a name="component-versions"></a>Versões de componente

As versões de Apache oficiais de todos os componentes do HDInsight 4.0 são indicadas abaixo. Os componentes listados são versões das versões estáveis mais recentes disponíveis.

- Apache Ambari 2.7.1
- Apache Hadoop 3.1.1
- Apache HBase 2.0.0
- Apache Hive 3.1.0
- Apache Kafka 1.1.1
- Apache Mahout 0.9.0+
- Apache Oozie 4.2.0
- Apache Phoenix 4.7.0
- Apache Pig 0.16.0
- Apache Ranger 0.7.0
- Apache Slider 0.92.0
- Apache Spark 2.3.1
- O Apache Sqoop 1.4.7
- Apache TEZ 0.9.1
- Apache Zeppelin 0.8.0
- Apache ZooKeeper 3.4.6

Versões posteriores dos componentes do Apache, às vezes, estão incluídas na distribuição HDP, além das versões listadas acima. Neste caso, estas versões mais recentes estão listados na tabela pré-visualizações técnicas e não devem substituir para as versões de componente do Apache da lista acima no ambiente de produção.

## <a name="apache-patch-information"></a>Informações de patch do Apache

Para obter mais informações sobre correções disponíveis no HDInsight 4.0, consulte o patch de listagem para cada produto na tabela abaixo.

| Nome do produto | Informações de patch |
|---|---|
| Ambari | [Informações de patch do Ambari](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.1.0/bk_ambari-release-notes/content/ambari_relnotes-2.7.1.0-patch-information.html) |
| Hadoop | [Informações de patch do Hadoop](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hadoop.html) |
| HBase | [Informações de patch de HBase](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hbase.html) |
| Hive  | Esta versão fornece Hive 3.1.0 com sem patches Apache adicionais.  |
| Kafka | Esta versão fornece Kafka 1.1.1 com sem patches Apache adicionais. |
| Oozie | [Informações de patch de Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_oozie.html) |
| Phoenix | [Informações de patch do Phoenix](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_phoenix.html) |
| Pig | [Informações de patch do PIg](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_pig.html) |
| Ranger | [Informações de patch do Ranger](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_ranger.html) |
| Spark | [Informações de patch do Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_spark.html) |
| Sqoop | Esta versão fornece Sqoop 1.4.7 com sem patches Apache adicionais. |
| Tez | Esta versão fornece Tez 0.9.1 com sem patches Apache adicionais. |
| Zeppelin | Esta versão fornece Zeppelin 0.8.0 com sem patches Apache adicionais. |
| Zookeeper | [Informações de patch do zookeeper](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_zookeeper.html) |

## <a name="fixed-common-vulnerabilities-and-exposures"></a>Foi corrigido vulnerabilidades e exposições comuns

Para obter mais informações sobre segurança problemas solucionados desta versão, consulte o Hortonworks [fixo vulnerabilidades e exposições comuns para HDP 3.0.1](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/cve.html).

## <a name="known-issues"></a>Problemas conhecidos

### <a name="replication-is-broken-for-secure-hbase-with-default-installation"></a>A replicação está quebrada para proteger o HBase com instalação padrão

Para o HDInsight 4.0, siga os passos abaixo:

1. Ative a comunicação entre clusters.
1. Inicie sessão no nó principal do Active Directory.
1. Transferir um script para ativar a replicação com o seguinte comando:

    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Escreva o comando `sudo kinit <domainuser>`.
1. Escreva o seguinte comando para executar o script:

    ```
    sudo bash hdi_enable_replication.sh -m <hn0> -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```
Para o HDInsight 3.6, faça o seguinte:

1. Inicie sessão no Active Directory HMaster ZK.
1. Transferir um script para ativar a replicação com o seguinte comando:
    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Escreva o comando `sudo kinit -k -t /etc/security/keytabs/hbase.service.keytab hbase/<FQDN>@<DOMAIN>`.
1. Escreva o seguinte comando:

    ```bash
    sudo bash hdi_enable_replication.sh -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```

### <a name="phoenix-sqlline-stops-working-after-migrating-hbase-cluster-to-hdinsight-40"></a>Phoenix Sqlline deixa de funcionar depois de migrar cluster do HBase para HDInsight 4.0

Siga os passos abaixo:

1. Remova as tabelas de Phoenix seguintes:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.MUTEX`
    1. `SYSTEM.CATALOG`
1. Se não é possível eliminar qualquer uma das tabelas, reinicie o HBase para limpar todas as ligações para as tabelas.
1. Execute novamente `sqlline.py`. Phoenix recriará todas as tabelas que foram eliminadas no passo 1.
1. Voltar a gerar as tabelas de Phoenix e vistas para os seus dados do HBase.

### <a name="phoenix-sqlline-stops-working-after-replicating-hbase-phoenix-metadata-from-hdinsight-36-to-40"></a>Phoenix Sqlline deixa de funcionar depois replicar os metadados de HBase Phoenix do HDInsight 3.6 4.0

Siga os passos abaixo:

1. Antes de fazer a replicação, vá para o cluster de destino 4.0 e executar `sqlline.py`. Este comando irá gerar tabelas de Phoenix, como `SYSTEM.MUTEX` e `SYSTEM.LOG` que só existem no 4.0.
1. Remova as tabelas seguintes:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.CATALOG`
1. Começar a replicação de HBase

## <a name="deprecation"></a>Preterição

O Apache Storm e ML serviços não estão disponíveis no HDInsight 4.0.
