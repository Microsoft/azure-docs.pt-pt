---
title: Conecte seu ambiente ao Power BI-Azure Time Series Insights | Microsoft Docs
description: Saiba como conectar Azure Time Series Insights a Power BI para compartilhar, exibir gráficos e mostrar dados em sua organização.
author: deepakpalled
ms.author: dpalled
manager: cshankar
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 1f6b753052b882b75884503f8c05f34b8cead821
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749377"
---
# <a name="visualize-data-from-time-series-insights-in-power-bi"></a>Visualizar dados de Time Series Insights no Power BI

Azure Time Series Insights é uma plataforma para armazenar, gerenciar, consultar e Visualizar dados de série temporal na nuvem. [Power bi](https://powerbi.microsoft.com) é uma ferramenta de análise de negócios com recursos de visualização avançados que permite que você compartilhe informações e resultados em toda a sua organização. Ambos os serviços agora podem ser integrados para obter o melhor dos dois Time Series Insights recursos de visualização inerentes, bem como Power BI.

Vai aprender a:

* Conectar Time Series Insights ao Power BI usando o conector de nuvem
* Criar visuais com seus dados no Power BI
* Publicar o relatório para Power BI e compartilhá-lo com o restante da sua organização

Ao final, você aprenderá como Visualizar dados de série temporal por meio de Azure Time Series Insights e aprimorá-los com a visualização de dados forte e recursos de compartilhamento fácil de Power BI.

Lembre-se de inscrever-se para uma [assinatura gratuita do Azure](https://azure.microsoft.com/free/) se você ainda não tiver uma.

## <a name="prerequisites"></a>Pré-requisitos

* Baixe e instale a versão mais recente do [Power bi desktop](https://powerbi.microsoft.com/downloads/)
* Ter ou criar uma [instância de visualização de Azure Time Series insights](time-series-insights-update-how-to-manage.md)

> [!IMPORTANT]
> Há suporte para o conector de Power BI no Time Series Insights visualização dos ambientes pré- *pago* configurados para **armazenamento quente**.

## <a name="connect-data-from-time-series-insights-to-power-bi"></a>Conectar dados de Time Series Insights para Power BI

Para conectar seu ambiente de Time Series Insights ao Power BI, siga estas etapas:

1. Abrir Time Series Insights Explorer
1. Exportar dados como uma consulta ou dados brutos
1. Abrir Power BI Desktop
1. Carregar da consulta personalizada

### <a name="export-data-into-power-bi-desktop"></a>Exportar dados para o Power BI desktop

Para começar:

1. Abra o Time Series Insights Gerenciador de visualização e visualize seus dados.
1. Depois de criar uma exibição com a qual você está satisfeito, navegue até o menu suspenso **mais ações** e clique em **conectar-se a Power bi**.

    [![Time Series Insights exportar Gerenciador de visualização](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. Defina os parâmetros nesta guia:

   1. Especifique um período de tempo relativo a ser exibido. Se você estiver satisfeito com o modo de exibição existente, deixe isso como um **período de tempo existente**.
   
   1. Escolha entre eventos **agregados** e **brutos**. 
   
       > [!NOTE]
       > Você sempre pode agregar seus dados posteriormente em Power BI, mas não pode reverter para dados brutos após a agregação. 
       
       > [!NOTE]
       > Há um limite de contagem de eventos de 100-K para dados brutos de nível de evento.

       [![conectar](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. Se você não tiver configurado sua instância de Time Series Insights para **armazenamento quente**, receberá um aviso.

       [![aviso de armazenamento quente](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > Você pode configurar sua instância existente para **armazenamento quente** na portal do Azure.

1. Selecione **copiar consulta para a área de transferência**.
1. Agora, inicie o Power BI Desktop.
1. Em Power BI Desktop na guia **início** , selecione **obter dados** no canto superior esquerdo e, em seguida, **mais**.

    [menu suspenso ![Home](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. Procure **Time Series insights**, selecione **Azure Time Series insights (beta)** e **Conecte-se**.

    [![conectar Power BI ao Time Series Insights](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    Como alternativa, navegue até a guia **Azure** , selecione **Azure Time Series insights (beta)** e **Conecte-se**.
    
1. Uma caixa de diálogo de mensagem será exibida solicitando permissão para se conectar a recursos de terceiros. Selecione **continuar**.

    [![escolher criar consulta personalizada](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. No menu suspenso, em **fonte de dados**, escolha **criar consulta personalizada**. Cole da área de transferência no campo opcional de **consulta personalizada (opcional)** abaixo e pressione **OK**.

    [![passar na consulta personalizada e selecionar OK](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. A tabela de dados será carregada agora. Pressione **carregar** para carregar no Power bi.

    [![examinar os dados carregados na tabela e selecionar carregar](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

Se você tiver concluído essas etapas, pule para a próxima seção.

## <a name="create-a-report-with-visuals"></a>Criar um relatório com elementos visuais

Agora que você importou os dados para Power BI, é hora de criar um relatório com visuais.

1. No lado esquerdo da janela, verifique se você selecionou o modo de exibição de **relatório** .

    [![selecionar o modo de exibição de relatório](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1.  Na coluna **visualizações** , selecione seu visual de sua escolha. Por exemplo, selecione **gráfico de linhas**. Isso adicionará um gráfico de linhas em branco à tela.
 
1.  Na lista **campos** , selecione **carimbo de data/hora** e arraste-o para o campo **eixo** para exibir itens ao longo do eixo X.

1.  Novamente, na lista **campos** , selecione **timeseriesid** e arraste-o para o campo **valores** para exibir itens ao longo do eixo Y.

    [![criar um gráfico de linhas](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1.  Para adicionar outro gráfico à tela, clique em qualquer lugar na tela fora do gráfico de linhas e repita esse processo.

    [![criar gráficos adicionais para compartilhar](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

Depois de criar o relatório, você poderá publicá-lo no Power BI Reporting Services.

## <a name="advanced-editing"></a>Edição avançada

Se você já carregou um conjunto de dados em Power BI mas deseja modificar a consulta (como os parâmetros de ID de ambiente ou de data/hora ou), você pode fazer isso por meio da funcionalidade de Editor Avançado do Power BI. Consulte a [documentação do Power bi](https://docs.microsoft.com/power-bi/desktop-query-overview) para saber mais.

Como uma visão geral:

1. Em Power BI Desktop, selecione **editar consultas**.
1. Pressione **Editor avançado**.

    [![editar consultas no Editor Avançado](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. Modifique a carga JSON conforme desejado.
1. Selecione **concluído** e **feche & aplicar** na janela do **Editor de Power Query**.

Você deve ver as alterações desejadas aplicadas.  

## <a name="next-steps"></a>Próximos Passos

* Leia sobre os [conceitos do conector de Power bi](https://docs.microsoft.com/power-bi/desktop-query-overview) para Azure Time Series insights.

* Saiba mais sobre o [Power bi desktop](https://docs.microsoft.com/power-bi/desktop-query-overview).

* Consulte [Time Series insights GA Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart) e [Time Series insights Preview Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).
