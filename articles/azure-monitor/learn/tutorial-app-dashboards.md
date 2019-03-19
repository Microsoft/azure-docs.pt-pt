---
title: Criar dashboards personalizados no Azure Application Insights | Microsoft Docs
description: Tutorial para criar dashboards de KPI personalizados com o Azure Application Insights.
keywords: ''
services: application-insights
author: lgayhardt
ms.author: lagayhar
ms.date: 01/11/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 3abe0511200bf5828b485b15a4b8a512731c4ffa
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082131"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Criar dashboards de KPI personalizados com o Azure Application Insights

Pode criar múltiplos dashboards no portal do Azure em que cada um inclui mosaicos que visualizam os dados de vários recursos do Azure entre diferentes grupos de recursos e subscrições.  Pode afixar diferentes gráficos e vistas a partir do Azure Application Insights para criar dashboards personalizados que fornecem uma visão geral do estado de funcionamento e do desempenho da aplicação.  Este tutorial orienta-o através da criação de um dashboard personalizado que inclui vários tipos de dados e visualizações a partir do Azure Application Insights.  Saiba como:

> [!div class="checklist"]
> * Criar um dashboard personalizado no Azure
> * Adicionar um mosaico a partir da Galeria de Mosaicos
> * Adicionar métricas padrão no Application Insights ao dashboard 
> * Adicionar um gráfico de métricas personalizado no Application Insights ao dashboard
> * Adicionar os resultados de uma consulta do Analytics ao dashboard 



## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Implemente uma aplicação .NET no Azure e [ative o Application Insights SDK](../../azure-monitor/app/asp-net.md). 

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-new-dashboard"></a>Criar um novo dashboard
Um único dashboard pode conter recursos de várias aplicações, grupos de recursos e subscrições.  Comece o tutorial ao criar um novo dashboard para a sua aplicação.  

1. No painel de dashboard, selecione **novo dashboard**.

   ![Novo dashboard](media/tutorial-app-dashboards/1newdashboard.png)

1. Escreva um nome para o dashboard.
1. Veja a **Galeria de Mosaicos** para obter uma variedade de mosaicos que pode adicionar ao dashboard.  Além de adicionar mosaicos a partir da galeria, pode afixar gráficos e outras vistas diretamente a partir do Application Insights ao dashboard.
1. Localize o mosaico **Markdown** e arraste-o para o dashboard.  Este mosaico permite-lhe adicionar texto formatado no markdown, o que é ideal para adicionar texto descritivo ao dashboard.
1. Adicione texto às propriedades do mosaico e redimensione-o na tela do dashboard.
    
    ![Editar mosaico Markdown](media/tutorial-app-dashboards/2dashboard-text.png)

1. Clique em **personalização concluída** na parte superior do ecrã para sair do modo de personalização do mosaico.

## <a name="add-health-overview"></a>Adicionar descrição geral do estado de funcionamento
Um dashboard com apenas texto estático não é muito interessante, por isso, adicione agora um mosaico a partir do Application Insights para mostrar informações sobre a aplicação.  Pode adicionar mosaicos do Application Insights a partir da Galeria de Mosaicos ou afixá-los diretamente a partir dos ecrãs do Application Insights.  Isto permite-lhe configurar gráficos e vistas com os quais já está familiarizado antes de afixá-los ao dashboard.  Comece por adicionar a descrição geral do estado de funcionamento padrão para a sua aplicação.  Isto não requer nenhuma configuração e permite uma personalização mínima no dashboard.


1. Selecione seu **Application Insights** recursos no ecrã inicial.
2. Na **descrição geral** painel, clique no ícone de pushpin para adicionar o mosaico ao último dashboard que estava a visualizar.  

    ![Afixar linha cronológica de Descrição Geral](media/tutorial-app-dashboards/3overview.png)
 
3. No canto superior direito, uma notificação será exibida a que o seu mosaico foi afixado ao dashboard. Clique em **afixado ao dashboard** na notificação para regressar ao seu dashboard ou utilizar o painel de dashboard.
4. Agora, no mosaico é adicionado ao seu dashboard. Selecione **editar** para alterar o posicionamento do mosaico. Clique e arraste o it para a posição pretendida e, em seguida, clique em **personalização concluída**. O dashboard tem agora um mosaico algumas informações úteis.

    ![Dashboard com linha cronológica de Descrição Geral](media/tutorial-app-dashboards/4dashboard-edit.png)



## <a name="add-custom-metric-chart"></a>Adicionar gráfico de métricas personalizado
O painel **Métricas** permite-lhe criar um gráfico de uma métrica recolhida pelo Application Insights ao longo do tempo com filtros e agrupamentos opcionais.  Tal como tudo no Application Insights, pode adicionar este gráfico ao dashboard.  É necessário efetuar primeiro uma pequena personalização.

1. Selecione seu **Application Insights** recurso na tela inicial.
1. Selecione **Métricas**.  
2. Já foi criado um gráfico vazio e está a ser-lhe pedido para adicionar uma métrica.  Adicione uma métrica ao gráfico e, opcionalmente, adicione um filtro e um agrupamento.  O exemplo abaixo mostra o número de pedidos de servidor agrupados com êxito.  Isto fornece uma vista de execução de pedidos com e sem êxito.

    ![Adicionar métrica](media/tutorial-app-dashboards/5sumserverrequests.png)

4. Selecione **afixar ao dashboard** à direita. Esta ação adiciona o mosaico ao último dashboard em que estava a trabalhar.

    ![Afixar gráfico de métricas](media/tutorial-app-dashboards/6sumserverrequests-pin.png)

3.  No canto superior direito, uma notificação será exibida a que o seu mosaico foi afixado ao dashboard. Clique em **afixado ao dashboard** na notificação para regressar ao seu dashboard ou utilize o painel de dashboard.

4. Agora, no mosaico é adicionado ao seu dashboard. Selecione **editar** para alterar o posicionamento do mosaico. Clique e arraste o it para a posição pretendida e, em seguida, clique em **personalização concluída**.

    ![Dashboard com métricas](media/tutorial-app-dashboards/7dashboard-edit2.png)

## <a name="add-analytics-query"></a>Adicionar a consulta do Analytics
O Azure Application Insights Analytics fornece uma linguagem de consulta avançada que permite analisar todos os dados recolhidos pelo Application Insights.  Tal como outros gráficos e vistas, pode adicionar a saída de uma consulta do Analytics ao dashboard.   

Como o Azure Applications Insights Analytics é um serviço separado, tem de partilhar o dashboard com o mesmo para incluir uma consulta do Analytics. Quando partilha um dashboard do Azure, publica-o como um recurso do Azure que pode disponibilizá-lo para outros utilizadores e recursos.  

1. Na parte superior do ecrã do dashboard, clique em **Partilhar**.

    ![Publicar o dashboard](media/tutorial-app-dashboards/8dashboard-share.png)

2. Mantenha o mesmo **Nome do dashboard** e selecione o **Nome da Subscrição** para partilhar o dashboard.  Clique em **Publicar**.  O dashboard está agora disponível para outros serviços e subscrições.  Opcionalmente, pode definir utilizadores específicos que devem ter acesso ao dashboard.
1. Selecione seu **Application Insights** recurso na tela inicial.
2. Clique em **Analytics** na parte superior do ecrã para abrir o portal do Analytics.

    ![Iniciar o Analytics](media/tutorial-app-dashboards/9analytics.png)

3. Escreva a consulta seguinte, que devolve as 10 páginas mais pedidas e a respetiva contagem de pedidos:

    ```
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10 
    ```

4. Clique em **executar** para validar os resultados da consulta.
5. Clique no ícone de pin e selecione o nome do dashboard. O motivo para esta opção permitir selecionar um dashboard ao contrário dos passos anteriores em que foi utilizado o último dashboard é porque a consola do Analytics é um serviço separado e tem de selecionar a partir de todos os dashboards partilhados disponíveis.

    ![Afixar a consulta do Analytics](media/tutorial-app-dashboards/10query.png)

5. Antes de regressar ao dashboard, adicione outra consulta, mas desta vez componha-a como um gráfico para ver as diferentes formas de visualizar uma consulta do Analytics num dashboard.  Comece com a consulta seguinte que resume as 10 principais operações com a maioria das exceções.

    ```
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10 
    ```

6. Selecione **Gráfico** e, em seguida, mude para **Anel** para visualizar os resultados.

    ![Gráfico do Analytics](media/tutorial-app-dashboards/11querychart.png)

6. Clique no ícone de pin para afixar o gráfico ao dashboard e, desta vez, selecione a ligação para regressar ao dashboard.
4. Os resultados das consultas estão agora adicionados ao dashboard no formato que selecionou.  Clique e arraste cada um para a posição pretendida e, em seguida, clique em **personalização concluída**.
5. Selecione o ícone de lápis em cada título para lhes dar um título descritivo.

    ![Dashboard com Analytics](media/tutorial-app-dashboards/12edit-title.png)

5. Selecione **partilha** para voltar a publicar as alterações ao dashboard que agora inclui uma variedade de gráficos e visualizações do Application Insights.


## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu a criar dashboards personalizados, veja a restante documentação do Application Insights, incluindo um caso prático.

> [!div class="nextstepaction"]
> [Diagnósticos avançados](../../azure-monitor/app/devops.md)
