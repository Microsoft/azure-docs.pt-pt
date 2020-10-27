---
title: O redutor é lento em Azure HDInsight
description: O redutor é lento em Azure HDInsight a partir de possíveis distorções de dados
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 2fbe68c5dfe9b0d5b60e1e3eeabc95b201b878c6
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92532821"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>Cenário: Redutor é lento em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao utilizar componentes de consultainterna interactive em clusters Azure HDInsight.

## <a name="issue"></a>Problema

Ao executar uma consulta como `insert into table1 partition(a,b) select a,b,c from table2` o plano de consulta começa um monte de redutores, mas os dados de cada partição vão para um único redutor. Isto faz com que a consulta seja tão lenta quanto o tempo tomado pelo redutor da maior partição.

## <a name="cause"></a>Causa

Abra [a beeline](../hadoop/apache-hadoop-use-hive-beeline.md) e verifique o valor do conjunto `hive.optimize.sort.dynamic.partition` .

O valor desta variável destina-se a ser definido como verdadeiro/falso com base na natureza dos dados.

Se as divisórias na tabela de entrada forem menos (digamos menos de 10), assim como o número de divisórias de saída, e a variável for definida para, isto faz com que os `true` dados sejam classificados globalmente e escritos usando um único redutor por partição. Mesmo que o número de redutores disponíveis seja maior, alguns redutores podem ficar para trás devido a distorção de dados e o paralelismo máximo não pode ser alcançado. Quando alterado para `false` , mais de um redutor pode manusear uma única partição e vários ficheiros menores serão escritos, resultando numa inserção mais rápida. Isto pode afetar mais consultas, embora devido à presença de ficheiros menores.

Um valor `true` de faz sentido quando o número de divisórias é maior e os dados não são distorcidos. Nesses casos, o resultado da fase do mapa será escrito de modo a que cada partição seja tratada por um único redutor, resultando num melhor desempenho de consulta subsequente.

## <a name="resolution"></a>Resolução

1. Tente repartir os dados para normalizar em múltiplas divisórias.

1. Se #1 não for possível, desafie o valor do config em falso na sessão beeline e tente novamente a consulta. `set hive.optimize.sort.dynamic.partition=false`. Não é aconselhável definir o valor em falso a um nível de cluster. O valor `true` é ideal e definir o parâmetro conforme necessário com base na natureza dos dados e consulta.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais detalhadas, [reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).