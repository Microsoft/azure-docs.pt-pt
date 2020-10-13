---
title: Conflito portuário ao iniciar serviços em Azure HDInsight
description: Etapas de resolução de problemas e possíveis resoluções para problemas de conflito portuário ao interagir com clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 0d39f31e9d52359d0c91317280a7f8db06c1c1b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530969"
---
# <a name="scenario-port-conflict-when-starting-services-in-azure-hdinsight"></a>Cenário: Conflito portuário ao iniciar serviços no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Um serviço não começa.

## <a name="cause"></a>Causa

Existe um conflito portuário.

## <a name="resolution"></a>Resolução

### <a name="method-1"></a>Método 1

Utilize comandos abaixo para obter/eliminar todos os processos de execução, que são afetados pela questão da porta.

```bash
netstat -lntp | grep <port>
ps -ef | grep <service>
kill -9 <service>
```

Então inicie o serviço.

### <a name="method-2"></a>Método 2

Reinicie o nó.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligação da comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).
