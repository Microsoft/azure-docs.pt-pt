---
title: Ligue o seu ambiente ao Power BI - Azure Time Series Insights Microsoft Docs
description: Saiba como ligar o Azure Time Series Insights ao Power BI para partilhar, traçar e exibir dados em toda a sua organização.
author: deepakpalled
ms.author: dpalled
manager: cshankar
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 0f51f6867d773c906d2df24c7e61ad897a4304f6
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84258896"
---
# <a name="visualize-data-from-time-series-insights-in-power-bi"></a>Visualizar dados da Time Series Insights no Power BI

Azure Time Series Insights é uma plataforma para armazenar, gerir, consultar e visualizar dados de séries de tempo na nuvem. [Power BI](https://powerbi.microsoft.com) é uma ferramenta de análise de negócios com ricas capacidades de visualização que lhe permite partilhar insights e resultados em toda a sua organização. Ambos os serviços podem agora ser integrados para obter o melhor das capacidades inerentes de visualização da Time Series Insights, bem como do Power BI' s.

Vai aprender a:

* Conecte insights da série de tempo para power bi usando o conector de nuvem
* Crie visuais com os seus dados em Power BI
* Publique o relatório no Power BI e partilhe com o resto da sua organização

No final, aprenderás a visualizar dados de séries de tempo através do Azure Time Series Insights e a realçá-lo com a forte visualização de dados e capacidades de partilha fácil do Power BI.

Certifique-se de se inscrever para uma [subscrição gratuita do Azure](https://azure.microsoft.com/free/) se ainda não tiver uma.

## <a name="prerequisites"></a>Pré-requisitos

* Descarregue e instale a versão mais recente do [Power BI Desktop](https://powerbi.microsoft.com/downloads/)
* Ter ou criar uma [instância de visualização de insights da série de tempo Azure](time-series-insights-update-how-to-manage.md)

> [!IMPORTANT]
> O conector Power BI é atualmente suportado em ambientes de pré-visualização de visualização de tempo *como-você-go* configurados para **Warm Store**.

## <a name="connect-data-from-time-series-insights-to-power-bi"></a>Conecte os dados da Time Series Insights ao Power BI

Para ligar o ambiente de Insights de Séries Tempos ao Power BI, siga estes passos:

1. Explorador de insights da série de tempo aberto
1. Dados de exportação como consulta ou como dados brutos
1. Ambiente de trabalho open power BI
1. Carga da Consulta Personalizada

### <a name="export-data-into-power-bi-desktop"></a>Dados de exportação para o ambiente de trabalho Power BI

Para começar:

1. Abra o Time Series Insights Preview Explorer e cuide dos seus dados.
1. Depois de ter criado uma vista com a qual está satisfeito, navegue para o menu de abandono **de ações Mais** e selecione **Connect to Power BI**.

    [![Previsão de visualização de previsão de tempo Exportação Explorer exportação](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. Desa parte dos seus parâmetros dentro deste separador:

   1. Especifique um prazo relativo para visualizar. Se está satisfeito com a sua visão existente, deixe-o como **o prazo existente.**
   
   1. Escolha entre **Eventos Agregados** e **Crus.** 
   
       > [!NOTE]
       > Pode sempre agregar os seus dados mais tarde no Power BI, mas não pode voltar a dados brutos após a agregação. 
       
       > [!NOTE]
       > Existe um limite de contagem de eventos de 100 K para dados de nível de Evento Bruto.

       [![Ligar](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. Se não tiver configurado a sua instância de Insights de Séries Temporizadas para **a Warm Store,** receberá um aviso.

       [![Aviso de loja quente](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > Pode configurar a sua instância existente para **a Warm Store** no portal Azure.

1. Selecione **a consulta de cópia para a área de transferência**.
1. Agora, lançar Power BI Desktop.
1. No Power BI Desktop no separador **'Casa',** **selecione Obter Dados** no canto superior esquerdo e, em seguida, **Mais**.

    [![Dropdown em casa](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. Search for **Time Series Insights**, selecione **Azure Time Series Insights (Beta)** e, em seguida, **Connect**.

    [![Ligue o Power BI a insights da série de tempo](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    Em alternativa, navegue no separador **Azure,** selecione **Azure Time Series Insights (Beta)** e, em seguida, **Conecte-se**.
    
1. Uma caixa de diálogo de mensagens será exibida pedindo permissão para se ligar a recursos de terceiros. Selecione **continuar**.

    [![Escolha Criar Consulta Personalizada](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. No menu suspenso em **Data Source**, escolha Criar **consulta personalizada**. Cole da sua prancheta no campo de consulta personalizada opcional **(opcional)** abaixo e, em seguida, prima **OK**.

    [![Passe na consulta personalizada e selecione OK](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. A tabela de dados vai agora carregar. Prima **a carga** para carregar em Power BI.

    [![Reveja os dados carregados na tabela e selecione Load](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

Se tiver completado estes passos, avance para a secção seguinte.

## <a name="create-a-report-with-visuals"></a>Criar um relatório com elementos visuais

Agora que importou os dados para o Power BI, está na hora de construir um relatório com visuais.

1. No lado esquerdo da janela, certifique-se de que selecionou a vista **'Relatório'.**

    [![Selecione a vista de relatório](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1.  Na coluna **Visualizações,** selecione o seu visual de escolha. Por exemplo, selecione **gráfico de linha**. Isto irá adicionar um gráfico de linha em branco à sua tela.

1.  Na lista **Fields,** selecione **_Timestamp** e arraste-o para o campo **eixo** para exibir itens ao longo do eixo X. Certifique-se de que muda para **_Timestamp** para o valor do **eixo** (predefinição é **hierarquia de data).**

    [![Selecione a vista de relatório](media/how-to-connect-power-bi/select-timestamp.png)](media/how-to-connect-power-bi/select-timestamp.png#lightbox)

1.  Mais uma vez, na lista **Fields,** selecione **TimeSeriesId** e arraste-o para o campo **Valores** para exibir itens ao longo do eixo Y.

    [![Criar um gráfico de linhas](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1.  Para adicionar outro gráfico à sua tela, selecione qualquer lugar na tela fora da tabela de linha e repita este processo.

    [![Criar gráficos adicionais para partilhar](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

Uma vez criado o seu relatório, pode publicá-lo nos Serviços de Reporte do Power BI.

## <a name="advanced-editing"></a>Edição avançada

Se já carregou um conjunto de dados no Power BI mas gostaria de modificar a consulta (como os parâmetros de data/hora ou ID do ambiente), pode fazê-lo através da funcionalidade De Redação Avançada do Power BI. Consulte a [documentação power BI](https://docs.microsoft.com/power-bi/desktop-query-overview) para saber mais.

Como uma visão geral:

1. No Power BI Desktop, **selecione Editar Consultas**.
1. **Editor Avançado de Imprensa.**

    [![Editar consultas no Editor Avançado](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. Modifique a carga útil JSON conforme desejado.
1. Selecione **Feito** e, em seguida, **Feche & Aplicar** dentro da janela do editor de consulta de **energia**.

A interface irá agora refletir as alterações desejadas que aplicou.  

## <a name="next-steps"></a>Passos Seguintes

* Leia sobre [os conceitos de conector Power BI](https://docs.microsoft.com/power-bi/desktop-query-overview) para Azure Time Series Insights.

* Saiba mais sobre [o ambiente de trabalho Power BI](https://docs.microsoft.com/power-bi/desktop-query-overview).

* Leia [o Time Series Insights GA Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart) e o Time Series Insights Preview [Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).
