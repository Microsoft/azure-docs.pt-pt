---
title: Alertas e notificações de configuração (portal Azure)
description: Utilize o portal Azure para criar alertas, que podem desencadear notificações ou automatização quando as condições que especifica forem satisfeitas.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 05/04/2020
ms.openlocfilehash: 0e9b8f7bc4129c07145130f198b738839b0c7b55
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84047861"
---
# <a name="create-alerts-for-azure-sql-database-and-azure-synapse-using-azure-portal"></a>Criar alertas para a Base de Dados Azure SQL e para o Azure Synapse utilizando o portal Azure
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


## <a name="overview"></a>Descrição geral

Este artigo mostra como configurar alertas para bases de dados em Azure SQL Database e Azure Synapse (ex-Azure SQL Data Warehouse) utilizando o portal Azure. Os alertas podem enviar-lhe um e-mail ou chamar um gancho web quando alguma métrica (por exemplo, tamanho de base de dados ou utilização de CPU) atinge o limiar. Este artigo também fornece as melhores práticas para definir períodos de alerta.

> [!NOTE]
> Para instruções específicas de instância gerida azure SQL, consulte Criar alertas para a instância gerida pelo [Azure SQL](../managed-instance/alerts-create.md).

Pode receber um alerta com base em métricas de monitorização ou eventos nos seus serviços Azure.

* **Valores métricos** - O alerta dispara quando o valor de uma métrica especificada atravessa um limiar que atribui em qualquer direção. Isto é, dispara tanto quando a condição é cumprida pela primeira vez e depois quando essa condição já não está a ser satisfeita.
* **Eventos** de registo de atividades - Um alerta pode desencadear em *todos os* eventos, ou, apenas quando um certo número de eventos ocorre.

Pode configurar um alerta para fazer o seguinte quando aciona:

* Enviar notificações por e-mail ao administrador de serviço sondar e coadministradores
* Envie e-mail para e-mails adicionais que especifice.
* Chamar um webhook

Pode configurar e obter informações sobre as regras de alerta usando

* [Portal do Azure](../../azure-monitor/platform/alerts-classic-portal.md)
* [PowerShell](../../azure-monitor/platform/alerts-classic-portal.md)
* [interface de linha de comando (CLI)](../../azure-monitor/platform/alerts-classic-portal.md)
* [API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Criar uma regra de alerta sobre uma métrica com o portal Azure

1. No [portal,](https://portal.azure.com/)localize o recurso que está interessado em monitorizar e selecioná-lo.
2. Selecione **Alertas** na secção monitorização. O texto e o ícone podem variar ligeiramente para diferentes recursos.  

   ![Monitorização](./media/alerts-insights-configure-portal/Alerts.png)
  
3. Selecione o novo botão de regra de **alerta** para abrir a página de **regra Criar.**
  ![Criar regra](./media/alerts-insights-configure-portal/create-rule.png)

4. Na secção **Condição,** clique em **Adicionar**.
  ![Definir condição](./media/alerts-insights-configure-portal/create-rule.png)
5. Na página lógica do **sinal Configurar,** selecione um sinal.
  ![Selecione sinal ](./media/alerts-insights-configure-portal/select-signal.png) .
6. Depois de selecionar um sinal, como a percentagem de **CPU,** aparece a página lógica do **sinal Configurar.**
  ![Configurar lógica de sinal](./media/alerts-insights-configure-portal/configure-signal-logic.png)
7. Nesta página, configure esse tipo de limiar, operador, tipo de agregação, valor limiar, granularidade de agregação e frequência de avaliação. Em seguida, clique em **Done**.
8. Na **regra Criar,** selecione um grupo de **Ação** existente ou crie um novo grupo. Um grupo de ação permite definir a ação a ser tomada quando ocorre uma condição de alerta.
  ![Definir grupo de ação](./media/alerts-insights-configure-portal/action-group.png)

9. Defina um nome para a regra, forneça uma descrição opcional, escolha um nível de gravidade para a regra, escolha se permite a regra sobre a criação de regras e, em seguida, clique em **Criar alerta** de regra para criar o alerta de regra métrica.

Dentro de 10 minutos, o alerta está ativo e dispara como descrito anteriormente.

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [configurar webhooks em alertas](../../azure-monitor/platform/alerts-webhooks.md).
