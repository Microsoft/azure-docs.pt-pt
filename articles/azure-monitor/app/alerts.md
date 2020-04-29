---
title: Definir alertas em insights de aplicação azure
description: Seja notificado sobre tempos de resposta lentos, exceções e outras alterações de desempenho ou utilização na sua aplicação web.
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 28fd59556a586b85a6d3caf188d9e02c11d31e3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295087"
---
# <a name="set-alerts-in-application-insights"></a>Definir alertas em insights de aplicação

O [Azure Application Insights][start] pode alertá-lo para alterações nas métricas de desempenho ou de utilização na sua aplicação web. 

Application Insights monitoriza a sua aplicação ao vivo em uma [grande variedade de plataformas][platforms] para ajudá-lo a diagnosticar problemas de desempenho e entender padrões de uso.

Existem vários tipos de alertas:

* [**Os alertas métricos**](../../azure-monitor/platform/alerts-metric-overview.md) dizem-lhe quando uma métrica cruza um valor limiar durante algum período - como tempos de resposta, contagem de exceções, utilização de CPU ou visualizações de página.
* [**Os Alertas**](../../azure-monitor/platform/alerts-unified-log.md) de Registo são usados para descrever alertas onde o sinal de alerta é baseado numa consulta personalizada de Kusto.
* [**Os testes na Web**][availability] dizem-lhe quando o seu site está indisponível na internet ou responde lentamente. [Saiba mais][availability].
* [**Os diagnósticos proactivos**](../../azure-monitor/app/proactive-diagnostics.md) são configurados automaticamente para notificá-lo sobre padrões de desempenho incomuns.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Como definir um alerta de exceção usando a pesquisa de registo personalizado

Nesta secção, vamos passar por como definir um alerta de exceção baseado em consulta. Para este exemplo, digamos que queremos um alerta quando a taxa falhada for superior a 10% nas últimas 24 horas.

1. Vá ao seu recurso Application Insight no portal Azure.
2. À esquerda, sob configuração clique em **Alerta**.

    ![À esquerda sob alerta de clique configurado](./media/alerts/1appinsightalert.png)

3. Na parte superior do separador de alerta selecione **Nova regra de alerta**.

     ![No topo do separador de alerta clique em nova regra de alerta](./media/alerts/2createalert.png)

4. O seu recurso deve ser selecionado automaticamente. Para definir uma condição, clique em **Adicionar condição**.

    ![Clique em adicionar condição](./media/alerts/3addcondition.png)

5. No separador lógica de sinal configurar selecione **pesquisa de registo personalizada**

    ![Clique na pesquisa de registo personalizado](./media/alerts/4customlogsearch.png)

6. No separador de pesquisa de registo personalizado, introduza a sua consulta na caixa "Pesquisa". Para este exemplo, usaremos a consulta abaixo de Kusto.
    ```kusto
    let percentthreshold = 10;
    let period = 24h;
    requests
    | where timestamp >ago(period)
    | summarize requestsCount = sum(itemCount)
    | project requestsCount, exceptionsCount = toscalar(exceptions | where timestamp >ago(period) | summarize sum(itemCount))
    | extend exceptionsRate = toreal(exceptionsCount)/toreal(requestsCount) * 100
    | where exceptionsRate > percentthreshold

    ```

    ![Digite consulta na caixa de consulta de pesquisa](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > Também pode aplicar estes passos a outros tipos de alertas baseados em consultas. Você pode aprender mais sobre a linguagem de consulta Kusto a partir deste [Kusto começando doc](https://docs.microsoft.com/azure/kusto/concepts/) ou este [SQL para Kusto folha de batota](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. Em "Lógica de alerta", escolha se é baseado no número de resultados ou na medição métrica. Em seguida, escolha a condição (maior do que, igual a, menos do que) e um limiar. Enquanto está a alterar estes valores, poderá notar alterações na frase de pré-visualização da condição. Neste exemplo estamos a usar "igual a".

    ![Sob a lógica de alerta escolha entre as opções previstas com base e condição, em seguida, digitar um limiar](./media/alerts/6alertlogic.png)

8. Em "Avaliado com base", dedefinido o período e a frequência. O período aqui deve corresponder ao valor que colocamos durante o período na consulta acima. Em seguida, clique **em feito**.

    ![Definir período e frequência na parte inferior e, em seguida, clicar feito](./media/alerts/7evaluate.png)

9. Vemos agora a condição que criamos com o custo mensal estimado. Abaixo em ["Action Groups"](../platform/action-groups.md) pode criar um novo grupo ou selecionar um existente. Se quiser, pode personalizar as ações.

    ![clique no select ou criar botões sob grupo de ação](./media/alerts/8actiongroup.png)

10. Adicione finalmente os seus dados de alerta (nome da regra do alerta, descrição, gravidade). Quando terminar, clique em Criar a regra de **alerta** na parte inferior.

    ![Em detalhes de alerta escreva o nome da regra do alerta, escreva uma descrição e escolha uma gravidade](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Como cancelar a subscrição de notificações clássicas de e-mail de alerta

Esta secção aplica-se a **alertas clássicos**de disponibilidade, **alertas métricos clássicos de Aplicação Insights,** e a **alertas de anomalias de falha clássica.**

Está a receber notificações por e-mail para estes alertas clássicos se algum dos seguintes se aplicar:

* O seu endereço de e-mail está listado no campo de destinatários de e-mail de notificação nas definições da regra de alerta.

* A opção de enviar notificações de e-mail para utilizadores que detenham determinadas funções para a subscrição é ativada, e você detém uma função respetiva para essa subscrição específica do Azure.

![Screenshot de notificação de alerta](./media/alerts/alert-notification.png)

Para melhor controlar a sua segurança e privacidade recomendamos que especifique explicitamente os destinatários da notificação para os seus alertas clássicos no campo de destinatários de **email notificação.** A opção de notificar todos os utilizadores que detenham determinadas funções está prevista para a retrocompatibilidade.

Para cancelar a subscrição de notificações de e-mail geradas por uma determinada regra de alerta, remova o seu endereço de e-mail do campo de destinatários de **email da Notificação.**

Se o seu endereço de e-mail não estiver listado explicitamente, recomendamos que desative a opção de notificar automaticamente todos os membros de determinadas funções e, em vez disso, listar todos os e-mails dos utilizadores que necessitem de receber notificações para essa regra de alerta no campo de destinatários de e-mail de notificação.

## <a name="who-receives-the-classic-alert-notifications"></a>Quem recebe as notificações de alerta (clássicas)?

Esta secção aplica-se apenas a alertas clássicos e irá ajudá-lo a otimizar as notificações de alerta para garantir que apenas os destinatários pretendidos recebam notificações. Para saber mais sobre a diferença entre [os alertas clássicos](../platform/alerts-classic.overview.md) e a experiência dos novos alertas, consulte o artigo sobre visão geral dos [alertas.](../platform/alerts-overview.md) Para controlar a notificação de alerta na experiência dos novos alertas, utilize [grupos de ação.](../platform/action-groups.md)

* Recomendamos a utilização de destinatários específicos para notificações clássicas de alerta.

* Para alertas sobre quaisquer métricas de Insights de Aplicação (incluindo métricas de disponibilidade), a opção de caixa de verificação **a granel/grupo,** se ativada, envia para os utilizadores com funções de proprietário, contribuinte ou leitor na subscrição. Com efeito, _todos os_ utilizadores com acesso à subscrição o recurso Application Insights estão no âmbito e receberão notificações.

> [!NOTE]
> Se utilizar atualmente a opção de caixa de verificação **a granel/grupo** e a desativar, não poderá reverter a alteração.

Utilize a nova experiência de alerta/alertas em tempo quase real se precisar notificar os utilizadores com base nas suas funções. Com grupos de [ação,](../platform/action-groups.md)pode configurar notificações de e-mail para utilizadores com qualquer uma das funções de contribuinte/proprietário/leitor (não combinadas como uma única opção).

## <a name="automation"></a>Automatização
* [Use powerShell para automatizar alertas de configuração](../../azure-monitor/app/powershell-alerts.md)
* [Use webhooks para automatizar respondendo a alertas](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>Consulte também
* [Testes Web de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)
* [Automatizar alertas](../../azure-monitor/app/powershell-alerts.md)
* [Diagnósticos proativos](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

