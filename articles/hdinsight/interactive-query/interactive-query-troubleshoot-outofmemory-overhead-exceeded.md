---
title: Junta-se à Colmeia Apache leva a erro outOfMemory - Azure HDInsight
description: Lidar com erros outOfMemory "Limite de sobrecarga GC ultrapassado erro"
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: c227758e31b3b17768b8140475872245b2f34e52
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92532956"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>Cenário: Junta-se à Colmeia Apache leva a um erro outOfMemory em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao utilizar componentes de consultainterna interactive em clusters Azure HDInsight.

## <a name="issue"></a>Problema

O comportamento padrão para a Colmeia Apache junta-se é carregar todo o conteúdo de uma tabela na memória para que uma junção possa ser realizada sem ter que executar um passo mapa/redução. Se a mesa da Colmeia for demasiado grande para caber na memória, a consulta pode falhar.

## <a name="cause"></a>Causa

Ao correr junta-se em colmeia de tamanho suficiente, encontra-se o seguinte erro:

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>Resolução

Evite que a Colmeia carrene as tabelas na memória em juntas (em vez de executar um passo Mapa/Redução) definindo o seguinte valor de configuração da Colmeia:

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>Passos seguintes

Se a definição deste valor não resolveu o seu problema, visite um dos seguintes...

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais detalhadas, [reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).