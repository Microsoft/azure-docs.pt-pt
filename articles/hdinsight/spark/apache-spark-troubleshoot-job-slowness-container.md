---
title: Apache Spark lento quando o armazenamento Azure HDInsight tem muitos ficheiros
description: O trabalho da Apache Spark funciona lentamente quando o contentor de armazenamento Azure contém muitos ficheiros em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/21/2019
ms.openlocfilehash: e389c05a6de85287bc86eff510e137f470837e56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75894332"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>A tarefa do Apache Spark é executada lentamente quando o contentor de armazenamento do Azure contém muitos ficheiros no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao utilizar componentes Apache Spark em clusters Azure HDInsight.

## <a name="issue"></a>Problema

Ao executar um cluster HDInsight, o trabalho Apache Spark que escreve para o recipiente de armazenamento Azure torna-se lento quando há muitos ficheiros/sub-pastas. Por exemplo, leva 20 segundos ao escrever para um novo recipiente, mas cerca de 2 minutos ao escrever para um recipiente que tem 200 mil ficheiros.

## <a name="cause"></a>Causa

Este é um assunto conhecido da Spark. A lentidão vem das `ListBlob` `GetBlobProperties` operações e operações durante a execução do trabalho de Spark.

Para rastrear as divisórias, a Spark tem de manter uma `FileStatusCache` que contenha informações sobre a estrutura do diretório. Utilizando esta cache, a Spark pode analisar os caminhos e estar atento às divisórias disponíveis. O benefício de rastrear divisórias é que a Spark só toca nos ficheiros necessários quando lê dados. Para manter esta informação atualizada, quando escreve novos dados, a Spark tem de listar todos os ficheiros no diretório e atualizar esta cache.

No Spark 2.1, embora não precisemos de atualizar a cache após cada escrita, a Spark verificará se uma coluna de partição existente corresponde à proposta no pedido de escrita atual, pelo que também conduzirá a operações de listagem no início de cada escrita.

No Spark 2.2, ao escrever dados com o modo de apêndice, este problema de desempenho deve ser corrigido.

## <a name="resolution"></a>Resolução

Quando cria um conjunto de dados dividido, é importante utilizar um esquema de partição que limitará o número de ficheiros que a Spark tem de listar para atualizar o `FileStatusCache` .

Para cada micro lote Nth onde N % 100 == 0 (100 é apenas um exemplo), mova os dados existentes para outro diretório, que pode ser carregado pela Spark.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais detalhadas, [reveja como criar um pedido de suporte Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).
