---
title: Visão geral do HDInsight 4.0 - Azure
description: Compare as funcionalidades, limitações e recomendações de atualização do HDInsight 3.6 com o HDInsight 4.0.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.openlocfilehash: 2716f037de533e14ae8e57706134c8a2b135b440
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322380"
---
# <a name="azure-hdinsight-40-overview"></a>Visão geral do Azure HDInsight 4.0

Azure HDInsight é um dos serviços mais populares entre os clientes empresariais da Apache Hadoop e da Apache Spark. HDInsight 4.0 é uma distribuição em nuvem de componentes Apache Hadoop. Este artigo fornece informações sobre a versão mais recente do Azure HDInsight e como atualizar.

## <a name="whats-new-in-hdinsight-40"></a>Quais as novidades no HDInsight 4.0?

### <a name="apache-hive-30-and-low-latency-analytical-processing"></a>Colmeia Apache 3.0 e processamento analítico de baixa latência

O processamento analítico de baixa latência da Criocia Apache Hive (LLAP) utiliza servidores de consulta persistentes e caching na memória. Este processo fornece resultados de consulta rápida sql em dados no armazenamento remoto de nuvem. Hive LLAP usa um conjunto de daemons persistentes que executam fragmentos de consultas de Colmeia. A execução de consultas do LLAP é semelhante à do Hive sem o LLAP, com as tarefas de trabalho em execução dentro dos daemons do LLAP em vez de contentores.

Os benefícios do LLAP do Hive incluem:

* Capacidade de fazer análises profundas do SQL sem sacrificar o desempenho e a adaptabilidade. Tais como juntas complexas, subqueries, funções de janela, triagem, funções definidas pelo utilizador e agregações complexas.

* Consultas interativas de dados no mesmo armazenamento onde os dados são preparados, eliminando a necessidade de mover dados do armazenamento para outro motor de processamento analítico.

* Os resultados da consulta de caching permitem reutilizar os resultados de consulta previamente calculados. Esta cache poupa tempo e recursos gastos a executar as tarefas de cluster necessárias para a consulta.

### <a name="hive-dynamic-materialized-views"></a>Vistas materializadas dinâmicas do Hive

A Colmeia agora suporta vistas dinâmicas materializadas, ou pré-computação de resumos relevantes. As vistas aceleram o processamento de consultas em armazéns de dados. As vistas materializadas podem ser armazenadas de forma nativa no Hive e podem utilizar a aceleração do LLAP de forma totalmente integrada.

### <a name="hive-transactional-tables"></a>Tabelas transacionais do Hive

HDI 4.0 inclui Apache Hive 3. A Colmeia 3 requer aatóatua, consistência, isolamento e durabilidade para as tabelas transacionais que vivem no armazém da Colmeia. As tabelas e os dados de tabela conformes ao ACID são acedidos e geridos pelo Hive. Os dados relativos às tabelas de criação, recuperação, atualização e eliminação (CRUD) devem estar no formato de ficheiro da Coluna de Linha Otimizada (ORC). As tabelas inseridas suportam todos os formatos de ficheiro.

* O ACID v2 tem melhorias de desempenho no formato de armazenamento e no motor de execução.

* O ACID está ativado por predefinição, para permitir o suporte total para atualizações de dados.

* As capacidades melhoradas do ACID permitem atualizar e eliminar a nível de linhas.

* Sem sobrecarga de Desempenho.

* Sem Registo necessário.

* O Spark pode ler e escrever em tabelas ACID do Hive através do Hive Warehouse Connector.

Saiba mais sobre o [Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html).

### <a name="apache-spark"></a>Apache Spark

O Apache Spark obtém tabelas e transações ACID atualizáveis com o Hive Warehouse Connector. O Hive Warehouse Connector permite registar tabelas transacionais do Hive como tabelas externas no Spark para aceder à funcionalidade transacional completa. As versões anteriores apenas suportavam a manipulação de partição de tabelas. O Conector do Armazém de Colmeia também suporta o Streaming DataFrames.  Este processo transmite leituras e grava em tabelas de hive transacionais e streaming de Spark.

Os executores do Spark podem ligar diretamente aos daemons LLAP do Hive para obter e atualizar dados de forma transacional, permitindo ao Hive manter o controlo dos dados.

O Apache Spark no HDInsight 4.0 suporta os seguintes cenários:

* Executar a preparação do modelo de machine learning na mesma tabela transacional utilizada para relatório.
* Utilizar transações ACID para adicionar com segurança colunas do Spark ML a uma tabela do Hive.
* Executar uma tarefa de transmissão em fluxo do Spark no feed de alterações de uma tabela de transmissão em fluxo do Hive.
* Crie ficheiros ORC diretamente a partir de uma tarefa de Transmissão em Fluxo Estruturada do Spark.

Já não tens de te preocupar em tentar aceder acidentalmente às tabelas transacionais da Hive diretamente da Spark. Resultando em resultados inconsistentes, duplicação de dados ou corrupção de dados. No HDInsight 4.0, as mesas de faísca e as mesas de colmeia são mantidas em Metastores separadas. Utilize o Hive Data Warehouse Connector para registar explicitamente tabelas transacionais do Hive como tabelas externas do Spark.

Saiba mais sobre o [Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).

### <a name="apache-oozie"></a>Apache Oozie

O Apache Oozie 4.3.1 está incluído no HDI 4.0 com as seguintes alterações:

* O Oozie já não executa ações do Hive. A CLI do Hive foi removida e substituída pelo BeeLine.

* Pode excluir dependências indesejadas da biblioteca de partilhas, ao incluir um padrão de exclusão no ficheiro **job.properties**.

Saiba mais sobre o [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdinsight-40"></a>Como fazer upgrade para HDInsight 4.0

Teste minuciosamente os seus componentes antes de implementar a versão mais recente num ambiente de produção. O HDInsight 4.0 está disponível para iniciar o processo de atualização. HDInsight 3.6 é a opção padrão para evitar percalços acidentais.

Não existe um caminho de upgrade suportado das versões anteriores do HDInsight para HDInsight 4.0. Como os formatos de dados de Metastore e blob mudaram, o 4.0 não é compatível com as versões anteriores. É importante manter o seu novo ambiente HDInsight 4.0 separado do seu ambiente de produção atual. Se colocar o HDInsight 4.0 no seu ambiente atual, a sua Metastore será atualizada permanentemente.  

## <a name="limitations"></a>Limitações

* HDInsight 4.0 não suporta MapReduce para a Colmeia Apache. Use o Apache Tez em vez disso. Saiba mais sobre o [Apache Tez](https://tez.apache.org/).
* HDInsight 4.0 não suporta a Tempestade Apache.
* HDInsight 4.0 não suporta o tipo de cluster ML Services.
* O Hive View só está disponível em clusters HDInsight 4.0 com um número de versão igual ou superior a 4.1. Este número de versão está disponível em Ambari Admin -> Versões.
* O intérprete de conchas em Apache Zeppelin não é apoiado em agrupamentos de faíscas e consultas interativas.
* Não pode *desativar* o LLAP num cluster do Apache Spark-LLAP. Pode apenas desligar o LLAP.
* Azure Data Lake Storage Gen2 não consegue salvar os cadernos do Jupyter num aglomerado de faíscas.
* O porco apache corre em Tez por defeito, no entanto pode alterá-lo para Mapreduce
* A integração do Spark SQL Ranger para a segurança da linha e da coluna é depreciada
* Spark 2.4 e Kafka 2.1 estão disponíveis em HDInsight 4.0, pelo que a Spark 2.3 e a Kafka 1.1 já não estão suportadas. Recomendamos a utilização do Spark 2.4 & Kafka 2.1 e superior em HDInsight 4.0.

## <a name="next-steps"></a>Passos seguintes

* [Guia de migração HBase](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-migrate-new-version)
* [Guia de migração da colmeia](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-hive-migrate-workloads)
* [Guia de migração kafka](https://docs.microsoft.com/azure/hdinsight/kafka/migrate-versions)
* [Guia de migração de faíscas](https://docs.microsoft.com/azure/hdinsight/spark/migrate-versions)
* [Documentação Azure HDInsight](index.yml)
* [Notas de lançamento](hdinsight-release-notes.md)
