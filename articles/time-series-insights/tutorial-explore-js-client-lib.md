---
title: 'Tutorial: Explore a biblioteca de cliente do Azure Time Series Insights JavaScript | Documentos da Microsoft'
description: Saiba mais sobre a biblioteca de cliente JavaScript do Azure Time Series Insights e o modelo de programação associado.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: c6cfd2069851138d738b1533eaab74d9d7aedda6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243991"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Tutorial: Explorar a biblioteca de cliente JavaScript do Azure Time Series Insights

A biblioteca de clientes baseados em JavaScript D3 Azure Time Series Insights foi desenvolvida para ajudar a consulta de desenvolvedores da web e visualize os dados armazenados no Time Series Insights. Este tutorial orienta-o através da biblioteca de cliente do Time Series Insights e o modelo de programação, utilizando uma aplicação de exemplo hospedado.

O tutorial apresenta detalhes sobre como trabalhar com a biblioteca, como acessar dados do Time Series Insights e como usar controles de gráfico para compor e visualizar dados. Também aprenderá a fazer experiências com diferentes tipos de gráficos para visualizar os dados. No final do tutorial, poderá usar a biblioteca de cliente para incorporar as funcionalidades do Time Series Insights na sua própria aplicação web.

Especificamente, aprenderá sobre:

> [!div class="checklist"]
> * O aplicativo de exemplo do Time Series Insights
> * A biblioteca de cliente JavaScript do Time Series Insights
> * Como o aplicativo de exemplo utiliza a biblioteca para visualizar os dados do Time Series Insights

> [!NOTE]
> * O tutorial utiliza uma gratuita e hospedada [demonstração de web do Time Series Insights](https://insights.timeseries.azure.com/clientsample).
> * Os ficheiros de origem de aplicação de exemplo do Time Series Insights são fornecidos na [repositório de exemplo do GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * Leitura a [documentação de referência do Time Series Insights cliente](https://github.com/microsoft/tsiclient/blob/master/docs/API.md).

## <a name="video"></a>Vídeo

Neste vídeo, apresentamos o JavaScript SDK do código-fonte aberto Time Series Insights:
<br /><br />

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial utiliza o seu browser **ferramentas de programação** funcionalidade. Browsers modernos ([Microsoft Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [Firefox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/)e outros) oferecem, normalmente, acesso para o **Vista de um Inspetor do web** por meio da tecla de atalho F12 no teclado. Outra forma de acessar a exibição é com o botão direito numa página Web e, em seguida, selecione **inspecionar elemento**.

## <a name="time-series-insights-sample-application"></a>O exemplo de aplicação do Time Series Insights

Neste tutorial, utilizamos uma aplicação de exemplo do Time Series Insights gratuita e hospedada para explorar o código-fonte por trás do aplicativo e explorar a biblioteca de cliente JavaScript do Time Series Insights. Ao utilizar a aplicação de exemplo, aprenderá como interagir com o Time Series Insights em JavaScript e visualizar dados através de tabelas e gráficos.

1. Vá para o [aplicação de exemplo do Time Series Insights](https://insights.timeseries.azure.com/clientsample). É apresentado o seguinte pedido de início de sessão:

   [![Time Series Insights cliente exemplo início de sessão pedido](media/tutorial-explore-js-client-lib/tcs-sign-in.png)](media/tutorial-explore-js-client-lib/tcs-sign-in.png#lightbox)

1. Selecione **iniciar sessão** introduza ou selecione as suas credenciais. Utilize uma conta de organização da empresa (Azure Active Directory) ou uma conta pessoal (conta Microsoft).

   [![Credenciais de exemplo do Time Series Insights cliente solicitar](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png#lightbox)

1. Depois de iniciar sessão, é apresentada uma página que apresenta gráficos preenchidos com dados do Time Series Insights. A conta de utilizador e a opção de **Terminar sessão** encontram-se no canto superior direito:

   [![Time Series Insights cliente principal página de exemplo após o início de sessão](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png#lightbox)

### <a name="page-source-and-structure"></a>Origem e estrutura da página

Primeiro, vamos ver o [código-fonte HTML e JavaScript](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html) da página renderizada Web:

1. Abra **Ferramentas de Programação** no seu browser. Inspecione os elementos HTML que compõem a página atual (também conhecidos como HTML ou árvore DOM).

1. Expanda a `<head>` e `<body>` elementos e observe as seções a seguir:

   * Sob o `<head>` elemento, encontrará os metadados de página e as dependências de que a aplicação ser executada:
     * R `<script>` elemento que é utilizado para referenciar o ficheiro do Azure Active Directory Authentication Library (ADAL) *adal.min.js*. ADAL é uma biblioteca de JavaScript que fornece autenticação OAuth 2.0 (início de sessão) e aquisição de token para aceder a APIs.
     * Várias `<link>` elementos de folhas de estilo (também conhecido como *CSS*), como *sampleStyles.css* e *tsiclient.css*. As folhas de estilo controlam detalhes de estilo de página visual, como cores, fontes e espaçamento.
     * R `<script>` elemento que é utilizado para referenciar a biblioteca de cliente do Time Series Insights JavaScript *tsiclient.js*. A página usa a biblioteca para chamar as APIs de serviço do Time Series Insights e para processar os controles de gráfico na página.

     >[!NOTE]
     > * O código-fonte para a biblioteca de JavaScript da ADAL está disponível na [repositório azure-activedirectory-library-para-js](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > * O código-fonte para a biblioteca de cliente JavaScript do Time Series Insights está disponível na [tsiclient repositório](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).

   * Sob o `<body>` elemento, encontrará `<div>` elementos, que ajudam a definir o layout de itens na página e outro `<script>` elemento:
     * A primeira `<div>` elemento Especifica os **iniciar sessão** caixa de diálogo (`id="loginModal"`).
     * O segundo elemento `<div>` funciona como elemento principal de:
       * Um elemento de cabeçalho `<div>`, utilizado para mensagens de estado e informações de início de sessão junto ao cimo da página (`class="header"`).
       * R `<div>` elemento para o resto dos elementos do corpo da página, incluindo os gráficos (`class="chartsWrapper"`).
       * A `<script>` seção que contém o JavaScript que é utilizado para controlar a página.

   [![Exemplo de cliente de informações de série de tempo com ferramentas de programador](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

1. Expanda a `<div class="chartsWrapper">` elemento e irá encontrar o filho mais `<div>` elementos. Estes elementos são utilizados para posicionar cada exemplo de controlo do gráfico. Existem vários pares de `<div>` elementos, um para cada exemplo de gráfico:

   * O primeiro elemento (`class="rowOfCardsTitle"`) contém um título descritivo para resumir o que o gráfico ou gráficos ilustram. Por exemplo: `Static Line Charts With Full-Size Legends.`
   * A segunda (`class="rowOfCards"`) elemento é um elemento principal que contém os subordinados adicionais `<div>` elementos posicionar os controlos do gráfico real dentro de uma linha.

   [![Elementos do corpo div](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png#lightbox)

1. Expanda a `<script type="text/javascript">` elemento que está diretamente abaixo o `<div class="chartsWrapper">` elemento. O início da seção de JavaScript de nível de página é usado para lidar com toda a lógica de página (autenticação, chamar o serviço de Time Series Insights APIs, renderizando os controles de gráfico e muito mais):

   [![Script de corpo](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png#lightbox)

## <a name="time-series-insights-javascript-client-library-concepts"></a>Conceitos de biblioteca de cliente do Time Series Insights JavaScript

A biblioteca de cliente do Time Series Insights (*tsclient.js*) fornece abstrações para duas funcionalidades importantes do JavaScript:

* **Métodos de invólucro para chamar as APIs de consulta de informações de série de tempo**: APIs REST, que pode utilizar para consultar o Time Series Insights dados usando expressões de agregação. Os métodos são organizados de acordo com o espaço de nomes de TsiClient.Server da biblioteca.

* **Métodos para criar e Popular os vários tipos de controles de criação de gráficos**: Métodos que pode utilizar para processar o Time Series Insights agregam dados numa página Web. Os métodos são organizados de acordo com o espaço de nomes de TsiClient.UX da biblioteca.

Através desses simplificações, os desenvolvedores podem criar mais facilmente as componentes de gráfico e gráfico de interface do Usuário que têm a tecnologia com os dados do Time Series Insights.

### <a name="authentication"></a>Autenticação

O [aplicação de exemplo do Time Series Insights](https://insights.timeseries.azure.com/clientsample) é uma aplicação de página única que tem suporte de autenticação de utilizador da ADAL OAuth 2.0:

1. Quando utiliza a ADAL para autenticação, a aplicação cliente tem de estar registada no Azure Active Directory (Azure AD). Na verdade, a aplicação de página única está registada para utilizar o [fluxo de concessão implícito de OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-implicit-grant-flow).
1. A aplicação tem de especificar algumas das propriedades de registo em tempo de execução. As propriedades incluem o GUID do cliente (`clientId`) e o URI de redirecionamento (`postLogoutRedirectUri`).
1. Mais tarde, a aplicação está a solicitar um *token de acesso* do Azure AD. O token de acesso é emitido para um conjunto finito de permissões para um serviço específico ou o identificador de API (https:\//api.timeseries.azure.com). As permissões do token são emitidas em nome do utilizador com sessão iniciada. O identificador para o serviço ou a API é outra propriedade que está contida no registo da aplicação do Azure AD.
1. Depois de ADAL devolve o token de acesso à aplicação, ela é passada como um *token de portador* quando ele acessa as APIs do serviço do Time Series Insights.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=3-7,34-37)]

> [!TIP]
> Para saber mais sobre as bibliotecas de autenticação da Microsoft com suporte do Azure AD, veja a [documentação de referência do Azure Active Directory Authentication Library](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries#microsoft-supported-client-libraries).

### <a name="control-identification"></a>Identificação dos controlos

No exemplo fornecido, `<div>` elementos são dispostos num grafo pai `<body>` elemento para fornecer um layout adequado para o gráfico controla esse render na página.

Cada `<div>` elemento Especifica propriedades para o posicionamento e atributos visuais de controlos de gráfico. Elemento HTML `id` propriedades servem como identificadores exclusivos para vincular a controles específicos para processar e atualizar visualizados dados.

### <a name="aggregate-expressions"></a>Expressões agregadas

A biblioteca de cliente do Time Series Insights APIs utilizar expressões de agregação:

* Uma expressão de agregação fornece a capacidade de construir um ou mais *termos de pesquisa*.

* O cliente APIs foram concebidas para fornecer uma funcionalidade semelhante para outra aplicação de demonstração (a [Explorador do Time Series Insights](https://insights.timeseries.azure.com/demo)), que utiliza span, de pesquisa `where` predicados, medidas, e `splitBy` valores.

* Biblioteca de cliente a maioria das APIs tirar uma matriz de expressões de agregação que o serviço utiliza para criar uma consulta de dados do Time Series Insights.

### <a name="call-pattern"></a>Padrão de chamada

A povoar e composição de controlos de gráfico segue um padrão geral. Pode observar o padrão geral em toda a aplicação de exemplo e pode ajudá-lo ao utilizar a biblioteca de cliente:

1. Declarar um `array` para conter um ou mais expressões de agregação de Time Series Insights:

   ```javascript
   var aes =  [];
   ```

1. Crie *1* ao *n* agregar objetos de expressão. Em seguida, adicioná-los para a matriz de expressão de agregação:

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```

   **parâmetros de aggregateExpression**

   | Parâmetro | Descrição | Exemplo |
   | --------- | ----------- | ------- |
   | `predicateObject` | A expressão de filtragem de dados |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | O nome da propriedade da medida que é utilizado | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | As agregações da propriedade de medida que pretende | `['avg', 'min']` |
   | `searchSpan`      | O tamanho de duração e o intervalo da expressão de agregação | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | A propriedade de cadeia de caracteres que pretende dividir por (opcional: pode ser nulo) | `{property: 'Station', type: 'String'}` |
   | `color`         | A cor dos objetos que pretende processar | `'pink'` |
   | `alias`           | Um nome amigável para a expressão de agregação | `'Factory3Temperature'` |
   | `contextMenuActions` | Uma matriz de ações em estar vinculado aos objetos de série de tempo numa visualização (opcional) | Para obter mais informações, consulte [menus de contexto de pop-up](#pop-up-context-menus). |

1. Chame uma consulta do Time Series Insights com as APIs de TsiClient.Server para os dados agregados do pedido:

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **getAggregates parameters**

   | Parâmetro | Descrição | Exemplo |
   | --------- | ----------- | ------- |
   | `token`     | O token de acesso para a API de informações de série de tempo |  `authContext.getTsiToken()`<br />Para obter mais informações, veja [Autenticação](#authentication). |
   | `envFQDN`   | O nome de domínio completamente qualificado (FQDN) para o ambiente do Time Series Insights | No portal do Azure. Por exemplo: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | Uma matriz de expressões de consulta do Time Series Insights | Utilize o `aes` variável, conforme descrito anteriormente: `aes.map(function(ae){return ae.toTsx()}`. |

1. Transforme o resultado comprimido que é retornado a partir da consulta de Time Series Insights em JSON para visualização:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

1. Crie um controle de gráfico com as APIs de TsiClient.UX. Vinculá-lo a um do `<div>` elementos na página:

   ```javascript
   var barChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

1. Preencher o controle do gráfico com os objetos de dados JSON transformados e processar o controle na página:

   ```javascript
   barChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="render-controls"></a>Controles de composição

A biblioteca de cliente do Time Series Insights fornece oito controles de análise de out-of-the-box exclusivo:

* **Gráfico de linhas**
* **Gráfico circular**
* **Gráfico de barras**
* **heatmap**
* **controles de hierarquia**
* **grelha acessível**
* **evento discretos linhas do tempo**
* **linhas cronológicas de transição de estado**

### <a name="line-chart-bar-chart-and-pie-chart-examples"></a>Gráfico de linhas, gráfico de barras e exemplos de gráfico circular

Examinar o código de demonstração que é usado para processar alguns dos controles de gráfico padrão. Tenha em atenção que o modelo de programação e padrões para a criação desses controles. Especificamente, examine o HTML sob o `// Example 3/4/5` comentário, que renderiza os controles com o HTML `id` valores `chart3`, `chart4`, e `chart5`.

Lembre-se no passo 3 do [secção de origem e a estrutura de página](#page-source-and-structure) que controlos do gráfico são organizados em linhas na página. Cada controle de gráfico tem uma linha de título descritivo. Neste exemplo, os gráficos de três são preenchidos sob o `Multiple Chart Types From the Same Data` title `<div>` elemento e estão vinculados a três `<div>` elementos que estão abaixo do título:

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

A seção de código JavaScript a seguir utiliza padrões que foram descritos anteriormente: criar expressões de agregação do Time Series Insights, usá-los para consultar os dados do Time Series Insights e, em seguida, renderizar os gráficos de três. Três tipos de gráficos são utilizados do espaço de nomes tsiClient.ux: `LineChart`, `BarChart`, e `PieChart`. Os tipos de gráficos são utilizados para criar e processar os respectivos gráficos. Todos os gráficos de três podem utilizar os mesmos dados de expressão de agregação `transformedResult`:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

Depois de serem compostos, os gráficos aparecem da seguinte forma:

[![Vários Tipos de Gráficos a partir dos Mesmos Dados](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Funcionalidades avançadas

A biblioteca de cliente do Time Series Insights tem várias funcionalidades adicionais que pode utilizar para implementar visualizações de dados de forma criativa.

### <a name="states-and-events"></a>Estados e eventos

Uma funcionalidade avançada é a capacidade de adicionar as transições de estado e eventos discretos para gráficos. Esta funcionalidade é útil para incidentes de realce, alertas e criação de comutadores de estado (/ desativar comutadores, por exemplo).

Examinar o código que envolvem o `// Example 10` comentário. O código processa um controle de linha sob o título `Line Charts with Multiple Series Types` e liga-o para o `<div>` elemento com o HTML `id` valor `chart10`.

Os passos seguintes descrevem o processo:

1. Uma estrutura denominada `events4` é definida para manter os elementos de alteração de estado para controlar. A estrutura contém:

   * Uma chave de cadeia com o nome `Component States`.
   * Uma matriz de objetos de valor que representam os estados. Cada objeto inclui:
     * Uma chave de cadeia que contém um carimbo de data/hora ISO de JavaScript.
     * Uma matriz que contenha as características do estado: uma cor e uma descrição.

1. O `events5` estrutura é definida para `Incidents`, que contém uma matriz dos elementos de eventos para controlar. A estrutura da matriz tem a mesma forma daquela estrutura que foi descrita para `events4`.

1. O gráfico de linhas é processado e passa em duas estruturas com as opções de gráfico parâmetros: `events:` e `states:`. Tenha em atenção os outros parâmetros de opção para especificar uma `tooltip:`, `theme:`, ou `grid:`.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

A losango marcadores/pop-se do windows que é utilizado para indicar a incidentes e a coloridos barras/pop-se do windows ao longo da escala de tempo indica as alterações de estado:

[![Gráficos de linhas com vários tipos de série](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Menus de contexto pop-up

Outra funcionalidade avançada é a capacidade de criar menus de contexto personalizados (menus pop-up com botão direito do mouse). Menus de contexto personalizado são úteis para ativar ações e passos seguintes lógicos no âmbito da sua aplicação.

Ver o código em todo o `// Example 13/14/15` comentário. Este código renderiza inicialmente um gráfico de linhas sob o título `Line Chart with Context Menu to Create Pie/Bar Chart`. O gráfico está vinculado ao `<div>` elemento com o HTML `id` valor `chart13`.

Utilizando os menus de contexto, o gráfico de linhas proporciona a capacidade de criar dinamicamente um gráfico circular e de barras, que estão vinculados a elementos `<div>` com os IDs `chart14` e `chart15`. O gráfico de pizza e o gráfico de barras também utilizam menus de contexto para habilitar os seus próprios recursos: a capacidade de copiar dados de gráfico circular para o gráfico de barras e imprimir os dados de gráfico de barras para a janela de consola do browser, respectivamente.

Os passos seguintes descrevem o processo:

1. Uma série de ações personalizadas são definidos. Cada ação contém uma matriz com um ou mais elementos. Cada elemento define um item de menu de contexto único:

   * `barChartActions`: Esta ação define o menu de contexto para o gráfico de pizza, que contém um elemento para definir um único item:
     * `name`: O texto que é utilizado para o item de menu: "Parâmetros para o console de impressão".
     * `action`: A ação associada ao item de menu. A ação é sempre uma função anónima que recebe três argumentos que se baseiam na expressão agregada que é utilizada para criar o gráfico. Neste caso, os argumentos são escritos na janela da consola do browser:
       * `ae`: A matriz de expressão de agregação.
       * `splitBy`: O `splitBy` valor.
       * `timestamp`: O carimbo de hora.

   * `pieChartActions`: Esta ação define o menu de contexto para o gráfico de barras, que contém um elemento para definir um único item. A forma e o esquema é igual a `barChartActions` elemento descrito anteriormente, mas o `action` função é significativamente diferente: ele cria uma instância e compõe o gráfico de barras. O `ae` argumento é utilizado para especificar a matriz de expressão de agregação que é transmitida em tempo de execução quando abre o item de menu. A função também define a propriedade `ae.contextMenu` com o menu de contexto `barChartActions`.
   * `contextMenuActions`: Esta ação define o menu de contexto para o gráfico de linhas, que contém três elementos para definir três itens de menu. A forma e o esquema para cada elemento é o mesmo que os elementos que foram descritos anteriormente. Tal como o elemento `barChartActions`, o primeiro item escreve os três argumentos da função na janela da consola do browser. Semelhante a `pieChartActions` elemento, os segundo dois itens instanciar e compor o gráfico de pizza e um gráfico de barras, respectivamente. Também definem as respetivas propriedades `ae.contextMenu` com os menus de contexto `pieChartActions` e `barChartActions`, respetivamente.

1. Duas expressões de agregação são enviadas no `aes` matriz de expressão de agregação. Especifica o `contextMenuActions` matriz para cada item. Estas expressões são utilizadas com o controlo de gráfico de linhas.

1. O gráfico de linhas inicialmente é processado, do qual o gráfico de pizza e o gráfico de barras podem ser processados no tempo de execução.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

Captura de ecrã seguinte mostra os gráficos com os menus de contexto de pop-up respectivos. O gráfico de pizza e um gráfico de barras foram criadas dinamicamente com as opções de menu de contexto de gráfico de linha.

[![Gráfico de linhas com o menu de contexto para criar o gráfico de pizza e de gráfico de barras](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Pincéis

Pode usar pincéis para definir âmbito de um intervalo de tempo para definir ações como zoom e explorar.

O código que serve para ilustrar os Pincéis é mostrado no `Line Chart with Context Menu to Create Pie/Bar Chart` exemplo que descreve os menus de contexto de pop-up.

* As ações dos pincéis são semelhantes às dos menus de contexto, na medida em que definem uma série de ações personalizadas para o pincel. Cada ação contém uma matriz que tenha um ou mais elementos. Cada elemento define um item de menu de contexto único:
   * `name`: O texto que é utilizado para o item de menu: "Parâmetros para o console de impressão".
   * `action`: A ação associada ao item de menu, que é sempre uma função anônima que aceita dois argumentos. Neste caso, os argumentos são escritos na janela da consola do browser:
     * `fromTime`: O `from` timestamp da seleção de pincel.
     * `toTime`: O `to` timestamp da seleção de pincel.

* As ações do pincel são adicionadas como outra propriedade de opção do gráfico. O `brushContextMenuActions: brushActions` propriedade é passado para o `linechart.Render` chamar.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

[![Gráfico de linhas com o menu de contexto para criar o gráfico de pizza e de gráfico de barras com pincéis](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png#lightbox)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Iniciar sessão e explorar a aplicação de exemplo do Time Series Insights e a respetiva origem
> * Utilizar APIs na biblioteca de cliente do Time Series Insights JavaScript
> * Utilizar JavaScript para criar e Popular os controles de gráfico com dados do Time Series Insights

O aplicativo de exemplo do Time Series Insights utiliza um conjunto de dados de demonstração. Para saber como pode criar seu próprio ambiente do Time Series Insights e o conjunto de dados, leia o artigo seguinte:

> [!div class="nextstepaction"]
> [Tutorial: Criar um ambiente do Azure Time Series Insights](tutorial-create-populate-tsi-environment.md)

Em alternativa, ver os ficheiros de origem de aplicação de exemplo do Time Series Insights:

> [!div class="nextstepaction"]
> [Repositório de aplicação de exemplo do Time Series Insights](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)

Leia o documentação de referência da API de cliente do Time Series Insights:

> [!div class="nextstepaction"]
> [Documentação de referência do Time Series Insights API](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)
