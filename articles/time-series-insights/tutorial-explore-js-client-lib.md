---
title: 'Tutorial: Explore a biblioteca de cliente do Azure Time Series Insights JavaScript | Documentos da Microsoft'
description: Saiba mais sobre a biblioteca de cliente JavaScript do Azure Time Series Insights e o modelo de programação associado.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: 8cb1d06872f7eae04bac934220da9d58982d0f4b
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233726"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Tutorial: Explorar a biblioteca de cliente JavaScript do Azure Time Series Insights

Para ajudar os programadores Web a consultar e visualizar dados armazenados no Time Series Insights (TSI), foi desenvolvida uma biblioteca de cliente TSI baseada em D3 para JavaScript. Este tutorial irá guiá-lo através da biblioteca de cliente do TSI e o modelo de programação, utilizando uma aplicação de exemplo hospedado.

O tutorial apresenta detalhes sobre como trabalhar com a biblioteca, como aceder a dados do TSI e usar os controles de gráfico para compor e visualize os dados. Também aprenderá a fazer experiências com diferentes tipos de gráficos para visualizar os dados. Na conclusão do tutorial, poderá utilizar a biblioteca de cliente para incorporar funcionalidades TSI na sua própria aplicação web.

Especificamente, aprenderá sobre:

> [!div class="checklist"]
> * O exemplo de aplicação do TSI.
> * A biblioteca de cliente JavaScript do TSI.
> * De que forma é que aplicação de exemplo utiliza a biblioteca para visualizar dados do TSI.

> [!NOTE]
> * O Tutorial utiliza uma gratuita e hospedada, [demonstração de web do Time Series Insights](https://insights.timeseries.azure.com/clientsample).
> * Os ficheiros de origem de aplicação de exemplo do Time Series Insights são fornecidos na [repositório de exemplo do GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * Leitura a [documentação de referência de cliente do TSI](https://github.com/microsoft/tsiclient/blob/master/docs/API.md).

## <a name="video"></a>Vídeo

### <a name="in-this-video-we-introduce-the-open-source-time-series-insights-javascript-sdkbr"></a>Neste vídeo, apresentamos o SDK em JavaScript open source do Time Series Insights.</br>

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial utiliza o seu browser **ferramentas de programação** funcionalidade. Browsers modernos ([Microsoft Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [FireFox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/)e outros) oferecem, normalmente, acesso para o **Vista de um Inspetor do web** por meio do `F12` tecla de atalho. Caso contrário, pode ser acedido ao clicar com o botão direito numa página Web e selecionar **inspecionar elemento**.

## <a name="time-series-insights-sample-application"></a>O exemplo de aplicação do Time Series Insights

Neste tutorial, uma aplicação de exemplo do Time Series Insights gratuita e hospedada, é utilizada para explorar o código-fonte por trás do aplicativo e a biblioteca de cliente JavaScript do TSI. Através do mesmo, aprenderá como interagir com o TSI em JavaScript e visualizar dados através de tabelas e gráficos.

1. Navegue para a [aplicação de exemplo do Time Series Insights](https://insights.timeseries.azure.com/clientsample). Será apresentado o início de sessão seguinte na linha de comandos:

   [![Cliente de TSI exemplo início de sessão na linha de comandos](media/tutorial-explore-js-client-lib/tcs-sign-in.png)](media/tutorial-explore-js-client-lib/tcs-sign-in.png#lightbox)

1. Selecione **iniciar sessão** introduza ou selecione as suas credenciais. Utilize uma conta de organização da empresa (Azure Active Directory) ou uma conta pessoal (Microsoft Account ou MSA).

   [![Pedido de credencial do cliente do TSI exemplo](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png#lightbox)

1. Depois de iniciar sessão, verá uma página com vários tipos de gráficos, preenchidos com dados TSI. A conta de utilizador e a opção de **Terminar sessão** encontram-se no canto superior direito:

   [![Página principal do exemplo de cliente do TSI após o início de sessão](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png#lightbox)

### <a name="page-source-and-structure"></a>Origem e estrutura da página

Primeiro, vamos ver o [código-fonte HTML e JavaScript](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html) da web composto paginada:

1. Abra **Ferramentas de Programação** no seu browser. Inspecione os elementos HTML que compõem a página atual (também conhecidos como HTML ou árvore DOM).

1. Expanda a `<head>` e `<body>` elementos e observe as seções a seguir:

   * Sob o `<head>` elemento, encontrará metadados de página e as dependências de que a aplicação ser executada:
     * Um elemento `<script>` que é utilizado para referenciar a Biblioteca de Autenticação do Azure Active Directory **adal.min.js** (também conhecida como ADAL). ADAL é uma biblioteca de JavaScript que fornece autenticação OAuth 2.0 (início de sessão) e aquisição de token para aceder a APIs.
     * Vários elementos `<link>` de folhas de estilos (também conhecidas como CSS) como **sampleStyles.css** e **tsiclient.css**. As folhas de estilos são utilizadas para controlar os detalhes visuais de estilo da página, tais como cores, tipos de letra, espaçamento, entre outros.
     * Um elemento `<script>` que é usado para referenciar a biblioteca JavaScript do TSI **tsiclient.js**. A biblioteca é utilizada pela página para chamar APIs do serviço TSI e para composição de controlos do gráfico na página.

     >[!NOTE]
     > * O código de origem para a biblioteca JavaScript do ADAL está disponível no [repositório azure-activedirectory-library-for-js](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > * O código de origem da biblioteca JavaScript do Cliente TSI está disponível no [repositório tsiclient](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).

   * Sob o `<body>` elemento, encontrará `<div>` elementos, que ajudam a definir o layout de itens na página e outro `<script>` elemento:
     * O primeiro elemento `<div>` especifica a caixa de diálogo **Iniciar sessão** (`id="loginModal"`).
     * O segundo elemento `<div>` funciona como elemento principal de:
       * Um elemento de cabeçalho `<div>`, utilizado para mensagens de estado e informações de início de sessão junto ao cimo da página (`class="header"`).
       * Um elemento `<div>` para os restantes elementos do corpo da página, incluindo todos os gráficos (`class="chartsWrapper"`).
       * Uma secção `<script>`, que contém todos os JavaScript utilizados para controlar a página.

   [![Exemplo de Cliente TSI com Ferramentas de Programação](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

1. Expanda a `<div class="chartsWrapper">` elemento e irá encontrar o filho mais `<div>` elementos. Estes elementos são utilizados para posicionar cada exemplo de controlo do gráfico. Repare que existem vários pares de elementos `<div>`, um para cada exemplo de gráfico:

   * O primeiro elemento (`class="rowOfCardsTitle"`) contém um título descritivo para resumir o que o gráfico ou gráficos ilustram. Por exemplo: `Static Line Charts With Full-Size Legends.`
   * O segundo elemento (`class="rowOfCards"`) é um elemento principal, que contém elementos `<div>` subordinados adicionais que posicionam o controlo ou controlos de gráfico reais numa linha.

   [![Elementos do corpo div](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png#lightbox)

1. Agora, expanda o elemento `<script type="text/javascript">` que está diretamente abaixo do elemento `<div class="chartsWrapper">`. Tenha em atenção o início da seção de JavaScript de nível de página que é utilizada para processar toda a lógica de página (autenticação, chamar o serviço TSI APIs, renderizando os controles de gráfico e muito mais):

   [![Script de corpo](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png#lightbox)

## <a name="tsi-javascript-client-library-concepts"></a>Conceitos da biblioteca de cliente JavaScript do TSI

A biblioteca de cliente do TSI (**tsclient.js**) fornece abstrações para duas funcionalidades importantes do JavaScript:

* **Métodos de invólucro para chamar as APIs de consulta do TSI**: APIs REST que permitem a consultar dados TSI utilizando expressões de agregação. Os métodos estão organizados no espaço de nomes `TsiClient.Server` da biblioteca.

* **Métodos para criar e Popular os vários tipos de controles de criação de gráficos**: Métodos que são utilizados para processar os dados agregados do TSI numa página da web. Os métodos estão organizados no espaço de nomes `TsiClient.UX` da biblioteca.

Por meio desses simplificações, os desenvolvedores podem criar componentes de gráfico e gráfico de interface do Usuário que têm a tecnologia com os dados TSI mais facilmente.

### <a name="authentication"></a>Autenticação

O [aplicação de exemplo do Time Series Insights](https://insights.timeseries.azure.com/clientsample) é uma aplicação de página única com suporte de autenticação de utilizador da ADAL OAuth 2.0:

1. Quando utilizar a ADAL para autenticação, a aplicação cliente tem de estar registada no Azure Active Directory. Na verdade, a aplicação de página única está registada para utilizar o [fluxo de concessão implícito de OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-implicit-grant-flow).
1. Como tal, a aplicação tem de especificar algumas das propriedades de registo em tempo de execução. Estes incluem o GUID do cliente (`clientId`) e o URI de redirecionamento (`postLogoutRedirectUri`).
1. Mais tarde, a aplicação está a solicitar um **token de acesso** do Azure Active Directory. O token de acesso é emitido para um conjunto finito de permissões para um identificador de serviço/API específica (`https://api.timeseries.azure.com`). As permissões do token são emitidas em nome do utilizador com sessão iniciada. O identificador para o serviço/API é outra propriedade que está contida no registo do Azure Active Directory da aplicação.
1. Depois de ADAL devolve o token de acesso à aplicação, ela é passada como um **token de portador** quando acessar o TSI APIs de serviço.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=3-7,34-37)]

> [!TIP]
> Para saber mais sobre o suporte Microsoft bibliotecas ADAL, consulte a [documentação de referência da ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries#microsoft-supported-client-libraries).

### <a name="control-identification"></a>Identificação dos controlos

No exemplo fornecido, `<div>` elementos são dispostos num grafo pai `<body>` elemento para fornecer um layout adequado para todos os controles de gráfico renderizados na página.

Cada `<div>` elemento Especifica propriedades para o posicionamento e atributos visuais de controlos de gráfico. Elemento HTML `id` propriedades servem como identificadores exclusivos para vincular a controles específicos de renderização e atualizar dados visualizados.

### <a name="aggregate-expressions"></a>Expressões agregadas

A biblioteca de cliente do TSI APIs utilizar expressões de agregação:

* Uma expressão de agregação fornece a capacidade de construir um ou mais **termos de pesquisa**.

* As APIs do cliente são concebidas para fornecer uma funcionalidade semelhante para outra aplicação de demonstração de forneça (a [Explorador do Time Series Insights](https://insights.timeseries.azure.com/demo)), que utiliza o intervalo de pesquisa, o predicado where, medidas e o valor de divisão.

* A maioria da biblioteca de clientes APIs demorar uma matriz de expressões de agregação que são utilizados pelo serviço para criar uma consulta de dados do TSI.

### <a name="call-pattern"></a>Padrão de chamada

A povoar e composição de controlos de gráfico segue um padrão geral. Esse padrão geral pode ser observado em toda a aplicação de exemplo e irá ajudá-lo ao utilizar a biblioteca de cliente:

1. Declare uma `array` para conter uma ou mais expressões agregadas do TSI:

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
   | `predicateObject` | A expressão de filtragem de dados. |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | O nome da propriedade da medida que é utilizada. | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | As agregações pretendidas da propriedade da medida. | `['avg', 'min']` |
   | `searchSpan`      | A duração e o tamanho do intervalo da expressão agregada. | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | A propriedade da cadeia pela qual pretende dividir (opcional — pode ser nula). | `{property: 'Station', type: 'String'}` |
   | `color`         | A cor dos objetos que pretende compor. | `'pink'` |
   | `alias`           | Um nome amigável para a expressão agregada. | `'Factory3Temperature'` |
   | `contextMenuActions` | Uma matriz das ações a serem vinculadas aos objetos de séries de tempo numa visualização (opcional). | Para obter mais informações, consulte a secção [menus de contexto de pop-up](#pop-up-context-menus) |

1. Faça uma chamada de consulta TSI utilizando as APIs `TsiClient.Server` para pedir os dados agregados:

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **getAggregates parameters**

   | Parâmetro | Descrição | Exemplo |
   | --------- | ----------- | ------- |
   | `token`     | O Token de acesso para a API do TSI. |  `authContext.getTsiToken()` Para mais informações, veja a [secção de autenticação.](#authentication) |
   | `envFQDN`   | Nome de domínio completamente qualificado (FQDN) para o ambiente do TSI. | Do portal do Azure, por exemplo: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | Uma matriz de expressões de consultas do TSI. | Utilize a variável `aes`, conforme descrito anteriormente: `aes.map(function(ae){return ae.toTsx()}`. |

1. Transforme o resultado comprimido que é devolvido pela Consulta do TSI em JSON, para visualização:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

1. Crie um controlo de gráfico utilizando as APIs `TsiClient.UX` e vincule-o a um dos elementos `<div>` na página:

   ```javascript
   var barChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

1. Preencha o controlo do gráfico com o objeto ou objetos de dados JSON transformados e componha o controlo na página:

   ```javascript
   barChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="rendering-controls"></a>Controlos de composição

A biblioteca de cliente do TSI fornece oito de exclusivo, out-of-the-box, análise controles:

* **Gráfico de linhas**
* **Gráfico circular**
* **Gráfico de barras**
* **heatmap**
* **controles de hierarquia**
* **grelha acessível**
* **evento discretos linhas do tempo**
* **linhas cronológicas de transição de estado**

### <a name="line-bar-pie-chart-examples"></a>Exemplos de gráficos de linhas, barras e circulares

Ver o código de demonstração usado para processar algumas do controle de gráfico padrão. Tenha em atenção o modelo de programação e padrões para a criação desses controles. Especificamente, examine a seção do HTML sob o `// Example 3/4/5` comentário, que renderiza os controles com o HTML `id` valores `chart3`, `chart4`, e `chart5`.

Lembre-se pelo **passo 3** da [seção de origem e a estrutura de página](#page-source-and-structure) que controlos do gráfico são organizados em linhas na página de cada um com uma linha de título descritivo. Neste exemplo, os gráficos de três são preenchidos sob o título `"Multiple Chart Types From the Same Data"` `<div>` elemento e estão vinculados a três `<div>` elementos que estão abaixo do título:

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

A secção seguinte do código JavaScript utiliza padrões que foram descritos anteriormente: criar expressões agregadas do TSI, utilizá-las para consultar dados do TSI e compor os três gráficos. Repare nos três tipos que são utilizados a partir do espaço de nomes `tsiClient.ux`: `LineChart`, `BarChart` e `PieChart`, para criar e compor os respetivos gráficos. Repare também que os três gráficos podem utilizar os mesmos dados da expressão agregada `transformedResult`:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

Depois de serem compostos, os gráficos aparecem da seguinte forma:

[![Vários Tipos de Gráficos a partir dos Mesmos Dados](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Funcionalidades avançadas

A biblioteca de cliente do TSI possui vários recursos adicionais, que pode utilizar para implementar visualizações de dados de forma criativa.

### <a name="states-and-events"></a>Estados e eventos

Uma funcionalidade avançada é a capacidade de adicionar as transições de estado e eventos discretos para gráficos. Esta funcionalidade é útil para incidentes de realce, alertas e criação de comutadores de estado (/ desativar comutadores, por exemplo).

Examinar o código que envolvem o `// Example 10` comentário. O código processa um controle de linha sob o título `"Line Charts with Multiple Series Types"`e liga-o para o `<div>` elemento com o HTML `id` valor `chart10`.

1. Em primeiro lugar, uma estrutura com o nome `events4` é definida para manter os elementos de alteração de estado para controlar. A estrutura contém:

   * Uma chave de cadeia com o nome `Component States`.
   * Uma matriz de objetos de valor que representam os estados. Cada objeto inclui:
     * Uma chave de cadeia que contém um carimbo de data/hora ISO de JavaScript.
     * Uma matriz que contenha as características do estado: uma cor e uma descrição.

2. Em seguida, o `events5` estrutura é definida para `Incidents`, que contém uma matriz dos elementos de eventos para controlar. A estrutura da matriz tem a mesma forma daquela estrutura que foi descrita para `events4`.

3. Por fim, o gráfico de linhas é composto, transmitindo as duas estruturas com os parâmetros de opções do gráfico: `events:` e `states:`. Repare nos outros parâmetros de opções para especificar um `tooltip:`, `theme:` ou `grid:`.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

Visualmente, os marcadores/janelas de pop-up de diamante são utilizados para indicar incidentes e as barras/janelas de pop-up coloridos junto à escala de tempo indicam as alterações ao estado:

[![Line Charts with Multiple Series Types](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox) Gráficos de Linhas com Vários Tipos de Série

### <a name="pop-up-context-menus"></a>Menus de contexto pop-up

Outra funcionalidade avançada é a capacidade de criar menus de contexto personalizados (menus pop-up com botão direito do mouse). Menus de contexto personalizado são úteis para ativar ações e passos seguintes lógicos no âmbito da sua aplicação.

Olhar o código em volta a `// Example 13/14/15` comentário. Este código renderiza inicialmente um gráfico de linhas sob o título `"Line Chart with Context Menu to Create Pie/Bar Chart"` e o gráfico está vinculado ao `<div>` elemento com o HTML `id` valor `chart13`.

Utilizando os menus de contexto, o gráfico de linhas proporciona a capacidade de criar dinamicamente um gráfico circular e de barras, que estão vinculados a elementos `<div>` com os IDs `chart14` e `chart15`. Além disso, tanto o gráfico circular, como o de barras, utilizam também os menus de contexto para permitir as suas próprias funcionalidades: a capacidade de copiar dados do gráfico circular para o de barras e imprimir os dados deste último para a janela da consola do browser, respetivamente.

1. Primeiro, é definida uma série de ações predefinidas. Cada ação contém uma matriz com um ou mais elementos. Cada elemento define um item de menu de contexto único:

   * `barChartActions`: Esta ação define o menu de contexto para o gráfico de pizza, que contém um elemento para definir um único item:
     * `name`: O texto que é utilizado para o item de menu: "Parâmetros para o console de impressão".
     * `action`: A ação associada ao item de menu. A ação é sempre uma função anónima que recebe três argumentos que se baseiam na expressão agregada que é utilizada para criar o gráfico. Neste caso, os argumentos são escritos na janela da consola do browser:
       * `ae`: A matriz de expressão de agregação.
       * `splitBy`: O `splitBy` valor.
       * `timestamp`: O carimbo de hora.

   * `pieChartActions`: Esta ação define o menu de contexto para o gráfico de barras, que contém um elemento para definir um único item. A forma e o esquema são iguais aos do elemento `barChartActions` acima, mas tenha em conta que a função `action` é significativamente diferente: instancia e compõe o gráfico de barras. Tenha também em atenção que utiliza o argumento `ae` para especificar a matriz de expressão agregada que é transmitida no runtime quando o item de menu abre. A função também define a propriedade `ae.contextMenu` com o menu de contexto `barChartActions`.
   * `contextMenuActions`: Esta ação define o menu de contexto para o gráfico de linhas, que contém três elementos para definir três itens de menu. A forma e o esquema de cada elemento são iguais aos dos anteriores elementos que foram descritos. Tal como o elemento `barChartActions`, o primeiro item escreve os três argumentos da função na janela da consola do browser. Semelhante ao elemento `pieChartActions`, os dois itens seguintes instanciam e compõem os gráficos circular e de barras, respetivamente. Também definem as respetivas propriedades `ae.contextMenu` com os menus de contexto `pieChartActions` e `barChartActions`, respetivamente.

2. Seguidamente, são extraídas duas expressões agregadas para a matriz de expressões agregadas `aes`, e especificam a matriz `contextMenuActions` para cada item. Estas expressões são utilizadas com o controlo de gráfico de linhas.

3. Por fim, só o gráfico de linhas é inicialmente composto, a partir do qual os gráficos circulares e de barras podem ser compostos no momento de execução.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

A captura de ecrã mostra os gráficos com os menus de contexto pop-up correspondentes. Os gráficos circulares e de barras foram criados dinamicamente mediante a utilização das opções do menu de contexto do gráfico de linhas.

[![Gráfico de Linhas com Menu de Contexto para Criar Gráfico Circular/de Barras](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Pincéis

Os pincéis são utilizados para limitar um intervalo de tempo para definir ações como ampliar e explorar.

O código que serve para ilustrar os Pincéis é mostrado no exemplo anterior "Gráfico com o contexto Menu para criar gráfico circular/barra de gráfico de linhas" que descreve os menus de contexto de pop-up.

1. As ações dos pincéis são semelhantes às dos menus de contexto, na medida em que definem uma série de ações personalizadas para o pincel. Cada ação contém uma matriz com um ou mais elementos. Cada elemento define um item de menu de contexto único:
   * `name`: O texto que é utilizado para o item de menu: "Parâmetros para o console de impressão".
   * `action`: A ação associada ao item de menu, que é sempre uma função anônima que aceita dois argumentos. Neste caso, os argumentos são escritos na janela da consola do browser:
      * `fromTime`: O `from` timestamp da seleção de pincel.
      * `toTime`: O `to` timestamp da seleção de pincel.

2. As ações do pincel são adicionadas como outra propriedade de opção do gráfico. Repare na propriedade `brushContextMenuActions: brushActions` que está a ser transmitida à chamada `linechart.Render`.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

[![Gráfico de linhas com o menu de contexto para criar o gráfico de pizza/barras com pincéis](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png#lightbox)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Iniciar sessão e explorar o exemplo de aplicação do TSI e a respetiva origem.
> * Utilize a biblioteca de cliente JavaScript do TSI.
> * Utilizar o JavaScript para criar e preencher os controlos dos gráficos com dados do TSI.

Como visto, a aplicação de exemplo do TSI utiliza um conjunto de dados de demonstração. Para saber como pode criar o seu ambiente do TSI e o seu próprio conjunto de dados, avance para o próximo artigo:

> [!div class="nextstepaction"]
> [Tutorial: Criar um ambiente do Azure Time Series Insights](tutorial-create-populate-tsi-environment.md)

Ou ver os ficheiros de origem do aplicativo de exemplo TSI:

> [!div class="nextstepaction"]
> [Repositório de aplicação de exemplo do TSI](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)

Leia o documentação de referência da API de cliente do TSI:

> [!div class="nextstepaction"]
> [Documentação de referência de API do TSI](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)
