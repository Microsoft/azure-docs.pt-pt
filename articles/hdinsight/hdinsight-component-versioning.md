---
title: Componentes e versões Apache Hadoop - Azure HDInsight
description: Saiba mais sobre os componentes e versões Apache Hadoop em Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 08/13/2020
ms.openlocfilehash: 07db57ee36d39454e8a247be11dac4a9c51af3af
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946106"
---
# <a name="apache-components-and-versions-available-for-azure-hdinsight"></a>Componentes e versões Apache disponíveis para Azure HDInsight

Neste artigo, você aprende sobre os componentes e versões ambientais [Apache Hadoop](https://hadoop.apache.org/) em Azure HDInsight e o Pacote de Segurança Empresarial. Também aprende a verificar as versões de componentes Hadoop em HDInsight.

## <a name="apache-components-available-with-different-hdinsight-versions"></a>Componentes Apache disponíveis com diferentes versões HDInsight

O Azure HDInsight suporta várias versões de cluster Hadoop que podem ser implementadas a qualquer momento. Em 4 de abril de 2017, a versão padrão do cluster utilizada pelo Azure HDInsight foi de 3.6.

As versões componentes associadas às versões hdInsight cluster estão listadas na tabela seguinte.

> [!NOTE]
> A versão predefinitiva do serviço HDInsight pode mudar sem aviso prévio. Se tiver uma dependência de versão, especifique a versão HDInsight quando criar os seus clusters com o .NET SDK com o Azure PowerShell e o Clássico CLI do Azure.

| Componente              | HDInsight 4.0 | HDInsight 3.6 (padrão)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop e YARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Porco-apache             | 0.16.1        | 0.16.0                      |
| Colmeia Apache            | 3.1.0         | 1.2.1 (2.1.0 sobre consulta interativa ESP) |
| Colmeia Apache Tez       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.1.6         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0+                      |
| Fénix-apache         | 5             | 4.7.0                       |
| Apache Spark           | 2.4.4         | 2.3.2.                      |
| Apache Livy            | 0,5           | 0.4.                        |
| Apache Kafka           | 2.1.1         | 1.1                         |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> HDInsight 4.0: Spark 2.4 e Kafka 2.1 estão totalmente suportados. No entanto, os tipos de cluster Spark 2.3 e Kafka 1.1 não são reparados. HDInsight 3.6: Spark 2.3 e Kafka 1.1 estão totalmente suportados.  

## <a name="check-for-current-apache-component-version-information"></a>Verifique se existem informações sobre a versão do componente Apache

As versões de componentes ambientais Hadoop associadas às versões hdInsight cluster podem mudar com atualizações para HDInsight. Para verificar os componentes hadoop e verificar quais versões estão a ser utilizadas para um cluster, utilize a AMBAri REST API. O comando **GetComponentInformation** recupera informações sobre componentes de serviço. Para mais informações, consulte a [documentação apache Ambari.](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)

### <a name="release-notes"></a>Notas de versão

Para obter notas de lançamento adicionais sobre as versões mais recentes do HDInsight, consulte as notas de [lançamento do HDInsight](hdinsight-release-notes.md).

## <a name="supported-hdinsight-versions"></a>Versões HDInsight suportadas

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>Suporte expiração e reforma para versões HDInsight

**A expiração do suporte** significa que a Microsoft já não fornece suporte para a versão HDInsight específica. E já não está disponível através do portal Azure para a criação de clusters. Estas versões ainda podem ser criadas utilizando o CLI Azure ou os vários SDKs.

**A reforma** significa que os aglomerados existentes de uma versão HDInsight continuam a funcionar como está. Novos clusters desta versão não podem ser criados através de qualquer meio, que inclui os CLI e SDKs. Outras características do plano de controlo, como o dimensionamento manual e a autoscalagem, também podem não funcionar após a reforma da versão. O suporte não está disponível para versões aposentadas.

As tabelas a seguir listam as versões de HDInsight. As datas de validade e aposentadoria também são fornecidas quando são conhecidas.

### <a name="available-versions"></a>Versões disponíveis

Esta tabela lista as versões de HDInsight que estão disponíveis no portal Azure e através de outros métodos de implementação como PowerShell e o .NET SDK.

| Versão HDInsight | SO da VM | Data da versão | Prazo de validade do suporte | Data da reforma | Elevada disponibilidade |  Disponibilidade no portal Azure |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |Setembro de 24, 2018 | | |Yes |Yes |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |4 de abril de 2017      | *30 de junho de 2021 |30 de junho de 2021 |Yes |Yes |

*Estamos a alargar o prazo de suporte para certos tipos de cluster HDInsight 3.6

| Tipo de Cluster                    | Versão-quadro | Expiração do suporte atual        | Nova data de validade do suporte |
|---------------------------------|-------------------|-----------------------------------|-----------------------------|
| HDInsight 3.6 Hadoop            | 2.7.3             | Dez 31, 2020                      | 30 de junho de 2021               |
| HDInsight 3.6 Faísca             | 2.3               | Dez 31, 2020                      | 30 de junho de 2021               |
| HDInsight 3.6 Faísca             | 2,2               | Aposentado em 30 de junho de 2020          |                             |
| HDInsight 3.6 Faísca             | 2.1               | Aposentado em 30 de junho de 2020          |                             |
| HDInsight 3.6 Kafka             | 1.1               | Dez 31, 2020                      | 30 de junho de 2021               |
| HDInsight 3.6 Kafka             | 1.0               | Aposentado a 30 de junho de 2020.         |                             |
| HDInsight 3.6 HBase             | 1.1               | Dez 31, 2020                      | 30 de junho de 2021               |
| Consulta interativa HDInsight 3.6 | 2.1               | Dez 31, 2020                      | 30 de junho de 2021               |
| Tempestade HDInsight 3.6             | 1.1               | Dez 31, 2020                      | 30 de junho de 2021               |
| Serviços HDInsight 3.6 ML      | 9,3               | Dez 31, 2020                      | Dez 31, 2020                |

> [!NOTE]
> Depois de expirado o suporte para uma versão cluster, pode não estar disponível através do portal Azure. Em alguns casos, as versões de cluster continuam disponíveis utilizando o parâmetro **versão** no comando Windows PowerShell [New-AzHDInsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster) e no .NET SDK até à data de reforma da versão.

### <a name="retired-versions"></a>Versões aposentadas

Esta tabela lista as versões de HDInsight que não estão disponíveis no portal Azure.

| Versão HDInsight | Versão HDP | SO da VM | Data da versão | Prazo de validade do suporte | Data da reforma | Elevada disponibilidade |  Disponibilidade no portal Azure |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16.0.4 LTS |30 de setembro de 2016 |5 de setembro de 2017 |28 de junho de 2018 |Yes |No |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |29 de março de 2016 |29 de dezembro de 2016 |9 de janeiro de 2018 |Yes |No |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2 de dezembro de 2015 |27 de junho de 2016 |31 de julho de 2018 |Yes |No |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2 de dezembro de 2015 |27 de junho de 2016 |31 de julho de 2017 |Yes |No |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS ou Windows Server 2012 R2 |18 de fevereiro de 2015 |1 de março de 2016 |1 de abril de 2017 |Yes |No |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |24 de junho de 2014 |18 de maio de 2015 |30 de junho de 2016 |Yes |No |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |11 de fevereiro de 2014 |17 de setembro de 2014 |30 de junho de 2015 |Yes |No |
| HDInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |28 de outubro de 2013 |12 de maio de 2014 |31 de maio de 2015 |Yes |No |
| HDInsight 1.6 |HDP 1.1 | |28 de outubro de 2013 |26 de abril de 2014 |31 de maio de 2015 |No |No |

> [!NOTE]
> Os clusters altamente disponíveis com dois nós de cabeça são implantados por padrão para a versão 2.1 hdInsight e posterior. Não estão disponíveis para clusters HDInsight versão 1.6.

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Acordo de nível de serviço para versões de cluster HDInsight

O acordo de nível de serviço é definido como uma janela de _suporte._ Uma janela de suporte é o período de tempo em que uma versão HDInsight é suportada pelo Microsoft Customer Service and Support. Se a versão tiver passado a _data de validade_ do suporte, o cluster HDInsight fica fora da janela de suporte. A expiração de suporte para a versão X HDInsight (depois de uma versão X+1 mais recente estar disponível) é a mais recente de:

- **Fórmula 1:** Adicione 180 dias à data em que a versão X do cluster HDInsight foi lançada.
- **Fórmula 2:** Adicione 90 dias à data em que a versão do cluster HDInsight X+1 é disponibilizada no portal Azure.

A _data de aposentadoria_ é a data após a qual a versão cluster não pode ser criada no HDInsight. A partir de 31 de julho de 2017, não é possível redimensionar um cluster HDInsight após a data da sua reforma.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Configuração padrão do nó e tamanhos de máquina virtual para clusters

Para obter mais informações sobre quais as máquinas virtuais SKUs a selecionar para o seu cluster, consulte [os detalhes da configuração do cluster Azure HDInsight](hdinsight-supported-node-configuration.md).

## <a name="next-steps"></a>Próximos passos

- [Configuração de cluster para Apache Hadoop, Spark, e muito mais em HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Pacote de Segurança Enterprise](./enterprise-security-package.md)
- [Notas de lançamento da Hortonworks associadas às versões Azure HDInsight](./hortonworks-release-notes.md)
- [Trabalhar em Apache Hadoop em HDInsight a partir de um PC windows](hdinsight-hadoop-windows-tools.md)
