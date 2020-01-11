---
title: O redutor está lento no Azure HDInsight
description: O redutor está lento no Azure HDInsight a partir de uma possível distorção de dados
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8a9c7ed9f6b5b8ec89bfca6dd59034b11f05f9a3
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75895169"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>Cenário: o redutor está lento no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao usar componentes de consulta interativa em clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Ao executar uma consulta como `insert into table1 partition(a,b) select a,b,c from table2` o plano de consulta inicia um monte de redutores, mas os dados de cada partição vão para um único redutor. Isso faz com que a consulta seja tão lenta quanto o tempo gasto pelo redutor da partição maior.

## <a name="cause"></a>Causa

Abra [beeline](../hadoop/apache-hadoop-use-hive-beeline.md) e verifique o valor do conjunto `hive.optimize.sort.dynamic.partition`.

O valor dessa variável deve ser definido como true/false com base na natureza dos dados.

Se as partições na tabela de entrada forem menores (digamos menos de 10) e, portanto, for o número de partições de saída e a variável for definida como `true`, isso fará com que os dados sejam classificados globalmente e gravados usando um único redutor por partição. Mesmo que o número de redutores disponível seja maior, alguns redutores podem estar atrasados devido à distorção de dados e o paralelismo máximo não pode ser obtido. Quando alterado para `false`, mais de um redutor pode manipular uma única partição e vários arquivos menores serão gravados, resultando em uma inserção mais rápida. Isso pode afetar outras consultas, embora devido à presença de arquivos menores.

Um valor de `true` faz sentido quando o número de partições é maior e os dados não são distorcidos. Nesses casos, o resultado da fase de mapa será escrito de forma que cada partição será tratada por um único redutor, resultando em melhor desempenho de consulta subsequente.

## <a name="resolution"></a>Resolução

1. Tente reparticionar os dados para normalizar em várias partições.

1. Se #1 não for possível, defina o valor da configuração como false na sessão beeline e tente a consulta novamente. `set hive.optimize.sort.dynamic.partition=false`. Não é recomendável definir o valor como false em um nível de cluster. O valor de `true` é ideal e define o parâmetro conforme necessário com base na natureza dos dados e na consulta.

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com o [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
