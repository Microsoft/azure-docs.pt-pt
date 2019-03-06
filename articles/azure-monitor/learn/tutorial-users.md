---
title: Conhecer os seus clientes no Azure Application Insights | Microsoft Docs
description: Tutorial sobre como utilizar o Azure Application Insights para compreender de que forma os clientes estão a utilizar a sua aplicação.
keywords: ''
services: application-insights
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: e46dae199f4d45c325e41fa5432e98cba9a2f4ae
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57449602"
---
# <a name="use-azure-application-insights-to-understand-how-customers-are-using-your-application"></a>Utilize o Azure Application Insights para compreender de que forma os clientes estão a utilizar a sua aplicação

O Azure Application Insights recolhe informações de utilização que o ajudam a perceber de que forma é que os utilizadores interagem com a sua aplicação.  Este tutorial orienta-o pelos diferentes recursos que estão disponíveis para analisar essas informações.  Vai aprender a:

> [!div class="checklist"]
> * Analisar detalhes dos utilizadores que estão a aceder à aplicação
> * Utilizar informações de sessão para analisar a forma como os clientes utilizam a aplicação
> * Definir funis que lhe permitem comparar as atividade dos utilizadores que pretende com a atividade real deles 
> * Criar um livro que consolide as visualizações e as consultas num único documento
> * Agrupar utilizadores semelhantes para os analisar em conjunto
> * Saber que utilizadores regressam à aplicação
> * Inspecionar como é que os utilizadores navegam pela aplicação


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Instale o [2017 do Visual Studio](https://www.visualstudio.com/downloads/) com as seguintes cargas de trabalho:
    - Desenvolvimento ASP.NET e Web
    - Desenvolvimento do Azure
- Transfira e instale o [Visual Studio Snapshot Debugger](https://aka.ms/snapshotdebugger).
- Implemente uma aplicação .NET no Azure e [ative o Application Insights SDK](../../azure-monitor/app/asp-net.md). 
- [Envie telemetria da aplicação](../../azure-monitor/app/usage-overview.md#send-telemetry-from-your-app) para adicionar eventos personalizados/visualizações de páginas
- Envie [contexto do utilizador](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context) para acompanhar o que um determinado utilizador faz ao longo do tempo e utilizar ao máximo as funcionalidades de utilização.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="get-information-about-your-users"></a>Obter informações sobre os utilizadores
O painel **Users** (Utilizadores) permite-lhe ficar a par de detalhes importantes sobre os utilizadores sob diversas formas. Pode utilizar este painel para compreender informações, como, por exemplo, a partir de onde é que os utilizadores se estão a ligar, detalhes dos clientes deles e a que áreas da aplicação estão a aceder. 

1. Selecione **Application Insights** e, em seguida, selecione a sua subscrição.
2. Selecione **Users** (Utilizadores), no menu.
3. A vista predefinida mostra o número de utilizadores únicos que se ligaram à sua aplicação nas últimas 24 horas.  Pode alterar o período de tempo e definir vários outros critérios, para filtrar estas informações.

    ![Construtor de Consultas](media/tutorial-users/QueryBuilder.png)

6. Clique no menu pendente **During** (Durante) e altere o período de tempo para 7 dias.  Isto aumenta os dados incluídos nos diversos gráficos do painel.

    ![Alterar o Intervalo de Tempo](media/tutorial-users/TimeRange.png)

4. Clique no menu pendente **Split by** (Dividir por) para adicionar uma discriminação por propriedade do utilizador ao gráfico.  Selecione **Country or region** (País ou região).  O gráfico inclui os mesmos dados, mas permite ver uma discriminação do número de utilizadores de cada país.

    ![Gráfico de País ou Região](media/tutorial-users/CountryorRegion.png)

5. Posicione o cursor por cima das diferentes barras no gráfico e repare que a contagem de cada país reflete apenas o período de tempo que as barras representam.
6. Veja a coluna **Insights** (Informações), no lado direito, que analisam os seus dados de utilizador.  Essas análises disponibilizam informações como o número de sessões únicas durante o período de tempo e registos com propriedades comuns significativas dos dados dos utilizadores 

    ![Coluna Informações](media/tutorial-users/insights.png)


## <a name="analyze-user-sessions"></a>Analisar as sessões dos utilizadores
O painel **Sessions** (Sessões) é semelhante ao painel **Users** (Utilizadores).  Ao passo que **Users** (Utilizadores) o ajuda a compreender os detalhes dos utilizadores que estão a aceder à aplicação, **Sessions** (Sessões) ajuda-o a saber como é que a utilizaram.  

1. Selecione **Sessions** (Sessões), no menu.
2. Veja o gráfico e repare que as opções para filtrar e discriminar os dados são iguais às do painel **Users** (Utilizadores).

    ![Construtor de Consultas de Sessões](media/tutorial-users/SessionsBuilder.png)

3. O painel **Sample of these sessions** (Amostra destas sessões), no lado direito, mostra as sessões que incluem um grande número de eventos.  São sessões cuja análise é interessante.

    ![Amostra destas sessões](media/tutorial-users/SessionsSample.png)

4. Clique numa da sessões para ver **Session Timeline** (Linha Cronológica da Sessão), que mostra todas as ações na mesma.  Esta informação pode ajudá-lo a identificar informações, como as sessões com um grande número de exceções.

    ![Linha Cronológica das Sessões](media/tutorial-users/SessionsTimeline.png)

## <a name="group-together-similar-users"></a>Agrupar utilizadores semelhantes
R **coorte** é um conjunto de utilizadores agrupados nas características semelhantes.  Pode utilizar coortes para filtrar os dados de outros painéis, o que lhe permite analisar grupos específicos de utilizadores.  Por exemplo, poderá querer analisar apenas utilizadores que tenham concluído uma compra.

1.  Selecione **Cohorts** (Coortes), no menu.
2.  Clique em **New** (Novo) para criar um coorte novo.
3.  Selecione o menu pendente **Who used** (Quem utilizou) e uma ação.  Só serão incluídos os utilizadores que realizaram essa ação durante o período de tempo do relatório.

    ![Coorte que realizou ações especificadas](media/tutorial-users/CohortsDropdown.png)

4.  Selecione **Users** (Utilizadores), no menu.
5.  No menu pendente **Show** (Mostrar), selecione o coorte que acabou de criar.  Os dados do gráfico estão limitados a esses utilizadores.

    ![Ferramenta de coorte nos utilizadores](media/tutorial-users/UsersCohort.png)


## <a name="compare-desired-activity-to-reality"></a>Comparar a atividade pretendida com a realidade
Ao passo que os painéis acima estão orientados para o que os utilizadores da sua aplicação fizeram, **Funnels** (Funis) concentram-se naquilo que quer que os seus utilizadores façam.  Um funil representa um conjunto de passos na aplicação e a percentagem de utilizadores que se movimentam entre esses passos.  Por exemplo, pode criar um funil que meça a percentagem de utilizadores que se ligam à aplicação e que procuram um produto.  Depois, pode ver a percentagem de utilizadores que adicionaram esse produto a um carrinho e a percentagem daqueles que concluíram uma compra.

1. Selecione **Funnels** (Funis), no menu, e clique em **New** (Novo). 

    ![](media/tutorial-users/funnelsnew.png)

2. Preencha **Funnel Name** (Nome do Funil).
3. Crie um funil com, pelo menos, dois passos e selecione uma ação para cada passo.  A lista de ações é compilada a partir dos dados de utilização que o Application Insights recolhe.

    ![](media/tutorial-users/funnelsedit.png)

4. Clique em **Save** (Guardar) para guardar o funil e veja os resultados.  A janela no lado direito do funil mostra os eventos mais comuns antes da primeira atividade e depois da última atividade, que o ajudam a compreender as tendências do utilizador em torno da sequência específica.

    ![](media/tutorial-users/funnelsright.png)


## <a name="learn-which-customers-return"></a>Saber que clientes regressaram
**Retention** (Retenção) ajuda-o a saber que utilizadores regressam à aplicação.  

1. Selecione **Retention** (Retenção), no menu.
2. Por predefinição, as informações analisadas incluem utilizadores que realizaram qualquer ação e, depois, regressaram para realizar outra ação qualquer.  Pode mudar este filtro para qualquer ação, como, por exemplo, apenas os utilizadores que regressaram depois de concluírem uma compra.

    ![](media/tutorial-users/retentionquery.png)

3. Os utilizadores que regressam e que correspondam aos critérios são apresentados, quer num gráfico, quer numa tabela, relativamente a períodos diferentes.  O padrão típico mostra uma queda gradual nos utilizadores que regressam ao longo do tempo.  Uma queda abrupta de um período de tempo para o seguinte pode ser motivo de preocupação. 

    ![](media/tutorial-users/retentiongraph.png)

## <a name="analyze-user-navigation"></a>Analisar a navegação dos utilizadores
Os **fluxos de utilizador** mostram como é que os utilizadores navegam entre as páginas e as funcionalidades da aplicação.  Essas informações permitem responder a perguntas como para onde, regra geral, é que os utilizadores vão de uma determinada página, como é que, geralmente, saem da sua aplicação e se repetem regularmente alguma ação.

1.  Selecione **User flows** (Fluxos de utilizador), no menu.
2.  Clique em **New** (Novo), para criar um fluxo de utilizador novo, e clique em **Edit** (Editar), para editar os detalhes.
3.  Aumente **Time Range** (Intervalo de Tempo) para 7 dias e selecione um evento inicial.  O fluxo vai acompanhar sessões de utilizadores que comecem com esse evento.

    ![](media/tutorial-users/flowsedit.png)

4.  O fluxo do utilizador é apresentado e pode ver os diferentes caminhos do utilizador, bem como os números de sessões.  As linhas azuis indicam uma ação que o utilizador realizou a seguir à ação atual.  As linhas vermelhas indicam o fim da sessão do utilizador.

    ![](media/tutorial-users/flows.png)

5.  Para remover um evento do fluxo, clique em **x**, no canto da ação, e clique em**Create Graph** (Criar Gráfico).  O gráfico é redesenhado com todas as instâncias desse evento removidas.  Clique em **Edit** (Editar) para ver que o evento foi adicionado a **Excluded events** (Eventos excluídos).

    ![](media/tutorial-users/flowsexclude.png)

## <a name="consolidate-usage-data"></a>Consolidar os dados de utilização
Os **livros** combinam visualizações de dados, consultas do Analytics e textos em documentos interativos.  Pode utilizar os livros para agrupar informações de utilização comuns, consolidar informações de um incidente específico ou informar a sua equipa sobre a utilização da aplicação.

1.  Selecione **Workbooks** (Livros), no menu.
2.  Clique em **New** (Novo) para criar um livro novo.
3.  Já é fornecida uma consulta que inclui todos os dados de utilização no último dia, apresentada como um gráfico de barras.  Pode utilizar essa consulta, editá-la manualmente ou clicar em **Sample queries** (Exemplos de consultas) para selecionar outras consultas úteis.

    ![](media/tutorial-users/samplequeries.png)

4.  Clique em **Done editing** (Edição concluída).
5.  Clique em **Edit** (Editar), no painel superior, para editar o texto que aparece no cimo do livro.  Esse texto é formatado com Markdown.

    ![](media/tutorial-users/markdown.png)

6.  Clique em **Add users** (Adicionar utilizadores) para adicionar um gráfico com informações do utilizador.  Se pretender, edite os detalhes do gráfico e clique em **Done editing** (Edição concluída), para o guardar.


## <a name="next-steps"></a>Passos Seguintes
Agora que já aprendeu a analisar os seus utilizadores, avance para o tutorial seguinte e aprenda a criar dashboards personalizados que combinam essas informações com outros dados úteis da aplicação.

> [!div class="nextstepaction"]
> [Criar dashboards personalizados](../../azure-monitor/learn/tutorial-app-dashboards.md)
