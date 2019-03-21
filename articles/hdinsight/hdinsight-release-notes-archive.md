---
title: Notas de versão arquivados para Azure HDInsight
description: Notas de versão arquivados e versões do Azure HDInsight.
services: hdinsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: hrasheed
ms.openlocfilehash: 7626b8359befe234a981ee3d5de1c7cedc5bdaa5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58007678"
---
# <a name="archived-release-notes-for-azure-hdinsight"></a>Notas de versão arquivados para Azure HDInsight

Para o **mais recente** atualizações de versão do Azure HDInsight, consulte [notas de versão do HDInsight](hdinsight-release-notes.md).

> [!IMPORTANT]  
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, consulte [artigo de controle de versão do HDInsight](hdinsight-component-versioning.md).

## <a name="notes-for-06272018---release-of-new-open-source-versions-adls-gen2-etc-on-hdinsight-36"></a>Notas para 06/27/2018 – lançamento de novas versões de código-fonte aberto, etc. de geração 2 do ADLS no HDInsight 3.6
A versão de Junho de 2018 do HDInsight é uma versão significativa com muita novas atualizações e recursos para os nossos clientes. Leia este [publicar](https://azure.microsoft.com/blog/enterprises-get-deeper-insights-with-hadoop-and-spark-updates-on-azure-hdinsight/) para obter mais detalhes.

Seguem-se os destaques. Para obter as notas de versão detalhados, erros corrigidos, problemas conhecidos, etc., leia os [notas de versão do Azure HDInsight](hdinsight-release-notes.md).

- **Atualizar outros projetos de código-fonte aberto e Apache Hadoop** – além de 1000 + correções de bugs em projetos de código-fonte aberto mais de 20, esta atualização contém uma nova versão do Apache Spark (2.3) e o Apache Kafka (1.0).
- **Atualizar o R Server 9.1 para Machine Learning Services 9.3** – com esta versão, estamos oferecendo os cientistas de dados e engenheiros com o melhor do código-fonte aberto melhorado com inovações algorítmica e a facilidade de operacionalização, todos disponível no respetivo idioma preferencial com a velocidade do Apache Spark. Esta versão expande os recursos oferecidos no R Server com foi adicionado suporte para o Python, que leva para a alteração do nome de cluster do R Server nos serviços de ML. 
- **Suporte para a geração 2 de armazenamento do Azure Data Lake** – HDInsight irá suportar a versão de pré-visualização de geração 2 de armazenamento do Azure Data Lake. Nas regiões disponíveis, os clientes poderão escolher uma conta do ADLS Gen2 como um arquivo para os clusters do HDInsight.
- **Atualizações de pacote de segurança do HDInsight Enterprise (pré-visualização)** – suporte (pré-visualização) Virtual rede pontos finais de serviço para o blob do Azure, armazenamento, o ADLS Gen1, o Cosmos DB e o Azure DB. 

## <a name="notes-for-03202018---release-of-spark-22-on-hdinsight-36"></a>Notas para 20/03/2018 - versão do Spark 2.2 no HDInsight 3.6

- Spark 2.2.0 melhora a estabilidade em Spark Core, SQL, ML e traz a transmissão em fluxo estruturada de mensagens em fila para o estado de disponibilidade geral. Spark 2.2.0 está agora disponível no HDInsight 3.6.


## <a name="notes-for-08012017-release-of-hdinsight"></a>Notas de versão 08/01/2017 do HDInsight

| Título | Descrição | Área afetada  | Tipo de Cluster  | 
| --- | --- | --- | --- |
| Versão do Microsoft R Server 9.1 no HDInsight |HDInsight suporta agora o aprovisionamento de clusters de R Server 9.1 no HDInsight. |Serviço |R Server |
| HDInsight 3.6 agora inclui as versões mais recentes da pilha do Hadoop|<ul><li>Para obter uma lista detalhada das versões atualizadas, consulte [versões de componente do Apache Hadoop disponíveis no HDInsight](hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).</li><li>Para obter uma lista de erros corrigidos nas versões mais recentes da pilha do Hadoop, consulte [informações de Patch do Apache](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/patch_parent.html).</li><li>Para obter uma lista de quebras de código entre HDP 2.6.1 (que agora está disponível no HDInsight 3.6), consulte [ https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html ](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html).</li><li>Para obter uma lista dos problemas conhecidos no HDP 2.6.1, consulte [problemas conhecidos](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/known_issues.html).</li></ul> |Serviço |Todos |
| Atualizações para clusters do Interactive Hive (pré-visualização) |<ul><li><b>Melhoria da funcionalidade.</b> Implementação de metastore em cache que reduz a carga no back-end do SQL, colocação em cache os metadados e melhora o desempenho para todas as operações de metadados.  Esta melhoria é agora uma predefinição em todos os clusters do Apache Interactive Hive. Para mais informações, consulte [https://issues.apache.org/jira/browse/HIVE-16520](https://issues.apache.org/jira/browse/HIVE-16520).</li><li><b>Melhoria da funcionalidade.</b> Carregamento dinâmico de partição está otimizado. Para mais informações, consulte [https://issues.apache.org/jira/browse/HIVE-14204](https://issues.apache.org/jira/browse/HIVE-14204).</li><li><b>Melhoria da funcionalidade.</b> Otimizações de configuração para o HDInsight no Linux.</li><li><b>Correção de erros.</b> `CredentialProviderFactory$getProviders` Não é thread-safe. Esse problema agora é corrigido. Para mais informações, consulte [https://issues.apache.org/jira/browse/HADOOP-14195](https://issues.apache.org/jira/browse/HADOOP-14195).</li><li><b>Correção de erros.</b> Elevada utilização da CPU com o controlador WASB `liststatus` API, resultando em mau desempenho ATS. Esse problema agora é corrigido. Para mais informações, consulte [https://github.com/Azure/azure-storage-java/pull/154](https://github.com/Azure/azure-storage-java/pull/154).</li></ul> |Serviço |Hive interativo (pré-visualização) | |
| Atualizações para clusters do Hadoop |Fiabilidade de operação de tarefa Templeton foi aprimorada. Para obter mais informações, consulte [https://issues.apache.org/jira/browse/HIVE-15947](https://issues.apache.org/jira/browse/HIVE-15947) |Serviço |Hadoop | |
| Atualizações YARN | Agora o HDInsight cria um 250 GB Ambari da base de dados (sem aumentar o custo), que resulta numa melhor experiência para os clientes. Esta alteração deve impedir que o ATS obtenção preenchido para cima e é provável que tenha um desempenho melhor. |Serviço |Todos | |
| Atualizações do Spark | Versão do Spark 2.1.1. Para obter mais informações, consulte [Apache Spark versão 2.1.1](https://spark.apache.org/releases/spark-release-2-1-1.html). | Serviço | Spark | |

  


## <a name="04062017---general-availability-of-hdinsight-36"></a>04/06/2017 - disponibilidade geral do HDInsight 3.6

* Com esta versão, o Azure HDInsight adiciona versão 3.6, que se baseia em HDP 2.6. Notas de versão HDP 2.6 estão disponíveis [aqui](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html) e obter mais informações sobre as versões do HDInsight podem ser encontradas [aqui](hdinsight-component-versioning.md). 3.6 do HDInsight está disponível para as cargas de trabalho seguintes:

    * Hadoop v2.7.3
    * HBase v1.1.2
    * Storm v1.1.0
    * Spark v2.1.0
    * Interactive Hive v2.1.0

* **Suporte para a vista do Hive 2.0**. Isto deve melhorar a experiência do usuário para o Interactive Hive. Para obter mais informações, consulte [Hortonworks documentação](https://docs.hortonworks.com/HDPDocuments/Ambari-2.5.0.3/bk_ambari-views/content/ch_using_hive_view.html).

* **Melhorias de desempenho com LLAP do Hive**. Para obter mais informações, consulte [Hortonworks documentação](https://hortonworks.com/blog/top-5-performance-boosters-with-apache-hive-llap/).

* **Novos recursos do Hive**. Ver [Hortonworks documentação](https://hortonworks.com/apache/hive/#section_4).

* **Descontinuação de CLI de Hive**: Hive CLI está a ser preterido e os clientes são incentivados a usar o Beeline em vez disso. Para obter mais informações, consulte [documentação do Apache](https://cwiki.apache.org/confluence/display/Hive/Replacing+the+Implementation+of+Hive+CLI+Using+Beeline). Para obter instruções sobre como utilizar Beeline com o HDInsight, consulte [utilizar Beeline com o HDInsight Hadoop clusters](hadoop/apache-hadoop-use-hive-beeline.md).

* **Novos recursos do Apache Phoenix e HBase**.
    * Suporte de quota de armazenamento: Normalmente utilizado em ambientes de multi-inquilinos, permitindo que o espaço de armazenamento limitada num por tabela e por nível de espaço de nomes.
    * Melhorias de indexação de Phoenix: Criação de índices de incremental e reconstrução/retomar a indexação de falhas anteriores.
    * Ferramenta de integridade de dados de Phoenix: Suporta a validação de esquema, o índice e outros metadados.


* **Problema com o HBase**: Ao executar um trabalho do MapReduce de carregamento em massa CSV, a seguinte sintaxe poderá resultar num erro.

        HADOOP_CLASSPATH=$(hbase mapredcp):/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv

    Utilize em vez disso, a seguinte sintaxe:

        HADOOP_CLASSPATH=/path/to/hbase-protocol.jar:/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv


## <a name="02282017---release-of-spark-21-on-hdinsight-36-preview"></a>02/28/2017 - versão do Spark 2.1 no HDInsight 3.6 (pré-visualização)
* [Spark 2.1](https://spark.apache.org/releases/spark-release-2-1-0.html) melhora muitos problemas de usabilidade e estabilidade com versões anteriores. Também apresenta novos recursos em todos os Spark cargas de trabalho, tais como Spark Core, SQL, ML e a transmissão em fluxo.
* A transmissão em fluxo estruturada obtém uma escalabilidade melhorada com suporte para marcas de água de tempo de eventos e conector de Kafka 0,10.
* A criação de partições de Spark SQL agora é processada através de novo mecanismo de processamento de partição dimensionável. Ver mais detalhes [aqui](https://spark.apache.org/releases/spark-release-2-1-0.html) sobre como atualizar.
* 2.1 do Spark no HDInsight 3.6 pré-visualização Azure atualmente não suporta a conectividade de ferramenta de BI usando o controlador ODBC.
* Acesso de armazenamento do Azure Data Lake de clusters do Spark 2.1 não é suportado nesta pré-visualização.


## <a name="11182016---release-of-spark-201-on-hdinsight-35"></a>11/18/2016 – a versão do Spark 2.0.1 no HDInsight 3.5
Spark 2.0.1 está agora disponível em clusters do Spark (HDInsight versão 3.5).

## <a name="11162016---release-of-r-server-90-on-hdinsight-35-spark-20"></a>11/16/2016 – a versão do R Server 9.0 no HDInsight 3.5 (Spark 2.0)
*   Os clusters do R Server incluem agora a opção para duas versões: R Server 9.0 no HDI 3.5 (Spark 2.0) e do R Server 8.0 no HDI 3.4 (Spark 1.6).
*   R Server 9.0 no HDI 3.5 (Spark 2.0) baseia-se R 3.3.2 e inclui novas funções de origem do dados ScaleR chamadas RxHiveData e RxParquetData para carregar dados do Hive e no Parquet diretamente para o Spark DataFrames para análise por parte do ScaleR. Para obter mais informações, consulte o inline ajuda para as funções em R através da utilização do **? RxHiveData** e **? RxParquetData** comandos.
*   Edição de Comunidade do RStudio Server está agora instalada por padrão (com uma opção de recusa) no painel de configuração de Cluster como parte do fluxo de aprovisionamento.

## <a name="11092016---release-of-spark-20-on-hdinsight"></a>11/09/2016 – a versão do Spark 2.0 no HDInsight
* Os clusters do Spark 2.0 no HDInsight 3.5 agora Livy e Jupyter serviços de suporte.

## <a name="10262016---release-of-r-server-on-hdinsight"></a>10/26/2016 – a versão do R Server no HDInsight
* O URI para o acesso de nó edge foi alterado para **clustername**-ed-ssh.azurehdinsight.net
* R Server no aprovisionamento do cluster de HDInsight foi otimizado.
* R Server no HDInsight está agora disponível como HDInsight regular "R Server" tipo de cluster e já não está instalado como um aplicativo separado do HDInsight. O nó de extremidade e os binários de R Server agora são aprovisionados como parte da implementação de cluster do R Server. Isto melhora a velocidade e a confiabilidade de aprovisionamento. Modelo de preços do R Server é devidamente atualizado.
* Preço de tipo de cluster do R Server agora está baseado no preço do escalão Standard, além de preço de sobretaxa de R Server. Esta alteração não afeta o preço em vigor a partir do R Server; ele é alterado apenas como os encargos são apresentados na fatura. Todos os clusters de R Server existentes continuam a funcionar e modelos do Resource Manager continuam a funcionar até que o aviso de preterição. **Recomenda-se para atualizar as implementações com script para utilizar o novo modelo do Resource Manager.**

