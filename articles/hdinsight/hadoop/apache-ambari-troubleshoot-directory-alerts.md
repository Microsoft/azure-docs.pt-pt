---
title: Alertas de diretório Apache Ambari em Azure HDInsight
description: Discussão e análise de possíveis razões e soluções para alertas de diretório Apache Ambari em HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 47d1f9797a44d7dc918677c21ffc7a124808525d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943293"
---
# <a name="scenario-apache-ambari-directory-alerts-in-azure-hdinsight"></a>Cenário: Alertas de diretório Apache Ambari em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Você recebe erros de Apache Ambari que são semelhantes a:

```
1/1 local-dirs have errors: [ /mnt/resource/hadoop/yarn/local : Cannot create directory: /mnt/resource/hadoop/yarn/local ]
1/1 log-dirs have errors: [ /mnt/resource/hadoop/yarn/log : Cannot create directory: /mnt/resource/hadoop/yarn/log ]
```

## <a name="cause"></a>Causa

Faltam os referidos diretórios de alerta de Ambari nos nó(s) dos trabalhadores afetados.

## <a name="resolution"></a>Resolução

Criar manualmente diretórios em falta nos nó(s) do trabalhador afetados.

1. SSH para o nó de trabalhador relevante.

1. Obtenha o utilizador de raiz: `sudo su` .

1. Recursivamente criar diretórios necessários.

1. Mude de proprietário e grupo para estes diretórios.

    ```bash
    chown -R yarn /mnt/resource/hadoop/yarn/local
    chgrp -R hadoop /mnt/resource/hadoop/yarn/local
    chown -R yarn /mnt/resource/hadoop/yarn/log
    chgrp -R hadoop /mnt/resource/hadoop/yarn/log
    ```

1. A partir da Ia Apache Ambari, desative e, em seguida, ative o alerta.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]