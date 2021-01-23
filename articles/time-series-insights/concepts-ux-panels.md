---
title: Visualizar dados no Time Series Insights Explorer - Azure Time Series Insights Gen2| Microsoft Docs
description: Saiba mais sobre funcionalidades e opções disponíveis no Azure Time Series Insights Explorer.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.custom: seodec18
ms.openlocfilehash: 55e102cc7e27cbcd9a65999f590c8264f1ad4434
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736857"
---
# <a name="azure-time-series-insights-explorer"></a>Explorador do Time Series Insights

Este artigo descreve as várias funcionalidades e opções disponíveis no ambiente de [demonstração](https://insights.timeseries.azure.com/preview/demo)da Azure Time Series Insights Gen2 .

## <a name="prerequisites"></a>Pré-requisitos

Para começar com o Azure Time Series Insights Explorer, deve:

* Tenha um ambiente Azure Time Series Insights Gen2. Saiba mais sobre o provisionamento de um exemplo lendo o tutorial [Azure Time Series Insights Gen2.](./tutorials-set-up-tsi-environment.md)
* Fornecer acesso de [dados](./concepts-access-policies.md) ao ambiente Azure Time Series Insights Gen2 que criou para a conta. Você pode fornecer acesso aos outros, bem como a si mesmo.
* Adicione uma fonte de evento ao ambiente Azure Time Series Insights Gen2 para empurrar dados para o ambiente:
  * Saiba [como se conectar a um centro de eventos](./how-to-ingest-data-event-hub.md)
  * Saiba [como se conectar a um hub IoT](./how-to-ingest-data-iot-hub.md)

## <a name="explore-the-azure-time-series-insights-explorer"></a>Explore o Azure Time Series Insights Explorer

O Azure Time Series Insights Explorer é composto pelos seguintes sete elementos:

[![Visão geral do Azure Time Series Insights Explorer](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [Painel ambiente](#1-environment-panel): Exibe todos os ambientes da Série De Tempo Azure Insights Gen2.
1. [Barra de navegação](#2-navigation-bar): Permite alternar entre as páginas **Analisar** e **Model.**
1. [Árvore de hierarquia e painel de pesquisa](#3-hierarchy-tree-and-search-panel): Permite-lhe selecionar e procurar elementos de dados específicos a serem cartografados.
1. [Séries temporais bem](#4-time-series-well): Mostra todos os elementos de dados atualmente selecionados.
1. [Painel de gráficos](#5-chart-panel): Apresenta o seu gráfico de trabalho atual.
1. [Linha do tempo](#6-time-editor-panel): Permite modificar o seu tempo de trabalho.
1. [Barra de aplicação](#7-app-bar): Contém as suas opções de gestão do utilizador (como o inquilino atual), e permite-lhe alterá-las e configurações de idioma.

## <a name="1-environment-panel"></a>1. Painel ambiental

O painel de ambiente exibe todos os ambientes Azure Time Series Insights Gen2 a que tem acesso. A lista inclui ambientes gen2, bem como ambientes gen1. Basta selecionar o ambiente que pretende utilizar para ser imediatamente levado para lá.

1. Selecione a seta para baixo ao lado do ambiente visualizado.

   [![Painel ambiental](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. Em seguida, selecione o ambiente que deseja.

## <a name="2-navigation-bar"></a>2. Barra de navegação

  [![A barra de navegação](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

Utilize a barra de navegação para selecionar entre duas vistas:

* **Análise:** Use-o para traçar e executar análises ricas nos dados da série de tempo modelada ou não modelada.
* **Modelo**: Use-o para empurrar novos tipos, hierarquias e instâncias da Série De Tempo Azure Insights Gen2 para o seu Modelo série de tempo.

### <a name="model-authoring"></a>Autoria do modelo

O Azure Time Series Insights Gen2 suporta operações completas de criação, leitura, atualização e eliminação (CRUD) no seu Modelo de Séries Tempores.

[![O painel de pesquisa modelo](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **Tipo de modelo série de tempo**: Pode utilizar os tipos de modelos de séries de tempo para definir variáveis ou fórmulas para fazer cálculos. Estão associados a uma dada instância do Modelo da Série De Tempo. Um tipo pode ter uma ou mais variáveis.
* **Hierarquia do Modelo série de tempo**: As hierarquias são organizações sistemáticas dos seus dados. As hierarquias retratam as relações entre diferentes instâncias no seu modelo de séries de tempo.
* **Exemplo do modelo da Série de Tempo**: As instâncias são as próprias séries hortemias. Na maioria dos casos, são o **DeviceID** ou **o AssetID,** que é o identificador único do ativo no ambiente.

Para saber mais sobre o Modelo série de tempo, leia [os Modelos Times Series](./concepts-model-overview.md).

## <a name="3-hierarchy-tree-and-search-panel"></a>3. Árvore hierárquica e painel de pesquisa

A hierarquia e o painel de pesquisa permitem-lhe pesquisar e navegar facilmente na sua hierarquia [do Modelo série de tempo](./concepts-model-overview.md) para encontrar as instâncias específicas da série de tempo que pretende exibir na sua tabela. Quando seleciona os seus casos, não só são adicionados ao gráfico atual, como também são adicionados bem aos dados.

[![Árvore de hierarquia e painel de pesquisa](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

O painel de resultados da pesquisa também permite visualizar os seus resultados numa vista de hierarquia ou numa vista de lista, facilitando a visualização das instâncias que pretende exibir.

## <a name="4-time-series-well"></a>4. Séries temporais bem

O poço exibe campos de exemplo e outros metadados associados a instâncias selecionadas do Modelo de Séries De Tempo. Ao selecionar as caixas de verificação no lado direito, pode ocultar ou exibir instâncias específicas a partir do gráfico atual.

  [![O poço gen2](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

Pode remover bem elementos de dados específicos dos seus dados atuais selecionando o controlo vermelho **Delete** (lata de lixo) no lado esquerdo do elemento. O poço também permite controlar como cada elemento é exibido na tabela. Pode optar por adicionar sombras min/max, pontos de dados, deslocar o elemento no tempo e visualizar o caso de forma pisada.

Além disso, o controlo das explorações permite criar mudanças de tempo e dispersar facilmente os enredos.  

  [![Bem opções de layout](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> Se aparecer a seguinte mensagem, a instância não tem quaisquer dados durante o período de tempo selecionado. Para resolver o problema, aumente o tempo ou confirme que a instância está a empurrar dados.
>
> ![Sem notificação de dados](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. Painel gráfico

O gráfico permite-lhe visualizar as séries de tempo como linhas. Pode colapsar o painel de ambiente, o modelo de dados e o painel de controlo do tempo clicando nos controlos da web para tornar o gráfico maior.

  [![Visão geral do gráfico da Gen2](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **Tipo de gráfico**: Controla quais os elementos de dados disponíveis para visualização.

1. **Tamanho do intervalo**: A ferramenta de deslizamento de tamanho de intervalo permite-lhe fazer zoom dentro e fora de intervalos ao longo do mesmo período de tempo. Isto proporciona um controlo mais preciso do movimento entre grandes fatias de tempo que mostram tendências suaves até fatias tão pequenas como o milissegundo, permitindo-lhe rever cortes granulares e de alta resolução dos seus dados. O ponto de partida padrão do slider é definido como a visão mais ideal dos dados da sua seleção; resolução de equilíbrio, velocidade de consulta e granularidade.

1. **Zoom e panela**: Selecione este controlo para fazer zoom e panorâmica na tabela.

1. **Controlo do eixo Y**: Ciclos através das opções de visualização y-axis disponíveis:

    * `Stacked`: Cada linha tem um eixo Y individual.
    * `Overlap`: Utilize-o para empilhar várias linhas no mesmo eixo Y, com os dados do eixo Y a mudar em função da linha selecionada.
    * `Shared`: Todos os dados do eixo Y apresentados em conjunto.

1. **Elemento marcador**: O elemento de dados atualmente selecionado e os seus detalhes associados.

Pode perfurar ainda mais uma fatia de dados específica **clicando** num ponto de dados no gráfico atual enquanto segura o rato e, em seguida, arrastando a área selecionada para o ponto final da sua escolha. Clique com o **botão direito** na área azul, selecionada e, em seguida, selecione **Zoom** como mostrado abaixo. Também pode visualizar e baixar os eventos de telemetria no tempo selecionado.

  [![Zoom de gráfico gen2](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

Depois de efetuar a ação **Zoom,** o seu conjunto de dados selecionado será apresentado. Selecione o controlo do formato para pedalar através das representações de três eixos y dos seus dados.

  [![Gen2 gráfico y-eixo](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

Aqui, é fornecido um exemplo de um **gráfico sobreposto:**

  [![Opção de gráfico sobreposto](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

O botão **Mais ações** expande-se para exibir o **Download como CSV**, **Connect to Power BI**, **Mostrar dados de gráfico como tabela**, e explorar opções de **eventos crus.**

  [![Mais opção de ações](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

Saiba mais sobre a opção **Connect to Power BI** no [conector Power BI](concepts-power-bi.md).

## <a name="6-time-editor-panel"></a>6. Painel de editores de tempo

Ao trabalhar com a Azure Time Series Insights Gen2, seleciona primeiro um intervalo de tempo. O tempo de tempo selecionado controlará o conjunto de dados que está disponível para manipulação com os widgets de atualização da Azure Time Series Insights Gen2.

  [![Painel de seleção de tempo](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> Uma parte da linha do tempo é destacada numa cor âmbar ou laranja para indicar o alcance dos dados que estão disponíveis na loja quente.

Os seguintes controlos web estão disponíveis no Azure Time Series Insights Gen2 para selecionar o seu tempo de trabalho.

  [![Controlo de poços de exploração](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **Controlo do deslizamento de gama de datas interiores**: Utilize os dois comandos de ponto final arrastando-os durante o período de tempo que pretende. Este intervalo de data interior é limitado pelo controlo do deslizamento de datas exteriores.

1. **Aumentar e diminuir os botões de intervalo** de datas : Aumente ou diminua o seu tempo selecionando qualquer um dos botões para o intervalo que deseja.

1. **Controlo do colapso do tempo**: Este controlo web permite ocultar todos os controlos, exceto a ferramenta de deslizamento de gama de datas interior.

1. **Controlo do deslizamento de gama de datas exteriores**: Utilize os comandos de ponto final para selecionar o intervalo de datas exteriores, que estará disponível para o controlo da gama de datas interiores.

1. **Controlo do deslizamento do intervalo** de tempo : Utilize-o para alternar rapidamente entre as seleções de tempo pré-estamos, tais como os **últimos 30 minutos**, as **últimas 12 horas**, ou uma gama **personalizada**. A alteração deste valor também altera os intervalos de intervalos disponíveis discutidos na ferramenta de deslizamento de tamanho de intervalo.

   [![De e para painel de seleção](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. Barra de aplicativos

O painel de navegação Azure Time Series Insights Gen2 aparece no topo da sua aplicação. Fornece as seguintes funcionalidades:

### <a name="current-session-share-link-control"></a>Controlo atual da ligação de ações da sessão

  [![Ícone Partilhar](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

Selecione o novo ícone **partilhar** uma ligação URL com a sua equipa.

  [![Partilhe o seu URL de exemplo](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>Secção de inquilinos

  [![Seleção de inquilinos](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* Apresenta as informações atuais da conta de insusição da Azure Time Series Insights Gen2.
* Use-o para alternar entre os temas disponíveis.
* Use-o para ver o ambiente de [demonstração](https://insights.timeseries.azure.com/preview/demo)da Gen2.

### <a name="theme-selection"></a>Seleção temática

Para selecionar um novo tema, selecione o ícone de perfil localizado no canto superior direito. Em seguida, selecione **Alterar O Tema**.

  [![Seleção temática](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> A seleção de idiomas também está disponível selecionando o ícone de perfil.

Azure Time Series Insights Explorer suporta dois temas:

* **Tema de** luz : O tema predefinido mostrado ao longo deste documento.
* **Tema escuro**: Torna o Explorador como mostrado aqui:

  [![Tema escuro selecionado](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="gen1-environment-controls"></a>Controlos ambientais da Gen1

### <a name="gen2-terms-panel"></a>Painel de termos da Gen2

Esta secção aplica-se apenas aos ambientes existentes da Gen1 que tentam utilizar o Explorer na UI atualizada. É melhor utilizar o produto Gen1 e o produto Gen2 em combinação. Adicionámos algumas funcionalidades da UI existente ao Explorador atualizado, mas podes obter a experiência completa de UI para um ambiente Gen1 no novo Azure Time Series Insights Explorer.

Em vez da hierarquia, é apresentado o painel de termos da Azure Time Series Insights Gen2. O painel de termos permite definir consultas no seu ambiente. Utilize-o também para filtrar dados com base num predicado.

  [![Onde painel de consulta](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

O painel de editores de termos da Azure Time Series Insights Gen2 tem os seguintes parâmetros:

**Onde:** Utilize a cláusula "onde" filtra rapidamente os seus eventos utilizando o conjunto de operands listados na tabela seguinte. Se realizar uma pesquisa selecionando um operand, o predicado é automaticamente atualizado com base nessa pesquisa. Os tipos de ópera suportados incluem os seguintes:

| Operação    | Tipos suportados    | Notas |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Duplo, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, TimeSpan, NU |
| `IN` | String, Bool, Double, DateTime, TimeSpan, NU | Todos os óperas devem ser do mesmo tipo ou ser constantes NU. |
| `HAS` | String | Apenas as cordas constantes literais são permitidas no lado direito. Corda vazia e NU NÃO são permitidos. |

Para saber mais sobre operações de consulta suportadas e tipos de dados, leia [a Expressão da Série de Tempo (TSX)](/rest/api/time-series-insights/reference-time-series-expression-syntax).

### <a name="examples-of-where-clauses"></a>Exemplos de onde cláusulas

  [![Onde exemplos de cláusulas](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**Medida**: Uma lista de espera que exibe todas as colunas numéricas **(Duplos)** pode utilizar como elementos para a sua tabela atual.

**Dividido por:** Esta lista de drop-down apresenta todas as colunas categóricas disponíveis (Strings) no seu modelo que pode agrupar os seus dados. Pode adicionar até cinco termos para ver no mesmo eixo x. Introduza os parâmetros que pretende e, em seguida, **selecione Adicione** para adicionar um novo termo.

  [![Vista questionada e filtrada](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

Pode mostrar e ocultar elementos no painel de gráficos selecionando o ícone visível, como mostra a imagem seguinte. Para remover completamente as consultas, selecione o **X** vermelho .

  [![Cancelar uma opção consultada e filtrada](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>Próximos passos

* Saiba [como ingerir dados](./concepts-ingestion-overview.md) no seu ambiente.

* Confira o artigo sobre [armazenamento.](concepts-storage.md)

* Leia sobre [modelação de dados](./concepts-model-overview.md) em Azure Time Series Insights Gen2.

* Aprenda [a diagnosticar e a resolver problemas no](./how-to-diagnose-troubleshoot.md) seu ambiente.
