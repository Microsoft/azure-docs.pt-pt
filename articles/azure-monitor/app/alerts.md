---
title: Definir alertas em insights de aplicação azure
description: Seja notificado sobre tempos de resposta lentos, exceções e outras alterações de desempenho ou utilização na sua aplicação web.
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 80759c94d7cc5b60b6e38a34b85fb64c3c18fd2e
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666722"
---
# <a name="set-alerts-in-application-insights"></a>Definir alertas em insights de aplicação

O [Azure Application Insights][start] pode alertá-lo para alterações nas métricas de desempenho ou de utilização na sua aplicação web. 

Application Insights monitoriza a sua aplicação ao vivo em uma [grande variedade de plataformas][platforms] para ajudá-lo a diagnosticar problemas de desempenho e entender padrões de uso.

Existem vários tipos de alertas:

* [**Os alertas métricos**](../../azure-monitor/platform/alerts-metric-overview.md) dizem-lhe quando uma métrica cruza um valor limiar durante algum período - como tempos de resposta, contagem de exceções, utilização de CPU ou visualizações de página.
* [**Os Alertas**](../../azure-monitor/platform/alerts-unified-log.md) de Registo são usados para descrever alertas onde o sinal de alerta é baseado numa consulta personalizada de Kusto.
* [**Os testes na Web**][availability] dizem-lhe quando o seu site está indisponível na internet ou responde lentamente. [Saiba mais][availability].
* [**Os diagnósticos proactivos**](../../azure-monitor/app/proactive-diagnostics.md) são configurados automaticamente para notificá-lo sobre padrões de desempenho incomuns.

## <a name="set-a-metric-alert"></a>Definir um alerta métrico

Abra o separador regras de alerta e, em seguida, use o botão adicionar.

![No separador de regras de Alerta, escolha Adicionar Alerta. Detete a sua aplicação como recurso para medir, forneça um nome para o alerta e escolha uma métrica.](./media/alerts/01-set-metric.png)

* Desloque o recurso antes das outras propriedades. **Escolha o recurso "(componentes)"** se pretender definir alertas sobre as métricas de desempenho ou de utilização.
* O nome que dá ao alerta deve ser único dentro do grupo de recursos (e não apenas a sua aplicação).
* Tenha cuidado para observar as unidades em que lhe é pedido que entre no valor limiar.
* Se verificar a caixa "E-mails dos proprietários...", os alertas são enviados por e-mail a todos os que tenham acesso a este grupo de recursos. Para expandir este conjunto de pessoas, adicione-as ao grupo de [recursos ou subscrição](../../azure-monitor/app/resources-roles-access-control.md) (não ao recurso).
* Se especificar "E-mails adicionais", os alertas são enviados para esses indivíduos ou grupos (se verificou ou não os "e-mails dos proprietários..." caixa). 
* Detete um [endereço webhook](../../azure-monitor/platform/alerts-webhooks.md) se tiver configurado uma aplicação web que responda a alertas. Chama-se quando o alerta é Ativado e quando é resolvido. (Mas note que, atualmente, os parâmetros de consulta não são passados como propriedades webhook.)
* Pode desativar ou ativar o alerta: consulte os botões na parte superior.

*Não vejo o botão Add Alert.*

* Estás a usar uma conta organizacional? Pode definir alertas se tiver acesso ao proprietário ou colaborador a este recurso de aplicação. Veja o separador Controlo de Acesso. Saiba o que é o controlo de [acesso.][roles]

> [!NOTE]
> Na lâmina de alerta, vê-se que já existe um alerta: [Diagnósticos Proativos](../../azure-monitor/app/proactive-failure-diagnostics.md). O alerta automático monitoriza uma métrica específica, solicitando uma taxa de falha. A menos que decida desativar o alerta proactivo, não precisa de definir o seu próprio alerta sobre a taxa de falha de pedido.
> 
> 

## <a name="see-your-alerts"></a>Veja os seus alertas
Recebe um e-mail quando um alerta muda de estado entre inativo e ativo. 

O estado atual de cada alerta é mostrado no separador de regras de alerta.

Há um resumo da atividade recente nos alertas:

![Alertas caem](./media/alerts/010-alert-drop.png)

A história das mudanças de Estado está no Registo de Atividades:

![No separador 'Visão Geral', clique em Definições, Registos de Auditoria](./media/alerts/09-alerts.png)

## <a name="how-alerts-work"></a>Como funcionam os alertas
* Um alerta tem três estados: "Nunca ativado", "Ativado" e "Resolvido". Ativado significa que a condição que especificou era verdadeira, quando foi avaliada pela última vez.
* Uma notificação é gerada quando um estado de alerta muda de estado. (Se a condição de alerta já fosse verdadeira quando criou o alerta, pode não receber uma notificação até que a condição se desfaça.)
* Cada notificação gera um e-mail se verificou a caixa de e-mails ou forneceu endereços de e-mail. Pode também ver a lista de notificações.
* Um alerta é avaliado cada vez que uma métrica chega, mas não de outra forma.
* A avaliação agrega a métrica durante o período anterior e, em seguida, compara-a ao limiar para determinar o novo estado.
* O período que escolher especifica o intervalo sobre o qual as métricas são agregadas. Não afeta a frequência com que o alerta é avaliado: depende da frequência de chegada das métricas.
* Se não chegarem dados para uma determinada métrica durante algum tempo, a lacuna tem efeitos diferentes na avaliação de alerta e nos gráficos do explorador métrico. No explorador métrico, se não forem observados dados por mais tempo do que o intervalo de amostragem do gráfico, o gráfico mostra um valor de 0. Mas um alerta baseado na mesma métrica não é reavaliado, e o estado do alerta permanece inalterado. 
  
    Quando os dados eventualmente chegam, o gráfico salta para um valor não-zero. O alerta avalia com base nos dados disponíveis para o período que especificou. Se o novo ponto de dados for o único disponível no período, o agregado baseia-se apenas nesse ponto de dados.
* Um alerta pode piscar frequentemente entre estados de alerta e estados saudáveis, mesmo que estabeleça um longo período. Isto pode acontecer se o valor métrico pairar em torno do limiar. Não há histerese no limiar: a transição para o alerta acontece ao mesmo valor que a transição para saudável.

## <a name="what-are-good-alerts-to-set"></a>Quais são os bons alertas para definir?
Depende da sua candidatura. Para começar, é melhor não definir muitas métricas. Passe algum tempo a olhar para os seus gráficos métricos enquanto a sua aplicação está em execução, para ter uma ideia de como se comporta normalmente. Esta prática ajuda-o a encontrar formas de melhorar o seu desempenho. Em seguida, instale alertas para lhe dizer quando as métricas sairem da zona normal. 

Os alertas populares incluem:

* [As métricas do navegador][client], especialmente **os tempos**de carregamento da página do navegador, são boas para aplicações web. Se a sua página tem muitos scripts, deve procurar **exceções**ao navegador . Para obter estas métricas e alertas, tem de configurar a [monitorização][client]da página web .
* **Tempo de resposta** do servidor para o lado do servidor das aplicações web. Além de configurar alertas, fique de olho nesta métrica para ver se varia desproporcionalmente com altas taxas de pedido: a variação pode indicar que a sua aplicação está a ficar sem recursos. 
* **Exceções** do servidor - para vê-las, você tem que fazer alguma [configuração adicional](../../azure-monitor/app/asp-net-exceptions.md).

Não se esqueça que os [diagnósticos proactivos](../../azure-monitor/app/proactive-failure-diagnostics.md) da taxa de falha monitorizam automaticamente a taxa a que a sua aplicação responde a pedidos com códigos de falha.

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

Se o seu endereço de e-mail não estiver listado explicitamente, recomendamos que desative a opção de notificar automaticamente todos os membros de determinadas funções e, em vez disso, listar todos os e-mails dos utilizadores que necessitem de receber notificações para essa regra de alerta no e-mail de notificação campo de destinatários.

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
* [Disponibilidade de testes web](../../azure-monitor/app/monitor-web-app-availability.md)
* [Automatizar alertas](../../azure-monitor/app/powershell-alerts.md)
* [Diagnósticos proativos](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

