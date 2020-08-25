---
title: Notas de versão do Azure HDInsight
description: Últimas notas de lançamento para Azure HDInsight. Obtenha dicas de desenvolvimento e detalhes para Hadoop, Spark, R Server, Hive, e muito mais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 0587a179b98a410cdba46b7817d86567f275f25d
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826825"
---
# <a name="azure-hdinsight-release-notes"></a>Notas de lançamento do Azure HDInsight

Este artigo fornece informações sobre as **mais recentes** atualizações de lançamento do Azure HDInsight. Para obter informações sobre ver lançamentos anteriores, consulte [o HDInsight Release Notes Archive](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumo

O Azure HDInsight é um dos serviços mais populares entre os clientes empresariais para análises de código aberto em Azure.

## <a name="release-date-08092020"></a>Data de lançamento: 08/09/2020

Esta versão aplica-se apenas a HDInsight 4.0. O lançamento hdInsight é disponibilizado a todas as regiões ao longo de vários dias. A data de lançamento aqui indica a primeira data de lançamento da região. Se não vir alterações abaixo, aguarde que o lançamento seja transmitido ao vivo na sua região dentro de vários dias.

## <a name="new-features"></a>Novas funcionalidades
### <a name="support-for-sparkcruise"></a>Apoio à SparkCruise
SparkCruise é um sistema automático de reutilização de computação para a Spark. Seleciona subexpressões comuns para se materializar com base na carga de trabalho de consulta anterior. O SparkCruise materializa estas subexpressões como parte do processamento de consultas e reutilização de cálculo é automaticamente aplicado em segundo plano. Pode beneficiar-se do SparkCruise sem qualquer modificação ao código Spark.
 
### <a name="support-hive-view-for-hdinsight-40"></a>Suporte Vista hive para HDInsight 4.0
Apache Ambari Hive View foi projetado para ajudá-lo a autorizar, otimizar e executar consultas de Hive a partir do seu navegador web. A Hive View é suportada de forma nativa para clusters HDInsight 4.0 a partir desta versão. Não se aplica aos aglomerados existentes. Você precisa cair e recriar o cluster para obter a vista de colmeia incorporada.
 
### <a name="support-tez-view-for-hdinsight-40"></a>Vista Tez de suporte para HDInsight 4.0
Apache Tez View é usado para rastrear e depurar a execução do trabalho de Hive Tez. O Tez View é suportado de forma nativa para HDInsight 4.0 a partir desta versão. Não se aplica aos aglomerados existentes. Você precisa cair e recriar o cluster para obter a vista de Tez incorporada.

## <a name="deprecation"></a>Preterição
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Descontinuação do Spark 2.1 e 2.2 no cluster do Spark no HDInsight 3.6
A partir de 1 de julho de 2020, os clientes não podem criar novos clusters Spark com Spark 2.1 e 2.2 no HDInsight 3.6. Os clusters existentes funcionarão como está sem o suporte da Microsoft. Considere mover-se para Spark 2.3 no HDInsight 3.6 até 30 de junho de 2020 para evitar possíveis interrupções do sistema/suporte.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Descontinuação do Spark 2.3 no cluster do Spark no HDInsight 4.0
A partir de 1 de julho de 2020, os clientes não podem criar novos clusters Spark com Spark 2.3 em HDInsight 4.0. Os clusters existentes funcionarão como está sem o suporte da Microsoft. Considere migrar para o Spark 2.4 no HDInsight 4.0 até 30 de junho de 2020 para evitar a potencial interrupção do sistema/suporte.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Descontinuação do Kafka 1.1 no cluster do Kafka no HDInsight 4.0
A partir de 1 de julho de 2020, os clientes não poderão criar novos clusters Kafka com Kafka 1.1 no HDInsight 4.0. Os clusters existentes funcionarão como está sem o suporte da Microsoft. Considere migrar para o Kafka 2.1 no HDInsight 4.0 até 30 de junho de 2020 para evitar a potencial interrupção do sistema/suporte.

## <a name="behavior-changes"></a>Mudanças de comportamento
### <a name="ambari-stack-version-change"></a>Mudança de versão da pilha Ambari
A partir desta versão, a versão Ambari é alterada de 2.x.x.x para 4.1. Você pode obter a versão Ambari de Ambari UI > About.

## <a name="upcoming-changes"></a>Próximas alterações
Sem próximas alterações de rutura a que tens de prestar atenção.

## <a name="bug-fixes"></a>Correções de erros
O HDInsight continua a fazer melhorias na fiabilidade do cluster e no desempenho. 

Abaixo, os JIRAs estão de volta para a Colmeia:
* [HIVE-23619](https://issues.apache.org/jira/browse/HIVE-23619)
* [HIVE-21223](https://issues.apache.org/jira/browse/HIVE-21223)
* [HIVE-22599](https://issues.apache.org/jira/browse/HIVE-22599)
* [HIVE-22121](https://issues.apache.org/jira/browse/HIVE-22121)
* [HIVE-22136](https://issues.apache.org/jira/browse/HIVE-22136)
* [HIVE-18786](https://issues.apache.org/jira/browse/HIVE-18786)

Abaixo, os JIRAs estão de volta para a Base H:
* [HBASE-21458](https://issues.apache.org/jira/browse/HBASE-21458)
* [HBASE-24208](https://issues.apache.org/jira/browse/HBASE-24208)
* [HBASE-24205](https://issues.apache.org/jira/browse/HBASE-24205)

## <a name="component-version-change"></a>Alteração da versão do componente
Nenhuma alteração da versão componente para esta versão. Pode encontrar as versões componentes atuais para HDInsight 4.0 e HDInsight 3.6 [neste doc](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).

## <a name="known-issues"></a>Problemas conhecidos

Um problema foi corrigido no Portal Azure, onde os utilizadores estavam a sofrer um erro quando estavam a criar um cluster Azure HDInsight utilizando um tipo de chave pública de autenticação SSH. Quando os utilizadores clicarem **em Rever + Criar,** receberiam o erro "Não deve conter três caracteres consecutivos do nome de utilizador SSH." Este problema foi corrigido, mas pode exigir que refresque a cache do seu navegador ao acertar no CTRL + F5 para carregar a vista corrigida. A solução para esta questão foi criar um cluster com um modelo ARM. 
