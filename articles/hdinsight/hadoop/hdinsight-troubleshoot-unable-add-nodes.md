---
title: Incapaz de adicionar nódes ao cluster Azure HDInsight
description: Resolução de problemas por que não é capaz de adicionar nó no aglomerado Apache Hadoop em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: b11d1edef2f3a6fa0fb39c76d1f25ec05ff15d07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944320"
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