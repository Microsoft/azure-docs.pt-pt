---
title: Componentes e versões Apache Hadoop - Azure HDInsight
description: Aprenda os componentes e versões Apache Hadoop no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/02/2020
ms.openlocfilehash: 13c064701a5ecae2090a99980249f5daaa8d5395
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198961"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Quais são os componentes e versões Apache Hadoop disponíveis com o HDInsight?

Conheça os componentes e versões do ecossistema [Apache Hadoop](https://hadoop.apache.org/) no Microsoft Azure HDInsight, bem como o Pacote de Segurança Empresarial. Além disso, aprenda a verificar as versões de componentes Hadoop no HDInsight.

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Componentes Apache Hadoop disponíveis com diferentes versões HDInsight

O Azure HDInsight suporta várias versões de cluster Hadoop que podem ser implementadas a qualquer momento. A partir de 4 de abril de 2017, a versão padrão do cluster usada pelo Azure HDInsight é de 3.6.

As versões componentes associadas às versões de cluster HDInsight estão listadas na tabela seguinte:

> [!NOTE]  
> A versão predefinida para o serviço HDInsight pode mudar sem aviso prévio. Se tiver uma dependência de versão, especifique a versão HDInsight quando criar os seus clusters com o .NET SDK com o Azure PowerShell e o Azure Classic CLI.

| Componente              | HDInsight 4.0 | HDInsight 3.6 (Padrão)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop e ARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Porco Apache             | 0.16.0        | 0.16.0                      |
| Colmeia Apache            | 3.1.0         | 2.1.0, 1.2.1                |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.0.2         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0+                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1, 2.4    | 2.3.0, 2.2.0, 2.1.0         |
| Apache Livy            | 0,5           | 0.4, 0.4, 0.3               |
| Apache Kafka           | 1.1.1, 2.1    | 1.1, 1.0 * (Ver nota abaixo) |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> Devido a considerações de desempenho do sistema, o suporte para a versão 0.10 da Kafka expirou em março de 2019.

## <a name="check-for-current-hadoop-component-version-information"></a>Verifique se existem informações atuais sobre a versão do componente Hadoop

As versões de componentes do ecossistema Hadoop associadas às versões de cluster HDInsight podem mudar com atualizações para HDInsight. Para verificar os componentes Hadoop e para verificar quais as versões que estão a ser utilizadas para um cluster, utilize a API Ambari REST. O comando **GetComponentInformation** recupera informações sobre componentes de serviço. Para mais detalhes, consulte a [documentação Apache Ambari.](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)

### <a name="release-notes"></a>Notas de versão

Consulte [as notas de lançamento do HDInsight](hdinsight-release-notes.md) para obter notas de lançamento adicionais nas versões mais recentes do HDInsight.

## <a name="supported-hdinsight-versions"></a>Versões HDInsight suportadas

As tabelas seguintes listam as versões do HDInsight. A data de validade do apoio e da reforma também são fornecidas, quando são conhecidas.

### <a name="available-versions"></a>Versões disponíveis

A tabela seguinte lista as versões do HDInsight que estão disponíveis no portal Azure, bem como outros métodos de implementação como powerShell e .NET SDK.

| Versão HDInsight | VM OS | Data da versão | Data de validade do suporte | Data de aposentadoria | Elevada disponibilidade |  Disponibilidade no portal Azure |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |24 de setembro de 2018 | | |Sim |Sim |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |4 de abril de 2017 | 31 de dezembro de 2020 |31 de dezembro de 2020 |Sim |Sim |

O apoio spark 2.1, 2.2 e Kafka 1.0 expirará em 30 de junho de 2020.

> [!NOTE]  
> Depois de expirado o suporte para uma versão, pode não estar disponível através do portal Microsoft Azure. No entanto, as versões de cluster continuam disponíveis utilizando o parâmetro `Version` no comando Windows PowerShell [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) e no .NET SDK até à data de reforma da versão.

### <a name="retired-versions"></a>Versões reformadas

A tabela seguinte lista as versões do HDInsight que **não** estão disponíveis no portal Azure.

| Versão HDInsight | Versão HDP | VM OS | Data da versão | Data de validade do suporte | Data de aposentadoria | Elevada disponibilidade |  Disponibilidade no portal Azure |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16.0.4 LTS |30 de setembro de 2016 |5 de setembro de 2017 |28 de junho de 2018 |Sim |Não |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |29 de Março de 2016 |29 de dezembro de 2016 |9 de janeiro de 2018 |Sim |Não |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2 de dezembro de 2015 |27 de junho de 2016 |31 de julho de 2018 |Sim |Não |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2 de dezembro de 2015 |27 de junho de 2016 |31 de julho de 2017 |Sim |Não |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS ou Windows Server 2012 R2 |18 de fevereiro de 2015 |1 de março de 2016 |1 de abril de 2017 |Sim |Não |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |24 de junho de 2014 |18 de maio de 2015 |30 de Junho de 2016 |Sim |Não |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |11 de fevereiro de 2014 |17 de setembro de 2014 |30 de junho de 2015 |Sim |Não |
| HDInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |28 de outubro de 2013 |12 de maio de 2014 |31 de maio de 2015 |Sim |Não |
| HDInsight 1.6 |HDP 1.1 | |28 de outubro de 2013 |26 de abril de 2014 |31 de maio de 2015 |Não |Não |

> [!NOTE]  
> Clusters altamente disponíveis com dois nóóis de cabeça são implantados por padrão para a versão 2.1 da HDInsight e posteriormente. Não estão disponíveis para os clusters da versão 1.6 da Versão HDInsight.

## <a name="enterprise-security-package-for-hdinsight"></a>Pacote de segurança empresarial para HDInsight

Enterprise Security é um pacote opcional que pode adicionar no seu cluster HDInsight como parte da criação de fluxo de trabalho de cluster. O Pacote de Segurança Empresarial suporta:

- Integração com Diretório Ativo para autenticação.

    No passado, só pode criar clusters HDInsight com um utilizador de administração local e um utilizador local de SSH. O utilizador administrativo local pode aceder a todos os ficheiros, pastas, tabelas e colunas.  Com o Pacote de Segurança Empresarial, pode ativar o controlo de acesso baseado em papéis, integrando clusters HDInsight com o seu próprio Diretório Ativo, que incluem no local Ative Directory, Azure Ative Directory Domain Services ou Ative Directory no IaaS máquina virtual. O administrador de domínio do cluster pode conceder aos utilizadores que utilizem o seu próprio nome de utilizador e senha corporativa (domínio) para aceder ao cluster.

    Para obter mais informações, consulte:

    - [Uma introdução à segurança apache Hadoop com clusters HDInsight unidos pelo domínio](./domain-joined/hdinsight-security-overview.md)
    - [Grupos Apache Hadoop unidos pelo domínio do Plan Azure em HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Configure ambiente de caixa de areia unida para o domínio](./domain-joined/apache-domain-joined-configure.md)
    - [Configure clusters HDInsight unidos pelo domínio utilizando serviços de domínio de diretório ativo Azure](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Autorização de dados

  - Integração com Apache Ranger para autorização para filas de Colmeia, Spark SQL e Fios.
  - Pode definir o controlo de acesso em ficheiros e pastas.

    Para obter mais informações, consulte:

  - [Configure as políticas da Hive Apache no HDInsight filiado em domínio](./domain-joined/apache-domain-joined-run-hive.md)

- Consulte os registos de auditoria para monitorizar os acessos e as políticas configuradas.

### <a name="supported-cluster-types"></a>Tipos de cluster suportados

Atualmente, apenas os seguintes tipos de cluster suportam o Pacote de Segurança Empresarial:

- Hadoop (apenas HDInsight 3.6)
- Spark
- Kafka
- HBase
- Interactive Query

### <a name="support-for-azure-data-lake-storage"></a>Suporte para armazenamento de lagos de dados azure

O Pacote de Segurança Empresarial suporta a utilização do Armazenamento de Lagos De Dados Azure como armazenamento primário e armazenamento de complemento.

### <a name="pricing-and-service-level-agreement"></a>Acordo de preços e nível de serviço

Para obter informações sobre preços e SLA para o Pacote de Segurança Empresarial, consulte [os preços hDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Acordo de nível de serviço para versões de cluster HDInsight

O acordo de nível de serviço (SLA) é definido em termos de janela de _suporte_. A janela de suporte é o período de tempo em que uma versão de cluster HDInsight é suportada pelo Microsoft Customer Service and Support. Se a versão tiver uma data de validade de _suporte_ que tenha passado, o cluster HDInsight está fora da janela de suporte. A data de validade do suporte para uma versão X específica do HDInsight (após a disponibilização de uma versão X+1 mais recente) é calculada como a posterior:  

- Fórmula 1: Adicione 180 dias à data em que a versão do cluster HDInsight X foi lançada.
- Fórmula 2: Adicione 90 dias à data em que a versão de cluster HDInsight X+1 é disponibilizada no portal Azure.

A data de _aposentadoria_ é a data após a qual a versão cluster não pode ser criada no HDInsight. A partir de 31 de julho de 2017, não é possível redimensionar um cluster HDInsight após a data de reforma.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Notas de lançamento da Hortonworks associadas às versões HDInsight

A secção fornece links para notas de lançamento para as distribuições da Plataforma de Dados Hortonworks e componentes Apache que são usados com HDInsight.
* A versão 4.0 do cluster HDInsight utiliza uma distribuição Hadoop baseada na Plataforma de [Dados Hortonworks 3.0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html)
* A versão 3.6 do cluster HDInsight utiliza uma distribuição Hadoop baseada na Plataforma de [Dados Hortonworks 2.6](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html).
* A versão 3.5 do cluster HDInsight utiliza uma distribuição Hadoop baseada na Plataforma de [Dados Hortonworks 2.5](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). A versão 3.5 do cluster HDInsight é o cluster Hadoop _padrão_ que é criado no portal Azure.
* A versão 3.4 do cluster HDInsight utiliza uma distribuição Hadoop baseada na Plataforma de [Dados Hortonworks 2.4](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html).
* A versão 3.3 do cluster HDInsight utiliza uma distribuição Hadoop baseada na Plataforma de [Dados Hortonworks 2.3](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * As notas de lançamento da [Apache Storm](https://storm.apache.org/2015/11/05/storm0100-released.html) estão disponíveis no site da Apache.
  * As notas de lançamento da [Apache Hive](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) estão disponíveis no site da Apache.
* A versão 3.2 do cluster HDInsight utiliza uma distribuição Hadoop baseada na Plataforma de [Dados Hortonworks 2.2][hdp-2-2].

  * As notas de lançamento para componentes Apache específicos estão disponíveis da seguinte forma: [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Tempestade 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), e [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* A versão 3.1 do cluster HDInsight utiliza uma distribuição Hadoop baseada na Plataforma de [Dados Hortonworks 2.1.7][hdp-2-1-7]. Os clusters HDInsight 3.1 criados antes de novembro de 7 de 2014, são baseados na Plataforma de [Dados Hortonworks 2.1.1][hdp-2-1-1].
* A versão 3.0 do cluster HDInsight utiliza uma distribuição Hadoop baseada na Plataforma de [Dados Hortonworks 2.0][hdp-2-0-8].
* A versão 2.1 do cluster HDInsight utiliza uma distribuição Hadoop baseada na Plataforma de [Dados Hortonworks 1.3][hdp-1-3-0].
* A versão de cluster HDInsight 1.6 utiliza uma distribuição Hadoop baseada na Plataforma de [Dados Hortonworks 1.1][hdp-1-1-0].

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Configuração padrão do nó e tamanhos de máquina virtual para clusters

Para obter mais informações sobre quais as SKUs da máquina virtual para selecionar para o seu cluster, consulte os detalhes de [configuração do cluster Azure HDInsight](hdinsight-supported-node-configuration.md).

## <a name="next-steps"></a>Passos seguintes

- [Configuração de cluster para Apache Hadoop, Spark e muito mais no HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Trabalhe em Apache Hadoop no HDInsight a partir de um PC do Windows](hdinsight-hadoop-windows-tools.md)

[hdp-2-2]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.9/bk_HDP_RelNotes/content/ch_relnotes_v229.html

[hdp-2-1-7]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.1.1.16_1.html
