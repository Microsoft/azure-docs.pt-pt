---
title: Aplicação Apache Tez está pendurada no Azure HDInsight
description: Aplicação Apache Tez está pendurada no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/09/2019
ms.openlocfilehash: 56c68c26ae953034283031e2427b7a4afadee94e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98935932"
---
# <a name="scenario-apache-tez-application-hangs-in-azure-hdinsight"></a>Cenário: Aplicação Apache Tez está pendurada no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Depois de submeter o trabalho da Apache Hive, de Tez ver o estado de trabalho é "Running", mas não parece fazer qualquer progresso

## <a name="cause"></a>Causa

Demasiados postos de trabalho submetidos; longa fila de fios.

## <a name="resolution"></a>Resolução

Dimensione o aglomerado, ou espere até que a fila do Fio seja drenada.

Por `yarn.scheduler.capacity.maximum-applications` predefinição controla o número máximo de aplicações que estão em execução ou pendentes, e por defeito de `10000` .

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligação da comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).