---
title: Alertas de Apache Ambari em Azure HDInsight
description: Discussão e análise de possíveis razões e soluções para alertas de Apache Ambari em HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f9dfcb930e3fe4f862f9f51ff00270d0eb0c66ca
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539115"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Cenário: Apache Ambari alerta stale em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Na UI Apache Ambari, pode ver-se um alerta como este:

![Apache Ambari alerta de alerta](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>Causa

Os agentes ambari monitorizam continuamente a saúde de muitos recursos. *Os alertas* podem ser configurados para notificá-lo se propriedades específicas do cluster estão dentro dos limiares pré-determinados. Após cada verificação de recursos, se a condição de alerta for satisfeita, os agentes da Ambari reportam o estado de volta ao servidor Ambari e desencadeiam um alerta. Se um alerta não for verificado de acordo com o intervalo no seu Perfil de Alerta, o servidor aciona um alerta de Alerta Stale do *Servidor Ambari.*

Existem várias razões pelas quais um exame de saúde pode não ser executado no seu intervalo definido:

* Os anfitriões estão sob uso pesado (alta utilização de CPU), para que o agente Ambari não consiga recursos suficientes do sistema para executar os alertas a tempo.

* O cluster está ocupado a executar muitos empregos ou serviços durante um período de carga pesada.

* Um pequeno número de anfitriões no cluster estão hospedando muitos componentes e, por isso, são obrigados a executar muitos alertas. Se o número de componentes for grande, os postos de trabalho em alerta podem perder os intervalos programados.

## <a name="resolution"></a>Resolução

Experimente os seguintes métodos para resolver problemas com alertas ambari.

### <a name="increase-the-alert-interval-time"></a>Aumente o intervalo de alerta

Pode aumentar o valor de um intervalo de alerta individual, com base no tempo de resposta e carga do seu cluster:

1. No Apache Ambari UI, selecione o separador **Alertas.**
1. Selecione o nome de definição de alerta que deseja.
1. A partir da definição, **selecione Editar**.
1. Aumente o valor do intervalo de **verificação** e, em seguida, selecione **Guardar**.

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>Aumente o tempo de intervalo de alerta para alertas do servidor Ambari

1. No Apache Ambari UI, selecione o separador **Alertas.**
1. A partir da lista de lançamento seletiva de **Grupos,** selecione **AmBARI Predefinido**.
1. Selecione o alerta de **alerta sinuoso ambari.**
1. A partir da definição, **selecione Editar**.
1. Aumente o valor do intervalo de **verificação.**
1. Aumente o valor multiplicador de **intervalos** e, em seguida, selecione **Guardar**.

### <a name="disable-and-reenable-the-alert"></a>Desativar e reativar o alerta

Para descartar um alerta velho, desative-o e, em seguida, reativa-o:

1. No Apache Ambari UI, selecione o separador **Alertas.**
1. Selecione o nome de definição de alerta que deseja.
1. A partir da definição, selecione **Habilitado** na parte extrema-direita da UI.
1. Na janela pop-up **confirmação,** selecione **Confirmar Desativar**.
1. Aguarde alguns segundos para que todos os alertas "instâncias" mostrados na página sejam apurados.
1. A partir da definição, selecione **Disabled** na parte extrema-direita da UI.
1. Na janela pop-up **confirmação,** selecione **Confirmar Ativação**.

### <a name="increase-the-alert-grace-period"></a>Aumentar o período de alerta de graça

Há um período de graça antes de um agente ambari relatar que um alerta configurado falhou a sua agenda. Se o alerta não tiver cumprido o tempo previsto, mas que decorreu dentro do período de carência, o alerta não é gerado.

O valor de `alert_grace_period` por defeito é de 5 segundos. Pode configurar esta definição em /etc/ambari-agent/conf/ambari-agent.ini. Para os anfitriões em que ocorrem alertas em intervalos regulares, tente aumentar o valor para 10. Então, reinicie o agente Ambari.

## <a name="next-steps"></a>Passos seguintes

Se o seu problema não foi mencionado aqui ou se não for capaz de resolvê-lo, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure no [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) no Twitter. Esta é a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Liga a comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, envie um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Para lá chegar, selecione Ajuda ( **?** ) a partir do menu do portal ou abra o painel de **suporte Ajuda +.** Para mais informações, consulte [Como criar um pedido de apoio Azure.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) 

  O suporte para gestão de subscrições e faturação está incluído na subscrição do Microsoft Azure. O suporte técnico está disponível através dos Planos de [Apoio ao Azure.](https://azure.microsoft.com/support/plans/)
