---
title: Redutor é lento em Azure HDInsight
description: Redutor é lento em Azure HDInsight de possível distorção de dados
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8a9c7ed9f6b5b8ec89bfca6dd59034b11f05f9a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895169"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>Cenário: Redutor é lento em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao utilizar componentes de Consulta Interativa em clusters Azure HDInsight.

## <a name="issue"></a>Problema

Ao executar uma consulta `insert into table1 partition(a,b) select a,b,c from table2` como o plano de consulta começa um monte de redutores, mas os dados de cada partição vão para um único redutor. Isto faz com que a consulta seja tão lenta quanto o tempo devida pelo maior redutor da partição.

## <a name="cause"></a>Causa

Abra [a linha de apicultura](../hadoop/apache-hadoop-use-hive-beeline.md) e verifique o valor do conjunto `hive.optimize.sort.dynamic.partition`.

O valor desta variável destina-se a ser definido como verdadeiro/falso com base na natureza dos dados.

Se as divisórias na tabela de entrada forem inferiores (digamos menos de 10), `true`assim como o número de divisórias de saída, e a variável é definida para , isto faz com que os dados sejam classificados e escritos globalmente usando um único redutor por divisória. Mesmo que o número de redutores disponíveis seja maior, alguns redutores podem estar atrasados devido ao desvio de dados e o paralelismo máximo não pode ser atingido. Quando alterado `false`para , mais de um redutor pode manusear uma única divisória e vários ficheiros menores serão escritos, resultando numa inserção mais rápida. Isto pode afetar mais consultas devido à presença de ficheiros menores.

Um valor `true` faz sentido quando o número de divisórias é maior e os dados não são distorcidos. Nesses casos, o resultado da fase do mapa será escrito de modo a que cada partição seja manuseada por um único redutor, resultando num melhor desempenho subsequente da consulta.

## <a name="resolution"></a>Resolução

1. Tente repartipartição dos dados para normalizar em várias divisórias.

1. Se #1 não for possível, delineie o valor do config para falso em sessão de beeline e tente novamente a consulta. `set hive.optimize.sort.dynamic.partition=false`. Não é recomendada a definição do valor para falso a um nível de cluster. O valor `true` é ideal e define o parâmetro conforme necessário com base na natureza dos dados e da consulta.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, por favor reveja [como criar um pedido de apoio Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
