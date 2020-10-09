---
title: Apache Ambari UI mostra anfitriões e serviços em Azure HDInsight
description: Resolução de problemas num problema de IA apache Ambari quando mostra anfitriões e serviços em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: 5340b1c7a6510595376789bc5777e6fb6f07dd4a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75895639"
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

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais detalhadas, [reveja como criar um pedido de suporte Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).
