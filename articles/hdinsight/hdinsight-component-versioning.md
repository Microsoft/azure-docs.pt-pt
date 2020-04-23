---
title: Componentes e versões Apache Hadoop - Azure HDInsight
description: Aprenda os componentes e versões Apache Hadoop no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/26/2020
ms.openlocfilehash: 337d36d8f5e7a7b69b98de2f68ff372d4914f905
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084775"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Quais são os componentes e versões Apache Hadoop disponíveis com o HDInsight?

Conheça os componentes e versões ambientais [Apache Hadoop](https://hadoop.apache.org/) no Microsoft Azure HDInsight e o Pacote de Segurança Empresarial. Além disso, aprenda a verificar as versões de componentes Hadoop no HDInsight.

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Componentes Apache Hadoop disponíveis com diferentes versões HDInsight

O Azure HDInsight suporta várias versões de cluster Hadoop que podem ser implementadas a qualquer momento. Em 4 de abril de 2017, a versão padrão do cluster usada pelo Azure HDInsight é de 3.6.

As versões componentes associadas às versões de cluster HDInsight estão listadas na tabela seguinte:

> [!NOTE]  
> A versão predefinida para o serviço HDInsight pode mudar sem aviso prévio. Se tiver uma dependência de versão, especifique a versão HDInsight quando criar os seus clusters com o .NET SDK com o Azure PowerShell e o Azure Classic CLI.

| Componente              | HDInsight 4.0 | HDInsight 3.6 (Padrão)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop e ARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Porco Apache             | 0.16.0        | 0.16.0                      |
| Colmeia Apache            | 3.1.0         | 2.1.0 (clusters não ESP), 1.2.1 (clusters DEP)                |
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

As versões de componentes do ambiente Hadoop associadas às versões de cluster HDInsight podem alterar-se com atualizações para HDInsight. Para verificar os componentes Hadoop e para verificar quais as versões que estão a ser utilizadas para um cluster, utilize a API Ambari REST. O comando **GetComponentInformation** recupera informações sobre componentes de serviço. Para mais detalhes, consulte a [documentação Apache Ambari.](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)

### <a name="release-notes"></a>Notas de versão

Consulte [as notas de lançamento do HDInsight](hdinsight-release-notes.md) para obter notas de lançamento adicionais nas versões mais recentes do HDInsight.

## <a name="supported-hdinsight-versions"></a>Versões HDInsight suportadas

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>Expiração de suporte e aposentadoria para versões HDInsight

**A expiração** do suporte significa que a Microsoft deixará de fornecer suporte para a versão HDInsight especificada. E deixará de estar disponível através do portal Azure para a criação de clusters. No entanto, estas versões ainda podem ser criadas utilizando o Azure CLI ou os vários SDKs.

**A retirada** de uma versão HDInsight significa que os clusters existentes continuarão a funcionar como está. No entanto, novos clusters desta versão não podem ser criados através de quaisquer meios (incluindo CLI e SDKs). Outras características do plano de controlo (como a escala manual e a autoscalcificação) também podem não funcionar após a reforma da versão. O suporte não está disponível para versões reformadas.

As tabelas seguintes listam as versões do HDInsight. A data de validade do apoio e da reforma também são fornecidas, quando são conhecidas.

### <a name="available-versions"></a>Versões disponíveis

A tabela seguinte lista as versões do HDInsight que estão disponíveis no portal Azure e outros métodos de implementação como powerShell e .NET SDK.

| Versão HDInsight | SO da VM | Data da versão | Data de validade do suporte | Data de aposentadoria | Elevada disponibilidade |  Disponibilidade no portal Azure |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |Setembro de 24, 2018 | | |Sim |Sim |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |4 de abril de 2017 | 31 de dezembro de 2020 |31 de dezembro de 2020 |Sim |Sim |

Spark 2.1, 2.2 & kafka 1.0 apoio expirará em 30 de junho de 2020.

> [!NOTE]  
> Depois de expirado o suporte para uma versão, pode não estar disponível através do portal Microsoft Azure. No entanto, as versões `Version` de cluster continuam disponíveis utilizando o parâmetro no comando Windows PowerShell [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) e no .NET SDK até à data de reforma da versão.

### <a name="retired-versions"></a>Versões reformadas

A tabela seguinte lista as versões do HDInsight que **não estão** disponíveis no portal Azure.

| Versão HDInsight | Versão HDP | SO da VM | Data da versão | Data de validade do suporte | Data de aposentadoria | Elevada disponibilidade |  Disponibilidade no portal Azure |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16.0.4 LTS |30 de setembro de 2016 |5 de setembro de 2017 |28 de junho de 2018 |Sim |Não |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |29 de março de 2016 |29 de dezembro de 2016 |9 de janeiro de 2018 |Sim |Não |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2 de dezembro de 2015 |27 de junho de 2016 |31 de julho de 2018 |Sim |Não |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2 de dezembro de 2015 |27 de junho de 2016 |31 de julho de 2017 |Sim |Não |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS ou Windows Server 2012 R2 |18 de fevereiro de 2015 |1 de março de 2016 |1 de abril de 2017 |Sim |Não |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |24 de junho de 2014 |18 de maio de 2015 |30 de junho de 2016 |Sim |Não |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |11 de fevereiro de 2014 |17 de setembro de 2014 |30 de junho de 2015 |Sim |Não |
| HDInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |28 de outubro de 2013 |12 de maio de 2014 |31 de maio de 2015 |Sim |Não |
| HDInsight 1.6 |HDP 1.1 | |28 de outubro de 2013 |26 de abril de 2014 |31 de maio de 2015 |Não |Não |

> [!NOTE]  
> Clusters altamente disponíveis com dois nóóis de cabeça são implantados por padrão para a versão 2.1 da HDInsight e posteriormente. Não estão disponíveis para os clusters da versão 1.6 da Versão HDInsight.

## <a name="enterprise-security-package-for-hdinsight"></a>Pacote de segurança empresarial para HDInsight

Enterprise Security é um pacote opcional que pode adicionar no seu cluster HDInsight como parte da criação de fluxo de trabalho de cluster. O Pacote de Segurança Empresarial suporta:

- Integração com Diretório Ativo para autenticação.

    No passado, criou clusters HDInsight com o utilizador administrativo local e o utilizador local de SSH. O utilizador administrativo local pode aceder a todos os ficheiros, pastas, tabelas e colunas.  Com o Enterprise Security Package, permite o controlo de acesso baseado em funções, integrando o HDInsight com o seu Diretório Ativo. Que inclui o Ative Directory no local, os Serviços de Domínio de Diretório Ativo Azure. Ou Diretório Ativo na máquina virtual IaaS. O administrador de domínio do cluster pode conceder aos utilizadores a utilização do seu próprio nome de utilizador e palavra-passe corporativos (domínio).

    Para obter mais informações, consulte:

    - [Uma introdução à segurança apache Hadoop com clusters HDInsight unidos pelo domínio](./domain-joined/hdinsight-security-overview.md)
    - [Grupos Apache Hadoop unidos pelo domínio do Plan Azure em HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Configurar o ambiente sandbox associado a um domínio](./domain-joined/apache-domain-joined-configure.md)
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

### <a name="pricing-and-service-level-agreement-sla"></a>Acordo de preços e nível de serviço (SLA)

Para obter informações sobre preços e SLA para o Pacote de Segurança Empresarial, consulte [os preços hDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Acordo de nível de serviço para versões de cluster HDInsight

O acordo de nível de serviço (SLA) é definido como uma _janela de suporte_. Janela de suporte é o período de tempo `Microsoft Customer Service and Support`em que uma versão HDInsight é suportada por . Se a versão tiver uma data de validade de _suporte_aprovada, o cluster HDInsight está fora da janela de suporte. A expiração do suporte para a versão X do HDInsight (depois de uma versão X+1 mais recente) é a mais tarde:  

- Fórmula 1: Adicione 180 dias à data em que a versão do cluster HDInsight X foi lançada.
- Fórmula 2: Adicione 90 dias à data em que a versão de cluster HDInsight X+1 é disponibilizada no portal Azure.

A data de _aposentadoria_ é a data após a qual a versão cluster não pode ser criada no HDInsight. A partir de 31 de julho de 2017, não é possível redimensionar um cluster HDInsight após a data de reforma.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Configuração padrão do nó e tamanhos de máquina virtual para clusters

Para obter mais informações sobre quais as SKUs da máquina virtual para selecionar para o seu cluster, consulte os detalhes de [configuração do cluster Azure HDInsight](hdinsight-supported-node-configuration.md).

## <a name="next-steps"></a>Passos seguintes

- [Configuração de cluster para Apache Hadoop, Spark e muito mais no HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Trabalhe em Apache Hadoop no HDInsight a partir de um PC do Windows](hdinsight-hadoop-windows-tools.md)
- [Notas de lançamento da Hortonworks associadas às versões Azure HDInsight](./hortonworks-release-notes.md)
