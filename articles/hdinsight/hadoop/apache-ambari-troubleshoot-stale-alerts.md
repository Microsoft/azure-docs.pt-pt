---
title: Alertas de Apache Ambari em Azure HDInsight
description: Discussão e análise de possíveis razões e soluções para alertas de Apache Ambari em HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f19d499b5e50fbb5030a0f396296eed46fc6eee3
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722814"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Cenário: Apache Ambari alerta stale em Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

A partir do Apache Ambari UI, pode ver um alerta semelhante à seguinte imagem:

![Apache Ambari alerta de alerta](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>Causa

Os agentes ambari executam continuamente os controlos de saúde para monitorizar a saúde de muitos recursos. Cada alerta é configurado para ser executado em intervalos de tempo predefinidos. Após a execução de cada alerta, os agentes da Ambari reportam o estado ao servidor Ambari. Neste ponto, se o servidor Ambari detetar que algum dos alertas não foi executado em tempo útil, então desencadeia um "Alerta de Servidor Ambari". Existem várias razões pelas quais um exame de saúde pode não ser executado no seu intervalo definido:

* Quando os anfitriões estão sob forte utilização (alta CPU), existe a possibilidade de o Agente Ambari não ter conseguido recursos suficientes para executar os alertas em tempo útil.

* O cluster está ocupado a executar muitos empregos/serviços durante a carga pesada.

* Poucos anfitriões no cluster podem acolher muitos componentes e, portanto, serão obrigados a executar muitos alertas. Se o número de componentes for grande, é possível que os empregos de alerta possam perder os intervalos programados.

## <a name="resolution"></a>Resolução

### <a name="increase-alert-interval-time"></a>Aumentar o tempo de intervalo de alerta

Pode optar por aumentar o valor de um intervalo de alerta individual com base no tempo de resposta do seu cluster e na sua carga.

1. A partir do Apache Ambari UI, selecione o separador **Alertas.**
1. Selecione o nome de definição de alerta desejado.
1. A partir da definição, **selecione Editar**.
1. Modifique o valor do intervalo de **verificação** conforme desejado e, em seguida, selecione **Guardar**.

### <a name="increase-alert-interval-time-for-ambari-server-alerts"></a>Aumente o tempo de intervalo de alerta para alertas do servidor Ambari

1. A partir do Apache Ambari UI, selecione o separador **Alertas.**
1. A partir da lista de lançamento seletiva de **Grupos,** selecione **AmBARI Predefinido**.
1. Selecione **alertas ambari server**.
1. A partir da definição, **selecione Editar**.
1. Modificar o valor do intervalo de **verificação** conforme desejado.
1. Modifique o valor multiplicador de **intervalos** conforme desejado e, em seguida, selecione **Guardar**.

### <a name="disable-and-enable-the-alert"></a>Desativar e ativar o alerta

Pode desativar e, em seguida, ativar novamente o alerta para descartar quaisquer alertas velhos.

1. A partir do Apache Ambari UI, selecione o separador **Alertas.**
1. Selecione o nome de definição de alerta desejado.
1. A partir da definição, selecione **Habilitado** localizado na extrema-direita.
1. A partir do pop-up de **confirmação,** selecione **Confirmar Desativar**.
1. Aguarde alguns segundos para que todos os alertas "Instâncias" mostrados na página estejam limpos.
1. A partir da definição, selecione **Desativado** localizado na extrema-direita.
1. A partir do pop-up de **confirmação,** selecione **Confirmar Ativação**.

### <a name="increase-alert-grace-time"></a>Aumentar o tempo de alerta de graça

Antes do agente Ambari relatar que um alerta configurado falhou a sua agenda, há um tempo de graça aplicado. Mesmo que o alerta não tenha cumprido a hora programada, mas tenha sido acionado dentro do tempo de alerta, então o alerta não é disparado.

O valor de `alert_grace_period` por defeito é de 5 segundos. Esta definição `alert_grace_period` é configurável em `/etc/ambari-agent/conf/ambari-agent.ini`. Para os anfitriões a partir dos quais os alertas são disparados regularmente, tente aumentar para um valor de 10. Em seguida, reiniciar o agente Ambari

## <a name="next-steps"></a>Passos Seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
