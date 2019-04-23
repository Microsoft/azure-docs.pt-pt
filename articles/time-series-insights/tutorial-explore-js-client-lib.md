---
title: 'Tutorial: Explore a biblioteca de cliente do Azure Time Series Insights JavaScript | Documentos da Microsoft'
description: Saiba mais sobre a biblioteca de cliente JavaScript do Azure Time Series Insights e o modelo de programação associado.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 06/05/2018
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: 5e3005eb8f548e562e037431ae5fd89f82ec2100
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60150086"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Tutorial: Explorar a biblioteca de cliente JavaScript do Azure Time Series Insights

Para ajudar os programadores Web a consultar e visualizar dados armazenados no Time Series Insights (TSI), foi desenvolvida uma biblioteca de cliente TSI baseada em D3 para JavaScript.  Utilizando um exemplo de aplicação Web, este tutorial vai orientá-lo ao longo de uma exploração da biblioteca de cliente do TSI e do modelo de programação associado.

Os tópicos deste tutorial dão-lhe oportunidades de experimentar com a biblioteca, para obter uma compreensão mais aprofundada sobre como aceder a dados o TSI e utilizar controlos de gráfico para compor e visualizar dados. O objetivo consiste em disponibilizar-lhe suficiente detalhes, para que possa utilizar a biblioteca na sua própria aplicação Web.

Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
> * O exemplo de aplicação do TSI.
> * A biblioteca de cliente JavaScript do TSI.
> * De que forma é que aplicação de exemplo utiliza a biblioteca para visualizar dados do TSI.

> [!NOTE]
> Os ficheiros de origem de aplicação de exemplo do Time Series Insights podem ser encontrados no fornecido [repositório de exemplo do GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).

## <a name="video"></a>Vídeo: 

### <a name="in-this-video-we-introduce-the-open-source-time-series-insights-javascript-sdkbr"></a>Neste vídeo, apresentamos o SDK em JavaScript open source do Time Series Insights.</br>

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial utiliza a funcionalidade "Ferramentas de programador" (também conhecido como DevTools ou F12) que se encontra na maioria dos browsers modernos, inclusive [Microsoft Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [FireFox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/)entre outros. Se ainda não estiver familiarizado com esta funcionalidade, poderá explorá-la no browser antes de continuar.

## <a name="time-series-insights-sample-application"></a>O exemplo de aplicação do Time Series Insights

Ao longo deste tutorial, o exemplo de aplicação do Time Series Insights vai ser utilizado para explorar o código de origem por trás da aplicação, incluindo a utilização da biblioteca de cliente JavaScript do TSI. O exemplo é uma aplicação Web de página única (SPA) que demonstra como utilizar a biblioteca. O exemplo mostra como consultar e visualizar dados de um exemplo de ambiente de TSI.

1. Navegue para o [exemplo de aplicação do Time Series Insights](https://insights.timeseries.azure.com/clientsample). Verá uma página semelhante à da imagem seguinte com um pedido para iniciar sessão:

   ![Exemplo de pedido de início de sessão do Cliente TSI](media/tutorial-explore-js-client-lib/tcs-sign-in.png)

2. Selecione **Iniciar sessão** introduza ou selecione as suas credenciais. Utilize ou uma conta de empresa ou organização (Azure Active Directory) ou uma conta pessoal (Conta Microsoft ou MSA).

   ![Pedido de credenciais de exemplo de Cliente TSI](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)

3. Após um início de sessão bem-sucedido, verá uma página semelhante à da imagem seguinte. A página mostra vários estilos de gráficos que são povoados com dados TSI. A conta de utilizador e a opção de **Terminar sessão** encontram-se no canto superior direito:

   ![Página principal de exemplo de cliente TSI após o início de sessão](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)

### <a name="page-source-and-structure"></a>Origem e estrutura da página

Primeiro, vamos ver o [código-fonte HTML e JavaScript](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html) que está por trás da página que é processada no seu browser. Não vamos orientá-lo por todos os elementos, mas vai aprender as secções principais, o que lhe dá uma noção do funcionamento da página:

1. Abra **Ferramentas de Programação** no seu browser. Inspecione os elementos HTML que compõem a página atual (também conhecidos como HTML ou árvore DOM).

2. Expanda os elementos `<head>` e `<body>` e repare nas seguintes secções:

   - Sob o elemento `<head>`, vai encontrar elementos que extraem ficheiros adicionais, para ajudar ao funcionamento da página:
     - Um elemento `<script>` que é utilizado para referenciar a Biblioteca de Autenticação do Azure Active Directory **adal.min.js** (também conhecida como ADAL). ADAL é uma biblioteca de JavaScript que fornece autenticação OAuth 2.0 (início de sessão) e aquisição de token para aceder a APIs.
     - Vários elementos `<link>` de folhas de estilos (também conhecidas como CSS) como **sampleStyles.css** e **tsiclient.css**. As folhas de estilos são utilizadas para controlar os detalhes visuais de estilo da página, tais como cores, tipos de letra, espaçamento, entre outros.
     - Um elemento `<script>` que é usado para referenciar a biblioteca JavaScript do TSI **tsiclient.js**. A biblioteca é utilizada pela página para chamar APIs do serviço TSI e para composição de controlos do gráfico na página.

     >[!NOTE]
     > O código de origem para a biblioteca JavaScript do ADAL está disponível no [repositório azure-activedirectory-library-for-js](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > O código de origem da biblioteca JavaScript do Cliente TSI está disponível no [repositório tsiclient](https://github.com/Microsoft/tsiclient).

   - Sob o elemento `<body>`, vai encontrar os elementos `<div>`, que funcionam como contentores para definir o esquema dos itens na página, bem como outro elemento `<script>`:
     - O primeiro elemento `<div>` especifica a caixa de diálogo **Iniciar sessão** (`id="loginModal"`).
     - O segundo elemento `<div>` funciona como elemento principal de:
       - Um elemento de cabeçalho `<div>`, utilizado para mensagens de estado e informações de início de sessão junto ao cimo da página (`class="header"`).
       - Um elemento `<div>` para os restantes elementos do corpo da página, incluindo todos os gráficos (`class="chartsWrapper"`).
       - Uma secção `<script>`, que contém todos os JavaScript utilizados para controlar a página.

   [![Exemplo de Cliente TSI com Ferramentas de Programação](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

3. Expanda o elemento `<div class="chartsWrapper">` e encontrará mais elementos `<div>` subordinados. Estes elementos são utilizados para posicionar cada exemplo de controlo do gráfico. Repare que existem vários pares de elementos `<div>`, um para cada exemplo de gráfico:

   - O primeiro elemento (`class="rowOfCardsTitle"`) contém um título descritivo para resumir o que o gráfico ou gráficos ilustram. Por exemplo: "Gráficos de linhas estáticas com legendas em tamanho normal."
   - O segundo elemento (`class="rowOfCards"`) é um elemento principal, que contém elementos `<div>` subordinados adicionais que posicionam o controlo ou controlos de gráfico reais numa linha.

   ![Elementos div do corpo](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)

4. Agora, expanda o elemento `<script type="text/javascript">` que está diretamente abaixo do elemento `<div class="chartsWrapper">`. Repare no início da secção de JavaScript ao nível da página, que é utilizado para processar toda a lógica da página: autenticação, chamar as APIs de serviço do TSI, compor os controlos de gráficos, entre outras:

   ![Script de corpo](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)

## <a name="tsi-javascript-client-library-concepts"></a>Conceitos da biblioteca de cliente JavaScript do TSI

Embora não a analisaremos em detalhe, essencialmente a biblioteca do Cliente TSI **tsclient.js** fornece uma abstração para duas categorias importantes:

- **Métodos de invólucro para chamar as APIs de consulta do TSI**: APIs REST que permitem a consultar dados TSI utilizando expressões de agregação. Os métodos estão organizados no espaço de nomes `TsiClient.Server` da biblioteca.
- **Métodos para criar e Popular os vários tipos de controles de criação de gráficos**: Métodos que são utilizados para processar os dados agregados do TSI numa página da web. Os métodos estão organizados no espaço de nomes `TsiClient.UX` da biblioteca.

Os seguintes conceitos são universais e aplicam-se às APIs da biblioteca do Cliente TSI em geral.

### <a name="authentication"></a>Autenticação

Conforme mencionado anteriormente, este exemplo é uma SPA que utiliza o suporte para OAuth 2.0 do ADAL para a autenticação dos utilizadores. Eis alguns pontos de interesse nesta secção do script:

1. Quanto se utiliza o ADAL para autenticação, a aplicação cliente deve registar-se a si própria no registo de aplicações do Azure Active Directory (Azure AD). Sendo uma SPA, esta aplicação é registada para utilizar o fluxo de concessão de autorização OAuth 2.0 "implícito". Do mesmo modo, a aplicação especifica algumas das propriedades de registo no tempo de execução, como o GUID de ID do cliente (`clientId`) e o URI de redirecionamento (`postLogoutRedirectUri`), para participar no fluxo.

2. Mais tarde, a aplicação pede um "token de acesso" ao Azure AD. O token de acesso é emitido para um conjunto de permissões finito, para um identificador de serviço/API específico https://api.timeseries.azure.com. O identificador de API/serviço também é conhecido como o token "audience." As permissões do token são emitidas em nome do utilizador com sessão iniciada. O identificador do serviço/API é outra das propriedades que estão presentes no registo da aplicação no Microsoft Azure AD. Depois de o ADAL devolver o token de acesso à aplicação, aquele é transmitido como um "token de portador" durante o aceso às APIs de serviço do TSI.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=4-9,36-39)]

### <a name="control-identification"></a>Identificação dos controlos

Conforme abordado anteriormente, os elementos `<div>` dentro do elemento `<body>` fornecem o esquema para todos os controlos de gráficos que são demonstrados na página. Cada elemento `<div>` especifica propriedades para a colocação e atributos visuais do controlo de gráfico, incluindo uma propriedade `id`. A propriedade `id` fornece um identificador exclusivo, que é utilizado no código JavaScript para identificar e vincular a cada controlo, para composição e atualização.

### <a name="aggregate-expressions"></a>Expressões agregadas

As APIs da biblioteca do Cliente TSI utilizam intensivamente as expressões agregadas. As expressões agregadas oferecem a capacidade de construir um ou mais "termos de pesquisa." As APIs são concebidas de forma semelhante à forma como o [explorador do Time Series Insights](https://insights.timeseries.azure.com/demo), que utiliza os valores de “intervalo de pesquisa”, “onde”, “predicado”, “medidas” e “dividir por”. A maioria das APIs da biblioteca utiliza uma matriz de expressões agregadas, que o serviço utiliza para criar uma consulta de dados do TSI.

### <a name="call-pattern"></a>Padrão de chamada

O preenchimento e a composição dos controlos dos gráficos seguem um padrão geral. Esse padrão é utilizado em todo o JavaScript da página que instancia e carrega os controlos de exemplo de aplicação do TSI:

1. Declare uma `array` para conter uma ou mais expressões agregadas do TSI:

   ```javascript
   var aes =  [];
   ```

2. Crie 1 a n objetos de expressões agregadas e adicione-os à matriz de expressões agregadas:

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```

   **parâmetros de aggregateExpression**

   | Parâmetro | Descrição | Exemplo |
   | --------- | ----------- | ------- |
   | `predicateObject` | A expressão de Filtragem dos dados. |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | O nome da propriedade da medida que é utilizada. | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | As agregações pretendidas da propriedade da medida. | `['avg', 'min']` |
   | `searchSpan`      | A duração e o tamanho do intervalo da expressão agregada. | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | A propriedade da cadeia pela qual pretende dividir (opcional — pode ser nula). | `{property: 'Station', type: 'String'}` |
   | `color`         | A cor dos objetos que pretende compor. | `'pink'` |
   | `alias`           | Um nome amigável para a expressão agregada. | `'Factory3Temperature'` |
   | `contextMenuActions` | Uma matriz das ações a serem vinculadas aos objetos de séries de tempo numa visualização (opcional). | Para obter mais informações, consulte os menus de contexto de pop-up na secção recursos avançados. |

3. Faça uma chamada de consulta TSI utilizando as APIs `TsiClient.Server` para pedir os dados agregados:

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **getAggregates parameters**

   | Parâmetro | Descrição | Exemplo |
   | --------- | ----------- | ------- |
   | `token`     | O Token de acesso para a API do TSI. |  `authContext.getTsiToken()` Para mais informações, veja a [secção de autenticação.](#authentication) |
   | `envFQDN`   | Nome de domínio completamente qualificado (FQDN) para o ambiente do TSI. | Do portal do Azure, por exemplo: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | Uma matriz de expressões de consultas do TSI. | Utilize a variável `aes`, conforme descrito anteriormente: `aes.map(function(ae){return ae.toTsx()}`. |

4. Transforme o resultado comprimido que é devolvido pela Consulta do TSI em JSON, para visualização:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

5. Crie um controlo de gráfico utilizando as APIs `TsiClient.UX` e vincule-o a um dos elementos `<div>` na página:

   ```javascript
   var lineChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

6. Preencha o controlo do gráfico com o objeto ou objetos de dados JSON transformados e componha o controlo na página:

   ```javascript
   lineChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="rendering-controls"></a>Controlos de composição

A biblioteca de cliente TSI expõe atualmente oito controlo analíticos únicos: um gráfico de linhas, um gráfico circular, um gráfico de barras, um mapa térmico, controlos hierárquicos, uma grelha acessível, barras cronológicas de eventos discretos e barras cronológicas de transição de estado.

### <a name="line-bar-pie-chart-examples"></a>Exemplos de gráficos de linhas, barras e circulares

Vamos ver o código que está subjacente a alguns dos controlos de gráficos padrão demonstrados na aplicação, bem como o modelo/padrões de programação para criar os controlos. Mais concretamente, examine a secção do HTML abaixo do comentário `// Example 3/4/5`, o que compõe controlos com os valores de ID `chart3`, `chart4`e `chart5`.

Como vimos no passo 3 da [secção Origem e estrutura da página](#page-source-and-structure), que os controlos dos gráficos são dispostos em linhas na página, cada qual com uma linha com um título descritivo. Neste exemplo, os três gráficos são preenchidos estão no elemento `<div>` de título "Vários Tipos de Gráficos a partir dos Mesmos Dados" e estão vinculados aos três elementos `<div>` abaixo do título:

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

A secção seguinte do código JavaScript utiliza padrões que foram descritos anteriormente: criar expressões agregadas do TSI, utilizá-las para consultar dados do TSI e compor os três gráficos. Repare nos três tipos que são utilizados a partir do espaço de nomes `tsiClient.ux`: `LineChart`, `BarChart` e `PieChart`, para criar e compor os respetivos gráficos. Repare também que os três gráficos podem utilizar os mesmos dados da expressão agregada `transformedResult`:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

Depois de serem compostos, os gráficos aparecem da seguinte forma:

[![Vários Tipos de Gráficos a partir dos Mesmos Dados](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Funcionalidades avançadas

A biblioteca de cliente TSI também expõe algumas funcionalidades avançadas opcionais que poderá querer aproveitar.

### <a name="states-and-events"></a>Estados e eventos

Um exemplo da funcionalidade avançada que é oferecida é a capacidade de poder adicionar transições de estado e eventos discretos aos gráficos. Esta funcionalidade é útil para destacar incidentes, alertas e comutadores de estado, como ligado/desligado.

Veja o código que está subjacente à secção do HTML que está abaixo do comentário `// Example 10`. O código compõe um controlo de linha abaixo do título "Gráficos de linhas com Vários Tipos de Séries", vinculando-o ao elemento `<div>` com o valor de ID `chart10`.

1. Primeiro, é definida uma estrutura denominada `events4`, que vai conter os elementos de alteração de estado a acompanhar. A estrutura contém:

   - Uma chave de cadeia com o nome `Component States`.
   - Uma matriz de objetos de valor que representam os estados. Cada objeto inclui:
     - Uma chave de cadeia que contém um carimbo de data/hora ISO de JavaScript.
     - Uma matriz que contenha as características do estado: uma cor e uma descrição.

2. Em seguida, a estrutura `events5` é definida para "Incidentes," que contém uma matriz dos elementos do evento a acompanhar. A estrutura da matriz tem a mesma forma daquela estrutura que foi descrita para `events4`.

3. Por fim, o gráfico de linhas é composto, transmitindo as duas estruturas com os parâmetros de opções do gráfico: `events:` e `states:`. Repare nos outros parâmetros de opções para especificar um `tooltip:`, `theme:` ou `grid:`.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

Visualmente, os marcadores/janelas de pop-up de diamante são utilizados para indicar incidentes e as barras/janelas de pop-up coloridos junto à escala de tempo indicam as alterações ao estado:

[![Line Charts with Multiple Series Types](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox) Gráficos de Linhas com Vários Tipos de Série

### <a name="pop-up-context-menus"></a>Menus de contexto pop-up

Outro exemplo de funcionalidade avançada é menus de contexto personalizado (menus de pop-up que surgem ao clicar com o botão direito do rato). Menus de contexto personalizado são úteis para ativar ações e passos seguintes lógicos no âmbito da sua aplicação.

Veja o código que está subjacente à secção do HTML que está abaixo do comentário `// Example 13/14/15`. Inicialmente, este código compõe um gráfico de linhas sob o título "Gráfico de Linhas com Menu de Contexto para Criar Gráfico Circular/de Barras", e o gráfico é vinculado ao elemento `<div>` com o valor de ID `chart13`. Utilizando os menus de contexto, o gráfico de linhas proporciona a capacidade de criar dinamicamente um gráfico circular e de barras, que estão vinculados a elementos `<div>` com os IDs `chart14` e `chart15`. Além disso, tanto o gráfico circular, como o de barras, utilizam também os menus de contexto para permitir as suas próprias funcionalidades: a capacidade de copiar dados do gráfico circular para o de barras e imprimir os dados deste último para a janela da consola do browser, respetivamente.

1. Primeiro, é definida uma série de ações predefinidas. Cada ação contém uma matriz com um ou mais elementos. Cada elemento define um item de menu de contexto único:

   - `barChartActions`: Esta ação define o menu de contexto para o gráfico de pizza, que contém um elemento para definir um único item:
     - `name`: O texto que é utilizado para o item de menu: "Parâmetros para o console de impressão".
     - `action`: A ação associada ao item de menu. A ação é sempre uma função anónima que recebe três argumentos que se baseiam na expressão agregada que é utilizada para criar o gráfico. Neste caso, os argumentos são escritos na janela da consola do browser:
       - `ae`: A matriz de expressão de agregação.
       - `splitBy`: O valor de splitBy.
       - `timestamp`: O carimbo de hora.

   - `pieChartActions`: Esta ação define o menu de contexto para o gráfico de barras, que contém um elemento para definir um único item. A forma e o esquema são iguais aos do elemento `barChartActions` acima, mas tenha em conta que a função `action` é significativamente diferente: instancia e compõe o gráfico de barras. Tenha também em atenção que utiliza o argumento `ae` para especificar a matriz de expressão agregada que é transmitida no runtime quando o item de menu abre. A função também define a propriedade `ae.contextMenu` com o menu de contexto `barChartActions`.
   - `contextMenuActions`: Esta ação define o menu de contexto para o gráfico de linhas, que contém três elementos para definir três itens de menu. A forma e o esquema de cada elemento são iguais aos dos anteriores elementos que foram descritos. Tal como o elemento `barChartActions`, o primeiro item escreve os três argumentos da função na janela da consola do browser. Semelhante ao elemento `pieChartActions`, os dois itens seguintes instanciam e compõem os gráficos circular e de barras, respetivamente. Também definem as respetivas propriedades `ae.contextMenu` com os menus de contexto `pieChartActions` e `barChartActions`, respetivamente.

2. Seguidamente, são extraídas duas expressões agregadas para a matriz de expressões agregadas `aes`, e especificam a matriz `contextMenuActions` para cada item. Estas expressões são utilizadas com o controlo de gráfico de linhas.

3. Por fim, só o gráfico de linhas é inicialmente composto, a partir do qual os gráficos circulares e de barras podem ser compostos no momento de execução.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

A captura de ecrã mostra os gráficos com os menus de contexto pop-up correspondentes. Os gráficos circulares e de barras foram criados dinamicamente mediante a utilização das opções do menu de contexto do gráfico de linhas.

[![Gráfico de Linhas com Menu de Contexto para Criar Gráfico Circular/de Barras](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Pincéis

Os pincéis são utilizados para limitar um intervalo de tempo para definir ações como ampliar e explorar.

O código que serve para ilustrar os Pincéis é mostrado no exemplo anterior "Gráfico com o contexto Menu para criar gráfico circular/barra de gráfico de linhas" que descreve os menus de contexto de pop-up.

1. As ações dos pincéis são semelhantes às dos menus de contexto, na medida em que definem uma série de ações personalizadas para o pincel. Cada ação contém uma matriz com um ou mais elementos. Cada elemento define um item de menu de contexto único:
   - `name`: O texto que é utilizado para o item de menu: "Parâmetros para o console de impressão".
   - `action`: A ação associada ao item de menu, que é sempre uma função anônima que aceita dois argumentos. Neste caso, os argumentos são escritos na janela da consola do browser:
      - `fromTime`: Timestamp "de" da seleção de pincel.
      - `toTime`: O "para" período de tempo da seleção de pincel.

2. As ações do pincel são adicionadas como outra propriedade de opção do gráfico. Repare na propriedade `brushContextMenuActions: brushActions` que está a ser transmitida à chamada `linechart.Render`.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

![Gráfico de linhas com menu de contexto para criar gráfico circular/de barras com pincéis](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)

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