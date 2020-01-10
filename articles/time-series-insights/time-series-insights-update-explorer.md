---
title: Visualizar dados no Gerenciador de visualização-Azure Time Series Insights | Microsoft Docs
description: Saiba mais sobre os recursos e as opções disponíveis no Gerenciador do Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: seodec18
ms.openlocfilehash: d94daa5fbda4ee60ffc6671f7b50126662416043
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75746508"
---
# <a name="azure-time-series-insights-preview-explorer"></a>Gerenciador de visualização do Azure Time Series Insights

Este artigo descreve os vários recursos e opções disponíveis no [aplicativo Web de demonstração](https://insights.timeseries.azure.com/preview/demo)do Azure Time Series insights Preview.

## <a name="prerequisites"></a>Pré-requisitos

Para começar a usar o Azure Time Series Insights Explorer Preview, você deve:

* Ter um ambiente Time Series Insights provisionado. Saiba mais sobre como provisionar uma instância lendo o tutorial de [visualização Azure Time Series insights](./time-series-insights-update-create-environment.md) .
* [Forneça acesso a dados](./time-series-insights-data-access.md) para o ambiente de time Series insights que você criou para a conta. Pode fornecer acesso a outros utilizadores, bem como para si próprio.
* Adicione uma origem de evento ao ambiente de Time Series Insights para enviar dados por push para o ambiente:
  * Saiba [como se conectar a um hub de eventos](./time-series-insights-how-to-add-an-event-source-eventhub.md) 
  * Saiba [como se conectar a um hub IOT](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="explore-the-time-series-insights-preview-explorer"></a>Explore o Gerenciador de visualização do Time Series Insights

O Azure Time Series Insights Preview Explorer consiste nos seguintes sete elementos:

[Visão geral do ![Time Series Insights Preview Explorer](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [Painel de ambiente](#1-environment-panel): exibe todos os seus ambientes de Azure Time Series insights.
1. [Barra de navegação](#2-navigation-bar): permite alternar entre as páginas **analisar** e **modelo** .
1. [Árvore de hierarquia e painel de pesquisa](#3-hierarchy-tree-and-search-panel): permite que você selecione e pesquise elementos de dados específicos para serem gráficos.
1. [Compartimento de série temporal](#4-time-series-well): mostra todos os elementos de dados selecionados no momento.
1. [Painel de gráfico](#5-chart-panel): apresenta o gráfico de trabalho atual.
1. [Linha cronológica](#6-time-editor-panel): permite-lhe modificar o intervalo de tempo de trabalho.
1. [Barra de aplicativos](#7-app-bar): contém as opções de gerenciamento de usuário (como o locatário atual) e permite que você as altere e as configurações de idioma.


## <a name="1-environment-panel"></a>1. painel de ambiente

O painel de ambiente apresenta todos os ambientes do Time Series Insights que tem acesso. A lista inclui ambientes pagos conforme o uso (versão prévia), bem como ambientes S1/S2 (disponibilidade geral). Basta clicar no ambiente de Time Series Insights que você deseja usar para ser imediatamente colocado lá.

1. Selecione a seta suspensa ao lado do seu ambiente exibido.

   [painel de ambiente ![](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. Em seguida, selecione o ambiente desejado.

## <a name="2-navigation-bar"></a>2. barra de navegação

  [![barra de navegação](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

Use a barra de navegação para selecionar entre duas exibições:

* **Analise**: Use-o no gráfico e execute análises avançadas em seus dados de série temporal modelados ou não modelados.
* **Modelo**: Use-o para enviar por push novos Time Series insights tipos de visualização, hierarquias e instâncias para seu modelo de time Series insights.

### <a name="model-authoring"></a>Criação de modelos

A visualização de Azure Time Series Insights dá suporte a operações CRUD (criação, leitura, atualização e exclusão) completas no seu modelo de série temporal.

[![painel de pesquisa de modelo](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **Tipo de modelo de série temporal**: você pode usar tipos de time Series insights para definir variáveis ou fórmulas para fazer cálculos. Eles estão associados a uma determinada instância de Time Series Insights. Um tipo pode ter uma ou mais variáveis.
* **Hierarquia do modelo de série de tempo**: hierarquias são organizações sistemática dos seus dados. Hierarquias retratar as relações entre diferentes entidades nos seus dados do Time Series Insights.
* **Instância do modelo de série de tempo**: as instâncias são a série de tempo propriamente ditas. Na maioria dos casos, eles são **DeviceID** ou **AssetID**, que é o identificador exclusivo do ativo no ambiente.

Para saber mais sobre o modelo de série de tempo, veja [modelos de série de vezes](./time-series-insights-update-tsm.md).

## <a name="3-hierarchy-tree-and-search-panel"></a>3. árvore de hierarquia e painel de pesquisa

A árvore de hierarquia e o painel de pesquisa permitem pesquisar e navegar facilmente na hierarquia do [modelo de série temporal](./time-series-insights-update-tsm.md) para localizar as instâncias de série temporal específicas que você deseja exibir no gráfico. Quando você seleciona suas instâncias, elas não são adicionadas apenas ao gráfico atual, mas também são adicionadas ao bem de dados. 

[árvore de hierarquia de ![e painel de pesquisa](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

O painel resultados da pesquisa também permite exibir os resultados em uma exibição de hierarquia ou em uma exibição de lista, facilitando a localização das instâncias que você deseja exibir.
 
## <a name="4-time-series-well"></a>4. série temporal de tempo

O bem exibe os campos de instância e outros metadados associados às instâncias de Time Series Insights selecionadas. Ao marcar as caixas de seleção no lado direito, você pode ocultar ou exibir instâncias específicas do gráfico atual. 

  [![o compartimento de visualização](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

Você pode remover elementos de dados específicos de seus dados atuais bem, selecionando o controle vermelho **delete** (lixeira) no lado esquerdo do elemento. O também permite que você controle como cada elemento é exibido no gráfico. Você pode optar por adicionar sombras mínimas/máximas, pontos de dados, deslocar o elemento no tempo e visualizar a instância de uma maneira de nível. 

Além disso, o controle explorações permite que você crie facilmente turnos de tempo e dispersão.  

  [![opções de layout bem](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> Se você vir a mensagem a seguir, a instância não terá nenhum dado durante o período de tempo selecionado. Para resolver o problema, aumente o período de tempo ou confirme se a instância está enviando dados por push.
>
> ![Nenhuma notificação de dados](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. painel de gráfico

O gráfico permite que você exiba instâncias de série temporal como linhas. Pode fechar o painel de ambiente, o modelo de dados e o painel de controlo de span de tempo ao clicar em controles da web para aumentar o gráfico. 

  [Visão geral do gráfico de visualização de ![](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **Tipo de gráfico**: controla quais elementos de dados estão disponíveis para visualização.

1. **Tamanho do intervalo**: a ferramenta controle deslizante de tamanho do intervalo permite que você amplie e reduza os intervalos no mesmo período de tempo. Isso fornece um controle mais preciso da movimentação entre grandes fatias de tempo que mostram tendências suaves para fatias tão pequenas quanto o milissegundo, permitindo que você veja cortes granulares e de alta resolução dos seus dados. O ponto de partida padrão do controle deslizante é definido como a exibição ideal dos dados de sua seleção; resolução de balanceamento, velocidade de consulta e granularidade.

1. **Zoom e panorâmica**: clique neste controle para aplicar zoom e panorâmica do gráfico.

1. **Controle do eixo y**: percorre as opções de exibição do eixo y disponíveis:

    * `Stacked`: A cada linha tem um eixo y individual.
    * `Overlap`: Use para empilhar várias linhas no mesmo eixo Y, com os dados do eixo Y mudando com base na linha selecionada.
    * `Shared`: Apresentado em conjunto todos os dados do eixo y.

1. **Elemento Marker**: o elemento de dados selecionado no momento e seus detalhes associados.

Você pode detalhar ainda mais uma fatia de dados específica **clicando com** o botão esquerdo do mouse em um ponto de dados no grafo atual enquanto mantém o rato pressionado e arrastando a área selecionada para o ponto de extremidade de sua escolha. **Clique com o botão direito do mouse** na área azul selecionado e clique em **zoom** , conforme mostrado abaixo. Você também pode exibir e baixar os eventos de telemetria no período de tempo selecionado.

  [![o zoom do gráfico de visualização](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

Depois de executar a ação de **zoom** , você verá o conjunto de dados selecionado. Selecione o controle de formato para percorrer as três representações do eixo y de seus dados de Time Series Insights.

  [![eixo y do gráfico de visualização](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

Aqui, você pode ver um exemplo de um **gráfico sobreposto**:

  [opção ![sobreposição de gráfico](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

O **botão mais ações** se expande para exibir o **download como CSV**, **conectar-se a Power bi**, **Mostrar dados do gráfico como uma tabela**e explorar as opções de **eventos brutos** .

  [opção ![mais ações](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

Saiba mais sobre a opção **conectar-se a Power bi** no [Time Series insights conector Power bi nativo](concepts-power-bi.md).

## <a name="6-time-editor-panel"></a>6. painel de editor de tempo

Ao trabalhar com Time Series Insights primeiro, você selecionará um período de tempo. O período de tempo selecionado controlará o conjunto de dados que está disponível para manipulação com os widgets de atualização de Time Series Insights.

  [painel de seleção de ![tempo](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> Uma parte da linha do tempo é realçada em uma cor âmbar ou laranja para indicar o intervalo de dados que está disponível na loja a quente.

Os seguintes controles da Web estão disponíveis na atualização Time Series Insights para selecionar seu período de tempo de trabalho. 

  [controle bem ![de exploração](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **Controle deslizante de intervalo de datas interna**: Use os dois controles de ponto de extremidade arrastando-os sobre o período de tempo desejado. Esse intervalo de datas interna é restrito pelo controle deslizante de intervalo de datas externa.

1. **Aumentar e diminuir os botões de intervalo de datas**: aumento ou diminuição span de seu tempo ao selecionar o botão para o intervalo que pretende.

1. **Controle de recolhimento**de intervalo de tempo: esse controle da Web permite que você oculte todos os controles, exceto a ferramenta de controle deslizante intervalo de datas internas.

1. **Controle deslizante de intervalo de datas externas**: Use os controles de ponto de extremidade para selecionar o intervalo de datas externas, que estará disponível para o controle de intervalo de datas interna.

1. **Controle deslizante de intervalo de tempo**: Use-o para alternar rapidamente entre as seleções de intervalo de tempo predefinido, como os últimos **30 minutos**, as **últimas 12 horas**ou um **intervalo personalizado**. Alterar este valor também é alterado os intervalos de intervalo disponível discutidos na ferramenta de controlo de deslize do tamanho do intervalo.

   [![de e para o painel de seleção](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. barra de aplicativos

O painel de navegação Time Series Insights visualização aparece na parte superior do seu aplicativo Time Series Insights. Ele fornece as seguintes funcionalidades:

### <a name="current-session-share-link-control"></a>Controlo de sessão atual de ligação de partilha

  [ícone de compartilhamento de ![](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

Selecione o ícone novo **compartilhamento** para compartilhar um link de URL com sua equipe.

  [![compartilhar a URL da instância](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>Secção de inquilino

  [seleção de locatário ![](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* Exibe as informações atuais da sua conta de entrada de Time Series Insights.
* Use-o para alternar entre os temas de Time Series Insights disponíveis.
* Use-o para exibir o [aplicativo Web de demonstração](https://insights.timeseries.azure.com/preview/demo)de visualização.

### <a name="theme-selection"></a>Seleção de tema

Para selecionar um novo tema, selecione o ícone de perfil localizado no canto superior direito. Em seguida, selecione **alterar tema**.

  [seleção de tema de ![](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> A seleção de idioma também está disponível selecionando o ícone de perfil.

Pré-visualização de informações de série de tempo do Azure suporta dois temas:

* **Tema claro**: o tema padrão mostrado em todo este documento.
* **Tema escuro**: renderiza o Gerenciador como mostrado aqui:

  [![tema escuro selecionado](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="s1s2-environment-controls"></a>Controles de ambiente de S1/S2

### <a name="preview-terms-panel"></a>Painel de termos de visualização

Esta secção aplica-se apenas aos ambientes de S1/S2 existentes que tentam utilizar o Explorador na interface do Usuário atualizada. Talvez você queira usar o produto disponível e a visualização em combinação. Adicionámos algumas funcionalidades da interface do Usuário existente para o explorer atualizado, mas pode obter a experiência de interface do Usuário completa para o ambiente de S1/S2 no Explorador do Time Series Insights existente. 

Em vez da hierarquia, você verá o painel Time Series Insights termos, em que você define consultas em seu ambiente. Use-o para filtrar seus dados com base em um predicado.

  [![no painel de consulta](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

O painel de editor de termos de pré-visualização do Time Series Insights precisa dos seguintes parâmetros:

**Em que**: Use a cláusula WHERE para filtrar rapidamente seus eventos usando o conjunto de operandos listados na tabela a seguir. Se realizar uma pesquisa selecionando um operando, o predicado é atualizado automaticamente com base em que a pesquisa. Os tipos de operando com suporte incluem o seguinte:

| Operação | Tipos suportados   | Notas |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Período de tempo de Double, DateTime, | |
| `=`, `!=`, `<>` | Cadeia de caracteres, Bool, Double, DateTime, TimeSpan, nulo |
| `IN` | Cadeia de caracteres, Bool, Double, DateTime, TimeSpan, nulo | Todos os operandos devem ser do mesmo tipo ou ser constante NULL. |
| `HAS` | String | Somente literais de cadeia de caracteres constantes são permitidos no lado direito. Cadeia de caracteres vazia e NULL não são permitidos. |

Para saber mais sobre os tipos de dados e operações de consulta com suporte, confira [TSX (expressão de série temporal)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax).

### <a name="examples-of-where-clauses"></a>Exemplos de cláusulas WHERE

  [![exemplos de cláusula WHERE](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**Medida**: uma lista suspensa que exibe todas as colunas numéricas (**duplos**) que você pode usar como elementos para seu gráfico atual.

**Dividir por**: essa lista suspensa exibe todas as colunas categóricas disponíveis (cadeias de caracteres) em seu modelo no qual você pode agrupar seus dados. Você pode adicionar até cinco termos para exibir no mesmo eixo x. Insira os parâmetros desejados e, em seguida, selecione **Adicionar** para adicionar um novo termo.

  [![exibição consultada e filtrada um](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

Você pode mostrar e ocultar elementos no painel de gráfico selecionando o ícone visível, conforme mostrado na imagem a seguir. Para remover completamente as consultas, selecione o **X**vermelho.

  [![cancelar uma opção consultada e filtrada](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [armazenamento e a entrada](./time-series-insights-update-storage-ingress.md) na visualização de Azure Time Series insights.

- Leia o documento de visualização de Time Series Insights na [modelagem de dados](./time-series-insights-update-tsm.md).

- Saiba [como diagnosticar e solucionar problemas](./time-series-insights-update-how-to-troubleshoot.md) de sua instância de time Series insights.
