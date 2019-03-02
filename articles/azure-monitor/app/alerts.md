---
title: Definir alertas no Application Insights do Azure | Documentos da Microsoft
description: Seja notificado sobre tempos de resposta lenta, exceções e outros desempenho ou alterações de utilização na sua aplicação web.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.reviewer: lagayhar
ms.assetid: f8ebde72-f819-4ba5-afa2-31dbd49509a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: mbullwin
ms.openlocfilehash: c1165fcebf8382d30b1be86f102da78ef0a4ac9a
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57244371"
---
# <a name="set-alerts-in-application-insights"></a>Definir alertas no Application Insights
[O Azure Application Insights] [ start] pode alertá-lo para as alterações nas métricas de desempenho ou a utilização na sua aplicação web. 

O Application Insights monitoriza a sua aplicação em direto numa [ampla variedade de plataformas] [ platforms] para o ajudar a diagnosticar problemas de desempenho e compreender os padrões de utilização.

Existem vários tipos de alertas:

* [**Alertas de métricas** ](../../azure-monitor/platform/alerts-metric-overview.md) informá-lo quando uma métrica ultrapassa um valor de limiar durante um determinado período - por exemplo, tempos de resposta, contagens de exceção, utilização da CPU ou vistas de página.
* [**Alertas de registo** ](../../azure-monitor/platform/alerts-unified-log.md) é usado para descrever alertas em que o sinal de alerta baseia-se numa consulta de Kusto personalizada.
* [**Testes Web** ] [ availability] informá-lo quando o site estiver disponível na internet ou está a responder lentamente. [Saiba mais][availability].
* [**Diagnósticos proativos** ](../../azure-monitor/app/proactive-diagnostics.md) são automaticamente configurados para notificá-lo sobre os padrões de desempenho invulgar.

## <a name="set-a-metric-alert"></a>Definir um alerta de métrica
Abra o separador de regras de alerta e, em seguida, utilize o botão Adicionar.

![No separador de regras de alerta, escolha Adicionar alerta. Defina a aplicação que o recurso para medir, forneça um nome para o alerta e selecione uma métrica.](./media/alerts/01-set-metric.png)

* Defina o recurso antes das outras propriedades. **Escolha o recurso de "(componentes)"** se pretender definir alertas em métricas de desempenho ou na utilização.
* O nome que irá fornecer ao alerta tem de ser exclusivo no grupo de recursos (não apenas a sua aplicação).
* Cuidado-se de que tenha em atenção as unidades em que lhe for pedido para introduzir o valor de limiar.
* Se selecionar a caixa "Proprietários de E-Mail...", os alertas são enviados por e-mail para todos os utilizadores quem tem acesso a este grupo de recursos. Para expandir este conjunto de pessoas, adicioná-los para o [grupo de recursos ou subscrição](../../azure-monitor/app/resources-roles-access-control.md) (não o recurso).
* Se especificar "E-mails adicionais", os alertas são enviados para esses indivíduos ou grupos (ou não tiver selecionado a caixa "... os proprietários de e-mail"). 
* Definir um [webhook endereço](../../azure-monitor/platform/alerts-webhooks.md) se tiver configurado uma aplicação web que responde a alertas. Ele é chamado quando o alerta está ativado e quando este estiver resolvido. (Mas observe que, neste momento, parâmetros de consulta não são submetidos como propriedades de webhook.)
* Pode desativar ou ativar o alerta: consulte os botões na parte superior.

*Não vejo no botão Adicionar alerta.*

* Está a utilizar uma conta da organização? Pode definir alertas se tiver de proprietário ou Contribuidor aceder a este recurso de aplicação. Dar uma olhada no separador de controlo de acesso. [Saiba mais sobre o controlo de acesso][roles].

> [!NOTE]
> No painel de alertas, verá que já existe um conjunto de alerta: [Diagnósticos proativos](../../azure-monitor/app/proactive-failure-diagnostics.md). O alerta automática monitoriza uma determinada métrica, pedido taxa de falhas. A menos que se optar por desativar o alerta proativo, não precisa definir sua própria alerta na taxa de falhas de pedido.
> 
> 

## <a name="see-your-alerts"></a>Ver os seus alertas
Receber um e-mail quando um alerta muda de estado entre inativa e ativa. 

O estado atual de cada alerta é mostrado no separador regras de alerta.

Há um resumo da atividade recente nos alertas pendentes:

![Alertas pendentes](./media/alerts/010-alert-drop.png)

É o histórico de alterações de estado no registo de atividades:

![Na guia visão geral, clique em configurações, registos de auditoria](./media/alerts/09-alerts.png)

## <a name="how-alerts-work"></a>Como funcionam os alertas
* Um alerta tem três Estados: "Nunca ativado", "Ativado" e "Resolvido". Ativado significa que a condição especificada era true, quando foi avaliado pela última vez.
* Uma notificação é gerada quando um alerta muda de estado. (Se a condição de alerta já era verdadeira quando criou o alerta, poderá não receber uma notificação até que a condição for false.)
* Cada notificação gera uma mensagem de e-mail, se selecionado a caixa de mensagens de correio eletrónico ou fornecido endereços de e-mail. Também pode ver a notificações na lista pendente.
* Um alerta é avaliado sempre que uma métrica chega, mas, caso não o contrário.
* A avaliação agrega a métrica ao longo do período anterior e, em seguida, compara-o limiar para determinar o estado de novo.
* O período que escolher Especifica o intervalo durante o qual as métricas são agregadas. Não afeta a frequência de avaliação do alerta: que depende da frequência de chegada de métricas.
* Se não existem dados são recebidos para uma métrica em particular há algum tempo, a lacuna tem efeitos diferentes na avaliação de alerta e dos gráficos no Explorador de métrica. No Explorador de métrica, se nenhum dado é visto por mais tempo do que o intervalo de amostragem do gráfico, o gráfico mostra um valor de 0. Mas um alerta com base na mesma métrica não é reavaliada, e o estado do alerta permanece inalterado. 
  
    Quando dados eventualmente chegam, o gráfico salta para um valor diferente de zero. O alerta é avaliada com base nos dados disponíveis para o período especificado. Se o novo ponto de dados é o único disponível no período, a agregação baseia-se apenas no ponto de dados.
* Um alerta pode cintilação frequentemente entre os Estados de alerta e bom estado de funcionamento, mesmo que defina um longo período. Isto pode acontecer se o valor da métrica passa á volta o limiar. Não existe nenhum hysteresis no limiar: a transição para o alerta acontece com o mesmo valor que a transição para o bom estado de funcionamento.

## <a name="what-are-good-alerts-to-set"></a>O que são alertas de bom para definir?
Depende de seu aplicativo. Para começar, é melhor não definir métricas demasiadas. Passe algum tempo observando os gráficos de métricas, enquanto a aplicação está em execução, para ter uma noção de como ele se comporta normalmente. Essa prática ajuda-o a encontrar formas de melhorar seu desempenho. Em seguida, defina alertas para informá-lo quando as métricas enviados para fora da zona normal. 

Alertas populares incluem:

* [Métricas de browser][client], especialmente Browser **tempos de carregamento de página**, são ideais para aplicações web. Se sua página tiver muitos scripts, deve buscar **exceções do browser**. Para obter estas métricas e alertas, terá de configurar [monitorização de página web][client].
* **Tempo de resposta do servidor** no lado do servidor de aplicativos web. Bem como a configuração de alertas, manter a par esta métrica para ver se desproporcional varia com taxas de pedidos de alta: variação pode indicar que a aplicação está a ficar sem recursos. 
* **Exceções de servidor** - para vê-los, precisa fazer algumas [programa de configuração adicional](../../azure-monitor/app/asp-net-exceptions.md).

Não se esqueça de que [diagnóstico de taxa de falha proativos](../../azure-monitor/app/proactive-failure-diagnostics.md) monitorizar automaticamente a taxa a que a aplicação responde a solicitações com códigos de falha.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Como definir um alerta de exceção com pesquisa de registos personalizado

Nesta seção, veremos como configurar um alerta de exceção de consulta com base. Neste exemplo, digamos que um alerta quando a taxa de falha é superior a 10% nas últimas 24 horas.

1. Vá para o recurso do Application Insights no portal do Azure.
2. No lado esquerdo, em Configurar clique em **alerta**.

    ![À esquerda em Configurar clique alerta](./media/alerts/1appinsightalert.png)

3. Na parte superior do separador alerta, selecione **nova regra de alerta**.

     ![Na parte superior do separador alerta, clique em nova regra de alerta](./media/alerts/2createalert.png)

4. O recurso deve ser automaticamente selecionada. Para definir uma condição, clique em **adicionar condição**.

    ![Clique em Adicionar condição](./media/alerts/3addcondition.png)

5. No separador de lógica de sinal de configurar selecione **pesquisa de registos personalizado**

    ![Clique em pesquisa de registos personalizado](./media/alerts/4customlogsearch.png)

6. No separador de pesquisa de registo personalizado, introduza a consulta na caixa de "Consulta de pesquisa". Neste exemplo, utilizamos o abaixo a consulta de Kusto.
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

    ![Escreva a consulta na caixa de consulta de pesquisa](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > Também pode aplicar estas etapas para outros tipos de alertas com base em consulta. Pode saber mais sobre a linguagem de consulta de Kusto nesta [Kusto introdução doc](https://docs.microsoft.com/azure/kusto/concepts/) ou isso [SQL para Kusto referência rápida](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. Em "Alert logic", escolha se baseia-se no número de resultados ou medida da métrica. Em seguida, escolha a condição (maior que, igual a, menos do que) e um limiar. Enquanto estiver a alterar estes valores, pode observar as alterações de sentença de pré-visualização de condição. Neste exemplo utilizamos "igual a".

    ![Em lógica de alerta escolher de entre as opções fornecidas para com base em e a condição, em seguida, escreva um limiar](./media/alerts/6alertlogic.png)

8. Em "Evaluated com base no", defina o período e freqüência. O período de aqui tem de corresponder ao valor que colocamos para período na consulta acima. Em seguida, clique em **feito**.

    ![Definir o período e freqüência na parte inferior e, em seguida, clique em concluído](./media/alerts/7evaluate.png)

9. Agora, vemos a condição que criar com o custo mensal estimado. Abaixo sob ["Grupos de ação"](../platform/action-groups.md) pode criar um novo grupo ou selecione um existente. Se desejar, pode personalizar as ações.

    ![Clique em Selecionar ou criar botões no grupo de ação](./media/alerts/8actiongroup.png)

10. Por fim, adicione os detalhes do alerta (nome da regra, a descrição, a gravidade de alerta). Quando tiver terminado, clique em **criar regra de alerta** na parte inferior.

    ![Em detalhes de alerta escreva o nome de regra de alerta, escreva uma descrição e escolha uma gravidade ](./media/alerts/9alertdetails.png)

## <a name="who-receives-the-classic-alert-notifications"></a>Quem receberá notificações de alerta (clássicas)?

Esta secção apenas se aplica a alertas clássicos e irá ajudá-lo a otimizar suas notificações de alerta para garantir que apenas os destinatários desejados recebem notificações. Para saber mais sobre a diferença entre [alertas clássicos](../platform/alerts-classic.overview.md) e os alertas de nova experiência, consulte a [artigo de descrição geral de alertas](../platform/alerts-overview.md). Para controlar a notificação de alerta na nova experiência de alertas, utilize [grupos de ação](../platform/action-groups.md).

* Recomendamos a utilização de destinatários específicos para as notificações de alerta clássicas.

* Para alertas em qualquer métricas do Application Insights (incluindo as métricas de disponibilidade), o **em massa/grupo** opção de caixa de verificação, se estiver ativada, envia para os utilizadores com funções de proprietário, contribuinte ou leitor na subscrição. Na verdade, _todos os_ os utilizadores com acesso à subscrição do recurso do Application Insights estão no âmbito e irá receber notificações.

> [!NOTE]
> Se utilizar atualmente o **em massa/grupo** opção de caixa de verificação e desativá-la, não será capaz de reverter a alteração.

Utilize os nova experiência/perto-realtime os alertas do alerta se tiver de notificar os utilizadores com base nas suas funções. Com o [grupos de ação](../platform/action-groups.md), pode configurar notificações por e-mail aos utilizadores com qualquer uma das funções Contribuidor/proprietário/leitor (não combinadas em conjunto como uma única opção).

## <a name="automation"></a>Automatização
* [Utilize o PowerShell para automatizar a configuração de alertas](../../azure-monitor/app/powershell-alerts.md)
* [Utilizar webhooks para automatizar a responder a alertas](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>Consulte também
* [Testes web de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)
* [Automatizar a configuração de alertas](../../azure-monitor/app/powershell-alerts.md)
* [Diagnósticos proativos](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

