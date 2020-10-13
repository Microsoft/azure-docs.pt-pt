---
title: Alertas métricos de configuração - Portal Azure - Base de Dados Azure para MariaDB
description: Este artigo descreve como configurar e aceder a alertas métricos para a Base de Dados Azure para MariaDB a partir do portal Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 6ed890fca9dddcbc7f04914a6f920d530d18ed0f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91742847"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mariadb"></a>Utilize o portal Azure para configurar alertas sobre métricas para a Base de Dados Azure para MariaDB

Este artigo mostra-lhe como configurar a Base de Dados Azure para alertas MariaDB utilizando o portal Azure. Pode receber um alerta com base nas métricas de monitorização dos seus serviços Azure.

O alerta dispara quando o valor de uma métrica especificada cruza um limiar que atribui. O alerta dispara tanto quando a condição é recebida pela primeira vez, e depois quando essa condição já não está a ser satisfeita.

Pode configurar um alerta para fazer as seguintes ações quando ativa:
* Envie notificações por e-mail ao administrador de serviço e coadministradores
* Envie e-mail para e-mails adicionais que especifique.
* Chamar um webhook

Pode configurar e obter informações sobre as regras de alerta utilizando:
* [Portal do Azure](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [CLI do Azure](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [API REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric"></a>Criar uma regra de alerta numa métrica
1. No [portal Azure,](https://portal.azure.com/)selecione a Base de Dados Azure para o servidor MariaDB que pretende monitorizar.

2. Na secção **de monitorização** da barra lateral, selecione **Alertas** como mostrado:

   ![Selecione Regras de Alerta](./media/howto-alert-metric/2-alert-rules.png)

3. **Selecione Adicionar alerta métrico** (+ ícone).

4. A página **de regras Create** abre conforme mostrado abaixo. Preencha as informações necessárias:

   ![Adicione formulário de alerta métrico](./media/howto-alert-metric/4-add-rule-form.png)

5. Dentro da secção **Condição,** **selecione Adicionar a condição**.

6. Selecione uma métrica da lista de sinais a serem alertados. Neste exemplo, selecione "Storage percent".
   
   ![Selecionar métrica](./media/howto-alert-metric/6-configure-signal-logic.png)

7. Configure a lógica de alerta, incluindo a **Condição** (ex. "Maior do que"), **Threshold** (ex. 85 por cento), **Agregação do tempo,** **Período** de tempo a regra métrica deve ser satisfeita antes do gatilho de alerta (ex. "Nos últimos 30 minutos"), e **Frequência.**
   
   Selecione **Fazer** quando concluído.

   ![Selecione métrica 2](./media/howto-alert-metric/7-set-threshold-time.png)

8. Na secção **Grupos de Ação,** selecione **Criar Novo** para criar um novo grupo para receber notificações no alerta.

9. Preencha o formulário "Adicionar grupo de ação" com um nome, nome curto, subscrição e grupo de recursos.

10. Configure um tipo de ação **por E-mail/SMS/Push/Voice.**
    
    Escolha "Email Azure Resource Manager Role" para selecionar proprietários, contribuintes e leitores de subscrição para receber notificações.
   
    Opcionalmente, forneça um URI válido no campo **Webhook** se quiser que seja chamado quando o alerta disparar.

    Selecione **OK** quando concluído.

    ![Grupo de ações](./media/howto-alert-metric/10-action-group-type.png)

11. Especifique um nome de regra de alerta, descrição e severidade.

    ![Grupo de ação 2](./media/howto-alert-metric/11-name-description-severity.png) 

12. Selecione **Criar a regra de alerta** para criar o alerta.

    Em poucos minutos, o alerta está ativo e dispara como descrito anteriormente.

## <a name="manage-your-alerts"></a>Gerir os alertas
Uma vez criado um alerta, pode selecioná-lo e fazer as seguintes ações:

* Veja um gráfico que mostra o limiar métrico e os valores reais do dia anterior relevantes para este alerta.
* **Editar** ou **eliminar** a regra de alerta.
* **Desative** ou **Ative** o alerta, se pretender parar temporariamente ou retomar as notificações.


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [configurar webhooks em alertas](../monitoring-and-diagnostics/insights-webhooks-alerts.md).
* Obtenha uma [visão geral da recolha de métricas](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para se certificar de que o seu serviço está disponível e responsivo.
