---
title: Componentes e versões do Apache Hadoop – Azure HDInsight
description: Conheça os componentes e as versões do Apache Hadoop no HDInsight e os níveis de serviço disponíveis nesta distribuição na nuvem da plataforma de dados do Hortonworks.
keywords: versões do Hadoop, componentes de ecossistema do Hadoop, componentes do Hadoop, como verificar a versão do Hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/07/2019
ms.openlocfilehash: a9de7c75ef2bd29b2e401ba387ca16a5dfda34fb
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442035"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Quais são os componentes Apache Hadoop e as versões disponíveis com o HDInsight?

Saiba mais sobre os componentes e as versões do ecossistema [Apache Hadoop](https://hadoop.apache.org/) no Microsoft Azure HDInsight, bem como o Enterprise Security Package. Além disso, saiba como verificar as versões de componente do Hadoop no HDInsight.

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Apache Hadoop componentes disponíveis com diferentes versões do HDInsight

O Azure HDInsight dá suporte a várias versões de cluster Hadoop que podem ser implantadas a qualquer momento. Cada opção de versão cria uma versão específica da distribuição HDP e um conjunto de componentes que estão contidos nessa distribuição. A partir de 4 de abril de 2017, a versão de cluster padrão usada pelo Azure HDInsight é 3,6 e é baseada em HDP 2,6.

As versões de componente associadas às versões do cluster HDInsight estão listadas na tabela a seguir: 

> [!NOTE]  
> A versão padrão para o serviço HDInsight pode ser alterada sem aviso prévio. Se você tiver uma dependência de versão, especifique a versão do HDInsight ao criar seus clusters com o SDK do .NET com Azure PowerShell e a CLI clássica do Azure.

| Componente | HDInsight 4.0 | HDInsight 3,6 (padrão) | HDInsight 3.5 | HDInsight 3,4 | HDInsight 3,3 | HDInsight 3,2 |
|---------------------------|---------------|-----------------------------|---------------|---------------|---------------|----------------------|
| Hortonworks Data Platform | 3.0 | 2.6 | 2.5 | 2.4 | 2.3 | 2.2 |
| Apache Hadoop e YARN | 3.1.1 | 2.7.3 | 2.7.3 | 2.7.1 | 2.7.1 | 2.6.0 |
| Apache Tez | 0.9.1 | 0.7.0 | 0.7.0 | 0.7.0 | 0.7.0 | 0.5.2 |
| Apache Pig | 0.16.0 | 0.16.0 | 0.16.0 | 0.15.0 | 0.15.0 | 0.14.0 |
| Apache Hive e HCatalog | - | 1.2.1 | 1.2.1 | 1.2.1 | 1.2.1 | 0.14.0 |
| Apache Hive | 3.1.0 | 2.1.0 | - | - | - | - |
| Apache Tez Hive2 | - | 0.8.4 | - | - | - | - |
| Apache Ranger | 1.1.0 | 0.7.0 | 0.6.0 | - | - | - |
| Apache HBase | 2.0.1 | 1.1.2 | 1.1.2 | 1.1.2 | 1.1.1 | 0.98.4 |
| Apache Sqoop | 1.4.7 | 1.4.6 | 1.4.6 | 1.4.6 | 1.4.6 | 1.4.5 |
| Apache Oozie | 4.3.1 | 4.2.0 | 4.2.0 | 4.2.0 | 4.2.0 | 4.1.0 |
| Apache Zookeeper | 3.4.6 | 3.4.6 | 3.4.6 | 3.4.6 | 3.4.6 | 3.4.6 |
| Apache Storm | - | 1.1.0 | 1.0.1 | 0.10.0 | 0.10.0 | 0.9.3 |
| Apache Mahout | - | 0.9.0+ | 0.9.0+ | 0.9.0+ | 0.9.0+ | 0.9.0 |
| Apache Phoenix | 5 | 4.7.0 | 4.7.0 | 4.4.0 | 4.4.0 | 4.2.0 |
| Apache Spark | 2.3.1, 2.4 | 2.3.0, 2.2.0, 2.1.0 | 1.6.2, 2.0 | 1.6.0 | 1.5.2 | 1.3.1 (somente Windows) |
| Apache Livy | 0,5 | 0,4, 0,4, 0,3 | 0,3 | 0,3 | 0.2 | - |
| Apache Kafka | 1.1.1, 2.1 | 1,1, 1,0 * (veja a observação abaixo) | 0.10.0 | 0.9.0 | - | - |
| Apache Ambari | 2.7.0 | 2.6.0 | 2.4.0 | 2.2.1 | 2.1.0 | - |
| Apache Zeppelin | 0.8.0 | 0.7.0 | - | - | - | - |
| Mixagem | 4.2.1 | 4.2.1 | 4.2.1 | 3.2.8 | - | - |

> [!NOTE]
> Devido a considerações de desempenho do sistema, o suporte para Kafka versão 0,10 expirou em março de 2019.

## <a name="check-for-current-hadoop-component-version-information"></a>Verificar informações de versão do componente do Hadoop atual

As versões do componente de ecossistema do Hadoop associadas às versões do cluster HDInsight podem ser alteradas com as atualizações do HDInsight. Para verificar os componentes do Hadoop e verificar quais versões estão sendo usadas para um cluster, use a API REST do amAmbari. O comando **GetComponentInformation** recupera informações sobre componentes de serviço. Para obter detalhes, consulte a [documentação do Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Notas de versão

Consulte [notas de versão do hdinsight](hdinsight-release-notes.md) para obter notas de versão adicionais sobre as versões mais recentes do HDInsight.

## <a name="supported-hdinsight-versions"></a>Versões do HDInsight com suporte

As tabelas a seguir listam as versões do HDInsight. As versões HDP que correspondem a cada versão do HDInsight são listadas junto com as datas de lançamento do produto. As datas de expiração e desativação do suporte também são fornecidas, quando são conhecidas.

### <a name="available-versions"></a>Versões disponíveis

A tabela a seguir lista as versões do HDInsight que estão disponíveis no portal do Azure, bem como outros métodos de implantação, como o PowerShell e o SDK do .NET.

| Versão do HDInsight | Versão do HDP | SISTEMA OPERACIONAL DA VM | Data de lançamento | Data de validade do suporte | Data de aposentadoria | Elevada disponibilidade |  Disponibilidade no portal do Azure | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |HDP 3.0 |Ubuntu 16.0.4 LTS |24 de setembro de 2018 | | |Sim |Sim |
| HDInsight 3,6 |HDP 2.6 |Ubuntu 16.0.4 LTS |4 de abril de 2017 | 30 de junho de 2020 |31 de dezembro de 2020 |Sim |Sim |


> [!NOTE]  
> Depois que o suporte para uma versão tiver expirado, ele poderá não estar disponível por meio do portal do Microsoft Azure. No entanto, as versões do cluster continuam disponíveis `Version` usando o parâmetro no comando [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) do Windows PowerShell e o SDK do .net até a data de desativação da versão.
>

### <a name="retired-versions"></a>Versões desativadas

A tabela a seguir lista as versões do HDInsight que **não** estão disponíveis no portal do Azure.

| Versão do HDInsight | Versão do HDP | SISTEMA OPERACIONAL DA VM | Data de lançamento | Data de validade do suporte | Data de aposentadoria | Elevada disponibilidade |  Disponibilidade no portal do Azure | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 <br> (Não Spark) |HDP 2,5 |Ubuntu 16.0.4 LTS |30 de setembro de 2016 |5 de setembro de 2017 |28 de junho de 2018 |Sim |Não |
| HDInsight 3,4 |HDP 2.4 |Ubuntu 14.0.4 LTS |29 de Março de 2016 |29 de dezembro de 2016 |9 de janeiro de 2018 |Sim |Não |
| HDInsight 3,3 |HDP 2.3 |Windows Server 2012 R2 |2 de dezembro de 2015 |27 de junho de 2016 |31 de julho de 2018 |Sim |Não |
| HDInsight 3,3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2 de dezembro de 2015 |27 de junho de 2016 |31 de julho de 2017 |Sim |Não |
| HDInsight 3,2 |HDP 2.2 |Ubuntu 12, 4 LTS ou Windows Server 2012 R2 |18 de fevereiro de 2015 |1º de março de 2016 |1º de abril de 2017 |Sim |Não |
| HDInsight 3,1 |HDP 2.1 |Windows Server 2012 R2 |24 de junho de 2014 |18 de maio de 2015 |30 de Junho de 2016 |Sim |Não |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |11 de fevereiro de 2014 |17 de setembro de 2014 |30 de junho de 2015 |Sim |Não |
| HDInsight 2,1 |HDP 1.3 |Windows Server 2012 R2 |28 de outubro de 2013 |12 de maio de 2014 |31 de maio de 2015 |Sim |Não |
| HDInsight 1,6 |HDP 1.1 | |28 de outubro de 2013 |26 de abril de 2014 |31 de maio de 2015 |Não |Não |

> [!NOTE]  
> Clusters altamente disponíveis com dois nós de cabeçalho são implantados por padrão para o HDInsight versão 2,1 e posterior. Eles não estão disponíveis para clusters do HDInsight versão 1,6.

## <a name="enterprise-security-package-for-hdinsight"></a>Enterprise Security Package para HDInsight

O Enterprise Security é um pacote opcional que você pode adicionar ao seu cluster HDInsight como parte do fluxo de trabalho criar cluster. O Enterprise Security Package dá suporte a:

- Integração com o Active Directory para autenticação.

    No passado, você só pode criar clusters HDInsight com um usuário administrador local e um usuário SSH local. O usuário administrador local pode acessar todos os arquivos, pastas, tabelas e colunas.  Com o Enterprise Security Package, você pode habilitar o controle de acesso baseado em função integrando clusters HDInsight com seus próprios Active Directory, que incluem Active Directory local, Azure Active Directory Domain Services ou Active Directory no IaaS máquina virtual. O administrador de domínio no cluster pode permitir que os usuários usem seu próprio nome de usuário corporativo (domínio) e senha para acessar o cluster. 

    Para obter mais informações, consulte:

    - [Uma introdução à segurança de Apache Hadoop com clusters HDInsight ingressados no domínio](./domain-joined/hdinsight-security-overview.md)
    - [Planejar clusters de Apache Hadoop ingressados no domínio do Azure no HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Configurar ambiente de área restrita ingressado no domínio](./domain-joined/apache-domain-joined-configure.md)
    - [Configurar clusters HDInsight ingressados no domínio usando Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Autorização para dados

  - Integração com o Apache Ranger para autorização para as filas Hive, Spark SQL e yarn.
  - Você pode definir o controle de acesso em arquivos e pastas.

    Para obter mais informações, consulte:

  - [Configurar políticas de Apache Hive no HDInsight ingressado no domínio](./domain-joined/apache-domain-joined-run-hive.md)

- Exiba os logs de auditoria para monitorar os acessos e as políticas configuradas. 

### <a name="supported-cluster-types"></a>Tipos de cluster com suporte

Atualmente, somente os seguintes tipos de cluster dão suporte à Enterprise Security Package:

- Hadoop (somente HDInsight 3,6)
- Spark
- Interactive Query

### <a name="support-for-azure-data-lake-storage"></a>Suporte para Azure Data Lake Storage

O Enterprise Security Package dá suporte ao uso de Azure Data Lake Storage como o armazenamento primário e o armazenamento do complemento.

### <a name="pricing-and-service-level-agreement"></a>Preço e contrato de nível de serviço

Para obter informações sobre preços e SLA para o Enterprise Security Package, consulte [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).


## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Contrato de nível de serviço para versões de cluster HDInsight

O SLA (contrato de nível de serviço) é definido em termos de uma _janela de suporte_. A janela de suporte é o período de tempo em que uma versão de cluster do HDInsight tem suporte do serviço de suporte e atendimento ao cliente da Microsoft. Se a versão tiver uma _Data_ de expiração de suporte que tenha passado, o cluster HDInsight estará fora da janela de suporte. A data de validade do suporte para um HDInsight versão X especificado (depois que uma versão X + 1 mais nova estiver disponível) é calculada como a mais posterior de:  

* Fórmula 1: Adicione 180 dias à data em que o cluster HDInsight versão X foi liberado.
* Fórmula 2: Adicione 90 dias à data em que a versão X + 1 do cluster HDInsight é disponibilizada em portal do Azure.

A _data de aposentadoria_ é a data após a qual a versão do cluster não pode ser criada no HDInsight. A partir de 31 de julho de 2017, você não pode redimensionar um cluster HDInsight após sua data de desativação. 

> [!NOTE]  
> Os clusters do Windows HDInsight (incluindo as versões 2,1, 3,0, 3,1, 3,2 e 3,3) são executados na família de sistemas operacionais convidados do Azure versão 4, que usa a versão de 64 bits do Windows Server 2012 R2. A família de sistemas operacionais convidados do Azure versão 4 dá suporte às versões de .NET Framework 4,0, 4,5, 4.5.1 e 4.5.2.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Notas de versão do Hortonworks associadas a versões do HDInsight

A seção fornece links para notas de versão para as distribuições da plataforma de dados Hortonworks e os componentes do Apache que são usados com o HDInsight.
* O cluster HDInsight versão 4,0 usa uma distribuição do Hadoop baseada em [Hortonworks Data Platform 3,0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html)
* O cluster HDInsight versão 3,6 usa uma distribuição do Hadoop baseada em [Hortonworks Data Platform 2,6](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html).
* O cluster HDInsight versão 3,5 usa uma distribuição do Hadoop baseada em [Hortonworks Data Platform 2,5](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). O cluster HDInsight versão 3,5 é o cluster Hadoop _padrão_ criado no portal do Azure.
* O cluster HDInsight versão 3,4 usa uma distribuição do Hadoop baseada em [Hortonworks Data Platform 2,4](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html).
* O cluster HDInsight versão 3,3 usa uma distribuição do Hadoop baseada em [Hortonworks Data Platform 2,3](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * [Apache Storm notas de versão](https://storm.apache.org/2015/11/05/storm0100-released.html) estão disponíveis no site do Apache.
  * [Apache Hive notas de versão](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) estão disponíveis no site do Apache.
* O cluster HDInsight versão 3,2 usa uma distribuição do Hadoop baseada em [Hortonworks Data Platform 2,2][hdp-2-2].

  * As notas de versão para componentes específicos do Apache estão disponíveis da seguinte maneira: [Hive 0,14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0,14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2,6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2,6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [yarn 2,6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2,0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112)e [Oozie 4.1.0 ](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* O cluster HDInsight versão 3,1 usa uma distribuição do Hadoop baseada em [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. HDInsight 3.1 clusters created before November, 7, 2014, are based on [Hortonworks Data Platform 2.1.1][hdp-2-1-1].
* O cluster HDInsight versão 3,0 usa uma distribuição do Hadoop baseada em [Hortonworks Data Platform 2,0][hdp-2-0-8].
* O cluster HDInsight versão 2,1 usa uma distribuição do Hadoop baseada em [Hortonworks Data Platform 1,3][hdp-1-3-0].
* O cluster HDInsight versão 1,6 usa uma distribuição do Hadoop baseada em [Hortonworks Data Platform 1,1][hdp-1-1-0].

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Configuração de nó padrão e tamanhos de máquina virtual para clusters

As tabelas a seguir listam os tamanhos de VM (máquina virtual) padrão para clusters HDInsight.  Esse gráfico é necessário para entender os tamanhos de VM a serem usados ao criar o PowerShell ou CLI do Azure scripts para implantar clusters HDInsight.

> [!IMPORTANT]  
> Se você precisar de mais de 32 nós de trabalho em um cluster, deverá selecionar um tamanho de nó de cabeçalho com pelo menos 8 núcleos e 14 GB de RAM.

* Todas as regiões com suporte, exceto sul do Brasil e oeste do Japão:

|Tipo de cluster|Hadoop|HBase|Interactive Query|Storm|Spark|ML Server|Kafka|
|---|---|---|---|---|---|---|---|
|Cabeçalho: tamanho padrão da VM|D12 v2|D12 v2|D13 v2|A3|D12 v2|D12 v2|D3v2|
|Cabeçalho: tamanhos de VM recomendados|D3 v2|D3 v2|D13|A4 v2|D12 v2|D12 v2|A2M v2|
||D4 v2|D4 v2|D14|A8 v2|D13 v2|D13 v2|D3 v2|
||D12 v2|D12 v2|E16 v3|A2m v2|D14 v2|D14 v2|D4 v2|
||E4 v3|E4 v3|E32 v3|E4 v3|E4 v3|E4 v3|D12 v2|
|Trabalho: tamanho de VM padrão|D4 v2|D4 v2|D14 v2|D3 v2|D13 v2|D4 v2|4 D12v2 com 2 discos S30 por agente|
|Trabalho: tamanhos de VM recomendados|D3 v2|D3 v2|D13|D3 v2|D4 v2|D4 v2|D13 v2|
||D4 v2|D4 v2|D14|D4 v2|D12 v2|D12 v2|DS12 v2|
||D12 v2|D12 v2|E16 v3|D12 v2|D13 v2|D13 v2|DS13 v2|
||E4 v3|E4 v3|E20 v3|E4 v3|D14 v2|D14 v2|E4 v3|
||||E32 v3||E16 v3|E16 v3|ES4 v3|
||||E64 v3||E20 v3|E20 v3|E8 v3|
||||||E32 v3|E32 v3|ES8 v3|
||||||E64 v3|E64 v3||
|ZooKeeper: tamanho de VM padrão||A4 v2|A4 v2|A4 v2||A2 v2|D3v2|
|ZooKeeper: tamanhos de VM recomendados||A4 v2||A2 v2|||A2M v2|
|||A8 v2||A4 v2|||D3 v2|
|||A2m v2||A8 v2|||E8 v3|
|Serviços de ML: tamanho de VM padrão||||||D4 v2||
|Serviços de ML: tamanho de VM recomendado||||||D4 v2||
|||||||D12 v2||
|||||||D13 v2||
|||||||D14 v2||
|||||||E16 v3||
|||||||E20 v3||
|||||||E32 v3||
|||||||E64 v3||

* Somente sul do Brasil e oeste do Japão (sem tamanhos v2):

  | Tipo de cluster | Hadoop | HBase | Interactive Query |Storm | Spark | ML Services |
  | --- | --- | --- | --- | --- | --- | --- |
  | Cabeçalho: tamanho padrão da VM |D12 |D12  | D13 |A3 |D12 |D12 |
  | Cabeçalho: tamanhos de VM recomendados |D3,<br/> D4,<br/> D12 |D3,<br/> D4,<br/> D12  | D13<br/> D14 |A3,<br/> A4,<br/> A5 |D12<br/> D13<br/> D14 |D12<br/> D13<br/> D14 |
  | Trabalho: tamanho de VM padrão |D4 |D4  |  D14 |D3 |D13 |D4 |
  | Trabalho: tamanhos de VM recomendados |D3,<br/> D4,<br/> D12 |D3,<br/> D4,<br/> D12  | D13<br/> D14 |D3,<br/> D4,<br/> D12 |D4,<br/> D12<br/> D13<br/> D14 | D4,<br/> D12<br/> D13<br/> D14 |
  | ZooKeeper: tamanho de VM padrão | |A4 v2 | A4 v2| A4 v2 | | A2 v2|
  | ZooKeeper: tamanhos de VM recomendados | |A2,<br/> A3,<br/> A4 | |A2,<br/> A3,<br/> A4 | | |
  | Serviços de ML: tamanhos de VM padrão | | | | | |D4 |
  | Serviços de ML: tamanhos de VM recomendados | | | | | |D4,<br/> D12<br/> D13<br/> D14 |

> [!NOTE]
> - O cabeçalho é conhecido como *Nimbus* para o tipo de cluster Storm.
> - O Worker é conhecido como *supervisor* para o tipo de cluster Storm.
> - O Worker é conhecido como *região* para o tipo de cluster HBase.

## <a name="next-steps"></a>Passos seguintes
- [Configuração de cluster para Apache Hadoop, Spark e muito mais no HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Trabalhar em Apache Hadoop no HDInsight de um computador Windows](hdinsight-hadoop-windows-tools.md)

[hdp-2-2]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.9/bk_HDP_RelNotes/content/ch_relnotes_v229.html

[hdp-2-1-7]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.1.1.16_1.html
