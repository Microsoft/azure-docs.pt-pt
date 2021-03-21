---
title: Alertas apache Ambari em Azure HDInsight
description: Discussão e análise de possíveis razões e soluções para alertas apache Ambari em HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: e31e5d5a5b27f4dcb267905eae6329666fc3292c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946722"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Cenário: Alertas apache ambari em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Na Ia Apache Ambari, pode ver-se um alerta como este:

![Apache Ambari velho exemplo de alerta](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>Causa

Os agentes Ambari monitorizam continuamente a saúde de muitos recursos. *Os alertas* podem ser configurados para notificá-lo se propriedades específicas do cluster estão dentro de limiares pré-determinados. Após cada verificação de recursos ser executado, se a condição de alerta for cumprida, os agentes Ambari reportam o estado de volta ao servidor Ambari e desencadeiam um alerta. Se um alerta não for verificado de acordo com o intervalo no seu Perfil de Alerta, o servidor aciona um alerta *de Alerta Stales do Servidor Ambari.*

Existem várias razões pelas quais um exame de saúde pode não funcionar no seu intervalo definido:

* Os anfitriões estão sob uso pesado (uso elevado do CPU), de modo que o agente Ambari não pode obter recursos suficientes do sistema para executar os alertas a tempo.

* O cluster está ocupado executando muitos empregos ou serviços durante um período de carga pesada.

* Um pequeno número de anfitriões no cluster estão hospedando muitos componentes e por isso são obrigados a executar muitos alertas. Se o número de componentes for grande, os postos de alerta podem perder os intervalos programados.

## <a name="resolution"></a>Resolução

Experimente os seguintes métodos para resolver problemas com alertas ambari.

### <a name="increase-the-alert-interval-time"></a>Aumente o intervalo de alerta

Pode aumentar o valor de um intervalo de alerta individual, com base no tempo de resposta e carga do seu cluster:

1. No Apache Ambari UI, selecione o **separador Alertas.**
1. Selecione o nome de definição de alerta que deseja.
1. A partir da definição, **selecione Editar.**
1. Aumente o valor **do intervalo de verificação** e, em seguida, selecione **Guardar**.

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>Aumente o intervalo de alerta para alertas de servidores Ambari

1. No Apache Ambari UI, selecione o **separador Alertas.**
1. Na lista de **grupos** de down-down, selecione **AMBARI Predefinido**.
1. Selecione o alerta **de alertas do servidor Ambari.**
1. A partir da definição, **selecione Editar.**
1. Aumente o valor **do Intervalo de Verificação.**
1. Aumente o valor **multiplicador de intervalo** e, em seguida, selecione **Guardar**.

### <a name="disable-and-reenable-the-alert"></a>Desativar e reenuendível o alerta

Para descartar um alerta velho, desative-o e, em seguida, reensipe-o:

1. No Apache Ambari UI, selecione o **separador Alertas.**
1. Selecione o nome de definição de alerta que deseja.
1. A partir da definição, **selecione Ativado** na parte extrema-direita da UI.
1. Na janela pop-up **de confirmação,** selecione **Confirmar Desativar**.
1. Aguarde alguns segundos para que todas as "instâncias" de alerta mostradas na página sejam apuradas.
1. A partir da definição, **selecione Disabled** na parte extrema-direita da UI.
1. Na janela pop-up **de confirmação,** selecione **Confirmar Ativar**.

### <a name="increase-the-alert-grace-period"></a>Aumente o período de alerta

Há um período de graça antes de um agente de Ambari relatar que um alerta configurado falhou a sua agenda. Se o alerta falhou a hora prevista, mas correu dentro do período de graça, o alerta não é gerado.

O valor predefinido `alert_grace_period` é de 5 segundos. Pode configurar esta definição em /etc/ambari-agent/conf/ambari-agent.ini. Para os anfitriões em que os alertas obsoletos ocorrem em intervalos regulares, tente aumentar o valor para 10. Então, reinicie o agente Ambari.

## <a name="next-steps"></a>Passos seguintes

Se o seu problema não foi mencionado aqui ou se não conseguir resolvê-lo, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de peritos da Azure na [Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) no Twitter. Esta é a conta oficial da Microsoft Azure para melhorar a experiência do cliente. Liga a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, envie um pedido de apoio do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Para lá chegar, selecione Ajuda **(?**) do menu do portal ou abra o painel **de suporte Help +.** Para mais informações, consulte Como criar um pedido de [suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). 

  O suporte para gestão de subscrição e faturação está incluído na sua subscrição Microsoft Azure. O suporte técnico está disponível através dos [Planos de Apoio Azure.](https://azure.microsoft.com/support/plans/)