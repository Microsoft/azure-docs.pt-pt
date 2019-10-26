---
title: Visão geral do HDInsight 4,0 – Azure
description: Compare as funcionalidades, limitações e recomendações de atualização do HDInsight 3.6 com o HDInsight 4.0.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: 0463e3297bbb2fda50adfeefaa89f0a7a3ef8b0a
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901519"
---
# <a name="azure-hdinsight-40-overview"></a>Visão geral do Azure HDInsight 4,0

O Azure HDInsight é um dos serviços mais populares entre clientes corporativos para Apache Hadoop de software livre e análise de Apache Spark no Azure. O HDInsight 4,0 é uma distribuição em nuvem de componentes de Apache Hadoop. Este artigo fornece informações sobre a versão mais recente do Azure HDInsight e como atualizar.

## <a name="whats-new-in-hdinsight-40"></a>O que há de novo no HDInsight 4,0?

### <a name="apache-hive-30-and-llap"></a>Apache Hive 3,0 e LLAP

Apache Hive o processamento analítico de baixa latência (LLAP) usa servidores de consulta persistentes e cache na memória para fornecer resultados rápidos de consulta SQL sobre dados no armazenamento em nuvem remota. O LLAP do Hive tira partido de um conjunto de daemons persistentes que executam fragmentos de consultas do Hive. A execução de consultas do LLAP é semelhante à do Hive sem o LLAP, com as tarefas de trabalho em execução dentro dos daemons do LLAP em vez de contentores.

Os benefícios do LLAP do Hive incluem:

* Capacidade de executar análise de SQL profunda, como associações complexas, subconsultas, funções de múltiplas janelas, ordenação, funções definidas pelo utilizador e agregações complexas, sem sacrificar o desempenho e a escalabilidade.

* Consultas interativas de dados no mesmo armazenamento onde os dados são preparados, eliminando a necessidade de mover dados do armazenamento para outro motor de processamento analítico.

* A colocação em cache dos resultados da consulta permite que os resultados de uma consulta anteriormente calculada sejam reutilizados, o que poupa tempo e recursos despendidos a executar as tarefas de cluster necessárias para a consulta.

### <a name="hive-dynamic-materialized-views"></a>Vistas materializadas dinâmicas do Hive

O Hive agora suporta vistas materializadas dinâmicas ou computação prévia de resumos relevantes, utilizados para acelerar o processamento de consultas em armazéns de dados. As vistas materializadas podem ser armazenadas de forma nativa no Hive e podem utilizar a aceleração do LLAP de forma totalmente integrada.

### <a name="hive-transactional-tables"></a>Tabelas transacionais do Hive

O HDI 4.0 inclui o Apache Hive 3, que necessita de conformidade ACID (atomicidade, consistência, isolamento e durabilidade) para as tabelas transacionais que residem no armazém do Hive. As tabelas e os dados de tabela conformes ao ACID são acedidos e geridos pelo Hive. Os dados em tabelas CRUD (criar, obter, atualizar e eliminar) têm de estar no formato de ficheiro ORC (Optimized Row Column), mas as tabelas só de inserção suportam todos os formatos de ficheiro.

* O ACID v2 tem melhorias de desempenho no formato de armazenamento e no motor de execução.

* O ACID está ativado por predefinição, para permitir o suporte total para atualizações de dados.

* As capacidades melhoradas do ACID permitem atualizar e eliminar a nível de linhas.

* Sem sobrecarga de Desempenho.

* Sem Registo necessário.

* O Spark pode ler e escrever em tabelas ACID do Hive através do Hive Warehouse Connector.

Saiba mais sobre o [Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html).

### <a name="apache-spark"></a>Apache Spark

O Apache Spark obtém tabelas e transações ACID atualizáveis com o Hive Warehouse Connector. O Hive Warehouse Connector permite registar tabelas transacionais do Hive como tabelas externas no Spark para aceder à funcionalidade transacional completa. As versões anteriores apenas suportavam a manipulação de partição de tabelas. O Hive Warehouse Connector também suporta o Streaming DataFrames para transmitir leituras e escritas em fluxo para tabelas transacionais e em fluxo do Hive a partir do Spark.

Os executores do Spark podem ligar diretamente aos daemons LLAP do Hive para obter e atualizar dados de forma transacional, permitindo ao Hive manter o controlo dos dados.

O Apache Spark no HDInsight 4.0 suporta os seguintes cenários:

* Executar a preparação do modelo de machine learning na mesma tabela transacional utilizada para relatório.
* Utilizar transações ACID para adicionar com segurança colunas do Spark ML a uma tabela do Hive.
* Executar uma tarefa de transmissão em fluxo do Spark no feed de alterações de uma tabela de transmissão em fluxo do Hive.
* Crie ficheiros ORC diretamente a partir de uma tarefa de Transmissão em Fluxo Estruturada do Spark.

Já não tem de preocupar-se com o acesso acidental a tabelas transacionais do Hive diretamente a partir do Spark, dando origem a resultados inconsistentes, dados duplicados ou danos em dados. No HDInsight 4,0, as tabelas do Spark e as tabelas do hive são mantidas em metastores separados. Utilize o Hive Data Warehouse Connector para registar explicitamente tabelas transacionais do Hive como tabelas externas do Spark.

Saiba mais sobre o [Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).

### <a name="apache-oozie"></a>Apache Oozie

O Apache Oozie 4.3.1 está incluído no HDI 4.0 com as seguintes alterações:

* O Oozie já não executa ações do Hive. A CLI do Hive foi removida e substituída pelo BeeLine.

* Pode excluir dependências indesejadas da biblioteca de partilhas, ao incluir um padrão de exclusão no ficheiro **job.properties**.

Saiba mais sobre o [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdinsight-40"></a>Como atualizar para o HDInsight 4,0

Como em qualquer versão principal, é importante testar a fundo os componentes antes de implementar a versão mais recente num ambiente de produção. O HDInsight 4,0 está disponível para que você comece o processo de atualização, mas o HDInsight 3,6 é a opção padrão para evitar incidentes acidentais.

Não há nenhum caminho de atualização com suporte de versões anteriores do HDInsight para o HDInsight 4,0. Como os formatos de dados de metastore e BLOB foram alterados, o HDInsight 4,0 não é compatível com as versões anteriores. É importante que você mantenha seu novo ambiente do HDInsight 4,0 separado do seu ambiente de produção atual. Se você implantar o HDInsight 4,0 em seu ambiente atual, o metastore será atualizado e não poderá ser revertido.  

## <a name="limitations"></a>Limitações

* O HDInsight 4,0 não dá suporte a MapReduce para Apache Hive. Em vez disso, use Apache Tez. Saiba mais sobre o [Apache Tez](https://tez.apache.org/).
* O HDInsight 4,0 não oferece suporte a Apache Storm.
* A exibição do hive não está mais disponível no HDInsight 4,0.
* O interpretador de Shell no Apache Zeppelin não tem suporte em clusters de consulta do Spark e interativo.
* Não pode *desativar* o LLAP num cluster do Apache Spark-LLAP. Pode apenas desligar o LLAP.
* O Azure Data Lake Storage Gen2 não guarda blocos de notas Jupyter num cluster do Apache Spark.

## <a name="next-steps"></a>Passos seguintes

* [Documentação do Azure HDInsight](index.yml)
* [Notas de Versão](hdinsight-release-notes.md)
