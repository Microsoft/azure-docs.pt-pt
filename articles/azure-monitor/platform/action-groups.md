---
title: Criar e gerir grupos de ação no portal do Azure
description: Saiba como criar e gerir grupos de ação no portal do Azure.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 1/29/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: a817e3681f6b94b1b9ad7d5a2d2a1369935ee064
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56651776"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Criar e gerir grupos de ação no portal do Azure
## <a name="overview"></a>Descrição geral ##
Um grupo de ação é uma coleção de preferências de notificações definidos pelo proprietário de uma subscrição do Azure. Alertas de monitorização e estado de funcionamento do serviço do Azure utilizam grupos de ação para notificar os utilizadores que foi acionado um alerta. Vários alertas podem utilizar o mesmo grupo de ação ou a grupos de ação diferentes consoante os requisitos do utilizador. Só pode configurar grupos de ação até 2000 numa subscrição.

Configure uma ação para notificar uma pessoa por e-mail ou SMS, recebem uma confirmação que indica que qual foi adicionado ao grupo de ação.

Este artigo mostra-lhe como criar e gerir grupos de ação no portal do Azure.

Cada ação é constituída pelas seguintes propriedades:

* **Nome**: Um identificador exclusivo dentro do grupo de ação.  
* **Tipo de ação**: A ação executada. Os exemplos incluem enviar um e-mail de chamada, SMS, voz; ou acionar vários tipos de ações automatizadas. Ver tipos neste artigo. 
* **Detalhes**: Os detalhes de correspondentes que variam de acordo com *tipo de ação*. 

Para obter informações sobre como utilizar os modelos Azure Resource Manager para configurar grupos de ação, veja [modelos do Resource Manager de grupo de ação](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Criar um grupo de ação com o portal do Azure ##
1. Na [portal](https://portal.azure.com), selecione **Monitor**. O **Monitor** painel consolida todas as suas monitorização definições e dados numa vista.

    ![O serviço de "Monitor"](./media/action-groups/home-monitor.png)
1. Selecione **alertas** , em seguida, selecione **gerir grupos de ação**.

    ![Gerir o botão de grupos de ação](./media/action-groups/manage-action-groups.png)
1. Selecione **grupo de ação de adicionar**e preencha os campos.

    ![O comando "Adicionar grupo de ação"](./media/action-groups/add-action-group.png)
1. Introduza um nome na **nome do grupo de ação** caixa e introduza um nome na **nome abreviado** caixa. O nome abreviado é utilizado em vez de um nome de grupo de ação completo quando as notificações são enviadas através deste grupo.

      ![Caixa de diálogo do grupo de ação de adicionar"](./media/action-groups/action-group-define.png)

1. O **subscrição** caixa autofills com a sua subscrição atual. Esta subscrição é aquela na qual o grupo de ação é guardado.

1. Selecione o **grupo de recursos** no qual o grupo de ação é guardado.

1. Defina uma lista de ações. Fornece o seguinte para cada ação:

    a. **Nome**: Introduza um identificador exclusivo para esta ação.

    b. **Tipo de ação**: Selecione o E-Mail/SMS/Push/voz, aplicação lógica, Webhook, ITSM ou Runbook de automatização.

    c. **Detalhes**: Com base no tipo de ação, introduza um número de telefone, endereço de e-mail, webhook URI, aplicações do Azure, a ligação ITSM ou runbook de automatização. Para a ação de ITSM, adicionalmente especificar **Item de trabalho** e requer a sua ferramenta ITSM outros campos.

1. Selecione **OK** para criar o grupo de ação.

## <a name="manage-your-action-groups"></a>Gerir os seus grupos de ação ##
Depois de criar um grupo de ação, é visível na **grupos de ação** secção a **Monitor** painel. Selecione o grupo de ação que pretende gerir para:

* Adicionar, editar ou remover ações.
* Elimine o grupo de ação.

## <a name="action-specific-information"></a>Informações específicas da ação
> [!NOTE]
> Ver [limites de serviço de subscrição para monitorização](https://docs.microsoft.com/azure/azure-subscription-service-limits#monitor-limits) para limites numérico em cada um dos itens abaixo.  

**Aplicação do Azure Push** -pode ter um número limitado de ações de aplicação do Azure num grupo de ação. Neste momento, a ação de aplicações do Azure só suporta ServiceHealth alertas. Qualquer outra altura alerta será ignorada. Ver [configurar alertas sempre que uma notificação de estado de funcionamento do serviço é lançada](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

**E-mail** -serão enviadas mensagens de correio eletrónico dos seguintes endereços de e-mail. Certifique-se de que a filtragem de e-mail está configurado corretamente
   - azure-noreply@microsoft.com
   - azureemail-noreply@microsoft.com
   - alerts-noreply@mail.windowsazure.com

Pode ter um número limitado de ações de e-mail num grupo de ação. Consulte a [informações de limitação de taxas](./../../azure-monitor/platform/alerts-rate-limiting.md) artigo

**ITSM** -pode ter um número limitado de número limitado de ações do ITSM num grupo de ação. Ação de ITSM requer uma ligação de ITSM. Saiba como criar uma [ligação de ITSM](../../azure-monitor/platform/itsmc-overview.md).

**Aplicação lógica** -pode ter um número limitado de ações de aplicação lógica num grupo de ação.

**Aplicação de função** -a função de chaves para aplicações de funções configuradas como ações são lidas por meio da API de funções, que atualmente requer que as aplicações de função da v2 para configurar a aplicação definir "AzureWebJobsSecretStorageType" para "ficheiros". Para obter mais informações, consulte [é alterado para gestão de chaves no V2 funções]( https://aka.ms/funcsecrets).

**Runbook** -pode ter um número limitado de ações de Runbook num grupo de ação. Consulte a [limites de serviço de subscrição do Azure](../../azure-subscription-service-limits.md) para limites em cargas de Runbook.

**SMS** -pode ter um número limitado de ações de SMS num grupo de ação. Consulte também os [informações de limitação de taxas](./../../azure-monitor/platform/alerts-rate-limiting.md) e [comportamento de alertas do SMS](../../azure-monitor/platform/alerts-sms-behavior.md) para informação adicional importante. 

**Voz** -pode ter um número limitado de ações de voz num grupo de ação. Consulte a [informações de limitação de taxas](./../../azure-monitor/platform/alerts-rate-limiting.md) artigo.

**Webhook** -pode ter um número limitado de ações de Webhook num grupo de ação. Os Webhooks são repetidos utilizando as seguintes regras. A chamada de webhook é repetida um máximo de 2 horas quando os seguintes códigos de estado HTTP são devolvidos: 408, 429, 503, 504 ou o ponto final HTTP não responde. A primeira repetição ocorre ao fim de 10 segundos. A segunda repetição acontece após 100 segundos. Após duas falhas, nenhum grupo de ação irá chamar o ponto de extremidade durante 30 minutos. 

Intervalos de endereços IP de origem
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 52.244.68.117

Para receber atualizações sobre as alterações a estes endereços IP, recomendamos que configure um [Service Health alerta, que monitora informativas notificações sobre o serviço de grupos de ação.


## <a name="next-steps"></a>Passos Seguintes ##

* Saiba mais sobre [comportamento de alertas do SMS](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Obter um [compreensão sobre o esquema de webhook de alerta de registo de atividades](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Saiba mais sobre [conector ITSM](../../azure-monitor/platform/itsmc-overview.md)
* Saiba mais sobre [limitação de velocidade](../../azure-monitor/platform/alerts-rate-limiting.md) em alertas.
* Obter um [descrição geral dos alertas de registo de atividade](../../azure-monitor/platform/alerts-overview.md)e saiba como receber alertas.  
* Saiba como [configurar alertas sempre que uma notificação de estado de funcionamento do serviço é lançada](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

