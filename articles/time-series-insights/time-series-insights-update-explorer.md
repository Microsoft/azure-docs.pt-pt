---
title: Visualizar dados no explorador de pré-visualização - Azure Time Series Insights [ Microsoft Docs
description: Saiba mais sobre as funcionalidades e opções disponíveis no explorador de pré-visualização da Série de Tempo Azure Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: seodec18
ms.openlocfilehash: c117510749abcf997e414371faa6dea1dc79ea7a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75861766"
---
# <a name="azure-time-series-insights-preview-explorer"></a>Explorador de pré-visualização da Série de Tempo Azure Insights

Este artigo descreve as várias funcionalidades e opções disponíveis na [aplicação web](https://insights.timeseries.azure.com/preview/demo)de demonstração de visualização da Série de Tempo Azure Insights .

## <a name="prerequisites"></a>Pré-requisitos

Para começar com o explorador de pré-visualização da Série De Tempo Azure Insights, você deve:

* Tenha um ambiente time series insights provisionado. Saiba mais sobre o fornecimento de um exemplo lendo o tutorial de [pré-visualização da Série De Tempo Azure Insights.](./time-series-insights-update-create-environment.md)
* [Forneça acesso](./time-series-insights-data-access.md) aos dados ao ambiente Time Series Insights que criou para a conta. Você pode fornecer acesso a outros, bem como a si mesmo.
* Adicione uma fonte de evento ao ambiente Time Series Insights para empurrar dados para o ambiente:
  * Saiba como se conectar a um centro de [eventos](./time-series-insights-how-to-add-an-event-source-eventhub.md) 
  * Aprenda [a ligar-se a um hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="explore-the-time-series-insights-preview-explorer"></a>Explore o explorador de pré-visualização de insights da série de tempo

O explorador de pré-visualização da Série De Tempo Azure Insights consiste nos seguintes sete elementos:

[![Visão geral do explorador de pré-visualização da Série De Tempo Insights](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [Painel de ambiente](#1-environment-panel): Exibe todos os seus ambientes de Insights da Série De Tempo Azure.
1. [Barra de navegação](#2-navigation-bar): Permite alternar entre as páginas **De Análise** e **Modelo.**
1. [Árvore hierárquica e painel](#3-hierarchy-tree-and-search-panel)de pesquisa : Permite selecionar e procurar elementos de dados específicos a serem cartografados.
1. [Série de tempo bem](#4-time-series-well): Mostra todos os seus elementos de dados atualmente selecionados.
1. [Painel de gráficos](#5-chart-panel): Exibe o seu gráfico de trabalho atual.
1. [Cronologia](#6-time-editor-panel): Permite modificar o seu tempo de trabalho.
1. [Barra de aplicativos](#7-app-bar): Contém as opções de gestão do utilizador (como o atual inquilino), e permite-lhe alterá-las e configurações de idiomas.


## <a name="1-environment-panel"></a>1. Painel ambiental

O painel ambiental exibe todos os ambientes time series insights a que você tem acesso. A lista inclui ambientes pay-as-you-go (Pré-visualização) bem como ambientes S1/S2 (Disponibilidade Geral). Basta selecionar o ambiente Time Series Insights que pretende utilizar para ser imediatamente tomado lá.

1. Selecione a seta de descida ao lado do ambiente visualizado.

   [![Painel ambiental](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. Em seguida, selecione o ambiente que deseja.

## <a name="2-navigation-bar"></a>2. Barra de navegação

  [![A barra de navegação](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

Utilize a barra de navegação para selecionar entre duas vistas:

* **Analise:** Use-o para traçar gráficos e realizar análises ricas nos seus dados de séries de tempo modeladas ou não modeladas.
* **Modelo:** Use-o para empurrar novos tipos de pré-visualização de Insights da Série de Tempo, hierarquias e instâncias para o seu modelo Time Series Insights.

### <a name="model-authoring"></a>Autoria modelo

A Pré-visualização da Série de Tempo Azure suporta operações completas de criação, leitura, atualização e exclusão (CRUD) no seu Modelo de Série de Tempo.

[![O painel de pesquisa do modelo](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **Tipo de modelo de série**de tempo: Pode utilizar tipos de Insights da Série de Tempo para definir variáveis ou fórmulas para fazer cálculos. Estão associados a um dado exemplo da Time Series Insights. Um tipo pode ter uma ou mais variáveis.
* **Hierarquia**do Modelo da Série Tempo : As hierarquias são organizações sistemáticas dos seus dados. As hierarquias retratam as relações entre diferentes entidades nos seus dados da Time Series Insights.
* **Instância**modelo da Série Tempo : As instâncias são as próprias séries de tempo. Na maioria dos casos, são o **DeviceID** ou **AssetID,** que é o identificador único do ativo no ambiente.

Para saber mais sobre o Modelo da Série Tempo, leia [Os Modelos Times Series](./time-series-insights-update-tsm.md).

## <a name="3-hierarchy-tree-and-search-panel"></a>3. Árvore hierárquica e painel de pesquisa

A árvore e painel de pesquisa da hierarquia permitem-lhe pesquisar e navegar facilmente na sua hierarquia do Modelo de [Série seleção](./time-series-insights-update-tsm.md) para encontrar as instâncias específicas da série de tempo que pretende exibir na sua ficha. Quando selecionam os seus casos, não só são adicionados ao gráfico atual, como também são adicionados aos dados bem. 

[![Árvore hierárquica e painel de pesquisa](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

O painel de resultados da pesquisa também permite visualizar os seus resultados numa visão hierárquica ou numa visão de lista, facilitando a descoberta das instâncias que pretende expor.
 
## <a name="4-time-series-well"></a>4. Série seleção de horários bem

O poço exibe campos de exemplo e outros metadados associados a instâncias selecionadas da Time Series Insights. Selecionando as caixas de verificação do lado direito, pode ocultar ou apresentar instâncias específicas a partir da tabela atual. 

  [![O poço de pré-visualização](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

Pode remover bem elementos de dados específicos dos seus dados atuais selecionando o controlo vermelho **Delete** (caixote do lixo) no lado esquerdo do elemento. O poço também permite controlar como cada elemento é exibido na tabela. Pode optar por adicionar sombras min/max, pontos de dados, deslocar o elemento no tempo e visualizar a instância de uma forma pisada. 

Além disso, o controlo de explorações permite criar turnos de tempo e espalhar enredos facilmente.  

  [![Opções de layout bem](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> Se aparecer a seguinte mensagem, a instância não dispõe de dados durante o período de tempo selecionado. Para resolver o problema, aumente o tempo ou confirme que a instância está a empurrar dados.
>
> ![Sem notificação de dados](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. Painel de gráficos

O gráfico permite-lhe exibir as séries de tempo como linhas. Pode colapsar o painel ambiental, o modelo de dados e o painel de controlo do tempo, clicando nos controlos web para tornar o gráfico maior. 

  [![Visão geral do gráfico de pré-visualização](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **Tipo de gráfico**: Controla quais os elementos de dados disponíveis para visualização.

1. **Tamanho do intervalo:** A ferramenta de slider tamanho intervalo permite-lhe fazer zoom dentro e fora de intervalos ao longo do mesmo período de tempo. Isto proporciona um controlo mais preciso do movimento entre grandes fatias de tempo que mostram tendências suaves até fatias tão pequenas como o milissegundo, permitindo-lhe rever cortes granulares e de alta resolução dos seus dados. O ponto de partida padrão do slider é definido como a visão mais ideal dos dados da sua seleção; resolução de equilíbrio, velocidade de consulta e granularidade.

1. **Zoom e panela:** Selecione este controlo para ampliar e pan a tabela.

1. **Controlo do eixo Y**: Ciclos através das opções disponíveis de visão do eixo y:

    * `Stacked`: Cada linha tem um eixo Y individual.
    * `Overlap`: Utilize-o para empilhar várias linhas no mesmo eixo Y, com a alteração dos dados do eixo Y com base na linha selecionada.
    * `Shared`: Todos os dados do eixo Y apresentados em conjunto.

1. **Elemento marcador**: O elemento de dados atualmente selecionado e os seus detalhes associados.

Pode perfurar ainda mais uma fatia específica de dados **clicando** num ponto de dados no gráfico atual enquanto segura o rato e, em seguida, arrastando a área selecionada para o ponto final da sua escolha. **Clique na** área azul, selecionada e, em seguida, selecione **Zoom** como mostrado abaixo. Também pode ver e descarregar os eventos de telemetria no tempo de tempo selecionado.

  [![Zoom de gráfico de pré-visualização](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

Depois de realizar a ação **Zoom,** será apresentado o conjunto de dados selecionado. Selecione o controlo do formato para pedalar através das três representações do eixo y dos seus dados time series Insights.

  [![Gráfico de pré-visualização y-eixo](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

Aqui, é fornecido um exemplo de um **gráfico sobreposto:**

  [![Opção de gráfico sobreposto](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

O botão **Mais Ações** expande-se para exibir o Download **como CSV**, **Connect to Power BI,** **Mostrar dados de gráficos como uma tabela**e explorar opções de **eventos brutos.**

  [![Mais ações opção](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

Saiba mais sobre a opção **Connect to Power BI** no [conector nativo Power BI](concepts-power-bi.md)da Série Tempo Insights .

## <a name="6-time-editor-panel"></a>6. Painel de editor de tempo

Ao trabalhar com time series Insights, primeiro irá selecionar um período de tempo. O tempo de tempo selecionado controlará o conjunto de dados disponível para manipulação com os widgets de atualização time series insights.

  [![Painel de seleção de tempo](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> Uma parte da linha do tempo é destacada numa cor âmbar ou laranja para indicar o tempo de dados disponíveis na loja quente.

Os seguintes controlos web estão disponíveis na atualização Time Series Insights para selecionar o seu tempo de trabalho. 

  [![Controlo de poços de exploração](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. Controlo de slider de gama de **data interior:** Utilize os dois comandos de ponto final arrastando-os durante o período de tempo que desejar. Esta gama de datas interiores é limitada pelo controlo de slider de intervalo de data exterior.

1. **Aumentar e diminuir os botões**de intervalo de datas : Aumente ou diminua o seu tempo selecionando qualquer um dos botões para o intervalo que desejar.

1. **Controlo**de colapso do tempo : Este controlo web permite-lhe ocultar todos os controlos, exceto a ferramenta de slider de intervalo de data interior.

1. Controlo de **slider**de intervalo de data externa : Utilize os comandos de ponto final para selecionar o intervalo de data exterior, que estará disponível para o seu controlo de alcance de data interior.

1. Controlo do slider de intervalo de **tempo:** Utilize-o para alternar rapidamente entre as seleções de tempo pré-definidos, tais como os últimos **30 minutos**, as **últimas 12 horas**ou uma gama **personalizada**. Alterar este valor também altera as gamas de intervalos disponíveis discutidas na ferramenta de slider de tamanho de intervalo.

   [![De e para o painel de seleção](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. Barra de aplicativos

O painel de navegação de pré-visualização da Time Series Insights aparece no topo da sua aplicação Time Series Insights. Fornece as seguintes funcionalidades:

### <a name="current-session-share-link-control"></a>Controlo de ligação de partilha de sessão atual

  [![Partilhar ícone](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

Selecione o novo ícone **Partilhar** para partilhar uma ligação URL com a sua equipa.

  [![Partilhe o url da sua instância](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>Secção de inquilinos

  [![Seleção de inquilinos](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* Exibe as informações de inscrição da conta time series Insights.
* Use-o para alternar entre os temas disponíveis da Time Series Insights.
* Use-o para visualizar a [aplicação web](https://insights.timeseries.azure.com/preview/demo)Preview demo .

### <a name="theme-selection"></a>Seleção temática

Para selecionar um novo tema, selecione o ícone do seu perfil localizado no canto superior direito. Em seguida, selecione **Change Tema**.

  [![Seleção temática](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> A seleção de idiomas também está disponível selecionando o ícone do seu perfil.

A Pré-visualização da Série De Tempo Azure suporta dois temas:

* **Tema de luz**: O tema predefinido mostrado ao longo deste documento.
* **Tema escuro**: Torna o explorador como mostrado aqui:

  [![Tema escuro selecionado](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="s1s2-environment-controls"></a>Controlos ambientais S1/S2

### <a name="preview-terms-panel"></a>Painel de termos de pré-visualização

Esta secção aplica-se apenas aos ambientes S1/S2 existentes que tentam utilizar o explorador na UI atualizada. É possível que pretenda utilizar o produto geralmente disponível e pré-visualizar em combinação. Adicionámos algumas funcionalidades desde o UI existente ao explorador atualizado, mas podes obter toda a experiência ui para o ambiente S1/S2 no explorador de Insights da Série Time existente. 

Em vez da hierarquia, o painel de termos da Time Series Insights é apresentado. O painel de termos permite-lhe definir consultas no seu ambiente. Use-os também para filtrar dados com base num predicado.

  [![Onde painel de consulta](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

O painel de editor de termos de pré-visualização da Série De Tempo tem os seguintes parâmetros:

**Sempre**que : Utilize a cláusula onde filtrar rapidamente os seus eventos utilizando o conjunto de operands listados na tabela seguinte. Se realizar uma pesquisa selecionando um operand, o predicado é automaticamente atualizado com base nessa pesquisa. Os tipos de opere suportados incluem o seguinte:

| Operação | Tipos suportados   | Notas |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Duplo, Data, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | String, Bool, Double, DateTime, TimeSpan, NULL | Todos os operands devem ser do mesmo tipo ou ser nulos constantes. |
| `HAS` | String | Apenas os literais de corda constantes são permitidos no lado direito. Cordas vazias e NULO não são permitidos. |

Para saber mais sobre operações de consulta suportadas e tipos de dados, leia a Expressão da [Série De Tempo (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax).

### <a name="examples-of-where-clauses"></a>Exemplos de onde as cláusulas

  [![Onde exemplos de cláusula](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**Medida**: Uma lista de drop-down que mostra todas as colunas numéricas **(Duplos)** que pode utilizar como elementos para o seu gráfico atual.

**Dividido por**: Esta lista de drop-down apresenta todas as colunas categóricas disponíveis (Strings) no seu modelo que pode agrupar os seus dados. Pode adicionar até cinco termos para ver no mesmo eixo X. Introduza os parâmetros que deseja e, em seguida, selecione **Adicionar** para adicionar um novo termo.

  [![Consultado e filtrado vista um](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

Pode mostrar e ocultar elementos no painel de gráficos selecionando o ícone visível, como mostra a imagem seguinte. Para remover completamente as consultas, selecione o **X**vermelho .

  [![Cancele uma opção consultada e filtrada](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [armazenamento e ingresso](./time-series-insights-update-storage-ingress.md) na Pré-visualização de Insights da Série De Tempo Azure.

- Leia o documento de pré-visualização da Série de Tempo insights sobre [modelação de dados](./time-series-insights-update-tsm.md).

- Aprenda [a diagnosticar e resolver problemas](./time-series-insights-update-how-to-troubleshoot.md) na sua instância Time Series Insights.
