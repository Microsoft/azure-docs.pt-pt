---
title: Como implementar a navegação facetada em uma hierarquia de categoria-Azure Search
description: Adicione navegação de faceta a aplicativos que se integram ao Azure Search, um serviço de pesquisa hospedado na nuvem no Microsoft Azure.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 8e325abf1f58458d2fa035c8c8f081173efb0e65
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649901"
---
# <a name="how-to-implement-faceted-navigation-in-azure-search"></a>Como implementar a navegação por facetas no Azure Search
A navegação facetada é um mecanismo de filtragem que fornece navegação de busca detalhada Autodirigida em aplicativos de pesquisa. O termo ' navegação facetada ' pode não ser familiar, mas você provavelmente o usou antes. Como mostra o exemplo a seguir, a navegação facetada não é nada mais do que as categorias usadas para filtrar os resultados.

 ![Demonstração do portal de trabalho Azure Search](media/search-faceted-navigation/azure-search-faceting-example.png "Demonstração do portal de trabalho Azure Search")

A navegação facetada é um ponto de entrada alternativo para pesquisar. Ele oferece uma alternativa conveniente para digitar expressões de pesquisa complexas manualmente. As facetas podem ajudá-lo a encontrar o que você está procurando, garantindo que você não obtenha nenhum resultado. Como desenvolvedor, as facetas permitem que você exponha os critérios de pesquisa mais úteis para navegar no índice de pesquisa. Em aplicativos de varejo online, a navegação facetada geralmente é criada sobre marcas, departamentos (sapatos de criança), tamanho, preço, popularidade e classificações. 

A implementação de navegação facetada difere entre as tecnologias de pesquisa. No Azure Search, a navegação facetada é criada no momento da consulta, usando os campos que você tenha atribuído anteriormente em seu esquema.

-   Nas consultas que seu aplicativo cria, uma consulta deve enviar *parâmetros de consulta de faceta* para obter os valores de filtro de faceta disponíveis para esse conjunto de resultados de documento.

-   Para realmente aparar o conjunto de resultados do documento, o aplicativo também `$filter` deve aplicar uma expressão.

No desenvolvimento de aplicativos, escrever código que construa consultas constitui a grande parte do trabalho. Muitos dos comportamentos de aplicativo que você esperaria da navegação facetada são fornecidos pelo serviço, incluindo suporte interno para definir intervalos e obter contagens de resultados de faceta. O serviço também inclui padrões razoáveis que ajudam a evitar estruturas de navegação difíceis. 

## <a name="sample-code-and-demo"></a>Exemplo de código e demonstração
Este artigo usa um portal de pesquisa de trabalho como exemplo. O exemplo é implementado como um aplicativo MVC ASP.NET.

-   Veja e teste a demonstração de trabalho online em [Azure Search do portal de trabalho](https://azjobsdemo.azurewebsites.net/).

-   Baixe o código do [repositório Azure-Samples no GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

## <a name="get-started"></a>Introdução
Se você for novo no desenvolvimento de pesquisa, a melhor maneira de considerar a navegação facetada é que ela mostra as possibilidades para a pesquisa autodirigida. É um tipo de experiência de pesquisa de busca detalhada, com base em filtros predefinidos, usado para restringir rapidamente os resultados da pesquisa por meio de ações de apontar e clicar. 

### <a name="interaction-model"></a>Modelo de interação

A experiência de pesquisa para navegação facetada é iterativa, portanto, vamos começar compreendendo-a como uma sequência de consultas que se desdobram em resposta às ações do usuário.

O ponto de partida é uma página de aplicativo que fornece navegação facetada, normalmente colocada no periferia. A navegação facetada geralmente é uma estrutura de árvore, com caixas de seleção para cada valor ou texto clicável. 

1. Uma consulta enviada para Azure Search especifica a estrutura de navegação facetada por meio de um ou mais parâmetros de consulta de faceta. Por exemplo, a consulta pode incluir `facet=Rating`, talvez com uma `:values` opção `:sort` ou para refinar ainda mais a apresentação.
2. A camada de apresentação renderiza uma página de pesquisa que fornece navegação facetada, usando as facetas especificadas na solicitação.
3. Dada uma estrutura de navegação facetada que inclui classificação, você clica em "4" para indicar que somente os produtos com uma classificação de 4 ou superior devem ser mostrados. 
4. Em resposta, o aplicativo envia uma consulta que inclui`$filter=Rating ge 4` 
5. A camada de apresentação atualiza a página, mostrando um conjunto de resultados reduzidos, contendo apenas os itens que atendem aos novos critérios (nesse caso, produtos classificados como 4 e acima).

Uma faceta é um parâmetro de consulta, mas não a confunda com a entrada de consulta. Ele nunca é usado como critério de seleção em uma consulta. Em vez disso, considere os parâmetros de consulta de faceta como entradas para a estrutura de navegação que retorna na resposta. Para cada parâmetro de consulta de faceta que você fornecer, Azure Search avalia quantos documentos estão nos resultados parciais para cada valor de faceta.

Observe o `$filter` na etapa 4. O filtro é um aspecto importante da navegação facetada. Embora facetas e filtros sejam independentes na API, você precisará de ambos para fornecer a experiência que pretende. 

### <a name="app-design-pattern"></a>Padrão de design de aplicativo

No código do aplicativo, o padrão é usar parâmetros de consulta de faceta para retornar a estrutura de navegação facetada junto com os resultados da faceta, além de uma expressão $filter.  A expressão de filtro manipula o evento de clique no valor da faceta. Imagine a `$filter` expressão como o código por trás do corte real dos resultados da pesquisa retornados para a camada de apresentação. Dada uma faceta Colors, clicar na cor vermelha é implementada `$filter` por meio de uma expressão que seleciona apenas os itens que têm uma cor de vermelho. 

### <a name="query-basics"></a>Noções básicas de consulta

No Azure Search, uma solicitação é especificada por meio de um ou mais parâmetros de consulta (consulte [Pesquisar documentos](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) para obter uma descrição de cada um). Nenhum dos parâmetros de consulta é necessário, mas você deve ter pelo menos um para que uma consulta seja válida.

A precisão, entendida como a capacidade de filtrar ocorrências irrelevantes, é obtida por meio de uma ou de ambas as expressões:

-   **search=**  
    O valor desse parâmetro constitui a expressão de pesquisa. Pode ser uma única parte do texto ou uma expressão de pesquisa complexa que inclui vários termos e operadores. No servidor, uma expressão de pesquisa é usada para pesquisa de texto completo, consultando campos pesquisáveis no índice para termos de correspondência, retornando resultados em ordem de classificação. Se você definir `search` como NULL, a execução da consulta será sobre o índice inteiro (ou `search=*`seja,). Nesse caso, outros elementos da `$filter` consulta, como um ou perfil de pontuação, são os principais fatores que afetam quais documentos são retornados `($filter`) e em que ordem (`scoringProfile` ou `$orderby`).

-   **$filter=**  
    Um filtro é um mecanismo poderoso para limitar o tamanho dos resultados da pesquisa com base nos valores de atributos específicos do documento. A `$filter` é avaliada primeiro, seguida pela lógica de faceta que gera os valores disponíveis e as contagens correspondentes para cada valor

Expressões de pesquisa complexas diminuem o desempenho da consulta. Sempre que possível, use expressões de filtro bem construídas para aumentar a precisão e melhorar o desempenho da consulta.

Para entender melhor como um filtro adiciona mais precisão, compare uma expressão de pesquisa complexa para uma que inclua uma expressão de filtro:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Ambas as consultas são válidas, mas a segunda será superior se você estiver procurando por não motéis com estacionamento em Seattle.
-   A primeira consulta depende dessas palavras específicas sendo mencionadas ou não mencionadas em campos de cadeia de caracteres como nome, descrição e qualquer outro campo que contenha dados pesquisáveis.
-   A segunda consulta procura correspondências precisas em dados estruturados e provavelmente será muito mais precisa.

Em aplicativos que incluem navegação facetada, certifique-se de que cada ação do usuário em uma estrutura de navegação facetada seja acompanhada de uma limitação dos resultados da pesquisa. Para restringir os resultados, use uma expressão de filtro.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Criar um aplicativo de navegação facetado
Você implementa a navegação facetada com Azure Search no código do aplicativo que cria a solicitação de pesquisa. A navegação facetada depende de elementos no esquema que você definiu anteriormente.

Predefinido no índice de pesquisa é `Facetable [true|false]` o atributo de índice, definido nos campos selecionados para habilitar ou desabilitar seu uso em uma estrutura de navegação facetada. Sem `"Facetable" = true`, um campo não pode ser usado na navegação de faceta.

A camada de apresentação em seu código fornece a experiência do usuário. Ele deve listar as partes constituintes da navegação facetada, como o rótulo, os valores, as caixas de seleção e a contagem. A API REST do Azure Search é independente da plataforma, portanto, use qualquer linguagem e plataforma que desejar. O importante é incluir elementos de interface do usuário que dão suporte à atualização incremental, com estado de interface do usuário atualizado, pois cada faceta adicional é selecionada. 

No momento da consulta, o código do aplicativo cria uma solicitação `facet=[string]`que inclui, um parâmetro de solicitação que fornece o campo pelo qual facetar. Uma consulta pode ter várias facetas, `&facet=color&facet=category&facet=rating`como cada uma separada por um caractere de e comercial (&).

O código do aplicativo também deve `$filter` construir uma expressão para manipular os eventos de clique na navegação facetada. Um `$filter` reduz os resultados da pesquisa, usando o valor da faceta como critérios de filtro.

Azure Search retorna os resultados da pesquisa, com base em um ou mais termos inseridos, juntamente com as atualizações da estrutura de navegação facetada. No Azure Search, a navegação facetada é uma construção de nível único, com valores de faceta e contagens de quantos resultados são encontrados para cada um.

Nas seções a seguir, examinaremos mais detalhadamente como criar cada parte.

<a name="buildindex"></a>

## <a name="build-the-index"></a>Compilar o índice
A facetação está habilitada em uma base de campo por campo no índice, por meio deste atributo de `"Facetable": true`índice:.  
`Facetable` Por padrão, todos os tipos de campo que poderiam ser usados na navegação facetada. Esses tipos de campo `Edm.String`incluem `Edm.DateTimeOffset`, e todos os tipos de campo numéricos (essencialmente, todos os tipos de campo `Edm.GeographyPoint`são facetable, exceto, que não podem ser usados na navegação facetada). 

Ao criar um índice, uma prática recomendada para navegação facetada é desativar explicitamente a faceta de campos que nunca devem ser usados como uma faceta.  Em particular, os campos de cadeia de caracteres para valores singleton, como uma ID ou nome de produto, `"Facetable": false` devem ser definidos como para evitar seu uso acidental (e ineficaz) na navegação facetada. Desativar a faceta de onde você não precisa dela ajuda a manter o tamanho do índice pequeno e geralmente melhora o desempenho.

A seguir, parte do esquema para o aplicativo de exemplo demonstração do portal de trabalho, cortada de alguns atributos para reduzir o tamanho:

```json
{
  ...
  "name": "nycjobs",
  "fields": [
    { “name”: "id",                 "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { “name”: "job_id",             "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { “name”: "agency",              "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "posting_type",        "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "num_of_positions",    "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "business_title",      "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "civil_service_title", "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "title_code_no",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "level",               "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_range_from",   "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_range_to",     "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { “name”: "salary_frequency",    "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { “name”: "work_location",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
…
    { “name”: "geo_location",        "type": "Edm.GeographyPoint",     "searchable": false, "filterable": true, ...  "facetable": false, ... },
    { “name”: "tags",                "type": "Collection(Edm.String)", "searchable": true,  "filterable": true, ...  "facetable": true, ...  }
  ],
…
}
```

Como você pode ver no esquema de exemplo, `Facetable` o é desativado para campos de cadeia de caracteres que não devem ser usados como facetas, como valores de ID. Desativar a faceta de onde você não precisa dela ajuda a manter o tamanho do índice pequeno e geralmente melhora o desempenho.

> [!TIP]
> Como prática recomendada, inclua o conjunto completo de atributos de índice para cada campo. Embora `Facetable` o esteja ativado por padrão para quase todos os campos, definir propositadamente cada atributo pode ajudá-lo a imaginar as implicações de cada decisão de esquema. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Verificar os dados
A qualidade dos seus dados tem um efeito direto sobre se a estrutura de navegação facetada materializa como você espera. Ele também afeta a facilidade de construir filtros para reduzir o conjunto de resultados.

Se você quiser facetar por marca ou preço, cada documento deverá conter valores para *brandname* e *ProductPrice* que sejam válidos, consistentes e produtivos como uma opção de filtro.

Aqui estão alguns lembretes sobre o que Depurar:

* Para cada campo que você deseja facetar, pergunte se ele contém valores que são adequados como filtros na pesquisa autodirigida. Os valores devem ser curtos, descritivos e bastante distintivos para oferecer uma opção clara entre as opções concorrentes.
* Erros de ortografia ou quase correspondência de valores. Se você facetar a cor e os valores de campo incluírem laranja e laranja (um erro de ortografia), uma faceta baseada no campo de cor pegaria ambos.
* O texto de caso misto também pode causar estragos na navegação facetada, com laranja e laranja aparecendo como dois valores diferentes. 
* As versões única e plural do mesmo valor podem resultar em uma faceta separada para cada uma.

Como você pode imaginar, a auditoria na preparação dos dados é um aspecto essencial da navegação facetada efetiva.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>Criar a IU
O trabalho de volta da camada de apresentação pode ajudá-lo a descobrir os requisitos que podem ser perdidos caso contrário, e entender quais recursos são essenciais para a experiência de pesquisa.

Em termos de navegação facetada, a página da Web ou do aplicativo exibe a estrutura de navegação facetada, detecta a entrada do usuário na página e insere os elementos alterados. 

Para aplicativos Web, o AJAX é comumente usado na camada de apresentação porque permite que você atualize as alterações incrementais. Você também pode usar o ASP.NET MVC ou qualquer outra plataforma de visualização que possa se conectar a um serviço de Azure Search por HTTP. O aplicativo de exemplo referenciado em todo este artigo – o **Azure Search demonstração do portal de trabalho** – é um aplicativo MVC ASP.net.

No exemplo, a navegação facetada é incorporada à página de resultados da pesquisa. O exemplo a seguir, extraído `index.cshtml` do arquivo do aplicativo de exemplo, mostra a estrutura HTML estática para exibir a navegação facetada na página de resultados da pesquisa. A lista de facetas é criada ou recriada dinamicamente quando você envia um termo de pesquisa ou seleciona ou limpa uma faceta.

```html
<div class="widget sidebar-widget jobs-filter-widget">
  <h5 class="widget-title">Filter Results</h5>
    <p id="filterReset"></p>
    <div class="widget-content">

      <h6 id="businessTitleFacetTitle">Business Title</h6>
      <ul class="filter-list" id="business_title_facets">
      </ul>

      <h6>Location</h6>
      <ul class="filter-list" id="posting_type_facets">
      </ul>

      <h6>Posting Type</h6>
      <ul class="filter-list" id="posting_type_facets"></ul>

      <h6>Minimum Salary</h6>
      <ul class="filter-list" id="salary_range_facets">
      </ul>

  </div>
</div>
```

O trecho de código a seguir `index.cshtml` da página cria dinamicamente o HTML para exibir a primeira faceta, título comercial. Funções semelhantes criam dinamicamente o HTML para as outras facetas. Cada faceta tem um rótulo e uma contagem, que exibe o número de itens encontrados para esse resultado da faceta.

```js
function UpdateBusinessTitleFacets(data) {
  var facetResultsHTML = '';
  for (var i = 0; i < data.length; i++) {
    facetResultsHTML += '<li><a href="javascript:void(0)" onclick="ChooseBusinessTitleFacet(\'' + data[i].Value + '\');">' + data[i].Value + ' (' + data[i].Count + ')</span></a></li>';
  }

  $("#business_title_facets").html(facetResultsHTML);
}
```

> [!TIP]
> Ao projetar a página de resultados da pesquisa, lembre-se de adicionar um mecanismo para limpar facetas. Se você adicionar caixas de seleção, poderá ver facilmente como limpar os filtros. Para outros layouts, talvez seja necessário um padrão de navegação estrutural ou outra abordagem criativa. Por exemplo, no aplicativo de exemplo do portal de pesquisa de trabalho, você `[X]` pode clicar no depois de uma faceta selecionada para limpar a faceta.

<a name="buildquery"></a>

## <a name="build-the-query"></a>Compilar a consulta
O código que você escreve para criar consultas deve especificar todas as partes de uma consulta válida, incluindo expressões de pesquisa, facetas, filtros, perfis de Pontuação – qualquer coisa usada para formular uma solicitação. Nesta seção, exploraremos onde as facetas se encaixam em uma consulta e como os filtros são usados com facetas para fornecer um conjunto de resultados reduzido.

Observe que as facetas são integrais neste aplicativo de exemplo. A experiência de pesquisa na demonstração do portal de trabalho foi projetada em relação à navegação e aos filtros facetados. O posicionamento proeminente da navegação facetada na página demonstra sua importância. 

Um exemplo é geralmente um bom lugar para começar. O exemplo a seguir, extraído `JobsSearch.cs` do arquivo, cria uma solicitação que cria a navegação da faceta com base no cargo, no local, no tipo de lançamento e no salário mínimo. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

Um parâmetro de consulta de faceta é definido como um campo e, dependendo do tipo de dados, pode ser mais parametrizado por uma lista delimitada por `count:<integer>`vírgulas `interval:<integer>`que inclui `values:<list>`, `sort:<>`, e. Há suporte para uma lista de valores para dados numéricos durante A configuração de intervalos. Consulte [Pesquisar documentos (Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) para obter detalhes de uso.

Juntamente com facetas, a solicitação formulada por seu aplicativo também deve criar filtros para restringir o conjunto de documentos candidatos com base em uma seleção de valor de faceta. Para uma loja de bicicletas, a navegação facetada fornece pistas para perguntas como *quais cores, fabricantes e tipos de bicicletas estão disponíveis?* . A filtragem responde a perguntas como *quais bicicletas exatas são vermelhas, Mountain Bikes, neste intervalo de preços?* . Quando você clica em "vermelho" para indicar que apenas os produtos vermelhos devem ser mostrados, a próxima consulta que `$filter=Color eq ‘Red’`o aplicativo envia inclui.

O trecho de código a seguir `JobsSearch.cs` da página adicionará o título de empresa selecionado ao filtro se você selecionar um valor da faceta de título comercial.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Dicas e práticas recomendadas

### <a name="indexing-tips"></a>Dicas de indexação
**Melhorar a eficiência do índice se você não usar uma caixa de pesquisa**

Se seu aplicativo usar a navegação facetada exclusivamente (ou seja, sem caixa de pesquisa), você poderá marcar o `searchable=false`campo `facetable=true` como, para produzir um índice mais compacto. Além disso, a indexação ocorre apenas em valores inteiros de faceta, sem nenhuma quebra de palavra ou indexação das partes do componente de um valor de várias palavras.

**Especificar quais campos podem ser usados como facetas**

Lembre-se de que o esquema do índice determina quais campos estão disponíveis para uso como uma faceta. Supondo que um campo seja facetable, a consulta especifica quais campos serão facetados. O campo pelo qual você está facetando fornece os valores que aparecem abaixo do rótulo. 

Os valores que aparecem em cada rótulo são recuperados do índice. Por exemplo, se o campo de faceta for *cor*, os valores disponíveis para filtragem adicional serão os valores para esse campo – vermelho, preto e assim por diante.

Somente para valores numéricos e DateTime, você pode definir valores explicitamente no campo de faceta (por exemplo `facet=Rating,values:1|2|3|4|5`,). Uma lista de valores é permitida para esses tipos de campo para simplificar a separação dos resultados da faceta em intervalos contíguos (intervalos com base em valores numéricos ou períodos de tempo). 

**Por padrão, você só pode ter um nível de navegação facetada** 

Como observado, não há suporte direto para aninhar facetas em uma hierarquia. Por padrão, a navegação facetada no Azure Search dá suporte apenas a um nível de filtros. No entanto, existem soluções alternativas. Você pode codificar uma estrutura de faceta hierárquica em `Collection(Edm.String)` um com um ponto de entrada por hierarquia. A implementação dessa solução alternativa está além do escopo deste artigo. 

### <a name="querying-tips"></a>Dicas de consulta
**Validar campos**

Se você criar a lista de facetas dinamicamente com base na entrada de usuário não confiável, valide se os nomes dos campos facetados são válidos. Ou então, escape os nomes ao compilar URLs usando `Uri.EscapeDataString()` o no .net ou o equivalente em sua plataforma de preferência.

### <a name="filtering-tips"></a>Dicas de filtragem
**Aumentar a precisão da pesquisa com filtros**

Use filtros. Se você confiar apenas em expressões de pesquisa, a lematização poderá fazer com que um documento seja retornado e não tenha o valor de faceta preciso em nenhum de seus campos.

**Aumentar o desempenho da pesquisa com filtros**

Filtros reduzem o conjunto de documentos candidatos para pesquisa e os exclui da classificação. Se você tiver um grande conjunto de documentos, usar uma busca detalhada de faceta seletiva geralmente oferecerá um melhor desempenho.
  
**Filtrar somente os campos facetados**

No detalhamento facetado, normalmente você deseja incluir apenas documentos que tenham o valor da faceta em um campo específico (facetado), não em qualquer lugar em todos os campos pesquisáveis. A adição de um filtro reforça o campo de destino direcionando o serviço para pesquisar somente no campo facetado em busca de um valor correspondente.

**Aparar resultados da faceta com mais filtros**

Os resultados da faceta são documentos encontrados nos resultados da pesquisa que correspondem a um termo de faceta. No exemplo a seguir, nos resultados da pesquisa para *computação em nuvem*, 254 itens também têm *especificação interna* como um tipo de conteúdo. Os itens não são necessariamente mutuamente exclusivos. Se um item atender aos critérios de ambos os filtros, ele será contado em cada um deles. Essa duplicação é possível ao facetar `Collection(Edm.String)` em campos, que geralmente são usados para implementar a marcação do documento.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

Em geral, se você achar que os resultados da faceta são consistentemente muito grandes, recomendamos que você adicione mais filtros para fornecer aos usuários mais opções para restringir a pesquisa.

### <a name="tips-about-result-count"></a>Dicas sobre a contagem de resultados

**Limitar o número de itens na navegação da faceta**

Para cada campo facetado na árvore de navegação, há um limite padrão de 10 valores. Esse padrão faz sentido para estruturas de navegação porque mantém a lista de valores em um tamanho gerenciável. Você pode substituir o padrão atribuindo um valor à contagem.

* `&facet=city,count:5`Especifica que apenas as cinco primeiras cidades encontradas nos principais resultados classificados são retornadas como um resultado de faceta. Considere uma consulta de exemplo com um termo de pesquisa de "aeroporto" e 32 correspondências. Se a consulta especificar `&facet=city,count:5`, somente as primeiras cinco cidades exclusivas com a maior parte dos documentos nos resultados da pesquisa serão incluídas nos resultados da faceta.

Observe a distinção entre os resultados da faceta e os resultados da pesquisa. Os resultados da pesquisa são todos os documentos que correspondem à consulta. Os resultados da faceta são as correspondências para cada valor de faceta. No exemplo, os resultados da pesquisa incluem nomes de cidades que não estão na lista de classificação da faceta (5 em nosso exemplo). Os resultados que são filtrados pela navegação facetada se tornam visíveis quando você limpa facetas ou escolhe outras facetas além da cidade. 

> [!NOTE]
> Discutir `count` quando há mais de um tipo pode ser confuso. A tabela a seguir oferece um breve resumo de como o termo é usado na API Azure Search, código de exemplo e documentação. 

* `@colorFacet.count`<br/>
  No código de apresentação, você deve ver um parâmetro de contagem na faceta, usado para exibir o número de resultados da faceta. Nos resultados da faceta, Count indica o número de documentos que correspondem ao período ou ao intervalo da faceta.
* `&facet=City,count:12`<br/>
  Em uma consulta de faceta, você pode definir Count como um valor.  O padrão é 10, mas você pode defini-lo como maior ou menor. A `count:12` configuração Obtém as 12 principais correspondências nos resultados da faceta por contagem de documentos.
* "`@odata.count`"<br/>
  Na resposta da consulta, esse valor indica o número de itens correspondentes nos resultados da pesquisa. Em média, é maior que a soma de todos os resultados da faceta combinados, devido à presença de itens que correspondem ao termo de pesquisa, mas não têm correspondências de valor de faceta.

**Obter contagens nos resultados da faceta**

Quando você adiciona um filtro a uma consulta facetada, talvez queira manter a instrução de faceta (por exemplo, `facet=Rating&$filter=Rating ge 4`). Tecnicamente, faceta = rating não é necessária, mas mantê-la retorna as contagens de valores de faceta para as classificações 4 e superiores. Por exemplo, se você clicar em "4" e a consulta incluir um filtro para maior ou igual a "4", as contagens serão retornadas para cada classificação 4 e superior.  

**Certifique-se de obter contagens de faceta precisas**

Em determinadas circunstâncias, você pode descobrir que as contagens de faceta não correspondem aos conjuntos de resultados (consulte [navegação facetada em Azure Search (postagem do fórum)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

As contagens de faceta podem ser imprecisas devido à arquitetura de fragmentação. Cada índice de pesquisa tem vários fragmentos, e cada fragmento relata as N facetas principais por contagem de documentos, que é então combinada em um único resultado. Se alguns fragmentos tiverem muitos valores correspondentes, enquanto outros têm menos, você verá que alguns valores de faceta estão ausentes ou sob conta nos resultados.

Embora esse comportamento possa mudar a qualquer momento, se você encontrar esse comportamento hoje, poderá contorná-lo artificialmente intensamente a contagem:\<número > a um grande número para impor relatórios completos de cada fragmento. Se o valor de Count: for maior ou igual ao número de valores exclusivos no campo, você estará garantido resultados precisos. No entanto, quando as contagens de documentos são altas, há uma penalidade de desempenho, portanto, use essa opção criteriosamente.

### <a name="user-interface-tips"></a>Dicas de interface do usuário
**Adicionar rótulos para cada campo na navegação da faceta**

Os rótulos normalmente são definidos no HTML ou no formulário`index.cshtml` (no aplicativo de exemplo). Não há API na Azure Search para rótulos de navegação de faceta ou quaisquer outros metadados.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Filtrar com base em um intervalo
A facetação de intervalos de valores é um requisito de aplicativo de pesquisa comum. Os intervalos têm suporte para dados numéricos e valores de data e hora. Você pode ler mais sobre cada abordagem em [Pesquisar documentos (Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

Azure Search simplifica a construção de intervalo fornecendo duas abordagens para a computação de um intervalo. Para ambas as abordagens, Azure Search cria os intervalos apropriados de acordo com as entradas que você forneceu. Por exemplo, se você especificar valores de intervalo de 10 | 20 | 30, ele criará automaticamente intervalos de 0-10, 10-20, 20-30. O aplicativo pode, opcionalmente, remover os intervalos que estiverem vazios. 

**Abordagem 1: Usar o parâmetro Interval**  
Para definir facetas de preço em incrementos de $10, você deve especificar:`&facet=price,interval:10`

**Abordagem 2: Usar uma lista de valores**  
Para dados numéricos, você pode usar uma lista de valores.  Considere o intervalo de faceta para `listPrice` um campo, renderizado da seguinte maneira:

  ![Lista de valores de exemplo](media/search-faceted-navigation/Facet-5-Prices.PNG "Lista de valores de exemplo")

Para especificar um intervalo de faceta como aquele na captura de tela anterior, use uma lista de valores:

    facet=listPrice,values:10|25|100|500|1000|2500

Cada intervalo é criado usando 0 como um ponto de partida, um valor da lista como um ponto de extremidade e, em seguida, cortado do intervalo anterior para criar intervalos discretos. Azure Search faz essas coisas como parte da navegação facetada. Você não precisa escrever código para estruturar cada intervalo.

### <a name="build-a-filter-for-a-range"></a>Criar um filtro para um intervalo
Para filtrar documentos com base em um intervalo selecionado, você pode usar os `"ge"` operadores `"lt"` de filtro e em uma expressão de duas partes que define os pontos de extremidade do intervalo. Por exemplo, se você escolher o intervalo de 10-25 para `listPrice` um campo, o filtro será `$filter=listPrice ge 10 and listPrice lt 25`. No código de exemplo, a expressão de filtro usa os parâmetros **os** e **PriceTo** para definir os pontos de extremidade. 

  ![Consultar um intervalo de valores](media/search-faceted-navigation/Facet-6-buildfilter.PNG "Consultar um intervalo de valores")

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Filtrar com base na distância
É comum ver filtros que ajudam a escolher uma loja, restaurante ou destino com base em sua proximidade com seu local atual. Embora esse tipo de filtro possa parecer como navegação facetada, trata-se apenas de um filtro. Mencionamos isso aqui para aqueles que estão procurando especificamente conselhos de implementação para esse problema de design específico.

Há duas funções geoespaciais em Azure Search, **Geo. distance** e **Geo. interseção**.

* A função **geográfico. distance** retorna a distância em quilômetros entre dois pontos. Um ponto é um campo e outro é uma constante passada como parte do filtro. 
* A função **Geo.** interseccionations retornará true se um determinado ponto estiver dentro de um determinado polígono. O ponto é um campo e o polígono é especificado como uma lista constante de coordenadas passadas como parte do filtro.

Você pode encontrar exemplos de filtro na [sintaxe de expressão OData (Azure Search)](query-odata-filter-orderby-syntax.md).

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Experimentar a demonstração
A demonstração do portal de trabalho Azure Search contém os exemplos mencionados neste artigo.

-   Veja e teste a demonstração de trabalho online em [Azure Search do portal de trabalho](https://azjobsdemo.azurewebsites.net/).

-   Baixe o código do [repositório Azure-Samples no GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

Conforme você trabalha com os resultados da pesquisa, observe a URL quanto a alterações na construção da consulta. Esse aplicativo ocorre para acrescentar facetas ao URI à medida que você seleciona cada um.

1. Para usar a funcionalidade de mapeamento do aplicativo de demonstração, obtenha uma chave do Bing Maps do [centro de desenvolvimento do Bing Maps](https://www.bingmapsportal.com/). Cole-o sobre a chave existente na `index.cshtml` página. A `BingApiKey` configuração`Web.config` no arquivo não é usada. 

2. Execute a aplicação. Faça o Tour opcional ou ignore a caixa de diálogo.
   
3. Insira um termo de pesquisa, como "analista", e clique no ícone de pesquisa. A consulta é executada rapidamente.
   
   Uma estrutura de navegação facetada também é retornada com os resultados da pesquisa. Na página resultado da pesquisa, a estrutura de navegação facetada inclui contagens para cada resultado da faceta. Nenhuma faceta é selecionada, portanto, todos os resultados correspondentes são retornados.
   
   ![Resultados da pesquisa antes de selecionar facetas](media/search-faceted-navigation/faceted-search-before-facets.png "Resultados da pesquisa antes de selecionar facetas")

4. Clique em um título de negócios, local ou salário mínimo. As facetas eram nulas na pesquisa inicial, mas como elas assumem valores, os resultados da pesquisa são cortados dos itens que não correspondem mais.
   
   ![Resultados da pesquisa depois de selecionar facetas](media/search-faceted-navigation/faceted-search-after-facets.png "Resultados da pesquisa depois de selecionar facetas")

5. Para limpar a consulta facetada para que você possa experimentar diferentes comportamentos de consulta, clique `[X]` no após as facetas selecionadas para limpar as facetas.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Saber mais
Assista [Azure Search aprofundamento](https://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). Em 45:25, há uma demonstração sobre como implementar facetas.

Para obter mais informações sobre princípios de design para navegação facetada, recomendamos os seguintes links:

* [Padrões de design: Navegação facetada](https://alistapart.com/article/design-patterns-faceted-navigation)
* [Preocupações de front-end ao implementar pesquisa facetada – parte 1](https://articles.uie.com/faceted_search2/)

