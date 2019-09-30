---
title: Alertas do log de atividades no Azure Monitor
description: Seja notificado via SMS, webhook, SMS, email e muito mais, quando determinados eventos ocorrerem no log de atividades.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: robb
ms.subservice: alerts
ms.openlocfilehash: b24f24edf2a3a0df8cb8ef9687f205a4a8868537
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71675222"
---
# <a name="alerts-on-activity-log"></a>Alertas no log de atividades 

## <a name="overview"></a>Descrição geral
Alertas do log de atividades são alertas que são ativados quando ocorre um novo [evento do log de atividades](activity-log-schema.md) que corresponde às condições especificadas no alerta. Com base na ordem e no volume dos eventos registrados no [log de atividades do Azure](activity-logs-overview.md), a regra de alerta será acionada. As regras de alerta do log de atividades são recursos do Azure, para que possam ser criadas usando um modelo de Azure Resource Manager. Eles também podem ser criados, atualizados ou excluídos no portal do Azure. Este artigo apresenta os conceitos por trás dos alertas do log de atividades. Para obter mais informações sobre a criação ou uso de regras de alerta do log de atividades, consulte [criar e gerenciar alertas do log de atividades](alerts-activity-log.md).

> [!NOTE]
> **Não é possível** criar alertas para eventos na categoria de alerta do log de atividades.

Normalmente, você cria alertas do log de atividades para receber notificações quando:

* Operações específicas ocorrem nos recursos em sua assinatura do Azure, com frequência no escopo de recursos ou grupos de recursos específicos. Por exemplo, talvez você queira ser notificado quando qualquer máquina virtual no myProductionResourceGroup for excluída. Ou talvez você queira ser notificado se alguma nova função for atribuída a um usuário em sua assinatura.
* Ocorre um evento de integridade do serviço. Os eventos de integridade do serviço incluem a notificação de incidentes e eventos de manutenção que se aplicam aos recursos em sua assinatura.

Uma analogia simples para entender as condições em que as regras de alerta podem ser criadas no log de atividades é explorar ou filtrar eventos por meio [do log de atividades em portal do Azure](activity-log-view.md#azure-portal). No log de atividades Azure Monitor, é possível filtrar ou localizar o evento necessário e, em seguida, criar um alerta usando o botão de **alerta Adicionar log de atividades** .

Em ambos os casos, um alerta do log de atividades monitora apenas os eventos na assinatura em que o alerta é criado.

Você pode configurar um alerta do log de atividades com base em qualquer propriedade de nível superior no objeto JSON para um evento do log de atividades. Para obter mais informações, consulte [visão geral do log de atividades do Azure](./activity-logs-overview.md#categories-in-the-activity-log). Para saber mais sobre eventos de integridade do serviço, consulte [receber alertas do log de atividades em notificações de serviço](./alerts-activity-log-service-notifications.md). 

Os alertas do log de atividades têm algumas opções comuns:

- **Categoria**: Administrativo, integridade do serviço, dimensionamento automático, segurança, política e recomendação. 
- **Escopo**: O recurso individual ou conjunto de recursos para os quais o alerta no log de atividades é definido. O escopo de um alerta do log de atividades pode ser definido em vários níveis:
    - Nível de recurso: Por exemplo, para uma máquina virtual específica
    - Nível do grupo de recursos: Por exemplo, todas as máquinas virtuais em um grupo de recursos específico
    - Nível de assinatura: Por exemplo, todas as máquinas virtuais em uma assinatura (ou) todos os recursos em uma assinatura
- **Grupo de recursos**: Por padrão, a regra de alerta é salva no mesmo grupo de recursos que o destino definido no escopo. O usuário também pode definir o grupo de recursos onde a regra de alerta deve ser armazenada.
- **Tipo de recurso**: Namespace definido pelo Gerenciador de recursos para o destino do alerta.
- **Nome da operação**: O nome da [operação de Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md) utilizado para o controle de acesso baseado em função. As operações não registradas com Azure Resource Manager não podem ser usadas em uma regra de alerta do log de atividades.
- **Nível**: O nível de severidade do evento (detalhado, informativo, aviso, erro ou crítico).
- **Status**: O status do evento, normalmente iniciado, com falha ou com êxito.
- **Evento iniciado por**: Também conhecido como "chamador". O endereço de email ou o identificador de Azure Active Directory do usuário que realizou a operação.

> [!NOTE]
> Em uma assinatura de até 100, as regras de alerta podem ser criadas para uma atividade de escopo em: um único recurso, todos os recursos no grupo de recursos (ou) todo o nível de assinatura.

Quando um alerta do log de atividades é ativado, ele usa um grupo de ações para gerar ações ou notificações. Um grupo de ações é um conjunto reutilizável de receptores de notificação, como endereços de email, URLs de webhook ou números de telefone de SMS. Os receptores podem ser referenciados de vários alertas para centralizar e agrupar seus canais de notificação. Ao definir o alerta do log de atividades, você tem duas opções. Pode:

* Use um grupo de ação existente no alerta do log de atividades.
* Crie um novo grupo de ação.

Para saber mais sobre grupos de ações, confira [criar e gerenciar grupos de ações no portal do Azure](action-groups.md).


## <a name="next-steps"></a>Passos seguintes

- Obtenha uma [visão geral dos alertas](alerts-overview.md).
- Saiba mais sobre como [criar e modificar alertas do log de atividades](alerts-activity-log.md).
- Examine o [esquema](activity-log-alerts-webhook.md)de webhook de alerta do log de atividades.
- Saiba mais sobre as [notificações de integridade do serviço](service-notifications.md).