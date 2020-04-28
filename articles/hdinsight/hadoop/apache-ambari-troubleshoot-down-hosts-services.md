---
title: Apache Ambari UI mostra anfitriões e serviços no Azure HDInsight
description: Problemas de resolução de um problema da UI Apache Ambari quando mostra anfitriões e serviços em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: 5340b1c7a6510595376789bc5777e6fb6f07dd4a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75895639"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Cenário: Apache Ambari UI mostra anfitriões e serviços no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Apache Ambari UI é acessível, mas a UI mostra que quase todos os serviços estão em baixo, todos os anfitriões mostrando batimentos cardíacos perdidos.

## <a name="cause"></a>Causa

Na maioria dos cenários, este é um problema com o servidor Ambari não funcionar no nódeo ativo. Verifique qual o nódoo ativo e certifique-se de que o seu servidor ambari corre no lado certo. Não inicie manualmente o servidor ambari, deixe que o serviço de controlador failover seja responsável por iniciar o servidor ambari no nódoado direito. Reinicie o nódeodo ativo para forçar uma falha.

As questões de networking também podem causar este problema. De cada nó de cluster, `headnodehost`veja se consegue ping. Há uma situação rara em que nenhum `headnodehost`nó de cluster pode ligar-se a:

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Resolução

Normalmente reiniciar o nódoado ativo irá atenuar esta questão. Caso contrário, contacte a equipa de suporte da HDInsight.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, por favor reveja [como criar um pedido de apoio Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
