---
title: Alertas de registo de atividade no Monitor Azure
description: Seja notificado via SMS, webhook, SMS, e-mail e muito mais, quando determinados eventos ocorrem no registo de atividade.
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 1a932aba55ec9bd5d92c60338a3c1fc4bb481c1b
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82137710"
---
# <a name="alerts-on-activity-log"></a>Alertas sobre o registo de atividade

## <a name="overview"></a>Descrição geral

Os alertas de registo de atividade são alertas que ativam quando ocorre um novo evento de registo de [atividade](activity-log-schema.md) que corresponde às condições especificadas no alerta. Com base na ordem e volume dos eventos registados no registo de [atividade sinuoso do Azure,](platform-logs-overview.md)a regra de alerta disparará. As regras de alerta de registo de atividade são recursos Azure, para que possam ser criados usando um modelo de Gestor de Recursos Azure. Também podem ser criados, atualizados ou eliminados no portal Azure. Este artigo introduz os conceitos por trás de alertas de registo de atividade. Para obter mais informações sobre a criação ou utilização de regras de alerta de registo de atividade, consulte [Criar e gerir alertas](alerts-activity-log.md)de registo de atividade .

> [!NOTE]
> Não **podem** ser criados alertas para eventos na categoria alerta de registo de atividade.

Normalmente, cria alertas de registo de atividade para receber notificações quando:

* Operações específicas ocorrem em recursos na sua subscrição Azure, muitas vezes âmbitoados a determinados grupos de recursos ou recursos. Por exemplo, é melhor que seja notificado quando qualquer máquina virtual do myProductionResourceGroup for eliminada. Ou, pode querer ser notificado se forem atribuídas novas funções a um utilizador na sua subscrição.
* Ocorre um evento de saúde de serviço. Os eventos de saúde do serviço incluem notificação de incidentes e eventos de manutenção que se aplicam aos recursos na sua subscrição.

Uma simples analogia para compreender as condições em que as regras de alerta podem ser criadas no registo de atividade, é explorar ou filtrar eventos através do [log de atividade no portal Azure](activity-log-view.md#azure-portal). No registo do Monitor de Atividade sairá- pode filtrar ou encontrar o evento necessário e, em seguida, criar um alerta utilizando o botão de alerta de registo de **atividade adicionar.**

Em qualquer dos casos, um alerta de registo de atividade monitoriza apenas para eventos na subscrição em que o alerta é criado.

Pode configurar um alerta de registo de atividade com base em qualquer propriedade de alto nível no objeto JSON para um evento de registo de atividade. Para mais informações, consulte [categorias no Registo de Atividades](activity-log-view.md#categories-in-the-activity-log). Para saber mais sobre eventos de saúde de serviço, consulte Receber alertas de registo de [atividade sonantes em notificações](alerts-activity-log-service-notifications.md)de serviço . 

Os alertas de registo de atividade têm algumas opções comuns:

- **Categoria**: Administrativa, Saúde dos Serviços, Escala Automática, Segurança, Política e Recomendação. 
- **Âmbito**: O recurso ou conjunto de recursos individuais para os quais é definido o alerta sobre o registo de atividade. O âmbito para um alerta de registo de atividade pode ser definido a vários níveis:
    - Nível de Recursos: Por exemplo, para uma máquina virtual específica
    - Nível de Grupo de Recursos: Por exemplo, todas as máquinas virtuais de um grupo de recursos específicos
    - Nível de Subscrição: Por exemplo, todas as máquinas virtuais numa subscrição (ou) todos os recursos numa subscrição
- **Grupo de recursos**: Por padrão, a regra de alerta é guardada no mesmo grupo de recursos que o alvo definido no Âmbito. O utilizador também pode definir o Grupo de Recursos onde a regra de alerta deve ser armazenada.
- **Tipo de recurso**: Gestor de Recursos definiu espaço de nome para o alvo do alerta.
- **Nome da operação**: O nome de [funcionamento do Gestor de Recursos Azure](../../role-based-access-control/resource-provider-operations.md) utilizado para controlo de acesso baseado em funções . As operações não registadas no Azure Resource Manager não podem ser utilizadas numa regra de alerta de registo de atividade.
- **Nível**: O nível de gravidade do evento (Informacional, Aviso, Erro ou Crítico).
- **Estado**: O estado do evento, tipicamente iniciado, falhado ou bem sucedido.
- **Evento iniciado por**: Também conhecido como "chamador". O endereço de e-mail ou identificador de Diretório Ativo Azure do utilizador que realizou a operação.

> [!NOTE]
> Numa subscrição até 100 regras de alerta podem ser criadas para uma atividade de âmbito em qualquer um: um único recurso, todos os recursos em grupo de recursos (ou) todo o nível de subscrição.

Quando um alerta de registo de atividade é ativado, utiliza um grupo de ação para gerar ações ou notificações. Um grupo de ação é um conjunto reutilizável de recetores de notificação, tais como endereços de e-mail, URLs webhook ou números de telefone SMS. Os recetores podem ser referenciados a partir de múltiplos alertas para centralizar e agrupar os seus canais de notificação. Quando define o alerta de registo de atividade, tem duas opções. Pode:

* Utilize um grupo de ação existente no seu alerta de registo de atividade.
* Criar um novo grupo de ação.

Para saber mais sobre grupos de ação, consulte Criar e gerir grupos de [ação no portal Azure.](action-groups.md)


## <a name="next-steps"></a>Passos seguintes

- Obtenha uma [visão geral dos alertas.](alerts-overview.md)
- Saiba sobre [criar e modificar alertas](alerts-activity-log.md)de registo de atividade.
- Reveja o esquema de alerta de registo de [atividade](activity-log-alerts-webhook.md).
- Conheça as notificações de saúde de [serviço.](service-notifications.md)
