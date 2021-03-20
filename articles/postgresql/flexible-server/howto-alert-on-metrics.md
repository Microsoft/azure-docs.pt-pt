---
title: Alertas de configuração - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor Flexível
description: Este artigo descreve como configurar e aceder a alertas métricos para Azure Database for PostgreSQL - Servidor Flexível a partir do portal Azure.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 3c6f1dd581f7b07bb7892cbfe23dd76348124f9a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579054"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---flexible-server"></a>Utilize o portal Azure para configurar alertas em métricas para a base de dados Azure para PostgreSQL - Servidor Flexível

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

Este artigo mostra-lhe como configurar a Base de Dados Azure para alertas PostgreSQL utilizando o portal Azure. Pode receber um alerta com base nas métricas de monitorização dos seus serviços Azure.

O alerta dispara quando o valor de uma métrica especificada cruza um limiar que atribui. O alerta dispara tanto quando a condição é recebida pela primeira vez, e depois quando essa condição já não está a ser satisfeita.

Pode configurar um alerta para fazer as seguintes ações quando ativa:

* Envie notificações por e-mail ao administrador de serviço e coadministradores.
* Envie e-mail para e-mails adicionais que especifique.
* Chame um webhook.

Pode configurar e obter informações sobre as regras de alerta utilizando:

* [Portal do Azure](../../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [CLI do Azure](../../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [API REST do Azure Monitor](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Criar uma regra de alerta numa métrica no portal do Azure

1. No [portal Azure,](https://portal.azure.com/)selecione a Base de Dados Azure para o servidor PostgreSQL que pretende monitorizar.

2. Na secção **de monitorização** da barra lateral, selecione **Alertas** como mostrado:

   :::image type="content" source="./media/howto-alert-on-metrics/2-alert-rules.png" alt-text="Selecione Regras de Alerta":::

3. **Selecione Adicionar alerta métrico** (+ ícone).

4. A página **de regras Create** abre conforme mostrado abaixo. Preencha as informações necessárias:

   :::image type="content" source="./media/howto-alert-on-metrics/4-add-rule-form.png" alt-text="Adicione formulário de alerta métrico":::

5. Dentro da secção **Condição,** **selecione Adicionar a condição**.

6. Selecione uma métrica da lista de sinais a serem alertados. Neste exemplo, selecione "Storage percent".

   :::image type="content" source="./media/howto-alert-on-metrics/6-configure-signal-logic.png" alt-text="Selecionar métrica":::

7. Configure a lógica de alerta, incluindo a **Condição** (ex. "Maior do que"), **Threshold** (ex. 85 por cento), **Agregação do tempo,** **Período** de tempo a regra métrica deve ser satisfeita antes do gatilho de alerta (ex. "Nos últimos 30 minutos"), e **Frequência.**

   Selecione **Fazer** quando concluído.

   :::image type="content" source="./media/howto-alert-on-metrics/7-set-threshold-time.png" alt-text="Definir limiar":::

8. Na secção **Grupos de Ação,** selecione **Criar Novo** para criar um novo grupo para receber notificações no alerta.

9. Preencha o formulário "Adicionar grupo de ação" com um nome, nome curto, subscrição e grupo de recursos.

10. Configure um tipo de ação **por E-mail/SMS/Push/Voice.**

    1. Escolha "Email Azure Resource Manager Role" para selecionar proprietários, contribuintes e leitores de subscrição para receber notificações.

    2. Opcionalmente, forneça um URI válido no campo **Webhook** se quiser que seja chamado quando o alerta disparar.

    3. Selecione **OK** quando concluído.

    :::image type="content" source="./media/howto-alert-on-metrics/10-action-group-type.png" alt-text="Grupo de ações":::

11. Especifique um nome de regra de alerta, descrição e severidade.

    :::image type="content" source="./media/howto-alert-on-metrics/11-name-description-severity.png" alt-text="Especificar, descrição do nome e gravidade"::: 

12. Selecione **Criar a regra de alerta** para criar o alerta.

    Em poucos minutos, o alerta está ativo e dispara como descrito anteriormente.

## <a name="manage-your-alerts"></a>Gerir os alertas

Uma vez criado um alerta, pode selecioná-lo e fazer as seguintes ações:

* Veja um gráfico que mostra o limiar métrico e os valores reais do dia anterior relevantes para este alerta.
* **Editar** ou **eliminar** a regra de alerta.
* **Desative** ou **Ative** o alerta, se pretender parar temporariamente ou retomar as notificações.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [configurar webhooks em alertas](../../azure-monitor/alerts/alerts-webhooks.md).
* Obtenha uma [visão geral da recolha de métricas](../../azure-monitor/data-platform.md) para se certificar de que o seu serviço está disponível e responsivo.