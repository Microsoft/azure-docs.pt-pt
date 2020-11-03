---
title: Incapaz de entrar no cluster Azure HDInsight
description: Resolução de problemas por que não consegue entrar no aglomerado Apache Hadoop em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 222c48ab2b9b718bc373e710f37f906e68bd31d4
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289007"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Cenário: Incapaz de entrar no cluster Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Incapaz de entrar no cluster Azure HDInsight.

## <a name="cause"></a>Causa

As razões podem variar. Tenha em mente que ao iniciar sessão nos painéis de instrumentos de agrupamento ou aplicações, utilize as suas credenciais de "cluster login" ou HTTP. Ao ligar remotamente, utilize as credenciais Secure Shell (SSH) ou de Ambiente de Trabalho Remoto.

## <a name="resolution"></a>Resolução

Para resolver problemas comuns, experimente um ou mais dos métodos seguintes.

* Experimente abrir o dashboard do cluster num novo separador do browser em modo de privacidade.

* Se não conseguir recordar as suas credenciais SSH, pode [redefinir as credenciais dentro da UI Ambari.](../hdinsight-administer-use-portal-linux.md#change-passwords)

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]