---
title: 'Tutorial: Visualize os dados a partir do Explorador de dados do Azure no Power BI'
description: Neste tutorial, vai aprender a ligar ao Azure Data Explorer com o Power BI e a visualizar os seus dados.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: f253911c1830e606dd47b64aaea1f17cb3478cd5
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757745"
---
# <a name="tutorial-visualize-data-from-azure-data-explorer-in-power-bi"></a>Tutorial: Visualize os dados a partir do Explorador de dados do Azure no Power BI

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. O Power BI é uma solução de análise de negócios que lhe permite visualizar os seus dados e partilhar os resultados na sua organização. Neste tutorial, vai aprender primeiro a compor elementos visuais no Azure Data Explorer. Depois, vai ligar ao Azure Data Explorer com o Power BI, criar um relatório com base nos dados de exemplo e publicar o relatório no serviço Power BI.

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar. Se não estiver inscrito no Power BI Pro, poderá [inscrever-se numa avaliação gratuita](https://app.powerbi.com/signupredirect?pbi_source=web) antes de começar.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Compor elementos visuais no Azure Data Explorer
> * Ligar ao Azure Data Explorer no Power BI Desktop
> * Trabalhar com dados no Power BI Desktop
> * Criar um relatório com elementos visuais
> * Publicar e partilhar o relatório

## <a name="prerequisites"></a>Pré-requisitos

Além das subscrições do Azure e do Power BI, precisa do seguinte para concluir este tutorial:

* [Um cluster e uma base de dados de teste](create-cluster-database-portal.md)

* [Dados de exemplo os StormEvents](ingest-sample-data.md). [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (selecionar **transferência gratuita**)

## <a name="render-visuals-in-azure-data-explorer"></a>Compor elementos visuais no Azure Data Explorer

Antes de avançar para o Power BI, vamos analisar como compor os elementos visuais no Azure Data Explorer. Este procedimento é ótimo para obter uma análise rápida.

1. Inicie sessão em [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. No painel esquerdo, selecione a base de dados de teste que contém os dados de exemplo StormEvents.

1. Cole a consulta seguinte na janela direita e selecione **Executar**.

    ```Kusto
    StormEvents
    | summarize event_count=count() by State
    | where event_count > 1800
    | project State, event_count
    | sort by event_count
    | render columnchart
    ```

    Esta consulta contabiliza os eventos de meteorologia por estado. Em seguida, compõe um gráfico de colunas para todos os estados com mais de 1800 eventos de meteorologia.

    ![Gráfico de colunas de eventos](media/visualize-power-bi/events-column-chart.png)

1. Cole a consulta seguinte na janela direita e selecione **Executar**.

    ```Kusto
    StormEvents
    | where State == "WASHINGTON" and StartTime >= datetime(2007-07-01) and StartTime <= datetime(2007-07-31)
    | summarize StormCount = count() by EventType
    | render piechart
    ```

    Esta consulta contabiliza os eventos de meteorologia por tipo para o mês de julho no estado de Washington. Em seguida, compõe um gráfico circular que mostra a percentagem de cada tipo de evento.

    ![Gráfico circular de eventos](media/visualize-power-bi/events-pie-chart.png)

Agora, é altura de analisar o Power BI, mas pode fazer muito mais coisas com os elementos visuais no Azure Data Explorer.

## <a name="connect-to-azure-data-explorer"></a>Ligar ao Azure Data Explorer

Agora, ligue-se ao Azure Data Explorer no Power BI Desktop.

1. No Power BI Desktop sobre a **home page** separador, selecione **obter dados** , em seguida, **mais**.

    ![Obter dados](media/visualize-power-bi/get-data-more.png)

1. Procure *Explorador de dados do Azure*, selecione **Explorador de dados do Azure (Beta)**, em seguida, **Connect**.

    ![Pesquisar e obter dados](media/visualize-power-bi/search-get-data.png)

1. No ecrã **Conetor de pré-visualização**, selecione **Continuar**.

1. No ecrã seguinte, introduza o nome do seu cluster de teste e a base de dados. O cluster deverá estar no formato `https://<ClusterName>.<Region>.kusto.windows.net`. Introduza *StormEvents* para o nome da tabela. Deixe todas as outras opções com os valores predefinidos e selecione **OK**.

    ![Opções do cluster, da base de dados e da tabela](media/visualize-power-bi/cluster-database-table.png)

1. No ecrã de pré-visualização de dados, selecione **Editar**.

    A tabela abre-se no Editor do Power Query, onde pode editar linhas e colunas antes de importar os dados.

## <a name="work-with-data-in-power-bi-desktop"></a>Trabalhar com dados no Power BI Desktop

Agora que tem uma ligação ao Azure Data Explorer, edite os dados no Editor do Power Query. Remova as linhas com valores nulos na coluna **BeginLat** e remova a coluna JSON **StormSummary** na íntegra. Estas são operações simples, mas também pode realizar transformações complexas ao importar dados.

1. Selecione a seta da coluna **BeginLat**, desmarque a caixa de verificação **null** e selecione **OK**.

    ![Filtrar coluna](media/visualize-power-bi/filter-column.png)

1. Clique com o botão direito do rato no cabeçalho da coluna **StormSummary** e, em seguida, selecione **Remover**.

    ![Remover coluna](media/visualize-power-bi/remove-column.png)

1. No painel **DEFINIÇÕES DA CONSULTA**, altere o nome *Query1* para *StormEvents*.

    ![Alterar o nome da consulta](media/visualize-power-bi/query-name.png)

1. No separador **Página Principal** do friso, selecione **Fechar e aplicar**.

    ![Fechar e aplicar](media/visualize-power-bi/close-apply.png)

    O Power Query aplica as alterações e, em seguida, importa os dados de exemplo para um *modelo de dados*. Os passos seguintes mostram como melhorar esse modelo. Novamente, este é apenas um exemplo simples para dar uma ideia do que é possível fazer.

1. No lado esquerdo da janela principal, selecione a vista de dados.

    ![Vista de dados](media/visualize-power-bi/data-view.png)

1. No separador **Modelação** do friso, selecione **Nova coluna**.

    ![Nova coluna](media/visualize-power-bi/new-column.png)

1. Introduza a seguinte fórmula do Data Analysis Expressions (DAX) na barra de fórmulas e, em seguida, prima Enter.

    ```DAX
    DurationHours = DATEDIFF(StormEvents[StartTime], StormEvents[EndTime], hour)
    ```

    ![Barra de fórmulas](media/visualize-power-bi/formula-bar.png)

    Esta fórmula cria a coluna *DurationHours* que calcula as horas que cada evento de meteorologia durou. Vai utilizar esta coluna num elemento visual na próxima secção.

1. Desloque-se para o lado direito da tabela para ver a coluna.

## <a name="create-a-report-with-visuals"></a>Criar um relatório com elementos visuais

Agora que os dados foram importados e melhorou o modelo de dados, está na altura de criar um relatório com os elementos visuais. Vai adicionar um gráfico de colunas com base na duração dos eventos e um mapa que mostra os danos nas colheitas.

1. No lado esquerdo da janela, selecione a vista de relatório.

    ![Vista de relatório](media/visualize-power-bi/report-view.png)

1. No painel **VISUALIZAÇÕES**, selecione o gráfico de colunas agrupadas.

    ![Adicionar gráfico de colunas](media/visualize-power-bi/add-column-chart.png)

    É adicionado um gráfico em branco à tela.

    ![Gráfico em branco](media/visualize-power-bi/blank-chart.png)

1. Na lista **CAMPOS**, selecione **DurationHours** e **Estado**.

    ![Selecionar campos](media/visualize-power-bi/select-fields.png)

    Agora, tem um gráfico que mostra o total de horas dos eventos de meteorologia por estado ao longo de um ano.

    ![Gráfico de colunas de duração](media/visualize-power-bi/duration-column-chart.png)

1. Clique em qualquer local na tela fora do gráfico de colunas.

1. No painel **VISUALIZAÇÕES**, selecione o mapa.

    ![Adicionar mapa](media/visualize-power-bi/add-map.png)

1. Na lista **CAMPOS**, selecione **CropDamage** e **Estado**. Redimensione o mapa para que possa ver claramente os estados dos EUA.

    ![Mapa de danos nas colheitas](media/visualize-power-bi/crop-damage-map.png)

    O tamanho dos balões representa o valor em dólares dos danos nas colheitas. Coloque o cursor rato sobre os balões para ver os detalhes.

1. Mova e redimensione os elementos visuais para que tenha um relatório parecido com a imagem seguinte.

    ![Relatório concluído](media/visualize-power-bi/finished-report.png)

1. Guarde o relatório com o nome *storm-events.pbix*.

## <a name="publish-and-share-the-report"></a>Publicar e partilhar o relatório

Até este ponto, todo o trabalho que fez no Power BI tem sido local, através do Power BI Desktop. Agora, vai publicar o relatório no serviço Power BI, onde poderá partilhá-lo com outras pessoas.

1. No Power BI Desktop, no separador **Página Principal** do friso, selecione **Publicar**.

    ![Botão Publicar](media/visualize-power-bi/publish-button.png)

1. Se ainda não tiver iniciado sessão no Power BI, siga o processo de início de sessão.

1. Selecione **A minha área de trabalho** e **Selecionar**.

    ![Selecionar área de trabalho](media/visualize-power-bi/select-workspace.png)

1. Quando a publicação estiver concluída, selecione **Abrir storm-events.pbix no Power BI**.

    ![Publicação com êxito](media/visualize-power-bi/publishing-succeeded.png)

    O relatório é apresentado no serviço, com os mesmos elementos visuais e o esquema que definiu no Power BI Desktop.

1. No canto superior direito do relatório, selecione **Partilhar**.

    ![Botão Partilhar](media/visualize-power-bi/share-button.png)

1. No ecrã **Partilhar relatório**, adicione um colega da sua organização, adicione uma nota e, em seguida, selecione **Partilhar**.

    ![Partilhar relatório](media/visualize-power-bi/share-report.png)

    Se o seu colega tiver as permissões adequadas, poderá aceder ao relatório que partilhou.

## <a name="clean-up-resources"></a>Limpar recursos

Se não quiser manter o relatório que criou, basta eliminar o ficheiro *storm-events.pbix*. Se quiser remover o relatório que publicou, siga estes passos.

1. Em **A minha área de trabalho**, desloque-se para baixo até **RELATÓRIOS** e localize **storm-events**.

1. Selecione as reticências (**...**) junto a **storm-events** e, em seguida, selecione **REMOVER**.

    ![Remover relatório](media/visualize-power-bi/remove-report.png)

1. Confirme a remoção.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Escrever consultas](write-queries.md)
