---
title: REST API para consulta Apache HBase em Azure HDInsight
description: ''
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/08/2020
ms.openlocfilehash: 3bf63aa08ec4c1deff2551cfcc0cf188a75261bc
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515485"
---
# <a name="rest-api-to-query-apache-hbase-in-azure-hdinsight"></a>REST API para consulta Apache HBase em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Utilizar a interface Apache HBase REST para consultar uma tabela sob um espaço de nome diferente do padrão resulta num erro de tempo de execução (status HTTP 500).

## <a name="cause"></a>Causa

HBase REST API só é suportado quando utilizar o espaço de nome padrão. Este é um problema conhecido no que diz respeito à utilização de espaços de nome HBase ou a chamadas que se referem a GETs específicos em colunas com famílias de colunas com servidor REST no HDInsight. Isto deve-se a um problema de segurança com o HDInsight Gateway. Ao utilizar a API para criar uma tabela com um espaço de nome, acedendo a colunas através de famílias de colunas, é necessário especificar o `:` personagem, que é considerado um problema de segurança no módulo IIS Gateway.

## <a name="mitigation"></a>Mitigação

Contorne o servidor Gateway/REST implementando a sua aplicação num VM que está localizado no mesmo Azure VNet que o cluster HDInsight. Em seguida, pode comunicar com hBase diretamente via RPC (contornando totalmente o servidor REST), ou com servidores REST individuais que executam em nós de trabalhadores contornando os Gateways HDInsight.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligar a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
