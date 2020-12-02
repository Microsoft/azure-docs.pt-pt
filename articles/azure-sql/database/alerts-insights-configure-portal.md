---
title: Alertas de configuração e notificações no portal Azure
description: Utilize o portal Azure para criar alertas, que podem desencadear notificações ou automatização quando as condições especificarem estão reunidas.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: aamalvea
ms.author: aamalvea
ms.reviewer: wiassaf, sstein
ms.date: 05/04/2020
ms.openlocfilehash: 86ac0f64a6a797b1bc702597ed30e6417f2efe14
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500976"
---
# <a name="create-alerts-for-azure-sql-database-and-azure-synapse-analytics-using-the-azure-portal"></a>Criar alertas para a Base de Dados Azure SQL e Azure Synapse Analytics utilizando o portal Azure
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


## <a name="overview"></a>Descrição geral

Este artigo mostra como configurar alertas para bases de dados em Azure SQL Database e Azure Synapse Analytics usando o portal Azure. Os alertas podem enviar-lhe um e-mail ou chamar um gancho web quando alguma métrica (por exemplo, tamanho da base de dados ou utilização do CPU) atinge o limiar.

> [!NOTE]
> Para obter instruções específicas para a Azure SQL, consulte [criar alertas para a exemplo gerida do Azure SQL](../managed-instance/alerts-create.md).

Pode receber um alerta baseado em métricas de monitorização ou eventos nos seus serviços Azure.

* **Valores métricos** - O alerta dispara quando o valor de uma métrica especificada cruza um limiar que atribui em qualquer direção. Ou seja, desencadeia tanto quando a condição é primeiramente satisfeita e depois quando essa condição já não está a ser satisfeita.
* **Eventos de registo de atividade** - Um alerta pode desencadear em *cada* evento, ou, apenas quando um certo número de eventos ocorrer.

Pode configurar um alerta para fazer o seguinte quando acionar:

* Envie notificações por e-mail ao administrador de serviço e coadministradores
* Envie e-mail para e-mails adicionais que especifique.
* Chamar um webhook

Pode configurar e obter informações sobre regras de alerta usando

* [O portal do Azure](../../azure-monitor/platform/alerts-classic-portal.md)
* [PowerShell](../../azure-monitor/platform/alerts-classic-portal.md)
* [Uma interface de linha de comando (CLI)](../../azure-monitor/platform/alerts-classic-portal.md)
* [API REST do Azure Monitor](/rest/api/monitor/alertrules)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Criar uma regra de alerta numa métrica com o portal Azure

1. No [portal,](https://portal.azure.com/)desateia o recurso que está interessado em monitorizar e selecione-o.
2. Selecione **Alertas** na secção de Monitorização. O texto e o ícone podem variar ligeiramente para diferentes recursos.  

   ![Monitorização](./media/alerts-insights-configure-portal/Alerts.png)
  
3. Selecione o botão **nova regra de alerta** para abrir a página regra **Criar.**
  ![Criar regra](./media/alerts-insights-configure-portal/create-rule.png)

4. Na secção **Condição,** clique em **Adicionar**.
  ![Definir condição](./media/alerts-insights-configure-portal/create-rule.png)
5. Na página lógica de **sinal de configuração,** selecione um sinal.
  ![Selecione sinal](./media/alerts-insights-configure-portal/select-signal.png)
6. Depois de selecionar um sinal, como **a percentagem de CPU,** aparece a página lógica de **sinal de configuração.**
  ![Configurar lógica de sinal](./media/alerts-insights-configure-portal/configure-signal-logic.png)
7. Nesta página, configurar esse tipo de limiar, operador, tipo de agregação, valor limiar, granularidade agregação e frequência de avaliação. Em seguida, clique em **Fazer**.
8. Na **regra Criar,** selecione um grupo de **Ação** existente ou crie um novo grupo. Um grupo de ação permite-lhe definir as medidas a tomar quando ocorre uma condição de alerta.
  ![Definir grupo de ação](./media/alerts-insights-configure-portal/action-group.png)

9. Defina um nome para a regra, forneça uma descrição opcional, escolha um nível de gravidade para a regra, escolha se deve ativar a regra após a criação de regras e, em seguida, clique em **Criar alerta de regra** para criar o alerta de regra métrica.

Dentro de 10 minutos, o alerta está ativo e dispara como descrito anteriormente.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [configurar webhooks em alertas](../../azure-monitor/platform/alerts-webhooks.md).