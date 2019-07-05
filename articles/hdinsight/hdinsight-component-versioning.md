---
title: Componentes do Apache Hadoop e de versões - Azure HDInsight
description: Conheça os componentes do Apache Hadoop e versões no HDInsight e os níveis de serviço disponíveis nesta distribuição de nuvem da Hortonworks Data Platform.
keywords: versões do hadoop, componentes do ecossistema do hadoop, componentes do hadoop, como verificar a versão do hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/07/2019
ms.openlocfilehash: 72be96e89cd46267d0c0795bd5121de76e48cf6f
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484087"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Quais são os componentes do Apache Hadoop e versões disponíveis com o HDInsight?

Saiba mais sobre o [Apache Hadoop](https://hadoop.apache.org/) componentes do ecossistema e versões no Microsoft Azure HDInsight, bem como o Enterprise Security Package. Além disso, saiba como verificar as versões de componentes do Hadoop no HDInsight.

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Componentes de Apache Hadoop disponíveis com diferentes versões do HDInsight

O Azure HDInsight oferece suporte a várias versões de cluster de Hadoop que podem ser implementadas em qualquer altura. Cada opção de versão cria uma versão específica da distribuição HDP e um conjunto de componentes que estão contidos dentro dessa distribuição. A partir de 4 de Abril de 2017, a versão de cluster predefinido utilizada pelo Azure HDInsight é 3.6 e se baseia em HDP 2.6.

As versões de componente associadas a versões de cluster do HDInsight estão listadas na tabela a seguir: 

> [!NOTE]  
> A versão predefinida para o serviço HDInsight podem ser alteradas sem aviso prévio. Se tiver uma dependência de versão, especifique a versão do HDInsight quando cria seus clusters com o SDK do .NET com o Azure PowerShell e CLI clássica do Azure.

| Componente | HDInsight 4.0 | HDInsight 3.6 (predefinição) | HDInsight 3.5 | HDInsight 3.4 | HDInsight 3.3 | HDInsight 3.2 |
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
| O Apache Zookeeper | 3.4.6 | 3.4.6 | 3.4.6 | 3.4.6 | 3.4.6 | 3.4.6 |
| Apache Storm | - | 1.1.0 | 1.0.1 | 0.10.0 | 0.10.0 | 0.9.3 |
| Apache Mahout | - | 0.9.0+ | 0.9.0+ | 0.9.0+ | 0.9.0+ | 0.9.0 |
| Apache Phoenix | 5 | 4.7.0 | 4.7.0 | 4.4.0 | 4.4.0 | 4.2.0 |
| Apache Spark | 2.3.1, 2.4 | 2.3.0, 2.2.0, 2.1.0 | 1.6.2, 2.0 | 1.6.0 | 1.5.2 | 1.3.1 (apenas Windows) |
| Apache Livy | 0,5 | 0.4, 0.4, 0.3 | 0.3 | 0.3 | 0.2 | - |
| Apache Kafka | 1.1.1, 2.1 | 1.1, 1.0 * (ver nota abaixo) | 0.10.0 | 0.9.0 | - | - |
| Apache Ambari | 2.7.0 | 2.6.0 | 2.4.0 | 2.2.1 | 2.1.0 | - |
| Apache Zeppelin | 0.8.0 | 0.7.0 | - | - | - | - |
| Mono | 4.2.1 | 4.2.1 | 4.2.1 | 3.2.8 | - | - |

> [!NOTE]
> Devido a considerações de desempenho do sistema, o suporte para o Kafka versão 0.10 expirou em Março de 2019.

## <a name="check-for-current-hadoop-component-version-information"></a>Verifique a existência de informações de versão do componente de Hadoop atuais

Versões de componente do ecossistema do Hadoop associadas a versões de cluster do HDInsight, podem alterar com as atualizações para o HDInsight. Para verificar os componentes do Hadoop e para verificar as versões que estão a ser utilizadas para um cluster, utilize a API de REST do Ambari. O **GetComponentInformation** comando obtém informações sobre os componentes de serviço. Para obter detalhes, consulte a [documentação do Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Notas de versão

Ver [notas de versão do HDInsight](hdinsight-release-notes.md) para notas de versão adicionais nas versões mais recentes do HDInsight.

## <a name="supported-hdinsight-versions"></a>Versões suportadas do HDInsight

As tabelas seguintes listam as versões do HDInsight. As versões HDP que correspondem a cada versão do HDInsight são listadas juntamente com as datas de lançamento do produto. As datas de expiração e extinção de suporte também são fornecidas, quando já forem conhecidas.

### <a name="available-versions"></a>Versões disponíveis

A tabela seguinte lista as versões do HDInsight que estão disponíveis no portal do Azure, bem como outros métodos de implantação, como o PowerShell e o SDK de .NET.

| Versão do HDInsight | Versão HDP | SO DE VM | Data da versão | Data de expiração de suporte | Data de retirada | Elevada disponibilidade |  Disponibilidade no portal do Azure | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |HDP 3.0 |Ubuntu 16.0.4 LTS |24 de setembro de 2018 | | |Sim |Sim |
| HDInsight 3.6 |HDP 2.6 |Ubuntu 16.0.4 LTS |4 de Abril de 2017 | 30 de Junho de 2020 |31 de Dezembro de 2020 |Sim |Sim |


> [!NOTE]  
> Depois de suporte para uma versão tiver expirado, poderá não ser disponível através do portal do Microsoft Azure. No entanto, a versões de cluster continuam a estar disponíveis através da `Version` parâmetro no Windows PowerShell [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) comando e o SDK de .NET até a data de retirada de versão.
>

### <a name="retired-versions"></a>Versões retiradas

A tabela seguinte lista as versões do HDInsight que são **não** disponível no portal do Azure.

| Versão do HDInsight | Versão HDP | SO DE VM | Data da versão | Data de expiração de suporte | Data de retirada | Elevada disponibilidade |  Disponibilidade no portal do Azure | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 <br> (Não-Spark) |HDP 2.5 |Ubuntu 16.0.4 LTS |30 de Setembro de 2016 |5 de Setembro de 2017 |28 de junho de 2018 |Sim |Não |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |29 de Março de 2016 |29 de Dezembro de 2016 |9 de Janeiro de 2018 |Sim |Não |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2 de Dezembro de 2015 |27 de Junho de 2016 |31 de julho de 2018 |Sim |Não |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2 de Dezembro de 2015 |27 de Junho de 2016 |31 de Julho de 2017 |Sim |Não |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS ou Windows Server 2012 R2 |18 de Fevereiro de 2015 |1 de Março de 2016 |1 de Abril de 2017 |Sim |Não |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |24 de Junho de 2014 |18 de Maio de 2015 |30 de Junho de 2016 |Sim |Não |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |11 de Fevereiro de 2014 |17 de Setembro de 2014 |30 de Junho de 2015 |Sim |Não |
| HDInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |28 de Outubro de 2013 |12 de Maio de 2014 |31 de Maio de 2015 |Sim |Não |
| HDInsight 1.6 |HDP 1.1 | |28 de Outubro de 2013 |26 de Abril de 2014 |31 de Maio de 2015 |Não |Não |

> [!NOTE]  
> Clusters de elevada disponibilidade com dois nós principais são implementadas por predefinição para o HDInsight versão 2.1 e posterior. Não estão disponíveis para os clusters do HDInsight versão 1.6.

## <a name="enterprise-security-package-for-hdinsight"></a>Enterprise Security Package para HDInsight

Segurança empresarial é um pacote opcional que pode adicionar no seu cluster do HDInsight como parte do fluxo de trabalho de cluster de criar. O Enterprise Security Package suporta:

- Integração com o Active Directory para autenticação.

    No passado, só pode criar clusters do HDInsight com um utilizador de administrador local e um utilizador SSH local. O utilizador de administrador local pode aceder a todos os arquivos, pastas, tabelas e colunas.  Com o Enterprise Security Package, pode ativar o controlo de acesso baseado em funções, integrando os clusters do HDInsight com o seu próprio Active Directory, que incluem no local do Active Directory, Azure Active Directory Domain Services ou do Active Directory no IaaS máquina virtual. Administrador de domínio no cluster pode conceder aos utilizadores para utilizar o seu próprio nome de utilizador (domínio) empresarial e a palavra-passe para aceder ao cluster. 

    Para obter mais informações, consulte:

    - [Uma introdução à segurança do Apache Hadoop com clusters do HDInsight associados a um domínio](./domain-joined/apache-domain-joined-introduction.md)
    - [Planear clusters do Apache Hadoop associados a um domínio do Azure no HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Configurar o ambiente sandbox associado a um domínio](./domain-joined/apache-domain-joined-configure.md)
    - [Configurar clusters do HDInsight associados a um domínio através do Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Autorização para dados

  - Integração com o Apache Ranger para autorização para Hive, o Spark SQL e o Yarn filas.
  - Pode definir o controlo de acesso em ficheiros e pastas.

    Para obter mais informações, consulte:

  - [Configurar políticas de Apache Hive no HDInsight associado a um domínio](./domain-joined/apache-domain-joined-run-hive.md)

- Ver os registos de auditoria para acessos de monitor e as políticas configuradas. 

### <a name="supported-cluster-types"></a>Tipos de cluster suportadas

Atualmente, apenas os seguintes tipos de cluster suportam o Enterprise Security Package:

- Hadoop (apenas no HDInsight 3.6)
- Spark
- Interactive Query

### <a name="support-for-azure-data-lake-storage"></a>Suporte para o armazenamento do Azure Data Lake

O suporte de Enterprise Security Package usando o armazenamento do Azure Data Lake como armazenamento primário e o armazenamento do suplemento.

### <a name="pricing-and-service-level-agreement"></a>Contrato de nível de preço e de serviço

Para obter informações sobre preços e SLA para o Enterprise Security Package, consulte [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).


## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Contrato de nível de serviço para versões de cluster do HDInsight

O contrato de nível de serviço (SLA) é definido em termos de um _janela de suporte_. A janela de suporte é o período de tempo que uma versão de cluster do HDInsight é suportada pelo suporte ao cliente da Microsoft. Se a versão tem um _suportam a data de expiração_ que tiver passado, o cluster do HDInsight está fora da janela de suporte. A data de expiração de suporte para uma versão X (depois de uma versão mais recente de X + 1 estiver disponível) de HDInsight especificado é calculada como o posterior de:  

* Fórmula 1: Adicione 180 dias para a data quando a versão do cluster do HDInsight X foi lançada.
* Fórmula 2: Adicione a 90 dias da data quando a versão do cluster do HDInsight X + 1 é disponibilizada no portal do Azure.

O _data de retirada_ é a data após o qual não é possível criar a versão do cluster HDInsight. A partir de 31 de Julho de 2017, não pode redimensionar um cluster do HDInsight depois da data de extinção. 

> [!NOTE]  
> Executam clusters do HDInsight Windows (incluindo as versões 2.1, 3.0, 3.1, 3.2 e 3.3) na família de SO convidado do Azure versão 4, que utiliza a versão de 64 bits do Windows Server 2012 R2. Família de SO convidado do Azure versão 4 suporta as versões do .NET Framework 4.0, 4.5, 4.5.1 e 4.5.2.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Hortonworks notas associadas com as versões do HDInsight

A secção fornece ligações para notas de versão das distribuições de Hortonworks Data Platform e os componentes do Apache que são utilizadas com o HDInsight.
* Clusters do HDInsight versão 4.0 utiliza uma distribuição do Hadoop que se baseia em [Hortonworks Data Platform 3.0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html)
* Clusters do HDInsight versão 3.6 utiliza uma distribuição do Hadoop que se baseia [Hortonworks Data Platform 2.6](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html).
* Versão de cluster do HDInsight 3.5 utiliza uma distribuição do Hadoop que se baseia [Hortonworks Data Platform 2.5](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). Versão do cluster do HDInsight 3.5 é o _predefinição_ cluster de Hadoop, que é criado no portal do Azure.
* Clusters do HDInsight versão 3.4 utiliza uma distribuição do Hadoop que se baseia [Hortonworks Data Platform 2.4](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html).
* Clusters do HDInsight versão 3.3 utiliza uma distribuição do Hadoop que se baseia [Hortonworks Data Platform 2.3](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * [Notas de versão do Apache Storm](https://storm.apache.org/2015/11/05/storm0100-released.html) estão disponíveis no site do Apache.
  * [Notas de versão do Apache Hive](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) estão disponíveis no site do Apache.
* Clusters do HDInsight versão 3.2 utiliza uma distribuição do Hadoop que se baseia [Hortonworks Data Platform 2.2][hdp-2-2].

  * Notas de versão para componentes de Apache específicos estão disponíveis da seguinte forma: [Hive 0,14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0,14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [comuns](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [0.9.3 do Storm](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), e [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* Clusters do HDInsight versão 3.1 utiliza uma distribuição do Hadoop que se baseia [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. HDInsight 3.1 clusters created before November, 7, 2014, are based on [Hortonworks Data Platform 2.1.1][hdp-2-1-1].
* Clusters do HDInsight versão 3.0 usa uma distribuição do Hadoop que se baseia [Hortonworks Data Platform 2.0][hdp-2-0-8].
* Clusters do HDInsight versão 2.1 utiliza uma distribuição do Hadoop que se baseia [Hortonworks Data Platform 1.3][hdp-1-3-0].
* Clusters do HDInsight versão 1.6 utiliza uma distribuição do Hadoop que se baseia [Hortonworks Data Platform 1.1][hdp-1-1-0].

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Configuração e a máquina virtual tamanhos de nó predefinidos para clusters

As tabelas seguintes listam os tamanhos de máquina virtual (VM) padrão para os clusters do HDInsight.  Este gráfico é necessário compreender os tamanhos VM para utilizar quando estiver a criar scripts do PowerShell ou da CLI do Azure para implementar clusters do HDInsight.

> [!IMPORTANT]  
> Se precisar de mais do que 32 nós de trabalho num cluster, tem de selecionar um tamanho de nó principal com, pelo menos, 8 núcleos e 14 GB de RAM.

* Suporte todas as regiões, exceto no sul do Brasil e oeste do Japão:

|Tipo de cluster|Hadoop|HBase|Interactive Query|Storm|Spark|Servidor de ML|Kafka|
|---|---|---|---|---|---|---|---|
|Cabeça: tamanho VM predefinido|D12 v2|D12 v2|D13 v2|A3|D12 v2|D12 v2|D3v2|
|HEAD: recomendado tamanhos de VM|D3 v2|D3 v2|D13|A4 v2|D12 v2|D12 v2|A2M v2|
||D4 v2|D4 v2|D14|A8 v2|D13 v2|D13 v2|D3 v2|
||D12 v2|D12 v2|E16 v3|A2m v2|D14 v2|D14 v2|D4 v2|
||E4 v3|E4 v3|E32 v3|E4 v3|E4 v3|E4 v3|D12 v2|
|Função de trabalho: tamanho VM predefinido|D4 v2|D4 v2|D14 v2|D3 v2|D13 v2|D4 v2|4 D12v2 com 2 S30 discos por Mediador|
|Função de trabalho: recomendado tamanhos de VM|D3 v2|D3 v2|D13|D3 v2|D4 v2|D4 v2|D13 v2|
||D4 v2|D4 v2|D14|D4 v2|D12 v2|D12 v2|DS12 v2|
||D12 v2|D12 v2|E16 v3|D12 v2|D13 v2|D13 v2|DS13 v2|
||E4 v3|E4 v3|E20 v3|E4 v3|D14 v2|D14 v2|E4 v3|
||||E32 v3||E16 v3|E16 v3|ES4 v3|
||||E64 v3||E20 v3|E20 v3|E8 v3|
||||||E32 v3|E32 v3|ES8 v3|
||||||E64 v3|E64 v3||
|ZooKeeper: tamanho da VM predefinida||A4 v2|A4 v2|A4 v2||A2 v2|D3v2|
|ZooKeeper: recomendado tamanhos de VM||A4 v2||A2 v2|||A2M v2|
|||A8 v2||A4 v2|||D3 v2|
|||A2m v2||A8 v2|||E8 v3|
|Dos serviços de ML: tamanho VM predefinido||||||D4 v2||
|Serviços de ML: recomendado o tamanho da VM||||||D4 v2||
|||||||D12 v2||
|||||||D13 v2||
|||||||D14 v2||
|||||||E16 v3||
|||||||E20 v3||
|||||||E32 v3||
|||||||E64 v3||

* Sul do Brasil e oeste do Japão apenas (não existem tamanhos v2):

  | Tipo de cluster | Hadoop | HBase | Interactive Query |Storm | Spark | Serviços ML |
  | --- | --- | --- | --- | --- | --- | --- |
  | Cabeça: tamanho VM predefinido |D12 |D12  | D13 |A3 |D12 |D12 |
  | HEAD: recomendado tamanhos de VM |D3,<br/> D4,<br/> D12 |D3,<br/> D4,<br/> D12  | D13,<br/> D14 |A3,<br/> A4,<br/> A5 |D12,<br/> D13,<br/> D14 |D12,<br/> D13,<br/> D14 |
  | Função de trabalho: tamanho VM predefinido |D4 |D4  |  D14 |D3 |D13 |D4 |
  | Função de trabalho: recomendado tamanhos de VM |D3,<br/> D4,<br/> D12 |D3,<br/> D4,<br/> D12  | D13,<br/> D14 |D3,<br/> D4,<br/> D12 |D4,<br/> D12,<br/> D13,<br/> D14 | D4,<br/> D12,<br/> D13,<br/> D14 |
  | ZooKeeper: tamanho da VM predefinida | |A4 v2 | A4 v2| A4 v2 | | A2 v2|
  | ZooKeeper: recomendado tamanhos de VM | |A2,<br/> A3,<br/> A4 | |A2,<br/> A3,<br/> A4 | | |
  | Dos serviços de ML: tamanhos de VM predefinido | | | | | |D4 |
  | Serviços de ML: recomendado tamanhos de VM | | | | | |D4,<br/> D12,<br/> D13,<br/> D14 |

> [!NOTE]
> - HEAD é conhecido como *Nimbus* tipo de cluster para o Storm.
> - Função de trabalho é conhecida como *Supervisor* tipo de cluster para o Storm.
> - Função de trabalho é conhecida como *região* tipo de cluster para o HBase.

## <a name="next-steps"></a>Passos Seguintes
- [Configuração de cluster para Apache Hadoop, Spark e muito mais no HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Trabalhar no Apache Hadoop no HDInsight a partir de um PC do Windows](hdinsight-hadoop-windows-tools.md)

[hdp-2-2]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.9/bk_HDP_RelNotes/content/ch_relnotes_v229.html

[hdp-2-1-7]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.1.1.16_1.html
