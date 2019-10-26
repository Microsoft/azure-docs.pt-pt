---
title: Criar dashboards personalizados no Azure Application Insights | Microsoft Docs
description: Tutorial para criar dashboards de KPI personalizados com o Azure Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 07/3/2019
ms.custom: mvc
ms.openlocfilehash: 47d8eaff5f154e198c277ec2b63a2d09e66f7180
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900512"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Criar dashboards de KPI personalizados com o Azure Application Insights

Pode criar múltiplos dashboards no portal do Azure em que cada um inclui mosaicos que visualizam os dados de vários recursos do Azure entre diferentes grupos de recursos e subscrições.  Pode afixar diferentes gráficos e vistas a partir do Azure Application Insights para criar dashboards personalizados que fornecem uma visão geral do estado de funcionamento e do desempenho da aplicação. Este tutorial orienta-o através da criação de um dashboard personalizado que inclui vários tipos de dados e visualizações a partir do Azure Application Insights.  Saiba como:

> [!div class="checklist"]
> * Criar um dashboard personalizado no Azure
> * Adicionar um mosaico a partir da Galeria de Mosaicos
> * Adicionar métricas padrão no Application Insights ao dashboard
> * Adicionar um gráfico de métricas personalizado no Application Insights ao dashboard
> * Adicionar os resultados de uma consulta de logs (análise) ao painel



## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Implemente uma aplicação .NET no Azure e [ative o Application Insights SDK](../../azure-monitor/app/asp-net.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-new-dashboard"></a>Criar um novo dashboard
Um único dashboard pode conter recursos de várias aplicações, grupos de recursos e subscrições.  Comece o tutorial ao criar um novo dashboard para a sua aplicação.  

1. No painel painel, selecione **novo painel**.

   ![Novo dashboard](media/tutorial-app-dashboards/1newdashboard.png)

1. Escreva um nome para o dashboard.
1. Veja a **Galeria de Mosaicos** para obter uma variedade de mosaicos que pode adicionar ao dashboard.  Além de adicionar blocos da galeria, você pode fixar gráficos e outras exibições diretamente do Application Insights ao painel.
1. Localize o mosaico **Markdown** e arraste-o para o dashboard.  Esse bloco permite que você adicione texto formatado em redução, o que é ideal para adicionar texto descritivo ao seu painel.
1. Adicione texto às propriedades do mosaico e redimensione-o na tela do dashboard.
    
    ![Editar mosaico Markdown](media/tutorial-app-dashboards/2dashboard-text.png)

1. Clique em **personalização concluída** na parte superior da tela para sair do modo de personalização de bloco.

## <a name="add-health-overview"></a>Adicionar descrição geral do estado de funcionamento
Um painel com texto estático não é muito interessante. agora, adicione um bloco de Application Insights para mostrar informações sobre seu aplicativo.  Pode adicionar mosaicos do Application Insights a partir da Galeria de Mosaicos ou afixá-los diretamente a partir dos ecrãs do Application Insights.  Isto permite-lhe configurar gráficos e vistas com os quais já está familiarizado antes de afixá-los ao dashboard.  Comece por adicionar a descrição geral do estado de funcionamento padrão para a sua aplicação.  Isto não requer nenhuma configuração e permite uma personalização mínima no dashboard.


1. Selecione seu recurso de **Application insights** na tela inicial.
2. No painel **visão geral** , clique no ícone de pino ![ícone de pino](media/tutorial-app-dashboards/pushpin.png) para adicionar o bloco ao último painel que você estava exibindo.  
 
3. No canto superior direito, será exibida uma notificação de que seu bloco foi fixado em seu painel. Clique em **fixado no painel** na notificação para retornar ao painel ou use o painel painel.
4. Esse bloco agora é adicionado ao seu painel. Selecione **Editar** para alterar o posicionamento do bloco. Clique e arraste-o para a posição e clique em **personalização concluída**. O dashboard tem agora um mosaico algumas informações úteis.

    ![Dashboard com linha cronológica de Descrição Geral](media/tutorial-app-dashboards/4dashboard-edit.png)

## <a name="add-custom-metric-chart"></a>Adicionar gráfico de métricas personalizado
O painel **Métricas** permite-lhe criar um gráfico de uma métrica recolhida pelo Application Insights ao longo do tempo com filtros e agrupamentos opcionais.  Tal como tudo no Application Insights, pode adicionar este gráfico ao dashboard.  É necessário efetuar primeiro uma pequena personalização.

1. Selecione o recurso de **Application insights** na tela inicial.
1. Selecione **Métricas**.  
2. Já foi criado um gráfico vazio e está a ser-lhe pedido para adicionar uma métrica.  Adicione uma métrica ao gráfico e, opcionalmente, adicione um filtro e um agrupamento.  O exemplo abaixo mostra o número de pedidos de servidor agrupados com êxito.  Isto fornece uma vista de execução de pedidos com e sem êxito.

    ![Adicionar métrica](media/tutorial-app-dashboards/metrics.png)

4. Selecione **fixar no painel** à direita. Esta ação adiciona o mosaico ao último dashboard em que estava a trabalhar.

3.  No canto superior direito, será exibida uma notificação de que seu bloco foi fixado em seu painel. Clique em **fixado no painel** na notificação para retornar ao seu painel ou use a folha painel.

4. Esse bloco agora é adicionado ao seu painel. Selecione **Editar** para alterar o posicionamento do bloco. Clique e arraste-o para a posição e clique em **personalização concluída**.

## <a name="add-logs-analytics-query"></a>Adicionar consulta de logs (análise)
Os logs do Aplicativo Azure insights (análise) fornecem uma linguagem de consulta avançada que permite que você analise todos os dados coletados Application Insights. Assim como os gráficos e outros modos de exibição, você pode adicionar a saída de uma consulta de logs ao seu painel.

Como os logs do Application insights do Azure (análise) são um serviço separado, você precisa compartilhar seu painel para incluir uma consulta de logs. Ao compartilhar um painel do Azure, você o publica como um recurso do Azure, que pode disponibilizá-lo para outros usuários e recursos.  

1. Na parte superior do ecrã do dashboard, clique em **Partilhar**.

    ![Publicar o dashboard](media/tutorial-app-dashboards/8dashboard-share.png)

2. Mantenha o mesmo **Nome do dashboard** e selecione o **Nome da Subscrição** para partilhar o dashboard.  Clique em **Publicar**.  O dashboard está agora disponível para outros serviços e subscrições.  Opcionalmente, pode definir utilizadores específicos que devem ter acesso ao dashboard.
1. Selecione o recurso de **Application insights** na tela inicial.
2. Clique em **logs (análise)** à esquerda em monitoramento para abrir o portal de logs (análise).
3. Escreva a consulta seguinte, que devolve as 10 páginas mais pedidas e a respetiva contagem de pedidos:

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. Clique em **executar** para validar os resultados da consulta.
5. Clique no ícone de pino ![Ícone de pino](media/tutorial-app-dashboards/pushpin.png) e selecione o nome do seu painel. O motivo pelo qual essa opção tem selecionado um painel diferente das etapas anteriores em que o último painel foi usado é porque o console de logs (Analytics) é um serviço separado e precisa selecionar entre todos os painéis compartilhados disponíveis.

5. Antes de voltar para o painel, adicione outra consulta, mas desta vez renderizá-la como um gráfico para que você veja as diferentes maneiras de visualizar uma consulta de logs em um painel. Comece com a consulta seguinte que resume as 10 principais operações com a maioria das exceções.

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. Selecione **Gráfico** e, em seguida, mude para **Anel** para visualizar os resultados.

    ![Gráfico de logs (análise)](media/tutorial-app-dashboards/11querychart.png)

6. Clique no ícone de pino ![Ícone de pino](media/tutorial-app-dashboards/pushpin.png) no canto superior direito para fixar o gráfico ao painel e, desta vez, selecione o link para retornar ao seu painel.
4. Os resultados das consultas estão agora adicionados ao dashboard no formato que selecionou.  Clique e arraste cada posição para e clique em **personalização concluída**.
5. Selecione o ícone de lápis ![Ícone de lápis](media/tutorial-app-dashboards/pencil.png) em cada título para dar a eles um título descritivo.

5. Selecione **compartilhar** para republicar suas alterações em seu painel que agora inclui uma variedade de gráficos e visualizações de Application insights.


## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu a criar dashboards personalizados, veja a restante documentação do Application Insights, incluindo um caso prático.

> [!div class="nextstepaction"]
> [Diagnósticos avançados](../../azure-monitor/app/devops.md)
