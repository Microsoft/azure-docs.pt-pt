---
title: REST API para consultar Apache HBase em Azure HDInsight
description: ''
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/08/2020
ms.openlocfilehash: 361eefa9b0dce811533bb4da9c236c207e31c61c
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547882"
---
# <a name="rest-api-to-query-apache-hbase-in-azure-hdinsight"></a>REST API para consultar Apache HBase em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

A utilização da interface Apache HBase REST para consultar uma tabela sob um espaço de nome que não seja o padrão resulta num erro de tempo de execução (estado HTTP 500).

## <a name="cause"></a>Causa

A API HBase REST só é suportada quando utilizar o espaço de nome predefinido. Este é um problema conhecido no que diz respeito à utilização de espaços de nome HBase ou a fazer chamadas que se referem a GETs específicos em colunas com famílias de colunas com servidor REST em HDInsight. Isto é devido a um problema de segurança com o HDInsight Gateway. Ao utilizar a API para criar uma tabela com um espaço de nome, acedendo a colunas através de famílias de colunas, é necessário especificar o `:` carácter, que é considerado um problema de segurança no módulo IIS Gateway.

## <a name="mitigation"></a>Mitigação

Contorne o servidor Gateway/REST implantando a sua aplicação num VM que está localizado no mesmo Azure VNet que o cluster HDInsight. Em seguida, pode comunicar com a HBase diretamente através do RPC (contornando completamente o servidor REST), quer para servidores individuais DO REST que executam nos nós dos trabalhadores que contornam o HDInsight Gateways.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligação da comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).