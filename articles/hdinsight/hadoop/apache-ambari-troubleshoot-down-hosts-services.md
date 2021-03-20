---
title: Apache Ambari UI mostra anfitriões e serviços em Azure HDInsight
description: Resolução de problemas num problema de IA apache Ambari quando mostra anfitriões e serviços em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/02/2019
ms.openlocfilehash: 0d3a393dc1cbd51b83edc0b6989bb5e7a5670a29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943276"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Cenário: Apache Ambari UI mostra anfitriões e serviços em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

A Ia Apache Ambari está acessível, mas a UI mostra que quase todos os serviços estão em baixo, todos os anfitriões mostram o batimento cardíaco perdido.

## <a name="cause"></a>Causa

Na maioria dos cenários, este é um problema com o servidor Ambari não funcionando no headnode ativo. Verifique qual cabeçano é o headnode ativo e certifique-se de que o seu servidor ambari funciona no direito. Não inicie manualmente o ambari-server, deixe que o serviço de controlador de failover seja responsável por iniciar o ambari-servidor no headnode direito. Reinicie o cabeçano ativo para forçar uma falha.

Problemas de ligação em rede também podem causar este problema. De cada nó de cluster, veja se consegue `headnodehost` pingar. Há uma situação rara em que nenhum nó de cluster pode ligar-se `headnodehost` a:

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Resolução

Normalmente, reiniciar o headnode ativo irá atenuar este problema. Caso contrário contacte a equipa de suporte da HDInsight.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]