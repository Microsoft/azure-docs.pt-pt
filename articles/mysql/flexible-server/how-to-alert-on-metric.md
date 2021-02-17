---
title: Alertas métricos de configuração - Portal Azure - Base de Dados Azure para MySQL - Servidor Flexível
description: Este artigo descreve como configurar e aceder a alertas métricos para Azure Database for MySQL Flexible Server a partir do portal Azure.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 632aae766e6fd1328dc6e0135a88a942d7ad0910
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595787"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql---flexible-server"></a>Utilize o portal Azure para configurar alertas em métricas para a base de dados Azure para MySQL - Servidor Flexível 

> [!IMPORTANT] 
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

Este artigo mostra-lhe como configurar a Base de Dados Azure para alertas MySQL utilizando o portal Azure. Pode receber um alerta com base nas métricas de monitorização dos seus serviços Azure.

O alerta dispara quando o valor de uma métrica especificada cruza um limiar que atribui. O alerta dispara tanto quando a condição é recebida pela primeira vez, e depois quando essa condição já não está a ser satisfeita. Os alertas métricos são imponentes, ou seja, só enviam notificações quando o Estado muda.

Pode configurar um alerta para fazer as seguintes ações quando ativa:
* Envie notificações por e-mail ao administrador de serviço e coadministradores
* Envie e-mail para e-mails adicionais que especifique.
* Chamar um webhook

Pode configurar e obter informações sobre as regras de alerta utilizando:
* [Portal do Azure](../../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [CLI do Azure](../../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [API REST do Azure Monitor](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Criar uma regra de alerta numa métrica no portal do Azure
1. No [portal Azure,](https://portal.azure.com/)selecione a Base de Dados Azure para o servidor flexível MySQL que pretende monitorizar.
2. Na secção **de monitorização** da barra lateral, selecione **Alertas**.
3. Selecione **+ Nova regra de alerta**.
4. A página **Criar regra** é apresentada. Preencha as informações necessárias:
5. Dentro da secção **Condição,** escolha **a condição de seleção.**
6. Verá uma lista de sinais suportados, selecione a métrica em que pretende criar um alerta. Por exemplo, selecione "Storage percent".
7. Verá um gráfico para a métrica nas últimas seis horas. Utilize o período de entrega do **gráfico** para selecionar para ver um histórico mais longo para a métrica.
8. Selecione o tipo **Threshold** (ex. "Estático" ou "Dinâmico"), **Operador** (ex. "Maior do que"), e **tipo de agregação** (ex. média). Isto determinará a lógica que a regra de alerta métrico irá avaliar.
    - Se estiver a utilizar um limiar **estático,** continue a definir um **valor limiar** (ex. 85 por cento). O gráfico métrico pode ajudar a determinar o que pode ser um limiar razoável.
    - Se estiver a utilizar um limiar **Dinâmico,** continue a definir a **sensibilidade Threshold**. O gráfico métrico apresentará os limiares calculados com base em dados recentes. [Saiba mais sobre o tipo de condições e opções de sensibilidade dos Limiares Dinâmicos](../../azure-monitor/alerts/alerts-dynamic-thresholds.md).
9. Refinar a condição ajustando o intervalo **de granularidade de agregação (Período)** sobre o qual os pontos de dados são agrupados utilizando a função do tipo de agregação (ex. "30 minutos"), e **Frequência** (ex "A cada 15 minutos").
10. Clique em **Concluído**.
11. Adicione um grupo de ação. Um grupo de ação é uma coleção de preferências de notificação definidas pelo proprietário de uma subscrição Azure. Dentro da secção **Grupos de Ação,** escolha **Selecionar grupo de ação** para selecionar um grupo de ação já existente para anexar à regra de alerta.
12. Também pode criar um novo grupo de ação para receber notificações no alerta. Consulte para [criar e gerir o grupo de ação](../../azure-monitor/alerts/action-groups.md) para obter mais informações.
13. Para criar um novo grupo de ação, escolha **+ Criar grupo de ação.** Preencha o formulário "Criar grupo de ação" com uma **Subscrição,** **grupo de recursos,** **nome de grupo de ação** e nome de **exibição.**
14. Configure **notificações** para grupo de ação.
    
    No **tipo de Notificação,** escolha "Email Azure Resource Manager Role" para selecionar proprietários, contribuintes e leitores de subscrição para receber notificações. Escolha a **Função de Gestor de Recursos Azure** para o envio do e-mail.
    Também pode escolher **mensagem de E-mail/SMS/Push/Voice** para enviar notificações a destinatários específicos.

    Fornecer **Nome** ao tipo de notificação e selecionar **Revisão + Criar** quando concluído.

    <!--:::image type="content" source="./media/howto-alert-on-metric/10-action-group-type.png" alt-text="Action group":::-->
    
15. Preencha **os detalhes da regra de alerta** como o nome da regra de **alerta**, **Descrição**, **Guarde a regra de alerta para o grupo de recursos** e a **Severidade**.

    <!--:::image type="content" source="./media/howto-alert-on-metric/11-name-description-severity.png" alt-text="Action group":::-->

16. Selecione **Criar a regra de alerta** para criar o alerta.
    Em poucos minutos, o alerta está ativo e dispara como descrito anteriormente.
## <a name="manage-your-alerts"></a>Gerir os alertas
Uma vez criado um alerta, pode selecioná-lo e fazer as seguintes ações:

* Veja um gráfico que mostra o limiar métrico e os valores reais do dia anterior relevantes para este alerta.
* **Editar** ou **eliminar** a regra de alerta.
* **Desative** ou **Ative** o alerta, se pretender parar temporariamente ou retomar as notificações.


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [a definição de alerta em métricas](../../azure-monitor/alerts/alerts-metric.md).
- Saiba mais sobre as métricas disponíveis [na Base de Dados Azure para o MySQL Flexible Server](./concepts-monitoring.md).
- [Compreender a forma como os alertas de métrica funcionam no Azure Monitor](../../azure-monitor/alerts/alerts-metric-overview.md)