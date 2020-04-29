---
title: Alertas e notificações de configuração (portal Azure)
description: Utilize o portal Azure para criar alertas de Base de Dados SQL, que podem desencadear notificações ou automatização quando as condições que especifica forem satisfeitas.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 67c47b35e84a93d7d9032ad55b425ae2bb6971fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79209500"
---
# <a name="create-alerts-for-azure-sql-database-and-azure-synapse-analytics-databases-using-azure-portal"></a>Criar alertas para bases de dados Azure SQL e Azure Synapse Analytics usando o portal Azure

## <a name="overview"></a>Descrição geral

Este artigo mostra como configurar alertas para bases de dados individuais, pooled e data warehouse na Base de Dados Azure SQL e Azure Synapse Analytics (ex-Azure SQL Data Warehouse) usando o portal Azure. Os alertas podem enviar-lhe um e-mail ou chamar um gancho web quando alguma métrica (por exemplo, tamanho de base de dados ou utilização de CPU) atinge o limiar. Este artigo também fornece as melhores práticas para definir períodos de alerta.

> [!IMPORTANT]
> Esta funcionalidade ainda não está disponível em Managed Instance. Como alternativa, pode utilizar o Agente SQL para enviar alertas de e-mail para algumas métricas baseadas em Pontos de Vista de [Gestão Dinâmica](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Pode receber um alerta com base em métricas de monitorização ou eventos nos seus serviços Azure.

* **Valores métricos** - O alerta dispara quando o valor de uma métrica especificada atravessa um limiar que atribui em qualquer direção. Isto é, dispara tanto quando a condição é cumprida pela primeira vez e depois quando essa condição já não está a ser satisfeita.
* **Eventos** de registo de atividades - Um alerta pode desencadear em *todos os* eventos, ou, apenas quando um certo número de eventos ocorre.

Pode configurar um alerta para fazer o seguinte quando aciona:

* Enviar notificações por e-mail ao administrador de serviço sondar e coadministradores
* Envie e-mail para e-mails adicionais que especifice.
* Chamar um webhook

Pode configurar e obter informações sobre as regras de alerta usando

* [Portal do Azure](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [interface de linha de comando (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Criar uma regra de alerta sobre uma métrica com o portal Azure

1. No [portal,](https://portal.azure.com/)localize o recurso que está interessado em monitorizar e selecioná-lo.
2. Selecione **Alertas** na secção monitorização. O texto e o ícone podem variar ligeiramente para diferentes recursos.  

   ![Monitorização](media/sql-database-insights-alerts-portal/Alerts.png)
  
3. Selecione o novo botão de regra de **alerta** para abrir a página de **regra Criar.**
  ![Criar regra](media/sql-database-insights-alerts-portal/create-rule.png)

4. Na secção **Condição,** clique em **Adicionar**.
  ![Definir condição](media/sql-database-insights-alerts-portal/create-rule.png)
5. Na página lógica do **sinal Configurar,** selecione um sinal.
  ![Selecione sinal](media/sql-database-insights-alerts-portal/select-signal.png).
6. Depois de selecionar um sinal, como a percentagem de **CPU,** aparece a página lógica do **sinal Configurar.**
  ![Configurar lógica de sinal](media/sql-database-insights-alerts-portal/configure-signal-logic.png)
7. Nesta página, configure esse tipo de limiar, operador, tipo de agregação, valor limiar, granularidade de agregação e frequência de avaliação. Em seguida, clique em **Done**.
8. Na **regra Criar,** selecione um grupo de **Ação** existente ou crie um novo grupo. Um grupo de ação permite definir a ação a ser tomada quando ocorre uma condição de alerta.
  ![Definir grupo de ação](media/sql-database-insights-alerts-portal/action-group.png)

9. Defina um nome para a regra, forneça uma descrição opcional, escolha um nível de gravidade para a regra, escolha se permite a regra sobre a criação de regras e, em seguida, clique em **Criar alerta** de regra para criar o alerta de regra métrica.

Dentro de 10 minutos, o alerta está ativo e dispara como descrito anteriormente.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [configurar webhooks em alertas](../azure-monitor/platform/alerts-webhooks.md).
