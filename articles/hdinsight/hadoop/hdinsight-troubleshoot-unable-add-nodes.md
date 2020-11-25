---
title: Incapaz de adicionar nódes ao cluster Azure HDInsight
description: Resolução de problemas por que não é capaz de adicionar nó no aglomerado Apache Hadoop em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 089eab563c83384bdb7e1681044fa89f5eb7f6c0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014674"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Cenário: Incapaz de adicionar nódes ao cluster Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Incapaz de adicionar nós ao cluster Azure HDInsight.

## <a name="cause"></a>Causa

As razões podem variar.

## <a name="resolution"></a>Resolução

Utilizando a função [de tamanho cluster,](../hdinsight-scaling-best-practices.md) calcule o número de núcleos adicionais necessários para o cluster. Isto baseia-se o número total de núcleos em nós de trabalho novos. Em seguida, experimente um ou mais dos seguintes passos:

* Verifique se há algum núcleo disponível na localização do cluster.

* Observe o número de núcleos disponíveis noutras localizações. Considere recriar o cluster numa localização diferente com núcleos suficientes disponíveis.

* Se pretender aumentar a quota principal para uma localização específica, envie um pedido de suporte para um aumento de quota principal do HDInsight.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]