---
title: Ligue o seu ambiente ao Power BI - Azure Time Series Insights Microsoft Docs
description: Saiba como ligar o Azure Time Series Insights ao Power BI para partilhar, traçar e exibir dados em toda a sua organização.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 07e79dbde142400677901ee02903144f9a42cd6b
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740769"
---
# <a name="visualize-data-from-azure-time-series-insights-in-power-bi"></a>Visualizar dados da Azure Time Series Insights em Power BI

Azure Time Series Insights é uma plataforma para armazenar, gerir, consultar e visualizar dados de séries de tempo na nuvem. [Power BI](https://powerbi.microsoft.com) é uma ferramenta de análise de negócios com ricas capacidades de visualização que lhe permite partilhar insights e resultados em toda a sua organização. Ambos os serviços podem agora ser integrados permitindo-lhe aumentar a poderosa análise do Azure Time Series Insights com a forte visualização de dados e capacidades de partilha fácil de Power BI.

Vai aprender a:

* Conecte insights da série de tempo Azure ao Power BI utilizando o conector Azure Time Series Insights
* Crie visuais com os seus dados da série de tempo no Power BI
* Publique o relatório no Power BI e partilhe com o resto da sua organização


## <a name="prerequisites"></a>Pré-requisitos

* Inscreva-se para uma [subscrição gratuita do Azure](https://azure.microsoft.com/free/) se ainda não tiver uma.
* Descarregue e instale a versão mais recente do [Power BI Desktop](https://powerbi.microsoft.com/downloads/)
* Ter ou criar um [ambiente Azure Time Series Insights Gen2](./how-to-provision-manage.md)

Por favor, reveja [as políticas de acesso](./concepts-access-policies.md) ao ambiente e certifique-se de que tem acesso direto ou acesso ao ambiente Azure Time Series Insights Gen2. 

> [!IMPORTANT]
> * Descarregue e instale a versão mais recente do [Power BI Desktop](https://powerbi.microsoft.com/downloads/). Para acompanhar os passos deste artigo, certifique-se de que tem pelo menos a versão de dezembro de 2020 (2.88.321.0) do Power BI Desktop instalada. 

## <a name="connect-data-from-azure-time-series-insights-to-power-bi"></a>Conecte os dados da Azure Time Series Insights ao Power BI

### <a name="1-export-data-into-power-bi-desktop"></a>1. Dados de exportação para o ambiente de trabalho Power BI

Para começar:

1. Abra o Azure Time Series Insights Gen2 Explorer e cuide dos seus dados. Estes são os dados que serão exportados para o Power BI.
1. Depois de ter criado uma vista com a qual está satisfeito, navegue para o menu de abandono **de ações Mais** e selecione **Connect to Power BI**.

    [![Azure Time Series Insights Gen2 Explorer exportação](media/how-to-connect-power-bi/export-from-explorer.jpg)](media/how-to-connect-power-bi/export-from-explorer.jpg#lightbox)

1. Desaça os seus parâmetros para exportação:

   * **Formato de dados**: Escolha se pretende exportar **dados agregados** ou **eventos brutos** para o Power BI. 

       > [!NOTE]
       > * Se exportar eventos crus, pode agregar esses dados mais tarde no Power BI. No entanto, se exportar dados agregados, não pode reverter para dados brutos no Power BI. 
       > * Há um limite de contagem de eventos de 250.000 para dados de nível de Eventos Brutos.

   * **Intervalo de tempo**: Escolha se gostaria de ver um intervalo de tempo **fixo** ou os dados **mais recentes** no Power BI. Escolher o intervalo de tempo fixo significa que os dados no período de pesquisa que cartografou serão exportados para o Power BI. Escolher o último intervalo de tempo significa que o Power BI irá recolher os dados mais recentes para o período de pesquisa que escolheu (por exemplo, se traçar uma hora de dados e escolher a definição "mais recente", o Power BI Connector irá sempre fazer consultas para as últimas 1 hora de dados.)
  
   * **Tipo de loja**: Escolha se pretende executar a sua consulta selecionada contra **a Warm Store** ou a Cold **Store**. 

    > [!TIP]
    > * O Azure Time Series Insights Explorer selecionará automaticamente os parâmetros recomendados, dependendo dos dados que escolheu para exportar. 

1. Depois de configurar as suas definições, selecione **copy consulta para a área de transferência**.

    [![Azure Time Series Insights Explorer exporta modal](media/how-to-connect-power-bi/choose-explorer-parameters.jpg)](media/how-to-connect-power-bi/choose-explorer-parameters.jpg#lightbox)

2. Inicie o Power BI Desktop.
   
3. No Power BI Desktop no separador **'Casa',** **selecione Obter Dados** no canto superior esquerdo e, em seguida, **Mais**.

    [![Obter dados no Power BI](media/how-to-connect-power-bi/get-data-power-bi.jpg)](media/how-to-connect-power-bi/get-data-power-bi.jpg#lightbox)

4. Search for **Azure Time Series Insights**, selecione **Azure Time Series Insights (Beta)** e, em seguida, **Connect**.

    [![Ligue o Power BI a Azure Time Series Insights](media/how-to-connect-power-bi/select-tsi-connector.jpg)](media/how-to-connect-power-bi/select-tsi-connector.jpg#lightbox)

    Em alternativa, navegue no separador **Azure,** selecione **Azure Time Series Insights (Beta)** e, em seguida, **Conecte-se**.

5. Cole a consulta que copiou do Azure Time Series Insights Explorer no campo **de consulta personalizada** e, em seguida, prima **OK**.

    [![Cole na consulta personalizada e selecione OK](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

6.  A tabela de dados vai agora carregar. Prima **a carga** para carregar em Power BI. Se desejar fazer quaisquer transformações nos dados, pode fazê-lo agora clicando em **"Transform Data".** Também pode transformar os seus dados depois de carregado.

    [![Reveja os dados na tabela e selecione Carregar](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

## <a name="create-a-report-with-visuals"></a>Criar um relatório com elementos visuais

Agora que importou os dados para o Power BI, está na hora de construir um relatório com visuais.

1. No lado esquerdo da janela, certifique-se de que selecionou a vista **'Relatório'.**

    [![A screenshot mostra o ícone da visualização do relatório.](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1. Na coluna **Visualizações,** selecione o seu visual de escolha. Por exemplo, selecione **gráfico de linha**. Isto irá adicionar um gráfico de linha em branco à sua tela.

1.  Na lista **Fields,** selecione **_Timestamp** e arraste-o para o campo **do eixo** para mostrar tempo ao longo do eixo X. Certifique-se de que muda para **_Timestamp** para o valor do **eixo** (predefinição é **hierarquia de data).**

    [![Selecione _Timestamp](media/how-to-connect-power-bi/select-timestamp.png)](media/how-to-connect-power-bi/select-timestamp.png#lightbox)

1.  Mais uma vez, na lista **Fields,** selecione a variável que deseja traçar e arraste-a para o campo **Valores** para mostrar valores ao longo do eixo Y. Selecione o seu valor de ID séries de tempo e arraste-o para o campo **Legend** para criar várias linhas na tabela, uma por Time Series ID. Isto irá tornar uma visão semelhante à fornecida pelo Azure Time Series Insights Explorer! 

    [![Criar um gráfico básico de linha](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1. Para adicionar outro gráfico à sua tela, selecione qualquer lugar na tela fora da tabela de linha e repita este processo.

    [![Criar gráficos adicionais para partilhar](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

Uma vez criado o seu relatório, pode publicá-lo na Power BI Reporting Services e partilhar com outros da sua organização.

## <a name="advanced-editing"></a>Edição avançada
Se já carregou um conjunto de dados no Power BI mas gostaria de modificar a consulta (como os parâmetros de data/hora ou ID do ambiente), pode fazê-lo através da funcionalidade De Redação Avançada do Power BI. Consulte a [documentação power BI](https://docs.microsoft.com/power-bi/desktop-query-overview) para saber mais sobre como fazer alterações usando o Power **Query Editor**. 

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [o ambiente de trabalho Power BI](https://docs.microsoft.com/power-bi/desktop-query-overview).

* Saiba mais sobre [a consulta de dados](concepts-query-overview.md) em Azure Time Series Insights Gen2.
