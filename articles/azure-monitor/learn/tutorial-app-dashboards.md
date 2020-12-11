---
title: Criar dashboards personalizados no Azure Application Insights | Microsoft Docs
description: Tutorial para criar dashboards de KPI personalizados com o Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 09/30/2020
ms.custom: mvc, contperf-fy21q1
ms.openlocfilehash: 701480300101b019830c57d9aa000534fa63bb6a
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028534"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Criar dashboards de KPI personalizados com o Azure Application Insights

Pode criar múltiplos dashboards no portal do Azure em que cada um inclui mosaicos que visualizam os dados de vários recursos do Azure entre diferentes grupos de recursos e subscrições.  Pode afixar diferentes gráficos e vistas a partir do Azure Application Insights para criar dashboards personalizados que fornecem uma visão geral do estado de funcionamento e do desempenho da aplicação. Este tutorial orienta-o através da criação de um dashboard personalizado que inclui vários tipos de dados e visualizações a partir do Azure Application Insights.

 Saiba como:

> [!div class="checklist"]
> * Criar um dashboard personalizado no Azure
> * Adicionar um mosaico a partir da Galeria de Mosaicos
> * Adicionar métricas padrão no Application Insights ao dashboard
> * Adicionar um gráfico de métricas personalizado no Application Insights ao dashboard
> * Adicione os resultados de uma consulta de Logs (Analytics) ao painel de instrumentos

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Implemente uma aplicação .NET no Azure e [ative o Application Insights SDK](../app/asp-net.md).

> [!NOTE]
> As permissões necessárias para trabalhar com dashboards são discutidas no artigo sobre a compreensão do [controlo de acesso para os dashboards](../../azure-portal/azure-portal-dashboard-share-access.md#understanding-access-control-for-dashboards).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-new-dashboard"></a>Criar um novo dashboard

> [!WARNING]
> Se deslocar o seu recurso Application Insights para um grupo ou subscrição de recursos diferentes, terá de atualizar manualmente o painel de instrumentos removendo os azulejos antigos e fixando novos azulejos do mesmo recurso Application Insights em novo local.

Um único dashboard pode conter recursos de várias aplicações, grupos de recursos e subscrições.  Comece o tutorial ao criar um novo dashboard para a sua aplicação.  

1. No menu suspenso à esquerda no portal Azure, selecione **Dashboard**.

    ![Entrega do menu do Portal Azure](media/tutorial-app-dashboards/dashboard-from-menu.png)

2. No painel do painel de instrumentos, selecione **Novo painel de instrumentos** e depois **painel em branco**.

   ![Novo dashboard](media/tutorial-app-dashboards/new-dashboard.png)

3. Escreva um nome para o dashboard.
4. Veja a **Galeria de Mosaicos** para obter uma variedade de mosaicos que pode adicionar ao dashboard.  Além de adicionar azulejos da galeria, pode pin gráficos e outras vistas diretamente de Application Insights para o dashboard.
5. Localize o mosaico **Markdown** e arraste-o para o dashboard.  Este azulejo permite-lhe adicionar texto formatado em marcação, o que é ideal para adicionar texto descritivo ao seu painel de instrumentos. Para saber mais, consulte [Use um azulejo de marcação nos painéis Azure para mostrar conteúdo personalizado](../../azure-portal/azure-portal-markdown-tile.md).
6. Adicione texto às propriedades do mosaico e redimensione-o na tela do dashboard.

    [![Editar mosaico Markdown](media/tutorial-app-dashboards/markdown.png)](media/tutorial-app-dashboards/dashboard-edit-mode.png#lightbox)

7. Selecione **Fazer a personalização** na parte superior do ecrã para sair do modo de personalização do azulejo.

## <a name="add-health-overview"></a>Adicionar descrição geral do estado de funcionamento

Um dashboard com texto estático não é muito interessante, por isso adicione agora um azulejo do Application Insights para mostrar informações sobre a sua aplicação. Pode adicionar mosaicos do Application Insights a partir da Galeria de Mosaicos ou afixá-los diretamente a partir dos ecrãs do Application Insights. Isto permite-lhe configurar gráficos e vistas com os quais já está familiarizado antes de afixá-los ao dashboard.  Comece por adicionar a descrição geral do estado de funcionamento padrão para a sua aplicação.  Isto não requer nenhuma configuração e permite uma personalização mínima no dashboard.


1. Selecione o seu recurso **Application Insights** no ecrã principal.
2. No **painel de visão geral,** selecione o ícone do pino do ![ pino ](media/tutorial-app-dashboards/pushpin.png) para adicionar o azulejo a um painel de instrumentos.
3. No separador "Pin to dashboard", selecione qual o painel de instrumentos para adicionar o azulejo ou criar um novo.
 
3. No topo direito, aparecerá uma notificação de que o seu azulejo estava preso ao seu painel de instrumentos.  Selecione **Pinned para o dashboard** na notificação para voltar ao seu painel de instrumentos ou utilizar o painel de instrumentos.
4. Esse azulejo é agora adicionado ao seu painel. **Selecione Editar** para alterar o posicionamento do azulejo. Selecione e arraste-o para a posição e, em seguida, selecione **Fazer a personalização**. O dashboard tem agora um mosaico algumas informações úteis.

    [![Painel de instrumentos no modo de edição](media/tutorial-app-dashboards/dashboard-edit-mode.png)](media/tutorial-app-dashboards/dashboard-edit-mode.png#lightbox)

## <a name="add-custom-metric-chart"></a>Adicionar gráfico de métricas personalizado

O painel **Métricas** permite-lhe criar um gráfico de uma métrica recolhida pelo Application Insights ao longo do tempo com filtros e agrupamentos opcionais.  Tal como tudo no Application Insights, pode adicionar este gráfico ao dashboard.  É necessário efetuar primeiro uma pequena personalização.

1. Selecione o seu recurso **Application Insights** no ecrã principal.
1. Selecione **Métricas**.  
2. Já foi criado um gráfico vazio e está a ser-lhe pedido para adicionar uma métrica.  Adicione uma métrica ao gráfico e, opcionalmente, adicione um filtro e um agrupamento.  O exemplo abaixo mostra o número de pedidos de servidor agrupados com êxito.  Isto fornece uma vista de execução de pedidos com e sem êxito.

    [![Adicionar métrica](media/tutorial-app-dashboards/metrics.png)](media/tutorial-app-dashboards/metrics.png#lightbox)

4. Selecione **Pin para painel** à direita.

3.  No topo direito, aparecerá uma notificação de que o seu azulejo estava preso ao seu painel de instrumentos. Selecione **Pinned para o dashboard** na notificação para voltar ao seu painel de instrumentos ou utilizar o separador do painel de instrumentos.

4. Esse azulejo é agora adicionado ao seu painel. **Selecione Editar** para alterar o posicionamento do azulejo. Selecione e arraste o azulejo para a posição e, em seguida, selecione **Fazer a personalização**.

## <a name="add-logs-query"></a>Adicionar consulta de Logs

O Azure Application Insights Logs fornece uma linguagem de consulta rica que lhe permite analisar todos os dados recolhidos Insights de Aplicação. Tal como gráficos e outras vistas, pode adicionar a saída de uma consulta de registos ao seu painel de instrumentos.

1. Selecione o seu recurso **Application Insights** no ecrã principal.
2. Selecione **Registos** à esquerda em "monitorização" para abrir o separador 'Registos'.
3. Escreva a consulta seguinte, que devolve as 10 páginas mais pedidas e a respetiva contagem de pedidos:

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. Selecione **Executar** para validar os resultados da consulta.
5. Selecionar o ícone afixar ![Ícone de afixar](media/tutorial-app-dashboards/pushpin.png) e selecione o nome do seu painel de instrumentos.

5. Antes de voltar ao painel, adicione outra consulta, mas faça-a como um gráfico para que veja as diferentes formas de visualizar uma consulta de registos num dashboard. Comece com a consulta seguinte que resume as 10 principais operações com a maioria das exceções.

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. Selecione **Gráfico** e, em seguida, mude para **Anel** para visualizar os resultados.

    [![Gráfico de donut com consulta acima](media/tutorial-app-dashboards/logs-doughnut.png)](media/tutorial-app-dashboards/logs-doughnut.png#lightbox)

6. Selecionar o ícone afixar ![Ícone de afixar](media/tutorial-app-dashboards/pushpin.png) no topo à direita para fixar a tabela no seu painel de instrumentos e, em seguida, voltar ao seu painel de instrumentos.
7. Os resultados das consultas estão agora adicionados ao dashboard no formato que selecionou. Selecione e arraste cada um para a posição e, em seguida, selecione **Fazer a personalização**.
8. Selecione o ícone do lápis ![Ícone de lápis](media/tutorial-app-dashboards/pencil.png) em cada título para dar-lhes um título descritivo.

## <a name="share-dashboard"></a>Partilhar dashboard

1. No topo do painel, **selecione Partilhar** para publicar as suas alterações.
2. Opcionalmente, pode definir utilizadores específicos que devem ter acesso ao dashboard. Para obter mais informações, consulte [os dashboards Share Azure utilizando o controlo de acesso baseado em funções Azure](../../azure-portal/azure-portal-dashboard-share-access.md).
3. Selecione **Publicar**.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a criar dashboards personalizados, veja a restante documentação do Application Insights, incluindo um caso prático.

> [!div class="nextstepaction"]
> [Diagnósticos avançados](../app/devops.md)
