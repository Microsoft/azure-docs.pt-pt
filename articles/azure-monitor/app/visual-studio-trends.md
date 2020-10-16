---
title: Analisar Tendências no Visual Studio | Microsoft Docs
description: Analise, visualize e explore tendências na sua telemetria do Application Insights no Visual Studio.
ms.topic: conceptual
ms.date: 03/17/2017
ms.custom: vs-azure
ms.openlocfilehash: 9b78dceed3c2a25d6afedf5fca348726d7aafd3d
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932248"
---
# <a name="analyzing-trends-in-visual-studio"></a>Analisar Tendências no Visual Studio
A ferramenta Application Insights Trends visualiza como eventos de telemetria importantes da sua aplicação Web alteram ao longo do tempo, ajudando-o a identificar rapidamente problemas e anomalias. Ao ligá-lo a informações mais detalhadas de diagnóstico, o Trends pode ajudá-lo a melhorar o desempenho da aplicação, identificar as causas de exceções e descobrir conhecimentos aprofundados dos seus eventos personalizados.

![Janela de exemplo do Trends](./media/visual-studio-trends/app-insights-trends-hero-750.png)

## <a name="configure-your-web-app-for-application-insights"></a>Configurar a aplicação Web para o Application Insights

Caso isto ainda não tenha sido realizado, [configure a aplicação Web para o Application Insights](./app-insights-overview.md). Isto permite enviar telemetria para o portal do Application Insights. A ferramenta Trends lê a telemetria a partir daí.

As Application Insights Trends está disponível no Visual Studio 2015 Update 3 e posteriores.

## <a name="open-application-insights-trends"></a>Abrir o Application Insights Trends
Para abrir a janela do Application Insights Trends:

* No botão da barra de ferramentas do Application Insights, selecione **Explorar Tendências de Telemetria** ou
* No menu de contexto do projeto, escolha **Application Insights > Explorar Tendências de Telemetria**, ou
* A partir da barra de menus do Visual Studio, escolha **Ver > Outras Janelas > Application Insights Trends**.

Pode ver uma mensagem para selecionar um recurso. Clique em **Selecione um recurso**, inicie sessão com uma subscrição do Azure e, em seguida, escolha um recurso do Application Insights da lista para a qual gostaria de analisar tendências de telemetria.

## <a name="choose-a-trend-analysis"></a>Escolher uma análise de tendências
![Menu de tipos comuns de análise de tendências](./media/visual-studio-trends/app-insights-trends-1-750.png)

Comece por escolher uma das cinco análises de tendência comuns, em que cada analisa os dados das últimas 24 horas:

* **Investigar problemas de desempenho com os seus pedidos de servidor** – Pedidos efetuados ao seu serviço, agrupado por tempos de resposta
* **Analisar erros nos seus pedidos de servidor** – Pedidos efetuados ao seu serviço, agrupado por códigos de resposta HTTP
* **Examinar as exceções na sua aplicação** – exceções a partir do seu serviço, agrupadas por tipo de exceção
* **Verificar o desempenho das dependências da aplicação** – Serviços chamados pelo seu serviço, agrupadas por tempos de resposta
* **Inspecione os eventos personalizados** – Eventos personalizados que configurou para o seu serviço, agrupados por tipo de evento.

Estas análises criadas previamente estão disponíveis mais tarde a partir do botão **Ver tipos comuns de análise de telemetria** no canto superior esquerdo da janela do Trends.

## <a name="visualize-trends-in-your-application"></a>Visualizar tendências na sua aplicação
O Application Insights Trends cria uma visualização de séries de tempo a partir da telemetria da sua aplicação. Cada visualização de séries de tempo apresenta um tipo de telemetria, agrupado por uma propriedade dessa telemetria, através de algum intervalo de tempo. Por exemplo, é melhor visualizar os pedidos do servidor, agrupados pelo país/região de onde se originaram, nas últimas 24 horas. Neste exemplo, cada bolha na visualização representaria uma contagem dos pedidos de servidor para alguns país/regiões durante uma hora.

Utilize os controlos na parte superior da janela para ajustar que tipos de telemetria pretende ver. Em primeiro lugar, escolha os tipos de telemetria nos quais está interessado:

* **Tipo de Telemetria** - Pedidos de servidor, exceções, dependências ou eventos personalizados
* **Intervalo de tempo** – Em qualquer lugar nos últimos 30 minutos para os últimos 3 dias
* **Agrupar Por** – Tipo de exceção, ID do problema, país/região e muito mais.

Em seguida, clique em **Analisar Telemetria** para executar a consulta.

Para navegar entre bolhas na visualização:

* Clique para selecionar uma bolha, que atualiza os filtros na parte inferior da janela, resumir apenas os eventos que ocorreram durante um período de tempo específico
* Clique duas vezes numa bolha para navegar na ferramenta De pesquisa e ver todos os eventos individuais de telemetria que ocorreram durante esse período de tempo
* Faça Ctrl-clique numa bolha desmarcá-la na visualização.

> [!TIP]
> As ferramentas Tendências e Procurar funcionam em conjunto para ajudá-lo a identificar as causas dos problemas do seu serviço entre milhares de eventos de telemetria. Por exemplo, se uma tarde os seus clientes verificarem que a aplicação está menos reativada, comece pelas Tendências. Analise os pedidos efetuados pelo seu serviço durante as últimas horas, agrupados por tempo de resposta. Ver se existe um cluster de excecionalmente grande de pedidos de lentos. Em seguida, faça duplo clique nessa bolha para ir para a ferramenta Pesquisa, filtrada para esses eventos de pedido. A partir da Pesquisa, pode explorar o conteúdo desses pedidos e navegar para o código necessário para resolver o problema.
> 
> 

## <a name="filter"></a>Filtrar
Detete tendências mais específicas com os controlos de filtro na parte inferior da janela. Para aplicar um filtro, clique no respetivo nome. Pode alternar rapidamente entre diferentes filtros para detetar as tendências que possam estar ocultas numa dimensão específica da sua telemetria. Se aplicar um filtro numa dimensão, como o Tipo de Exceção, os filtros noutras dimensões permanecem clicáveis, mesmo que pareçam acinzentados. Para desaplicá-lo, clique novamente nele. Faça CTRL-clique para selecionar vários filtros na mesma dimensão.

![Filtros de tendência](./media/visual-studio-trends/TrendsFiltering-750.png)

E se pretende aplicar vários filtros? 

1. Aplique o primeiro filtro. 
2. Clique no botão **Aplicar filtros selecionados e consultar novamente** pelo nome da dimensão do primeiro filtro. Isto irá efetuar uma nova consulta de telemetria apenas para os eventos que correspondem ao primeiro filtro. 
3. Aplica um segundo filtro. 
4. Repita o processo para localizar tendências em subconjuntos específicos da sua telemetria. Por exemplo, os pedidos de servidor com o nome "GET Home/Index" *e* que são provenientes da Alemanha *e* que receberam o código de resposta 500. 

Para anular a aplicação de um destes filtros, clique no botão **Remover filtros selecionados e consultar novamente** da dimensão.

![Vários filtros](./media/visual-studio-trends/TrendsFiltering2-750.png)

## <a name="find-anomalies"></a>Localizar anomalias
A ferramenta Trends pode realçar bolhas de eventos que são anómalos em comparação com outras bolhas na mesma série de tempo. Na lista pendente Tipo de vista, selecione **Contagens no registo de tempo (realçar anomalias)** ou **Percentagens no registo de tempo (realçar anomalias)**. As bolhas vermelhas são anómalas. As anomalias são definidas como bolhas com contagens/percentagens superiores a 2,1 vezes o desvio padrão das contagens/percentagens que ocorreram nos dois últimos períodos de tempo (48 horas se estiver a ver as últimas 24 horas, etc.).

![Os pontos coloridos indicam anomalias](./media/visual-studio-trends/TrendsAnomalies-750.png)

> [!TIP]
> Realçar anomalias é especialmente útil para encontrar valores atípicos na série de tempo de bolhas pequenas que, de outro modo, possam ter um tamanho semelhante.  
> 
> 

## <a name="next-steps"></a><a name="next"></a>Passos seguintes
* **[Trabalhando com a Application Insights no Estúdio Visual.](./visual-studio.md)** Procure telemetria, veja dados no CodeLens e configure o Application Insights. Tudo isto no Visual Studio. 
* **[Trabalhar com o portal Application Insights](./overview-dashboard.md)**. Dashboards, ferramentas de diagnóstico e análise poderosas, alertas, mapa de dependência em direto da aplicação e exportação de telemetria. 

