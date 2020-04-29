---
title: Visão geral do HDInsight 4.0 - Azure
description: Compare as funcionalidades, limitações e recomendações de atualização do HDInsight 3.6 com o HDInsight 4.0.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: d0fd9999abc4a67ded0f66977e1a3ba5310c87be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81383035"
---
# <a name="azure-hdinsight-40-overview"></a>Visão geral do Azure HDInsight 4.0

O Azure HDInsight é um dos serviços mais populares entre os clientes empresariais da Apache Hadoop e da Apache Spark. HDInsight 4.0 é uma distribuição em nuvem dos componentes Apache Hadoop. Este artigo fornece informações sobre a versão mais recente do Azure HDInsight e como atualizar.

## <a name="whats-new-in-hdinsight-40"></a>Quais as novidades no HDInsight 4.0?

### <a name="apache-hive-30-and-low-latency-analytical-processing"></a>Apache Hive 3.0 e processamento analítico de baixa latência

O processamento analítico de baixa latência da Apache Hive (LLAP) utiliza servidores de consulta persistentes e cachede na memória. Este processo fornece resultados rápidos de consulta SQL em dados em armazenamento remoto em nuvem. A Hive LLAP usa um conjunto de daemons persistentes que executam fragmentos de consultas da Colmeia. A execução de consultas do LLAP é semelhante à do Hive sem o LLAP, com as tarefas de trabalho em execução dentro dos daemons do LLAP em vez de contentores.

Os benefícios do LLAP do Hive incluem:

* Capacidade de fazer análises SQL profundas sem sacrificar desempenho e adaptabilidade. Tais como juntas complexas, subqueries, funções de janela, triagem, funções definidas pelo utilizador e agregações complexas.

* Consultas interativas de dados no mesmo armazenamento onde os dados são preparados, eliminando a necessidade de mover dados do armazenamento para outro motor de processamento analítico.

* Os resultados da consulta de cache permitem que os resultados de consulta previamente calculados sejam reutilizados. Esta cache poupa tempo e recursos gastos a executar as tarefas de cluster necessárias para a consulta.

### <a name="hive-dynamic-materialized-views"></a>Vistas materializadas dinâmicas do Hive

A Hive suporta agora pontos de vista materializados dinâmicos, ou pré-computação de resumos relevantes. As vistas aceleram o processamento de consultas em armazéns de dados. As vistas materializadas podem ser armazenadas de forma nativa no Hive e podem utilizar a aceleração do LLAP de forma totalmente integrada.

### <a name="hive-transactional-tables"></a>Tabelas transacionais do Hive

HDI 4.0 inclui Apache Hive 3. A Colmeia 3 requer atómico, consistência, isolamento e cumprimento da durabilidade das tabelas transacionais que vivem no armazém da Colmeia. As tabelas e os dados de tabela conformes ao ACID são acedidos e geridos pelo Hive. Os dados em criar, recuperar, atualizar e eliminar tabelas (CRUD) devem estar no formato de ficheiro sinuoso da Coluna de Linhas (ORC). As tabelas apenas de inserção suportam todos os formatos de ficheiros.

* O ACID v2 tem melhorias de desempenho no formato de armazenamento e no motor de execução.

* O ACID está ativado por predefinição, para permitir o suporte total para atualizações de dados.

* As capacidades melhoradas do ACID permitem atualizar e eliminar a nível de linhas.

* Sem sobrecarga de Desempenho.

* Sem Registo necessário.

* O Spark pode ler e escrever em tabelas ACID do Hive através do Hive Warehouse Connector.

Saiba mais sobre o [Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html).

### <a name="apache-spark"></a>Apache Spark

O Apache Spark obtém tabelas e transações ACID atualizáveis com o Hive Warehouse Connector. O Hive Warehouse Connector permite registar tabelas transacionais do Hive como tabelas externas no Spark para aceder à funcionalidade transacional completa. As versões anteriores apenas suportavam a manipulação de partição de tabelas. O Conector hive Warehouse também suporta o Streaming DataFrames.  Este processo transmite leituras e escritos em tabelas de colmeias transacionais e streaming a partir de Spark.

Os executores do Spark podem ligar diretamente aos daemons LLAP do Hive para obter e atualizar dados de forma transacional, permitindo ao Hive manter o controlo dos dados.

O Apache Spark no HDInsight 4.0 suporta os seguintes cenários:

* Executar a preparação do modelo de machine learning na mesma tabela transacional utilizada para relatório.
* Utilizar transações ACID para adicionar com segurança colunas do Spark ML a uma tabela do Hive.
* Executar uma tarefa de transmissão em fluxo do Spark no feed de alterações de uma tabela de transmissão em fluxo do Hive.
* Crie ficheiros ORC diretamente a partir de uma tarefa de Transmissão em Fluxo Estruturada do Spark.

Já não tens de te preocupar em tentar aceder acidentalmente às mesas transacionais da Hive diretamente da Spark. Resultando em resultados inconsistentes, dados duplicados ou corrupção de dados. Em HDInsight 4.0, mesas de faíscae e mesas de colmeia são mantidas em metastores separadas. Utilize o Hive Data Warehouse Connector para registar explicitamente tabelas transacionais do Hive como tabelas externas do Spark.

Saiba mais sobre o [Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).

### <a name="apache-oozie"></a>Apache Oozie

O Apache Oozie 4.3.1 está incluído no HDI 4.0 com as seguintes alterações:

* O Oozie já não executa ações do Hive. A CLI do Hive foi removida e substituída pelo BeeLine.

* Pode excluir dependências indesejadas da biblioteca de partilhas, ao incluir um padrão de exclusão no ficheiro **job.properties**.

Saiba mais sobre o [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdinsight-40"></a>Como atualizar para HDInsight 4.0

Teste bem os seus componentes antes de implementar a versão mais recente num ambiente de produção. O HDInsight 4.0 está disponível para iniciar o processo de atualização. HDInsight 3.6 é a opção padrão para evitar percalços acidentais.

Não existe um caminho de upgrade suportado de versões anteriores do HDInsight para HDInsight 4.0. Como os formatos de dados da Metastore e blob mudaram, 4.0 não é compatível com versões anteriores. É importante manter o seu novo ambiente HDInsight 4.0 separado do seu ambiente de produção atual. Se implementar o HDInsight 4.0 para o seu ambiente atual, a sua Metastore será permanentemente atualizada.  

## <a name="limitations"></a>Limitações

* HDInsight 4.0 não suporta MapReduce para Apache Hive. Use Apache Tez em vez disso. Saiba mais sobre o [Apache Tez](https://tez.apache.org/).
* HDInsight 4.0 não suporta tempestade Apache.
* A Hive View já não está disponível no HDInsight 4.0.
* O intérprete da shell em Apache Zeppelin não é suportado em clusters de consultas de faíscas e interativas.
* Não pode *desativar* o LLAP num cluster do Apache Spark-LLAP. Só pode desligar llap.
* O Azure Data Lake Storage Gen2 não consegue salvar os cadernos da Jupyter num aglomerado de faíscas.

## <a name="next-steps"></a>Passos seguintes

* [Documentação do Azure HDInsight](index.yml)
* [Notas de lançamento](hdinsight-release-notes.md)
