---
title: Ligue o seu ambiente ao Power BI - Azure Time Series Insights [ Microsoft Docs
description: Aprenda a ligar os Insights da Série De Tempo Azure ao Power BI para partilhar, gráfico e exibir dados em toda a sua organização.
author: deepakpalled
ms.author: dpalled
manager: cshankar
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 22053bdc3a9836b76aa92303234a095cac6448ef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75863847"
---
# <a name="visualize-data-from-time-series-insights-in-power-bi"></a>Visualizar dados da Time Series Insights em Power BI

A Azure Time Series Insights é uma plataforma para armazenar, gerir, consultar e visualizar dados da série de tempo na nuvem. [O Power BI](https://powerbi.microsoft.com) é uma ferramenta de análise de negócios com capacidades de visualização ricas que lhe permite partilhar insights e resultados em toda a sua organização. Ambos os serviços podem agora ser integrados para obter o melhor das capacidades de visualização inerentes tanto da Time Series Insights como da Power BI' s.

Vai aprender a:

* Conecte insights da série de tempo para power BI utilizando o conector de nuvem
* Crie visuais com os seus dados no Power BI
* Publique o relatório para a Power BI e partilhe com o resto da sua organização

No final, aprenderá a visualizar os dados da série de tempo através do Azure Time Series Insights e a realçá-lo com a forte visualização de dados e capacidades de partilha fáceis do Power BI.

Certifique-se de que se inscreva para uma [subscrição Azure gratuita](https://azure.microsoft.com/free/) se ainda não tiver uma.

## <a name="prerequisites"></a>Pré-requisitos

* Descarregue e instale a versão mais recente do [Power BI Desktop](https://powerbi.microsoft.com/downloads/)
* Ter ou criar uma instância de [pré-visualização](time-series-insights-update-how-to-manage.md) de Insights da Série De Tempo Azure

> [!IMPORTANT]
> O conector Power BI é suportado atualmente em ambientes *pay-as-you-go* da Time Series Insights configurados para **Warm Store**.

## <a name="connect-data-from-time-series-insights-to-power-bi"></a>Conecte dados de Insights da Série De Tempo para O Bi de Potência

Para ligar o ambiente time series Insights ao Power BI, siga estes passos:

1. Explorador de Insights da Série de Tempo Aberto
1. Dados de exportação como consulta ou como dados brutos
1. Ambiente de trabalho open power BI
1. Carga de consulta personalizada

### <a name="export-data-into-power-bi-desktop"></a>Dados de exportação para o ambiente de trabalho power BI

Para começar:

1. Abra o Time Series Insights Preview Explorer e cuide dos seus dados.
1. Uma vez criada uma vista com a qual está satisfeito, navegue para o menu **dropdown mais ações** e selecione **Connect to Power BI**.

    [![Time Series Insights Preview Explorer export](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. Coloque os seus parâmetros dentro deste separador:

   1. Especifique um prazo relativo para visualizar. Se está satisfeito com a sua visão existente, deixe isto como **prazo existente.**
   
   1. Escolha entre Eventos **Agregados** e **Crus.** 
   
       > [!NOTE]
       > Pode sempre agregar os seus dados mais tarde no Power BI, mas não pode voltar a dados brutos após a agregação. 
       
       > [!NOTE]
       > Há um limite de contagem de eventos de 100 K para dados de nível de evento bruto.

       [![Ligar](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. Se não configurar a sua instância de Insights da Série Time para **a Warm Store,** receberá um aviso.

       [![Aviso de loja quente](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > Pode configurar a sua instância existente para **a Warm Store** no portal Azure.

1. Selecione **Copiar consulta para clipboard**.
1. Agora, lança o Power BI Desktop.
1. No Power BI Desktop no separador **Home,** selecione **Obter Dados** no canto superior esquerdo e, em seguida, **Mais**.

    [![Dropdown em casa](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. Procure insights da **Série de Tempo,** selecione Insights da **Série de Tempo Azure (Beta)** e, em seguida, **conecte**.

    [![Ligar Power BI a Time Series Insights](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    Em alternativa, navegue para o separador **Azure,** selecione **Azure Time Series Insights (Beta)** e, em seguida, **Ligue**.
    
1. Uma caixa de diálogo de mensagens mostrará pedindo permissão para se conectar a recursos de terceiros. Selecione **continuar**.

    [![Escolha Criar consulta personalizada](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. No menu dropdown em Data **Source,** escolha **Criar consulta personalizada**. Colá-cola da sua prancheta no campo opcional de **consulta personalizada (opcional)** abaixo, em seguida, prima **OK**.

    [![Passe na consulta personalizada e selecione OK](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. A tabela de dados vai agora carregar. Pressione **a carga** para carregar no Power BI.

    [![Reveja os dados carregados na tabela e selecione Load](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

Se tiver concluído estes passos, avance para a secção seguinte.

## <a name="create-a-report-with-visuals"></a>Criar um relatório com elementos visuais

Agora que importaste os dados para o Power BI, está na altura de construir um relatório com visuais.

1. Do lado esquerdo da janela, certifique-se de que selecionou a vista **report.**

    [![Selecione a Visualização de Relatórios](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1.  Na coluna **Visualizações,** selecione o seu visual de eleição. Por exemplo, selecione **gráfico de linha**. Isto irá adicionar um gráfico de linha em branco à sua tela.
 
1.  Na lista **Fields,** selecione **timestamp** e arraste-o para o campo **Axis** para exibir itens ao longo do eixo X.

1.  Mais uma vez, na lista **Fields,** selecione **TimeSeriesId** e arraste-o para o campo **Valores** para exibir itens ao longo do eixo Y.

    [![Criar um gráfico de linhas](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1.  Para adicionar outro gráfico à sua tela, selecione em qualquer lugar da tela fora do gráfico de linha e repita este processo.

    [![Criar gráficos adicionais para partilhar](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

Assim que tiver criado o seu relatório, pode publicá-lo nos Serviços de Informação do Power BI.

## <a name="advanced-editing"></a>Edição avançada

Se já carregou um conjunto de dados no Power BI mas gostaria de modificar a consulta (como os parâmetros de data/hora ou ID do ambiente), pode fazê-lo através da funcionalidade Advanced Editor do Power BI. Consulte a [documentação](https://docs.microsoft.com/power-bi/desktop-query-overview) do Power BI para saber mais.

Como uma visão geral:

1. No Power BI Desktop, selecione **Editar Consultas**.
1. Editor **Avançado**de Imprensa.

    [![Editar consultas no Editor Avançado](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. Modificar a carga útil JSON conforme desejado.
1. Selecione **Done** e, **em seguida, Feche & Aplicar** dentro da janela do editor de consulta de **potência**.

A interface irá agora refletir as alterações desejadas que aplicou.  

## <a name="next-steps"></a>Passos Seguintes

* Leia sobre os conceitos de [conector Power BI](https://docs.microsoft.com/power-bi/desktop-query-overview) para Insights da Série De Tempo Azure.

* Saiba mais sobre o ambiente de [trabalho power BI](https://docs.microsoft.com/power-bi/desktop-query-overview).

* Leia [a Série de Tempo Insights GA Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart) e Time Series Insights Preview [Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).
