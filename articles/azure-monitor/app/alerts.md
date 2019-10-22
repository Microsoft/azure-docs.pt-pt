---
title: Definir alertas no Aplicativo Azure insights | Microsoft Docs
description: Seja notificado sobre tempos de resposta lentos, exceções e outras alterações de desempenho ou de uso em seu aplicativo Web.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.openlocfilehash: a21e2676d1b03472c58e2f95095a1a59d00b16be
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678396"
---
# <a name="set-alerts-in-application-insights"></a>Definir alertas no Application Insights
[Aplicativo Azure insights][start] pode alertá-lo sobre alterações nas métricas de desempenho ou de uso em seu aplicativo Web. 

O Application Insights monitora seu aplicativo em tempo real em uma [ampla variedade de plataformas][platforms] para ajudá-lo a diagnosticar problemas de desempenho e entender os padrões de uso.

Há vários tipos de alertas:

* Os [**alertas de métrica**](../../azure-monitor/platform/alerts-metric-overview.md) informam quando uma métrica cruza um valor de limite para algum período, como tempos de resposta, contagens de exceção, uso da CPU ou exibições de página.
* Os [**alertas de log**](../../azure-monitor/platform/alerts-unified-log.md) são usados para descrever alertas em que o sinal de alerta se baseia em uma consulta Kusto personalizada.
* Os [**testes da Web**][availability] informam quando seu site está indisponível na Internet ou está respondendo lentamente. [Saiba mais][availability].
* Os [**diagnósticos proativos**](../../azure-monitor/app/proactive-diagnostics.md) são configurados automaticamente para notificá-lo sobre padrões de desempenho incomuns.

## <a name="set-a-metric-alert"></a>Definir um alerta de métrica
Abra a guia regras de alerta e, em seguida, use o botão Adicionar.

![Na guia regras de alerta, escolha Adicionar alerta. Defina seu aplicativo como o recurso a ser medido, forneça um nome para o alerta e escolha uma métrica.](./media/alerts/01-set-metric.png)

* Defina o recurso antes das outras propriedades. **Escolha o recurso "(componentes)"** se desejar definir alertas sobre métricas de desempenho ou de uso.
* O nome que você fornece ao alerta deve ser exclusivo dentro do grupo de recursos (não apenas seu aplicativo).
* Tenha cuidado para observar as unidades nas quais você será solicitado a inserir o valor do limite.
* Se você marcar a caixa "proprietários de email...", os alertas serão enviados por email para todos que têm acesso a esse grupo de recursos. Para expandir esse conjunto de pessoas, adicione-as ao [grupo de recursos ou à assinatura (e](../../azure-monitor/app/resources-roles-access-control.md) não ao recurso).
* Se você especificar "emails adicionais", os alertas serão enviados a esses indivíduos ou grupos (se você tiver marcado ou não os "proprietários de email..." Box). 
* Defina um [endereço de webhook](../../azure-monitor/platform/alerts-webhooks.md) se você tiver configurado um aplicativo Web que responde a alertas. Ele é chamado quando o alerta é ativado e quando é resolvido. (Mas observe que, no momento, os parâmetros de consulta não são passados como propriedades de webhook.)
* Você pode desabilitar ou habilitar o alerta: consulte os botões na parte superior.

*Não vejo o botão adicionar alerta.*

* Você está usando uma conta institucional? Você pode definir alertas se tiver acesso de proprietário ou colaborador a este recurso de aplicativo. Dê uma olhada na guia controle de acesso. [saiba mais sobre o controle de acesso][roles].

> [!NOTE]
> Na folha alertas, você verá que já existe um alerta configurado: [Proactive Diagnostics](../../azure-monitor/app/proactive-failure-diagnostics.md). O alerta automático monitora uma métrica específica, taxa de falha de solicitação. A menos que você decida desabilitar o alerta proativo, não precisará definir seu próprio alerta na taxa de falha de solicitação.
> 
> 

## <a name="see-your-alerts"></a>Consulte seus alertas
Você receberá um email quando um alerta mudar de estado entre inativo e ativo. 

O estado atual de cada alerta é mostrado na guia regras de alerta.

Há um resumo das atividades recentes na lista suspensa alertas:

![Lista suspensa alertas](./media/alerts/010-alert-drop.png)

O histórico de alterações de estado está no log de atividades:

![Na guia Visão geral, clique em configurações, logs de auditoria](./media/alerts/09-alerts.png)

## <a name="how-alerts-work"></a>Como funcionam os alertas
* Um alerta tem três Estados: "nunca ativado", "ativado" e "resolvido". Ativado significa que a condição especificada era verdadeira, quando foi avaliada pela última vez.
* Uma notificação é gerada quando um alerta muda de estado. (Se a condição de alerta já foi verdadeira quando você criou o alerta, talvez você não receba uma notificação até que a condição seja falsa.)
* Cada notificação gera um email se você marcou a caixa emails ou os endereços de email fornecidos. Você também pode examinar a lista suspensa notificações.
* Um alerta é avaliado cada vez que uma métrica chega, mas não de outra forma.
* A avaliação agrega a métrica durante o período anterior e a compara com o limite para determinar o novo estado.
* O período escolhido especifica o intervalo em que as métricas são agregadas. Ele não afeta a frequência com que o alerta é avaliado: isso depende da frequência de chegada das métricas.
* Se nenhum dado chega para uma determinada métrica por algum tempo, a lacuna tem efeitos diferentes sobre a avaliação do alerta e sobre os gráficos no Gerenciador de métricas. No Metrics Explorer, se nenhum dado for visto por mais tempo do que o intervalo de amostragem do gráfico, o gráfico mostrará um valor de 0. Mas um alerta baseado na mesma métrica não é reavaliado e o estado do alerta permanece inalterado. 
  
    Quando os dados chegam eventualmente, o gráfico volta para um valor diferente de zero. O alerta é avaliado com base nos dados disponíveis para o período especificado. Se o novo ponto de dados for o único disponível no período, a agregação será baseada apenas no ponto de dados.
* Um alerta pode piscar frequentemente entre os Estados de alerta e íntegro, mesmo se você definir um longo período. Isso pode acontecer se o valor da métrica focalizar o limite. Não há nenhum histerese no limite: a transição para o alerta ocorre no mesmo valor que a transição para íntegro.

## <a name="what-are-good-alerts-to-set"></a>Quais são os bons alertas a serem definidos?
Depende do seu aplicativo. Para começar, é melhor não definir muitas métricas. Gaste algum tempo examinando seus gráficos de métrica enquanto seu aplicativo está em execução, para ter uma ideia de como ele se comporta normalmente. Essa prática ajuda você a encontrar maneiras de melhorar seu desempenho. Em seguida, configure alertas para informá-lo quando as métricas ficarem fora da zona normal. 

Os alertas populares incluem:

* As [métricas do navegador][client], especialmente **tempos de carregamento de página**do navegador, são boas para aplicativos Web. Se sua página tiver muitos scripts, você deverá procurar por **exceções de navegador**. Para obter essas métricas e alertas, você precisa configurar o [monitoramento de página da Web][client].
* **Tempo de resposta do servidor** para o lado do servidor de aplicativos Web. Além de configurar alertas, fique atento a essa métrica para ver se ela varia desproporcionalmente com altas taxas de solicitação: a variação pode indicar que seu aplicativo está ficando sem recursos. 
* **Exceções do servidor** – para vê-las, você precisa fazer algumas [configurações adicionais](../../azure-monitor/app/asp-net-exceptions.md).

Não se esqueça de que o [diagnóstico proativo da taxa de falha](../../azure-monitor/app/proactive-failure-diagnostics.md) monitora automaticamente a taxa na qual seu aplicativo responde a solicitações com códigos de falha.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Como definir um alerta de exceção usando a pesquisa de logs personalizada

Nesta seção, vamos examinar como definir um alerta de exceção baseada em consulta. Para este exemplo, digamos que desejamos um alerta quando a taxa de falha for maior que 10% nas últimas 24 horas.

1. Vá para o recurso do Application Insight no portal do Azure.
2. À esquerda, em configurar, clique em **alerta**.

    ![À esquerda, em configurar clique em alerta](./media/alerts/1appinsightalert.png)

3. Na parte superior da guia alerta, selecione **nova regra de alerta**.

     ![Na parte superior da guia alerta, clique em nova regra de alerta](./media/alerts/2createalert.png)

4. O recurso deve ser selecionado automaticamente. Para definir uma condição, clique em **Adicionar condição**.

    ![Clique em Adicionar condição](./media/alerts/3addcondition.png)

5. Na guia Configurar lógica de sinal, selecione **pesquisa de logs personalizada**

    ![Clique em pesquisa de logs personalizada](./media/alerts/4customlogsearch.png)

6. Na guia pesquisa de logs personalizada, insira sua consulta na caixa "consulta de pesquisa". Para este exemplo, usaremos a consulta Kusto abaixo.
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

    ![Consulta de tipo na caixa de consulta de pesquisa](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > Você também pode aplicar essas etapas a outros tipos de alertas baseados em consulta. Você pode saber mais sobre a linguagem de consulta do Kusto deste [documento de introdução do Kusto](https://docs.microsoft.com/azure/kusto/concepts/) ou desta página de dicas de [SQL para Kusto](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. Em "lógica de alerta", escolha se ele é baseado no número de resultados ou medição de métrica. Em seguida, escolha a condição (maior que, igual a, menor que) e um limite. Enquanto você estiver alterando esses valores, você poderá observar as alterações da sentença de visualização da condição. Neste exemplo, estamos usando "igual a".

    ![Em lógica de alerta, escolha entre as opções fornecidas para com base na condição e, em seguida, digite um limite](./media/alerts/6alertlogic.png)

8. Em "avaliado com base em", defina o período e a frequência. O ponto aqui deve corresponder ao valor que colocamos para o período na consulta acima. Em seguida, clique em **concluído**.

    ![Defina período e frequência na parte inferior e clique em concluído](./media/alerts/7evaluate.png)

9. Agora vemos a condição que criamos com o custo mensal estimado. Abaixo de ["grupos de ação"](../platform/action-groups.md) , você pode criar um novo grupo ou selecionar um existente. Se desejar, você poderá personalizar as ações.

    ![Clique nos botões selecionar ou criar em grupo de ações](./media/alerts/8actiongroup.png)

10. Finalmente, adicione os detalhes do alerta (nome da regra de alerta, descrição, severidade). Quando terminar, clique em **criar regra de alerta** na parte inferior.

    ![Em detalhe do alerta, digite o nome da regra de alerta, escreva uma descrição e escolha uma severidade](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Como cancelar a assinatura de notificações de email de alerta clássico

Esta seção se aplica a **alertas de disponibilidade clássicos**, **alertas de métrica de Application insights clássica**e a **alertas de anomalias de falha clássica**.

Você receberá notificações por email para esses alertas clássicos se uma das seguintes opções for aplicável:

* Seu endereço de email está listado no campo destinatários do email de notificação nas configurações da regra de alerta.

* A opção de enviar notificações por email a usuários que mantêm determinadas funções para a assinatura é ativada e você mantém uma função respectiva para essa assinatura específica do Azure.

![Captura de tela de notificação de alerta](./media/alerts/alert-notification.png)

Para controlar melhor sua segurança e privacidade, geralmente é recomendável especificar explicitamente os destinatários da notificação para seus alertas clássicos no campo **destinatários do email de notificação** . A opção de notificar todos os usuários que contêm determinadas funções é fornecida para compatibilidade com versões anteriores.

Para cancelar a assinatura de notificações por email geradas por uma determinada regra de alerta, remova seu endereço de email do campo **destinatários do email de notificação** .

Se seu endereço de email não estiver listado explicitamente, é recomendável desabilitar a opção para notificar todos os membros de determinadas funções automaticamente e, em vez disso, listar todos os emails do usuário que precisam receber notificações para essa regra de alerta no email de notificação campo de destinatários.

## <a name="who-receives-the-classic-alert-notifications"></a>Quem recebe as notificações de alerta (clássico)?

Esta seção se aplica somente a alertas clássicos e ajudará você a otimizar suas notificações de alerta para garantir que somente os destinatários desejados recebam notificações. Para entender mais sobre a diferença entre [alertas clássicos](../platform/alerts-classic.overview.md) e a nova experiência de alertas, consulte o [artigo Visão geral de alertas](../platform/alerts-overview.md). Para controlar a notificação de alerta na nova experiência de alertas, use [grupos de ação](../platform/action-groups.md).

* Recomendamos o uso de destinatários específicos para notificações de alerta clássicas.

* Para alertas sobre as métricas de Application Insights (incluindo métricas de disponibilidade), a opção de caixa de seleção **em massa/grupo** , se habilitada, envia para usuários com funções de proprietário, colaborador ou leitor na assinatura. Na verdade, _todos_ os usuários com acesso à assinatura o recurso Application insights estão no escopo e receberão notificações.

> [!NOTE]
> Se você usar a opção de caixa de seleção **Bulk/Group** no momento e desabilitá-la, não será possível reverter a alteração.

Use a nova experiência de alerta/alertas quase em tempo real se você precisar notificar os usuários com base em suas funções. Com os [grupos de ação](../platform/action-groups.md), você pode configurar notificações por email para os usuários com qualquer uma das funções colaborador/proprietário/leitor (não combinadas como uma única opção).

## <a name="automation"></a>Automatização
* [Usar o PowerShell para automatizar a configuração de alertas](../../azure-monitor/app/powershell-alerts.md)
* [Usar WebHooks para automatizar a resposta a alertas](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>Ver também
* [Testes da Web de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)
* [Automatizar a configuração de alertas](../../azure-monitor/app/powershell-alerts.md)
* [Diagnóstico proativo](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

