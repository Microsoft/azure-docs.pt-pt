---
title: A interface do usuário do Apache Ambari mostra hosts e serviços no Azure HDInsight
description: Solução de problemas de um problema da interface do usuário do Apache Ambari quando ele mostra hosts e serviços no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: 5340b1c7a6510595376789bc5777e6fb6f07dd4a
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75895639"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Cenário: a interface do usuário do Apache Ambari mostra hosts e serviços no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

A interface do usuário do Apache Ambari é acessível, mas a interface do usuário mostra quase todos os serviços estão inativos, todos os hosts mostrando a pulsação perdida

## <a name="cause"></a>Causa

Na maioria dos cenários, isso é um problema com o Ambari Server não estar em execução no Active cabeçalho. Verifique qual cabeçalho é o cabeçalho ativo e certifique-se de que o ambari-Server seja executado à direita. Não inicie manualmente o ambari-Server, deixe que o serviço do controlador de failover seja responsável por iniciar o ambari-Server no cabeçalho correto. Reinicialize o cabeçalho ativo para forçar um failover.

Problemas de rede também podem causar esse problema. Em cada nó de cluster, consulte se você pode executar ping `headnodehost`. Há uma situação rara em que nenhum nó de cluster pode se conectar a `headnodehost`:

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Resolução

Geralmente, reinicializar o cabeçalho ativo atenuará esse problema. Caso contrário, entre em contato com a equipe de suporte do HDInsight.

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com o [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
