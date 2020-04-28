---
title: Apache Spark lento quando o armazenamento Azure HDInsight tem muitos ficheiros
description: O trabalho da Apache Spark funciona lentamente quando o contentor de armazenamento Azure contém muitos ficheiros no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/21/2019
ms.openlocfilehash: e389c05a6de85287bc86eff510e137f470837e56
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75894332"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>A tarefa do Apache Spark é executada lentamente quando o contentor de armazenamento do Azure contém muitos ficheiros no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao usar componentes Apache Spark em clusters Azure HDInsight.

## <a name="issue"></a>Problema

Ao executar um cluster HDInsight, o trabalho apache Spark que escreve para o recipiente de armazenamento Azure torna-se lento quando há muitos ficheiros/subpastas. Por exemplo, leva 20 segundos ao escrever para um novo recipiente, mas cerca de 2 minutos ao escrever para um recipiente que tem ficheiros de 200k.

## <a name="cause"></a>Causa

Esta é uma questão conhecida de Spark. A lentidão vem `ListBlob` `GetBlobProperties` das operações durante a execução do spark job.

Para rastrear divisórias, `FileStatusCache` a Spark tem de manter uma informação que contém informações sobre a estrutura do diretório. Utilizando esta cache, a Spark pode analisar os caminhos e estar atento às divisórias disponíveis. O benefício de rastrear divisórias é que a Spark só toca nos ficheiros necessários quando lê os dados. Para manter esta informação atualizada, quando escrever novos dados, a Spark tem de listar todos os ficheiros sob o diretório e atualizar esta cache.

No Spark 2.1, embora não precisemos de atualizar a cache após cada escrita, a Spark verificará se uma coluna de partição existente corresponde à proposta no pedido de escrita atual, pelo que também levará a operações de listagem no início de cada escrita.

Na Spark 2.2, ao escrever dados com o modo apêndice, este problema de desempenho deve ser corrigido.

## <a name="resolution"></a>Resolução

Quando cria um conjunto de dados dividido, é importante utilizar um esquema de partilha que limitará `FileStatusCache`o número de ficheiros que a Spark tem de enumerar para atualizar o .

Para cada microlote Nth onde N % 100 == 0 (100 é apenas um exemplo), mova os dados existentes para outro diretório, que pode ser carregado pela Spark.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, por favor reveja [como criar um pedido de apoio Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
