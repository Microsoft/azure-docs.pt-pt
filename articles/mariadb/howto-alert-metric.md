---
title: Configure alertas métricos - Portal Azure - Base de Dados Azure para MariaDB
description: Este artigo descreve como configurar e aceder a alertas métricos para a Base de Dados Azure para MariaDB a partir do portal Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 683b89b29265159b0316d427a7ae616bf3766f7c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79529022"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mariadb"></a>Utilize o portal Azure para criar alertas sobre métricas para a Base de Dados Azure para MariaDB

Este artigo mostra-lhe como configurar a Base de Dados Azure para alertas MariaDB utilizando o portal Azure. Pode receber um alerta com base em métricas de monitorização dos seus serviços Azure.

O alerta dispara quando o valor de uma métrica especificada atravessa um limiar que atribui. O alerta dispara tanto quando a condição é satisfeita pela primeira vez, e depois quando essa condição já não está a ser satisfeita.

Pode configurar um alerta para fazer as seguintes ações quando dispara:
* Enviar notificações por e-mail ao administrador de serviço sondar e coadministradores
* Envie e-mail para e-mails adicionais que especifice.
* Chamar um webhook

Pode configurar e obter informações sobre as regras de alerta usando:
* [Portal do Azure](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [CLI do Azure](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [API REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric"></a>Criar uma regra de alerta numa métrica
1. No [portal Azure,](https://portal.azure.com/)selecione a Base de Dados Azure para o servidor MariaDB que pretende monitorizar.

2. Na secção **de monitorização** da barra lateral, selecione **Alertas** conforme mostrado:

   ![Selecione Regras de Alerta](./media/howto-alert-metric/2-alert-rules.png)

3. **Selecione Adicionar alerta métrico** (ícone+).

4. A página de **regras Create** abre como mostrado abaixo. Preencha as informações necessárias:

   ![Adicionar forma de alerta métrico](./media/howto-alert-metric/4-add-rule-form.png)

5. Dentro da secção **Condição,** **selecione Adicionar condição**.

6. Selecione uma métrica da lista de sinais a alertar. Neste exemplo, selecione "Storage percent".
   
   ![Selecionar métrica](./media/howto-alert-metric/6-configure-signal-logic.png)

7. Configure a lógica de alerta, incluindo a **Condição** (ex. "Maior do que"), **Threshold** (ex. 85 por cento), **Agregação**do Tempo , **Período** de tempo a regra métrica deve ser satisfeita antes do alerta disparar (ex. "Nos últimos 30 minutos"), e **Frequência.**
   
   Selecione **Feito** quando estiver completo.

   ![Selecionar métrica](./media/howto-alert-metric/7-set-threshold-time.png)

8. Dentro da secção **Grupos de Ação,** selecione **Create New** para criar um novo grupo para receber notificações no alerta.

9. Preencha o formulário "Adicionar grupo de ação" com um nome, nome curto, subscrição e grupo de recursos.

10. Configure um tipo de ação de **e-mail/SMS/Push/Voice.**
    
    Escolha "Email Azure Resource Manager Role" para selecionar proprietários de subscrição, colaboradores e leitores para receber notificações.
   
    Opcionalmente, forneça um URI válido no campo **Webhook** se quiser que seja chamado quando o alerta disparar.

    Selecione **OK** quando estiver concluído.

    ![Grupo de ações](./media/howto-alert-metric/10-action-group-type.png)

11. Especifique um nome de regra de alerta, descrição e gravidade.

    ![Grupo de ações](./media/howto-alert-metric/11-name-description-severity.png) 

12. Selecione **Criar a regra** de alerta para criar o alerta.

    Dentro de poucos minutos, o alerta está ativo e dispara como descrito anteriormente.

## <a name="manage-your-alerts"></a>Gerir os alertas
Uma vez criado um alerta, pode selecioná-lo e fazer as seguintes ações:

* Veja um gráfico que mostre o limiar métrico e os valores reais do dia anterior relevantes para este alerta.
* **Editar** ou **Eliminar** a regra de alerta.
* **Desative** ou **Ative** o alerta, caso pretenda parar ou retomar temporariamente a receção de notificações.


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [configurar webhooks em alertas](../monitoring-and-diagnostics/insights-webhooks-alerts.md).
* Obtenha uma [visão geral da recolha de métricas](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para garantir que o seu serviço está disponível e reativo.
