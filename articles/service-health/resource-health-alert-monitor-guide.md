---
title: Criar alertas de saúde de recursos usando o Portal Azure
description: Crie alerta utilizando o portal Azure que o notifica quando os seus recursos Azure ficarem indisponíveis.
ms.topic: conceptual
ms.date: 6/23/2020
ms.openlocfilehash: e48c400e5be3516b08496db7a4cb6a19e45d6c97
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594627"
---
# <a name="configure-resource-health-alerts-using-azure-portal"></a>Configurar alertas de estado de funcionamento dos recursos com o portal Azure

Este artigo mostra-lhe como configurar alertas de registo de atividade para notificações de saúde de recursos através do portal Azure.

A Azure Resource Health mantém-no informado sobre o estado de saúde atual e histórico dos seus recursos Azure. Os alertas do Azure Resource Health podem notificá-lo quase em tempo real quando estes recursos sofrem uma alteração no estado de funcionamento. Criar alertas de Saúde de Recursos permite programaticamente que os utilizadores criem e personalizem alertas a granel.

As notificações de saúde dos recursos são armazenadas no registo de [atividades do Azure](../azure-monitor/essentials/platform-logs-overview.md) Dado o eventual grande volume de informação armazenada no registo de atividades, existe uma interface de utilizador separada para facilitar a visualização e configuração de alertas nas notificações de saúde dos recursos.
Pode receber um alerta quando o recurso Azure enviar notificações de saúde de recursos para a sua subscrição do Azure. Pode configurar o alerta com base em:

* A assinatura foi afetada.
* O(s) tipo de recursos(s) afetados.
* O(s) grupo de recursos(s) afetado.
* Os recursos(s) afetados.
* O estado do(s) do ou dos recursos afetados.
* Os recursos(s) afetados.
* O(s) tipo(s) dos recursos afetados.

Também pode configurar a quem o alerta deve ser enviado:

* Selecione um grupo de ação existente.
* Crie um novo grupo de ação (que pode ser usado para futuros alertas).

Para saber mais sobre grupos de ação, veja [Criar e gerir grupos de ações](../azure-monitor/alerts/action-groups.md).

Para obter informações sobre como configurar alertas de notificação de saúde de recursos utilizando modelos do Gestor de Recursos Azure, consulte [os modelos do Gestor de Recursos](./resource-health-alert-arm-template-guide.md).
Alerta de saúde de recursos usando portal Azure

## <a name="resource-health-alert-using-azure-portal"></a>Alerta de saúde de recursos usando o portal Azure

1. No [portal](https://portal.azure.com/)Azure , selecione **Service Health**.

    ![Seleção de Saúde de Serviço](./media/resource-health-alert-monitor-guide/service-health-selection.png)
2. Na secção Saúde dos **Recursos,** selecione **Service Health**.
3. **Selecione Adicionar alerta de saúde de recursos** e preencha os campos.
4. Em Alerta, selecione a **Subscrição**, **Tipos de Recursos,** **Grupos de Recursos** e **Recursos** para os quais pretende ser alertado.

    ![Seleção de destino](./media/resource-health-alert-monitor-guide/alert-target.png)

5. Em estado de alerta selecione:
    1. O **Estado do Evento** para o que pretende ser alertado. O nível de gravidade do evento: Ativo, Resolvido, Em Curso, Atualizado
    2. O **Estado de Recursos** para o que pretende ser alertado. O estado de recursos do evento: Disponível, Indisponível, Desconhecido, Degradado
    3. O **tipo de razão** para o qual quer ser alertado. A causa do evento: Plataforma Iniciada, ![ Seleção de Saúde de Alerta Iniciado pelo Utilizador](./media/resource-health-alert-monitor-guide/alert-condition.png)
6. Em definição de detalhes de alerta, forneça os seguintes detalhes:
    1. **Nome da regra de alerta**: O nome da nova regra de alerta.
    2. **Descrição**: A descrição da nova regra de alerta.
    3. **Guardar alerta para o grupo de recursos**: Selecione o grupo de recursos onde pretende guardar esta nova regra.
7. No **grupo Ação**, a partir do menu suspenso, especifique o grupo de ação que pretende atribuir a esta nova regra de alerta. Ou [criar um novo grupo de ação](../azure-monitor/alerts/action-groups.md) e atribuí-lo à nova regra. Para criar um novo grupo, selecione + **Novo grupo.**
8. Para ativar as regras após a sua criação, selecione **Sim** para a regra Enable após a opção **de criação.**
9. Selecione **Criar regra de alerta**.

A nova regra de alerta para o registo de atividade é criada e uma mensagem de confirmação aparece no canto superior direito da janela.
Pode ativar, desativar, editar ou eliminar uma regra. Saiba mais sobre [como gerir as regras de registo de atividades.](../azure-monitor/alerts/alerts-activity-log.md#view-and-manage-in-the-azure-portal)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a Saúde dos Recursos:

* [Visão geral da Saúde dos Recursos Azure](Resource-health-overview.md)
* [Os tipos de recursos e verificações do estado de funcionamento disponíveis através do Azure Resource Health](resource-health-checks-resource-types.md)

Criar alertas de saúde de serviço:

* [Alertas de configuração para a saúde do serviço](./alerts-activity-log-service-notifications-portal.md) 
* [Esquema de evento de registo de atividade azure](../azure-monitor/essentials/activity-log-schema.md)
* [Configurar alertas de estado de funcionamento dos recursos com os modelos do Resource Manager](./resource-health-alert-arm-template-guide.md)
