---
title: Alertas de registo de atividade no Azure Monitor
description: Seja notificado via SMS, webhook, SMS, e-mail e muito mais, quando determinados eventos ocorrem no registo de atividade.
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: a8ab9520527a0d5ebc1de698f84e9f64e28b869a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101718119"
---
# <a name="alerts-on-activity-log"></a>Alertas no registo de atividades

## <a name="overview"></a>Descrição Geral

Os alertas de registo de atividade são alertas que se ativam quando ocorre um novo [evento de registo de atividade](../essentials/activity-log-schema.md) que corresponde às condições especificadas no alerta. Com base na ordem e volume dos eventos registados no [registo de atividades do Azure,](../essentials/platform-logs-overview.md)a regra de alerta disparará. As regras de alerta de registo de atividade são recursos Azure, para que possam ser criadas usando um modelo de Gestor de Recursos Azure. Também podem ser criados, atualizados ou eliminados no portal Azure. Este artigo introduz os conceitos por trás dos alertas de registo de atividade. Para obter mais informações sobre a criação ou utilização de regras de alerta de registo de atividades, consulte [Criar e gerir alertas de registo de atividades](alerts-activity-log.md).

> [!NOTE]
> * Não **podem** ser criados alertas para eventos na categoria de alerta de registo de atividade.
> * Os Alertas de Registo de Atividade com a categoria de Segurança podem ser definidos também num [novo fluxo atualizado](../../security-center/continuous-export.md?tabs=azure-portal) para o [ServiceNow](../../security-center/export-to-siem.md)

Normalmente, cria alertas de registo de atividade para receber notificações quando:

* Operações específicas ocorrem em recursos na sua subscrição Azure, muitas vezes telescópios para determinados grupos de recursos ou recursos. Por exemplo, é melhor ser notificado quando qualquer máquina virtual no myProductionResourceGroup for eliminada. Ou, pode querer ser notificado se alguma nova função for atribuída a um utilizador na sua subscrição.
* Ocorre um evento de saúde de serviço. Os eventos de saúde do serviço incluem notificação de incidentes e eventos de manutenção que se aplicam aos recursos na sua subscrição.

Uma simples analogia para compreender as condições em que as regras de alerta podem ser criadas no registo de atividades, é explorar ou filtrar eventos através do [log activity in Azure portal](../essentials/activity-log.md#view-the-activity-log). No Azure Monitor - Registo de atividade, pode-se filtrar ou encontrar o evento necessário e, em seguida, criar um alerta utilizando o botão **de alerta de registo de atividade Add.**

Em qualquer dos casos, um alerta de registo de atividade apenas monitoriza para eventos na subscrição em que o alerta é criado.

Pode configurar um alerta de registo de atividade com base em qualquer propriedade de nível superior no objeto JSON para um evento de registo de atividade. Para obter mais informações, consulte [categorias no Registo de Atividades.](../essentials/activity-log.md#view-the-activity-log) Para saber mais sobre os eventos de saúde do serviço, consulte [receber alertas de registo de atividades nas notificações do serviço.](../../service-health/alerts-activity-log-service-notifications-portal.md) 

Os alertas de registo de atividade têm algumas opções comuns:

- **Categoria**: Administrativo, Saúde de Serviço, Autoescala, Segurança, Política e Recomendação. 
- **Âmbito**: O recurso ou conjunto de recursos individuais para os quais o alerta no registo de atividade é definido. O âmbito para um alerta de registo de atividade pode ser definido a vários níveis:
    - Nível de recursos: Por exemplo, para uma máquina virtual específica
    - Nível do Grupo de Recursos: Por exemplo, todas as máquinas virtuais num grupo de recursos específico
    - Nível de Subscrição: Por exemplo, todas as máquinas virtuais numa subscrição (ou) todos os recursos numa subscrição
- **Grupo de recursos**: Por predefinição, a regra de alerta é guardada no mesmo grupo de recursos que a do alvo definido no Âmbito. O utilizador também pode definir o Grupo de Recursos onde a regra de alerta deve ser armazenada.
- **Tipo de recurso**: Gestor de recursos definiu o espaço de nome para o alvo do alerta.
- **Nome do funcionamento**: O nome de [operação do fornecedor de recursos Azure](../../role-based-access-control/resource-provider-operations.md) utilizado para o controlo de acesso baseado em funções Azure . As operações não registadas no Azure Resource Manager não podem ser utilizadas numa regra de alerta de registo de atividade.
- **Nível**: O nível de gravidade do evento (Informação, Aviso, Erro ou Crítico).
- **Estado**: O estado do evento, tipicamente iniciado, falhado ou bem sucedido.
- **Evento iniciado por:** Também conhecido como "chamador". O endereço de e-mail ou identificador do Diretório Ativo Azure do utilizador que realizou a operação.

> [!NOTE]
> Numa subscrição podem ser criadas até 100 regras de alerta para uma atividade de âmbito em ambos: um único recurso, todos os recursos em grupo de recursos (ou) nível de subscrição inteiro.

Quando um alerta de registo de atividade é ativado, utiliza um grupo de ação para gerar ações ou notificações. Um grupo de ação é um conjunto reutilizável de recetores de notificação, tais como endereços de e-mail, URLs webhook ou números de telefone SMS. Os recetores podem ser referenciados a partir de vários alertas para centralizar e agrupar os seus canais de notificação. Quando define o alerta de registo de atividade, tem duas opções. Pode:

* Utilize um grupo de ação existente no seu alerta de registo de atividade.
* Criar um novo grupo de ação.

Para saber mais sobre grupos de ação, consulte [Criar e gerir grupos de ação no portal Azure.](./action-groups.md)


## <a name="next-steps"></a>Passos seguintes

- Obtenha uma [visão geral dos alertas.](./alerts-overview.md)
- Saiba como [criar e modificar os alertas de registo de atividades.](alerts-activity-log.md)
- Reveja o [esquema de alerta de registo de atividade webhook](../alerts/activity-log-alerts-webhook.md).
- Saiba mais sobre [as notificações de saúde do serviço.](../../service-health/service-notifications.md)