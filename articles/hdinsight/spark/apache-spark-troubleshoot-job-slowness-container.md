---
title: Apache Spark lento quando o armazenamento Azure HDInsight tem muitos ficheiros
description: O trabalho da Apache Spark funciona lentamente quando o contentor de armazenamento Azure contém muitos ficheiros em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/21/2019
ms.openlocfilehash: c26baec66248ca00ef212acf3d773c2566b3aea9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946366"
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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]