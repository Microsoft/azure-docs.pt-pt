---
title: Alertas de configuração - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como configurar e aceder a alertas métricos para Azure Database for PostgreSQL - Single Server a partir do portal Azure.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: f37dcc1d092de10c51c0492bbc8671047e4a90a3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020114"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---single-server"></a>Utilize o portal Azure para configurar alertas em métricas para a base de dados Azure para PostgreSQL - Servidor Único

Este artigo mostra-lhe como configurar a Base de Dados Azure para alertas PostgreSQL utilizando o portal Azure. Pode receber um alerta com base nas métricas de monitorização dos seus serviços Azure.

O alerta dispara quando o valor de uma métrica especificada cruza um limiar que atribui. O alerta dispara tanto quando a condição é recebida pela primeira vez, e depois quando essa condição já não está a ser satisfeita. 

Pode configurar um alerta para fazer as seguintes ações quando ativa:
* Envie notificações por e-mail ao administrador de serviço e coadministradores.
* Envie e-mail para e-mails adicionais que especifique.
* Chame um webhook.

Pode configurar e obter informações sobre as regras de alerta utilizando:
* [Portal do Azure](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [CLI do Azure](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [API REST do Azure Monitor](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Criar uma regra de alerta sobre uma métrica a partir do portal Azure
1. No [portal Azure,](https://portal.azure.com/)selecione a Base de Dados Azure para o servidor PostgreSQL que pretende monitorizar.

2. Na secção **de monitorização** da barra lateral, selecione **Alertas** como mostrado:

   :::image type="content" source="./media/howto-alert-on-metric/2-alert-rules.png" alt-text="Selecione Regras de Alerta":::

3. **Selecione Adicionar alerta métrico** (+ ícone).

4. A página **de regras Create** abre conforme mostrado abaixo. Preencha as informações necessárias:

   :::image type="content" source="./media/howto-alert-on-metric/4-add-rule-form.png" alt-text="Adicione formulário de alerta métrico":::

5. Dentro da secção **Condição,** **selecione Adicionar a condição**.

6. Selecione uma métrica da lista de sinais a serem alertados. Neste exemplo, selecione "Storage percent".
   
   :::image type="content" source="./media/howto-alert-on-metric/6-configure-signal-logic.png" alt-text="Selecionar métrica":::

7. Configure a lógica de alerta, incluindo a **Condição** (ex. "Maior do que"), **Threshold** (ex. 85 por cento), **Agregação do tempo,** **Período** de tempo a regra métrica deve ser satisfeita antes do gatilho de alerta (ex. "Nos últimos 30 minutos"), e **Frequência.**
   
   Selecione **Fazer** quando concluído.

   :::image type="content" source="./media/howto-alert-on-metric/7-set-threshold-time.png" alt-text="Screenshot que realça a secção lógica alerta e o botão 'Fazer'.":::

8. Na secção **Grupos de Ação,** selecione **Criar Novo** para criar um novo grupo para receber notificações no alerta.

9. Preencha o formulário "Adicionar grupo de ação" com um nome, nome curto, subscrição e grupo de recursos.

10. Configure um tipo de ação **por E-mail/SMS/Push/Voice.**
    
    Escolha "Email Azure Resource Manager Role" para selecionar proprietários, contribuintes e leitores de subscrição para receber notificações.
   
    Opcionalmente, forneça um URI válido no campo **Webhook** se quiser que seja chamado quando o alerta disparar.

    Selecione **OK** quando concluído.

    :::image type="content" source="./media/howto-alert-on-metric/10-action-group-type.png" alt-text="Screenshot que mostra como adicionar um novo grupo de ação.":::

11. Especifique um nome de regra de alerta, descrição e severidade.

    :::image type="content" source="./media/howto-alert-on-metric/11-name-description-severity.png" alt-text="Grupo de ações"::: 

12. Selecione **Criar a regra de alerta** para criar o alerta.

    Em poucos minutos, o alerta está ativo e dispara como descrito anteriormente.

## <a name="manage-your-alerts"></a>Gerir os alertas
Uma vez criado um alerta, pode selecioná-lo e fazer as seguintes ações:

* Veja um gráfico que mostra o limiar métrico e os valores reais do dia anterior relevantes para este alerta.
* **Editar** ou **eliminar** a regra de alerta.
* **Desative** ou **Ative** o alerta, se pretender parar temporariamente ou retomar as notificações.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [configurar webhooks em alertas](../azure-monitor/platform/alerts-webhooks.md).
* Obtenha uma [visão geral da recolha de métricas](../azure-monitor/platform/data-platform.md) para se certificar de que o seu serviço está disponível e responsivo.