---
title: 'Tutorial: Explorar a biblioteca de cliente JavaScript Azure Time Series Insights | Microsoft Docs'
description: Tutorial para saber mais sobre a biblioteca de cliente Azure Time Series Insights JavaScript e o modelo de programação relacionado.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 10/02/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: a5184b49f4608acebbe6bf4734dde99857d16fc9
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71845262"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Tutorial: Explorar a biblioteca de cliente JavaScript do Azure Time Series Insights

A biblioteca de cliente Azure Time Series Insights com base em D3 do JavaScript foi desenvolvida para ajudar os desenvolvedores da Web a consultar e Visualizar dados armazenados no Time Series Insights. Este tutorial orienta você pela biblioteca de cliente Time Series Insights e pelo modelo de programação usando um aplicativo de exemplo hospedado.

O tutorial fornece detalhes sobre como trabalhar com a biblioteca, como acessar dados de Time Series Insights e como usar controles de gráfico para renderizar e Visualizar dados. Você também aprenderá a experimentar diferentes tipos de grafos para visualizar dados. No final do tutorial, você poderá usar a biblioteca de cliente para incorporar Time Series Insights recursos em seu próprio aplicativo Web.

Especificamente, você aprenderá sobre:

> [!div class="checklist"]
> * O aplicativo de exemplo Time Series Insights
> * A biblioteca de cliente Time Series Insights JavaScript
> * Como o aplicativo de exemplo usa a biblioteca para visualizar Time Series Insights dados

> [!NOTE]
> * O tutorial usa uma [demonstração Web](https://insights.timeseries.azure.com/clientsample)gratuita, hospedada Time Series insights.
> * Os arquivos de origem do aplicativo de exemplo Time Series Insights são fornecidos no [repositório de exemplo do GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * Leia a [documentação de referência do cliente Time Series insights](https://github.com/microsoft/tsiclient/blob/master/docs/API.md).

Inscreva-se para obter uma [assinatura gratuita do Azure](https://azure.microsoft.com/free/) se você ainda não tiver uma.

## <a name="prerequisites"></a>Pré-requisitos

* Este tutorial usa o recurso de **ferramentas para desenvolvedores** do seu navegador. Os navegadores da Web modernos ([Microsoft Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [Firefox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/)e outros) normalmente fornecem acesso à **exibição do inspetor da Web** por meio da tecla de atalho F12 no teclado. Outra maneira de acessar a exibição é clicar com o botão direito do mouse em uma página da Web e selecionar **inspecionar elemento**.

## <a name="review-video"></a>Examinar vídeo

Neste vídeo, apresentamos o SDK do JavaScript Time Series Insights software livre:
<br /><br />

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="sample-application"></a>Aplicação de exemplo

Ao longo deste tutorial, usamos um aplicativo de exemplo hospedado Time Series Insights e gratuito para explorar o código-fonte por trás do aplicativo e explorar a biblioteca de cliente Time Series Insights JavaScript. Usando o aplicativo de exemplo, você aprenderá a interagir com Time Series Insights em JavaScript e a Visualizar dados por meio de gráficos.

1. Vá para o [aplicativo de exemplo Time Series insights](https://insights.timeseries.azure.com/clientsample). O prompt de entrada a seguir é exibido:

   [prompt de entrada de exemplo de cliente do insights da série @no__t 1Time](media/tutorial-explore-js-client-lib/tcs-sign-in.png)](media/tutorial-explore-js-client-lib/tcs-sign-in.png#lightbox)

1. Selecione **fazer logon** para inserir ou selecionar suas credenciais. Use uma conta de organização corporativa (Azure Active Directory) ou uma conta pessoal (conta Microsoft).

   [prompt de credenciais de exemplo do cliente do insights da série @no__t 1Time](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png#lightbox)

1. Depois de entrar, uma página que exibe os gráficos populados com Time Series Insights dados é mostrada. A conta de utilizador e a opção de **Terminar sessão** encontram-se no canto superior direito:

   [Página principal de exemplo do cliente do insights da série @no__t 1Time após a entrada](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png#lightbox)

### <a name="page-source-and-structure"></a>Origem e estrutura da página

Primeiro, vamos exibir o [código-fonte HTML e JavaScript](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html) da página da Web renderizada:

1. Abra **Ferramentas de Programação** no seu browser. Inspecione os elementos HTML que compõem a página atual (também conhecidos como HTML ou árvore DOM).

1. Expanda os elementos `<head>` e `<body>` e observe as seguintes seções:

   * No elemento `<head>`, você encontrará metadados de página e dependências que permitem que o aplicativo seja executado:
     * Um elemento `<script>` que é usado para referenciar o arquivo Adal (biblioteca de autenticação Azure Active Directory) *Adal. min. js*. ADAL é uma biblioteca de JavaScript que fornece autenticação OAuth 2.0 (início de sessão) e aquisição de token para aceder a APIs.
     * Vários elementos `<link>` para folhas de estilo (também conhecido como *CSS*) como *sampleStyles. css* e *tsiclient. css*. As folhas de estilo controlam os detalhes de estilo da página visual, como cores, fontes e espaçamento.
     * Um elemento `<script>` usado para fazer referência ao Time Series Insights biblioteca de cliente JavaScript *tsiclient. js*. A página usa a biblioteca para chamar Time Series Insights APIs de serviço e para renderizar controles de gráfico na página.

     >[!NOTE]
     > * O código-fonte para a biblioteca de JavaScript ADAL está disponível no [repositório Azure-ActiveDirectory-library-for-js](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > * O código-fonte para a biblioteca de cliente Time Series Insights JavaScript está disponível no [repositório tsiclient](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).

   * No elemento `<body>`, você encontrará os elementos `<div>`, que ajudam a definir o layout dos itens na página e outro elemento `<script>`:
     * O primeiro elemento `<div>` especifica a caixa de diálogo **de logon** (`id="loginModal"`).
     * O segundo elemento `<div>` funciona como elemento principal de:
       * Um elemento de cabeçalho `<div>`, utilizado para mensagens de estado e informações de início de sessão junto ao cimo da página (`class="header"`).
       * Um elemento `<div>` para o restante dos elementos do corpo da página, incluindo os gráficos (`class="chartsWrapper"`).
       * Uma seção `<script>` que contém o JavaScript que é usado para controlar a página.

   [1Time-amostra de cliente do insights da série @no__t com Ferramentas para Desenvolvedores](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

1. Expanda o elemento `<div class="chartsWrapper">` e você encontrará mais elementos filho `<div>`. Estes elementos são utilizados para posicionar cada exemplo de controlo do gráfico. Há vários pares de elementos `<div>`, um para cada exemplo de gráfico:

   * O primeiro elemento (`class="rowOfCardsTitle"`) contém um título descritivo para resumir o que o gráfico ou gráficos ilustram. Por exemplo: `Static Line Charts With Full-Size Legends.`
   * O segundo elemento (`class="rowOfCards"`) é um pai que contém elementos filho adicionais `<div>` que posicionam os controles reais do gráfico dentro de uma linha.

   [elementos de ![Body div](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png#lightbox)

1. Expanda o elemento `<script type="text/javascript">` que está diretamente abaixo do elemento `<div class="chartsWrapper">`. O início da seção JavaScript de nível de página é usado para lidar com toda a lógica de página (autenticação, chamada Time Series Insights de APIs de serviço, renderização dos controles de gráfico e muito mais):

   [script ![Body](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png#lightbox)

## <a name="javascript-client-library"></a>Biblioteca de cliente JavaScript

A biblioteca de cliente do Time Series Insights (*tsiclient. js*) fornece abstrações para duas funcionalidades importantes do JavaScript:

* **Métodos de wrapper para chamar as APIs de consulta de time Series insights**: APIs REST que você pode usar para consultar dados de Time Series Insights usando expressões de agregação. Os métodos são organizados sob o namespace TsiClient. Server da biblioteca.

* **Métodos para criar e preencher vários tipos de controles de gráficos**: Métodos que você pode usar para renderizar os dados de agregação Time Series Insights em uma página da Web. Os métodos são organizados sob o namespace TsiClient. UX da biblioteca.

Por meio dessas simplificações, os desenvolvedores podem criar com mais facilidade gráficos de interface do usuário e componentes do gráfico que são compatíveis com Time Series Insights dados.

### <a name="authentication"></a>Authentication

O [aplicativo de exemplo Time Series insights](https://insights.timeseries.azure.com/clientsample) é um aplicativo de página única que tem suporte à autenticação de usuário OAuth 2,0 do Adal:

1. Quando você usa a ADAL para autenticação, o aplicativo cliente deve ser registrado no Azure Active Directory (AD do Azure). Na verdade, o aplicativo de página única é registrado para usar o [fluxo de concessão implícita do OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-implicit-grant-flow).
1. O aplicativo deve especificar algumas das propriedades de registro em tempo de execução. As propriedades incluem o GUID do cliente (`clientId`) e o URI de redirecionamento (`postLogoutRedirectUri`).
1. Posteriormente, o aplicativo solicita um *token de acesso* do Azure AD. O token de acesso é emitido para um conjunto finito de permissões para um identificador de API ou serviço específico (https: \//API. timeseries. Azure. com). As permissões do token são emitidas em nome do utilizador com sessão iniciada. O identificador para o serviço ou API é outra propriedade contida no registro do Azure AD do aplicativo.
1. Depois que a ADAL retorna o token de acesso para o aplicativo, ela é passada como um *token de portador* quando acessa as APIs de serviço Time Series insights.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=3-7,34-37)]

> [!TIP]
> Para saber mais sobre as bibliotecas de autenticação do Azure AD com suporte da Microsoft, consulte a [documentação de referência da biblioteca de autenticação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries#microsoft-supported-client-libraries).

### <a name="control-identification"></a>Identificação dos controlos

No exemplo fornecido, os elementos `<div>` são organizados no elemento pai `<body>` para fornecer um layout sensato para os controles de gráfico que são renderizados na página.

Cada elemento `<div>` especifica Propriedades para os atributos de posicionamento e visuais dos controles de gráfico. As propriedades do elemento HTML `id` servem como identificadores exclusivos para associar a controles específicos para renderizar e atualizar dados visualizados.

### <a name="aggregate-expressions"></a>Expressões agregadas

As APIs de biblioteca de cliente Time Series Insights usam expressões de agregação:

* Uma expressão de agregação fornece a capacidade de construir um ou mais *termos de pesquisa*.

* As APIs de cliente são projetadas para fornecer funcionalidade semelhante a outro aplicativo de demonstração (o [Time Series insights Explorer](https://insights.timeseries.azure.com/demo)), que usa os predicados de intervalo de pesquisa, `where`, medidas e valores `splitBy`.

* A maioria das APIs de biblioteca de cliente assume uma matriz de expressões de agregação que o serviço usa para criar uma consulta de dados de Time Series Insights.

### <a name="call-pattern"></a>Padrão de chamada

Preencher e renderizar controles de gráfico segue um padrão geral. Você pode observar o padrão geral em todo o aplicativo de exemplo e pode ajudá-lo ao usar a biblioteca de cliente:

1. Declare um `array` para armazenar uma ou mais expressões de agregação Time Series Insights:

   ```javascript
   var aes =  [];
   ```

1. Compilar objetos de expressão de agregação *1* a *n* . Em seguida, adicione-os à matriz de expressão de agregação:

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```

   **parâmetros de aggregateExpression**

   | Parâmetro | Descrição | Exemplo |
   | --------- | ----------- | ------- |
   | `predicateObject` | A expressão de filtragem de dados |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | O nome da propriedade da medida usada | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | As agregações da Propriedade Measure que você deseja | `['avg', 'min']` |
   | `searchSpan`      | O tamanho da duração e do intervalo da expressão de agregação | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | A propriedade de cadeia de caracteres que você deseja dividir (opcional: pode ser NULL) | `{property: 'Station', type: 'String'}` |
   | `color`         | A cor dos objetos que você deseja renderizar | `'pink'` |
   | `alias`           | Um nome amigável para a expressão de agregação | `'Factory3Temperature'` |
   | `contextMenuActions` | Uma matriz de ações a serem associadas aos objetos de série temporal em uma visualização (opcional) | Para obter mais informações, consulte [menus de contexto pop-up](#pop-up-context-menus). |

1. Chame uma consulta de Time Series Insights usando as APIs TsiClient. Server para solicitar os dados agregados:

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **getAggregates parameters**

   | Parâmetro | Descrição | Exemplo |
   | --------- | ----------- | ------- |
   | `token`     | O token de acesso para a API de Time Series Insights |  `authContext.getTsiToken()`<br />Para obter mais informações, veja [Autenticação](#authentication). |
   | `envFQDN`   | O FQDN (nome de domínio totalmente qualificado) para o ambiente de Time Series Insights | Do portal do Azure. Por exemplo: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | Uma matriz de expressões de consulta de Time Series Insights | Use a variável `aes` conforme descrito anteriormente: `aes.map(function(ae){return ae.toTsx()}`. |

1. Transforme o resultado compactado retornado da consulta Time Series Insights em JSON para visualização:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

1. Crie um controle de gráfico usando as APIs TsiClient. UX. Associe-o a um dos elementos `<div>` na página:

   ```javascript
   var barChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

1. Preencha o controle de gráfico com os objetos de dados JSON transformados e processe o controle na página:

   ```javascript
   barChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="render-controls"></a>Controles de renderização

A biblioteca de cliente do Time Series Insights fornece oito controles de análise exclusivos e prontos para uso:

* **gráfico de linhas**
* **gráfico de pizza**
* **gráfico de barras**
* **calor**
* **controles de hierarquia**
* **grade acessível**
* **linhas do tempo de evento discretos**
* **cronogramas de transição de estado**

### <a name="line-chart-bar-chart-and-pie-chart-examples"></a>Exemplos de gráfico de linhas, gráfico de barras e gráfico de pizza

Examine o código de demonstração usado para renderizar alguns dos controles de gráfico padrão. Observe o modelo de programação e padrões para criar esses controles. Especificamente, examine o HTML no comentário `// Example 3/4/5`, que processa os controles com os valores HTML `id` `chart3`, `chart4` e `chart5`.

Lembre-se da etapa 3 da [seção de origem e estrutura da página](#page-source-and-structure) que os controles do gráfico são organizados em linhas na página. Cada controle de gráfico tem uma linha de título descritiva. Neste exemplo, os três gráficos são preenchidos no elemento `Multiple Chart Types From the Same Data` title `<div>` e são associados aos três elementos `<div>` que estão abaixo do título:

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

A seção a seguir do código JavaScript usa padrões que foram descritos anteriormente: criar Time Series Insights expressões de agregação, usá-las para consultar dados de Time Series Insights e renderizar os três gráficos. Três tipos de gráfico são usados no namespace tsiClient. UX: `LineChart`, `BarChart` e `PieChart`. Os tipos de gráfico são usados para criar e renderizar os respectivos gráficos. Todos os três gráficos podem usar os mesmos dados de expressão de agregação `transformedResult`:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

Depois de serem compostos, os gráficos aparecem da seguinte forma:

[![Vários Tipos de Gráficos a partir dos Mesmos Dados](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="learn-about-advanced-features"></a>Saiba mais sobre os recursos avançados

A biblioteca de cliente Time Series Insights tem vários recursos adicionais que podem ser usados para implementar visualizações de dados de forma criativa.

### <a name="states-and-events"></a>Estados e eventos

Uma funcionalidade avançada é a capacidade de adicionar transições de estado e eventos discretos a gráficos. Esse recurso é útil para realçar incidentes, alertas e criar opções de estado (opções de ligar/desligar, por exemplo).

Examine o código ao redor do comentário `// Example 10`. O código processa um controle linha sob o título `Line Charts with Multiple Series Types` e o associa ao elemento `<div>` com o valor HTML `id` `chart10`.

As etapas a seguir descrevem o processo:

1. Uma estrutura chamada `events4` é definida para manter os elementos de alteração de estado a serem controlados. A estrutura contém:

   * Uma chave de cadeia com o nome `Component States`.
   * Uma matriz de objetos de valor que representam os estados. Cada objeto inclui:
     * Uma chave de cadeia que contém um carimbo de data/hora ISO de JavaScript.
     * Uma matriz que contenha as características do estado: uma cor e uma descrição.

1. A estrutura `events5` é definida para `Incidents`, que contém uma matriz dos elementos de evento a serem controlados. A estrutura da matriz tem a mesma forma daquela estrutura que foi descrita para `events4`.

1. O gráfico de linhas é renderizado e passa as duas estruturas com os parâmetros de opções de gráfico: `events:` e `states:`. Observe os outros parâmetros de opção para especificar um `tooltip:`, `theme:` ou `grid:`.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

As janelas de marcadores/pop-up de losango que são usadas para indicar incidentes e as janelas de barras/pop-up coloridas ao longo da escala de tempo indicam alterações de Estado:

[gráficos de @no__t 1Line com vários tipos de série](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Menus de contexto pop-up

Outra funcionalidade avançada é a capacidade de criar menus de contexto personalizados (clique com o botão direito do mouse em menus pop-up). Menus de contexto personalizado são úteis para ativar ações e passos seguintes lógicos no âmbito da sua aplicação.

Examine o código em volta do comentário `// Example 13/14/15`. Esse código inicialmente renderiza um gráfico de linhas sob o título `Line Chart with Context Menu to Create Pie/Bar Chart`. O gráfico está associado ao elemento `<div>` com o valor HTML `id` `chart13`.

Utilizando os menus de contexto, o gráfico de linhas proporciona a capacidade de criar dinamicamente um gráfico circular e de barras, que estão vinculados a elementos `<div>` com os IDs `chart14` e `chart15`. O gráfico de pizza e o gráfico de barras também usam menus de contexto para habilitar seus próprios recursos: a capacidade de copiar dados do gráfico de pizza para o gráfico de barras e imprimir os dados do gráfico de barras na janela do console do navegador, respectivamente.

As etapas a seguir descrevem o processo:

1. Uma série de ações personalizadas é definida. Cada ação contém uma matriz com um ou mais elementos. Cada elemento define um item de menu de contexto único:

   * `barChartActions`: Esta ação define o menu de contexto do gráfico de pizza, que contém um elemento para definir um único item:
     * `name`: O texto que é usado para o item de menu: "Imprimir parâmetros no console".
     * `action`: A ação associada ao item de menu. A ação é sempre uma função anónima que recebe três argumentos que se baseiam na expressão agregada que é utilizada para criar o gráfico. Neste caso, os argumentos são escritos na janela da consola do browser:
       * `ae`: A matriz de expressão de agregação.
       * `splitBy`: O valor `splitBy`.
       * `timestamp`: O carimbo de data/hora.

   * `pieChartActions`: Essa ação define o menu de contexto para o gráfico de barras, que contém um elemento para definir um único item. A forma e o esquema são os mesmos que o elemento `barChartActions` descrito anteriormente, mas a função `action` é drasticamente diferente: ela cria uma instância e renderiza o gráfico de barras. O argumento `ae` é usado para especificar a matriz de expressão de agregação que é passada em tempo de execução quando o item de menu é aberto. A função também define a propriedade `ae.contextMenu` com o menu de contexto `barChartActions`.
   * `contextMenuActions`: Essa ação define o menu de contexto para o gráfico de linhas, que contém três elementos para definir três itens de menu. A forma e o esquema de cada elemento são os mesmos que os elementos descritos anteriormente. Tal como o elemento `barChartActions`, o primeiro item escreve os três argumentos da função na janela da consola do browser. Semelhante ao elemento `pieChartActions`, os dois segundo itens instanciam e renderizam o gráfico de pizza e o gráfico de barras, respectivamente. Também definem as respetivas propriedades `ae.contextMenu` com os menus de contexto `pieChartActions` e `barChartActions`, respetivamente.

1. Duas expressões de agregação são enviadas para a matriz de expressão de agregação `aes`. Eles especificam a matriz `contextMenuActions` para cada item. Estas expressões são utilizadas com o controlo de gráfico de linhas.

1. Somente o gráfico de linhas é processado inicialmente, do qual o gráfico de pizza e o gráfico de barras podem ser renderizados em tempo de execução.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

A captura de tela a seguir mostra os gráficos com seus respectivos menus de contexto pop-up. O gráfico de pizza e o gráfico de barras foram criados dinamicamente usando as opções do menu de contexto de gráfico de linhas.

[gráfico ![Line com menu de contexto para criar gráfico de pizza e gráfico de barras](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Pincéis

Você pode usar pincéis para definir o escopo de um intervalo de tempo de modo a determinar ações como aplicar zoom e explorar.

O código usado para ilustrar pincéis é mostrado no exemplo `Line Chart with Context Menu to Create Pie/Bar Chart` que descreve os menus de contexto pop-up.

* As ações dos pincéis são semelhantes às dos menus de contexto, na medida em que definem uma série de ações personalizadas para o pincel. Cada ação contém uma matriz que tem um ou mais elementos. Cada elemento define um item de menu de contexto único:
   * `name`: O texto que é usado para o item de menu: "Imprimir parâmetros no console".
   * `action`: A ação associada ao item de menu, que é sempre uma função anônima que usa dois argumentos. Neste caso, os argumentos são escritos na janela da consola do browser:
     * `fromTime`: O carimbo de data/hora `from` da seleção de pincel.
     * `toTime`: O carimbo de data/hora `to` da seleção de pincel.

* As ações do pincel são adicionadas como outra propriedade de opção do gráfico. A propriedade `brushContextMenuActions: brushActions` é passada para a chamada `linechart.Render`.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

[gráfico ![Line com menu de contexto para criar gráfico de pizza e gráfico de barras usando pincéis](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png#lightbox)

## <a name="clean-up-resources"></a>Limpar recursos

Agora que você concluiu o tutorial, limpe os recursos que criou:

1. No menu à esquerda na [portal do Azure](https://portal.azure.com), selecione **todos os recursos**, localize o grupo de recursos Azure Time Series insights.
1. Exclua todo o grupo de recursos (e todos os recursos contidos nele) selecionando **excluir** ou remover cada recurso individualmente.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Entre e explore o Time Series Insights aplicativo de exemplo e sua origem
> * Usar APIs na biblioteca de cliente Time Series Insights JavaScript
> * Usar JavaScript para criar e preencher controles de gráfico com dados de Time Series Insights

O aplicativo de exemplo Time Series Insights usa um conjunto de teste de demonstração. Para saber como você pode criar seu próprio ambiente de Time Series Insights e conjunto de informações, leia o seguinte artigo:

> [!div class="nextstepaction"]
> [Tutorial: Criar um ambiente de Azure Time Series Insights @ no__t-0

Ou então, exiba os arquivos de origem do aplicativo de exemplo Time Series Insights:

> [!div class="nextstepaction"]
> [Time Series Insights repositório de aplicativo de exemplo](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)

Leia a documentação de referência da API de cliente do Time Series Insights:

> [!div class="nextstepaction"]
> [Documentação de referência da API Time Series Insights](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)
