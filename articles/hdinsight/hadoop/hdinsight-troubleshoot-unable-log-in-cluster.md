---
title: Incapaz de entrar no cluster Azure HDInsight
description: Resolução de problemas por que não consegue entrar no aglomerado Apache Hadoop em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: 03657606f7e24436ff5a851fe7eef1e0c4d5dc24
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944308"
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

## <a name="next-steps"></a>Próximos passos

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]