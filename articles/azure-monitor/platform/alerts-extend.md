---
title: Expandir alertas do Log Analytics (cópia) para o Azure Government Cloud
description: Visão geral do processo para copiar alertas do Log Analytics no portal do OMS para alertas do Azure, com detalhes de endereçamento preocupações mais comuns do cliente.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: efd2fc9d164564126b7b641ef35bbb10c4834f49
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60996492"
---
# <a name="extend-log-analytics-alerts-to-azure-alerts"></a>Expandir alertas do Log Analytics para alertas do Azure

> [!NOTE]
> Já a Microsoft concluiu o processo descrito neste artigo para as versões públicas do Azure. No entanto, ele ainda se aplica a versões de administração pública dos EUA.  

Até recentemente, o Azure Log Analytics incluído sua própria funcionalidade alerta, o que foi notificado proativamente das condições com base nos dados do Log Analytics. Gerido regras de alerta no portal do Microsoft Operations Management Suite. A nova experiência de alertas está agora integrado alertas em vários serviços no Microsoft Azure. Isto está disponível como **alertas** em Azure Monitor no portal do Azure e suporta a alertas dos registos de atividades, métricas e inicia a partir do Log Analytics e Azure Application Insights.

Clientes na cloud do Azure Government, com o portal do OMS, pode [voluntariamente expandir suas regras de alerta para o Azure](alerts-extend-tool.md) partir **1 de Fevereiro de 2019**. A partir **dia 1 de Março de 2019** - Microsoft será automaticamente-expandir de forma sistemática todas as regras de alerta existentes no Azure Government - portal do OMS para o Azure; sem qualquer período de inatividade ou interrupção para a sua monitorização. Qualquer nova área de trabalho criada em ou após **dia 1 de Março de 2019** na cloud do Azure Government OMS portal serão expandidos automaticamente para o Azure.

## <a name="benefits-of-extending-your-alerts"></a>Vantagens de expandir os seus alertas
Existem diversas vantagens de criar e gerir alertas no portal do Azure, tais como:

- Ao contrário no portal do Operations Management Suite, onde apenas 250 alertas pudessem ser criadas e visualizadas, alertas do Azure tem sem essa limitação.
- Dos alertas do Azure, pode gerir, enumerar e ver todos os tipos de alerta. Anteriormente, apenas possível fazer isso para alertas do Log Analytics.
- Pode limitar o acesso aos utilizadores apenas a monitorização e alertas, utilizando o [funções do Azure Monitor](../../azure-monitor/platform/roles-permissions-security.md).
- Nos alertas do Azure, pode utilizar [grupos de ação](../../azure-monitor/platform/action-groups.md). Isto permite-lhe ter mais de uma ação para cada alerta. SMS, pode enviar uma chamada de voz, invocar um runbook da automatização do Azure, invocar um webhook e configurar um conector de gestão de serviço de TI (ITSM). 

## <a name="process-of-extending-your-alerts"></a>Processo de expandir os seus alertas
O processo de mover os alertas do Log Analytics para alertas do Azure não envolve a alterar a definição de alerta, consulta ou configuração de qualquer forma. A única alteração necessária é que no Azure, executar todas as ações ao utilizar um grupo de ação. Se os grupos de ação já estão associados com o alerta, são incluídos quando expandidos para o Azure.

Quando agendar alertas numa área de trabalho do Log Analytics para ser estendido para o Azure, eles continuar a trabalhar e não em que qualquer forma de comprometer a configuração. Quando agendada, os alertas podem não estar disponíveis para modificação temporariamente, mas pode continuar a criar novos alertas do Azure durante este período. Se tentar editar ou criar alertas a partir do portal do Operations Management Suite, terá a opção para continuar a criá-los a partir de sua área de trabalho do Log Analytics. Também é possível criá-los dos alertas do Azure no portal do Azure.

 ![Captura de ecrã da opção para criar alertas do Log Analytics ou alertas do Azure](media/alerts-extend/ScheduledDirection.png)

> [!NOTE]
> Expandir alertas do Log Analytics para o Azure não incorre em custos à sua conta. Utilização de alertas do Azure para a consulta com base em alertas do Log Analytics não é faturado quando utilizado dentro dos limites e condições indicados os [política de preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).  


### <a name="how-to-extend-your-alerts-voluntarily"></a>Como expandir os seus alertas voluntariamente
Para expandir os alertas para alertas do Azure, pode utilizar um assistente disponível no portal do Operations Management Suite ou pode fazer por isso, por meio de programação com uma API. Para obter mais informações, consulte [expandir alertas para o Azure com o portal do Operations Management Suite e a API](alerts-extend-tool.md).

## <a name="experience-after-extending-your-alerts"></a>Experiência após expandir os alertas
Depois dos alertas são expandidos para alertas do Azure, continuarem a estar disponível no portal do Operations Management Suite para a gestão de não diferente do que antes.

![Portal de captura de ecrã do Operations Management Suite, com alertas listados](media/alerts-extend/PostExtendList.png)

Quando tenta editar um alerta existente ou criar um novo alerta no portal do Operations Management Suite, será automaticamente redirecionado para alertas do Azure.  

> [!NOTE]
> Certifique-se de que as permissões atribuídas aos indivíduos que tem de adicionar ou editar os alertas são corretamente atribuídas no Azure. Para compreender quais as permissões que tem de conceder, veja [permissões para utilizar o Azure Monitor e alertas](../../azure-monitor/platform/roles-permissions-security.md).  
> 

Pode continuar a criar alertas a partir da [API do Log Analytics](../../azure-monitor/platform/api-alerts.md) e [modelo de recursos do Log Analytics](../../azure-monitor/insights/solutions-resources-searches-alerts.md). Quando fizer isso, deve incluir grupos de ação.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre as ferramentas para [iniciar expandir alertas do Log Analytics para o Azure](alerts-extend-tool.md).
* Saiba mais sobre o [experiência de alertas do Azure](../../azure-monitor/platform/alerts-overview.md).
* Saiba como criar [alertas de registo nos alertas do Azure](alerts-unified-log.md).

