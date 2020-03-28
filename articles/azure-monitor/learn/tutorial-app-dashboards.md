---
title: Criar dashboards personalizados no Azure Application Insights | Microsoft Docs
description: Tutorial para criar dashboards de KPI personalizados com o Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 07/3/2019
ms.custom: mvc
ms.openlocfilehash: caf8b1899cad95ade6297e78e8f2cf35939ef189
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77661639"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Criar dashboards de KPI personalizados com o Azure Application Insights

Pode criar múltiplos dashboards no portal do Azure em que cada um inclui mosaicos que visualizam os dados de vários recursos do Azure entre diferentes grupos de recursos e subscrições.  Pode afixar diferentes gráficos e vistas a partir do Azure Application Insights para criar dashboards personalizados que fornecem uma visão geral do estado de funcionamento e do desempenho da aplicação. Este tutorial orienta-o através da criação de um dashboard personalizado que inclui vários tipos de dados e visualizações a partir do Azure Application Insights.  Saiba como:

> [!div class="checklist"]
> * Criar um dashboard personalizado no Azure
> * Adicionar um mosaico a partir da Galeria de Mosaicos
> * Adicionar métricas padrão no Application Insights ao dashboard
> * Adicionar um gráfico de métricas personalizado no Application Insights ao dashboard
> * Adicione os resultados de uma consulta de Registos (Analytics) ao painel de instrumentos



## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Implemente uma aplicação .NET no Azure e [ative o Application Insights SDK](../../azure-monitor/app/asp-net.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inscreva-se no portal [https://portal.azure.com](https://portal.azure.com)Azure em .

## <a name="create-a-new-dashboard"></a>Criar um novo dashboard
Um único dashboard pode conter recursos de várias aplicações, grupos de recursos e subscrições.  Comece o tutorial ao criar um novo dashboard para a sua aplicação.  

1. No painel do painel de instrumentos, selecione Novo painel de **instrumentos**.

   ![Novo dashboard](media/tutorial-app-dashboards/1newdashboard.png)

1. Escreva um nome para o dashboard.
1. Veja a **Galeria de Mosaicos** para obter uma variedade de mosaicos que pode adicionar ao dashboard.  Além de adicionar azulejos da galeria, pode fixar gráficos e outras vistas diretamente de Application Insights para o dashboard.
1. Localize o mosaico **Markdown** e arraste-o para o dashboard.  Este azulejo permite-lhe adicionar texto formatado em marcação, o que é ideal para adicionar texto descritivo ao seu painel de instrumentos.
1. Adicione texto às propriedades do mosaico e redimensione-o na tela do dashboard.
    
    ![Editar mosaico Markdown](media/tutorial-app-dashboards/2dashboard-text.png)

1. Clique em **Personalizar na** parte superior do ecrã para sair do modo de personalização do azulejo.

## <a name="add-health-overview"></a>Adicionar descrição geral do estado de funcionamento
Um dashboard com texto estático não é muito interessante, por isso adicione agora um azulejo da Application Insights para mostrar informações sobre a sua aplicação.  Pode adicionar mosaicos do Application Insights a partir da Galeria de Mosaicos ou afixá-los diretamente a partir dos ecrãs do Application Insights.  Isto permite-lhe configurar gráficos e vistas com os quais já está familiarizado antes de afixá-los ao dashboard.  Comece por adicionar a descrição geral do estado de funcionamento padrão para a sua aplicação.  Isto não requer nenhuma configuração e permite uma personalização mínima no dashboard.


1. Selecione o seu recurso **Application Insights** no ecrã principal.
2. No painel **de visão geral,** ![clique](media/tutorial-app-dashboards/pushpin.png) no ícone do pino do pino para adicionar o azulejo ao último painel de instrumentos que estava a ver.  
 
3. Na parte superior direita, aparecerá uma notificação de que o seu azulejo estava preso no seu painel de instrumentos. Clique **em Pinned para dashboard** na notificação para voltar ao seu painel de instrumentos ou utilizar o painel do painel de instrumentos.
4. O azulejo é agora adicionado ao seu painel. Selecione **Editar** para alterar o posicionamento do azulejo. Clique e arraste-o para a posição e, em seguida, clique em **Fazer personalizar**. O dashboard tem agora um mosaico algumas informações úteis.

    ![Dashboard com linha cronológica de Descrição Geral](media/tutorial-app-dashboards/4dashboard-edit.png)

## <a name="add-custom-metric-chart"></a>Adicionar gráfico de métricas personalizado
O painel **Métricas** permite-lhe criar um gráfico de uma métrica recolhida pelo Application Insights ao longo do tempo com filtros e agrupamentos opcionais.  Tal como tudo no Application Insights, pode adicionar este gráfico ao dashboard.  É necessário efetuar primeiro uma pequena personalização.

1. Selecione o seu recurso **Application Insights** no ecrã principal.
1. Selecione **Métricas**.  
2. Já foi criado um gráfico vazio e está a ser-lhe pedido para adicionar uma métrica.  Adicione uma métrica ao gráfico e, opcionalmente, adicione um filtro e um agrupamento.  O exemplo abaixo mostra o número de pedidos de servidor agrupados com êxito.  Isto fornece uma vista de execução de pedidos com e sem êxito.

    ![Adicionar métrica](media/tutorial-app-dashboards/metrics.png)

4. Selecione **Pin para painel risado** à direita. Esta ação adiciona o mosaico ao último dashboard em que estava a trabalhar.

3.  Na parte superior direita, aparecerá uma notificação de que o seu azulejo estava preso no seu painel de instrumentos. Clique **em Pinned para dashboard** na notificação para voltar ao seu painel de instrumentos ou use a lâmina do painel de instrumentos.

4. O azulejo é agora adicionado ao seu painel. Selecione **Editar** para alterar o posicionamento do azulejo. Clique e arraste-o para a posição e, em seguida, clique em **Fazer personalizar**.

## <a name="add-logs-analytics-query"></a>Adicionar consulta de registos (Analytics)
O Azure Application Insights Logs (Analytics) fornece uma linguagem de consulta rica que lhe permite analisar todos os dados recolhidos Insights de Aplicação. Tal como os gráficos e outras vistas, pode adicionar a saída de uma consulta de registos ao seu painel de instrumentos.

Uma vez que o Azure Applications Insights Logs (Analytics) é um serviço separado, precisa de partilhar o seu painel de instrumentos para que inclua uma consulta de registos. Quando partilha um dashboard Azure, publica-o como um recurso Azure, que pode disponibilizá-lo a outros utilizadores e recursos.  

1. Na parte superior do ecrã do dashboard, clique em **Partilhar**.

    ![Publicar o dashboard](media/tutorial-app-dashboards/8dashboard-share.png)

2. Mantenha o mesmo **Nome do dashboard** e selecione o **Nome da Subscrição** para partilhar o dashboard.  Clique em **Publicar**.  O dashboard está agora disponível para outros serviços e subscrições.  Opcionalmente, pode definir utilizadores específicos que devem ter acesso ao dashboard.
1. Selecione o seu recurso **Application Insights** no ecrã principal.
2. Clique em **Registos (Analytics)** à esquerda sob monitorização para abrir o portal Registos (Analytics).
3. Escreva a consulta seguinte, que devolve as 10 páginas mais pedidas e a respetiva contagem de pedidos:

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. Clique em **Executar** para validar os resultados da consulta.
5. Clique no ícone do pino ![Ícone de afixar](media/tutorial-app-dashboards/pushpin.png) e selecione o nome do seu painel de instrumentos. A razão pela qual esta opção tem seleciona um dashboard diferente dos passos anteriores onde o último dashboard foi utilizado é porque a consola Logs (Analytics) é um serviço separado e precisa selecionar de todos os dashboards partilhados disponíveis.

5. Antes de voltar ao painel de instrumentos, adicione outra consulta, mas desta vez torna-a como um gráfico para que veja as diferentes formas de visualizar uma consulta de logs num painel de instrumentos. Comece com a consulta seguinte que resume as 10 principais operações com a maioria das exceções.

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. Selecione **Gráfico** e, em seguida, mude para **Anel** para visualizar os resultados.

    ![Gráfico de logs (Analytics)](media/tutorial-app-dashboards/11querychart.png)

6. Clique no ícone do pino ![Ícone de afixar](media/tutorial-app-dashboards/pushpin.png) na parte superior direita para fixar o gráfico no seu painel de instrumentos e desta vez selecione o link para voltar ao seu painel de instrumentos.
4. Os resultados das consultas estão agora adicionados ao dashboard no formato que selecionou.  Clique e arraste cada um para a posição e, em seguida, clique em **Fazer personalizar**.
5. Selecione o ícone do lápis ![Ícone de lápis](media/tutorial-app-dashboards/pencil.png) em cada título para dar-lhes um título descritivo.

5. Selecione **Partilhar** para republicar as suas alterações no seu dashboard que agora inclui uma variedade de gráficos e visualizações a partir de Insights de Aplicação.


## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu a criar dashboards personalizados, veja a restante documentação do Application Insights, incluindo um caso prático.

> [!div class="nextstepaction"]
> [Diagnósticos avançados](../../azure-monitor/app/devops.md)
