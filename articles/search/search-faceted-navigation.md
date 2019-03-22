---
title: Como implementar a navegação por facetas numa hierarquia de categoria - Azure Search
description: Adicione a navegação de faceta para aplicações integradas no Azure Search, um serviço de pesquisa alojado na cloud no Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/10/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: f1d557396f17357afd6f9e5d701e946668fcee65
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57861048"
---
# <a name="how-to-implement-faceted-navigation-in-azure-search"></a>Como implementar a navegação por facetas no Azure Search
Navegação por facetas é um mecanismo de filtragem que fornece navegação de Detalhamento auto-direcionada em aplicativos de pesquisa. O termo 'navegação por facetas' pode estar familiarizado, mas provavelmente usou antes. Como mostra o exemplo seguinte, a navegação por facetas é nada mais do que as categorias, utilizadas para filtrar os resultados.

 ![Demonstração de Portal de tarefa de pesquisa do Azure][1]

Navegação por facetas é um ponto de entrada alternativa para procurar. Ele oferece uma alternativa conveniente para escrever expressões de pesquisa complexa manualmente. Facetas podem ajudar a encontrar o que está procurando, garantindo que não obtém zero resultados. Como desenvolvedor, facetas permitem-lhe expor os critérios de pesquisa mais úteis para navegar de corpo. Em aplicativos do setor do retalho online, navegação por facetas baseia-se, muitas vezes, ao longo de marcas, departamentos (sapatos para crianças), tamanho, preço, popularidade e classificações. 

Implementando a navegação por facetas difere em tecnologias de pesquisa. No Azure Search, navegação por facetas baseia-se no momento da consulta, com campos que anteriormente atribuído no seu esquema.

-   Nas consultas que cria a aplicação, terá de enviar uma consulta *parâmetros de consulta de faceta* para obter valores de filtro da faceta disponível para esse conjunto de resultados do documento.

-   Para cortar, na verdade, o resultado de documento definido, o aplicativo também tem de aplicar um `$filter` expressão.

No seu desenvolvimento de aplicativos, escrever um código que constrói as consultas constitui grande parte do trabalho. Muitos dos comportamentos de aplicativo que esperaria no painel de navegação por facetas são fornecidos pelo serviço, incluindo suporte interno para definir intervalos e ao obter as contagens de resultados de faceta. O serviço também inclui as predefinições sensíveis que o ajudam a evitar as estruturas de navegação complicado. 

## <a name="sample-code-and-demo"></a>Código de exemplo e demonstração
Este artigo utiliza um portal de pesquisa de tarefa como exemplo. O exemplo é implementado como um aplicativo ASP.NET MVC.

-   Ver e testar a demonstração de trabalho online no [demonstração de Portal do Azure Search tarefa](https://azjobsdemo.azurewebsites.net/).

-   Baixe o código a partir da [repositório de exemplos do Azure no GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

## <a name="get-started"></a>Introdução
Se estiver familiarizado com o desenvolvimento de pesquisa, a melhor forma de pensar em navegação por facetas é que ele mostra as possibilidades de pesquisa orientada. É um tipo de experiência de pesquisa de desagregação, com base nos filtros predefinidos, usados para rapidamente restringir os resultados da pesquisa por meio de ações de apontar e clicar. 

### <a name="interaction-model"></a>Modelo de interação

A experiência de pesquisa para navegação por facetas é interativa, então Vamos começar por entendê-lo como uma seqüência de consultas que Desdobrar em resposta a ações do usuário.

O ponto de partida é uma página de aplicativo que fornece navegação por facetas, normalmente é colocada na periferia. Navegação por facetas, muitas vezes, é uma estrutura de árvore, com caixas de verificação para cada valor ou o texto clicável. 

1. Uma consulta enviada para o Azure Search Especifica a estrutura de navegação por facetas por meio de um ou mais parâmetros de consulta de faceta. Por exemplo, a consulta pode incluir `facet=Rating`, talvez com um `:values` ou `:sort` opção para refinar ainda mais a apresentação.
2. A camada de apresentação renderiza uma página de pesquisa que fornece navegação por facetas, com as facetas especificadas no pedido.
3. Devido uma estrutura de navegação por facetas, que inclui a classificação, clicar em "4" para indicar que apenas os produtos com uma classificação de 4 ou superior devem ser mostrados. 
4. Em resposta, a aplicação envia uma consulta que inclui `$filter=Rating ge 4` 
5. A camada de apresentação atualiza a página, que mostra um conjunto de resultados reduzida, que contém apenas os itens que satisfazem os critérios de novo (neste caso, os produtos classificaram 4 e até).

Uma faceta é um parâmetro de consulta, mas não a confunda com entrada de consulta. Nunca é utilizado como critério de seleção de uma consulta. Em vez disso, considere os parâmetros de consulta de faceta como entradas para a estrutura de navegação retornado na resposta. Para cada parâmetro de consulta de faceta que indicar, o Azure Search avalia o número de documentos são nos resultados parciais para cada valor de faceta.

Tenha em atenção o `$filter` no passo 4. O filtro é um aspecto importante de navegação por facetas. Embora e filtros de facetas são independentes na API, precisa para fornecer a experiência de que pretende. 

### <a name="app-design-pattern"></a>Padrão de design de aplicações

No código da aplicação, o padrão é usar parâmetros de consulta de faceta para retornar a estrutura de navegação por facetas, juntamente com os resultados de Faceta, além de uma expressão de $filter.  A expressão de filtro processa o evento de clique no valor de faceta. Considere o `$filter` expressão, como o código por trás da remoção de real dos resultados da pesquisa retornado para a camada de apresentação. Dada uma faceta de cores, clicando a cor vermelho é implementado por meio de um `$filter` expressão que seleciona apenas os itens que tenham uma cor de vermelho. 

### <a name="query-basics"></a>Noções básicas de consulta

No Azure Search, um pedido é especificado por meio de um ou mais parâmetros de consulta (consulte [documentos sobre pesquisa](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) para obter uma descrição de cada um deles). Nenhum dos parâmetros de consulta são necessárias, mas tem de ter, pelo menos, um para que uma consulta ser válida.

Precisão, entendido como a capacidade para filtrar os resultados irrelevantes, é possível por meio de um ou ambos estas expressões:

-   **search=**  
    O valor deste parâmetro constitui a expressão de pesquisa. Pode ser uma única parte do texto, ou uma expressão de pesquisa complexas que inclui vários termos e operadores. No servidor, é utilizada uma expressão de pesquisa para pesquisa em texto completo, consultar os campos pesquisáveis no índice para efetuar a correspondência de termos, retornando resultados na ordem de classificação. Se definir `search` como nulo, a consulta de execução está sobre o índice completo (ou seja, `search=*`). Neste caso, outros elementos da consulta, por exemplo, um `$filter` ou o perfil de classificação são fatores principais que afetam os documentos que são devolvidos `($filter`) e por que ordem (`scoringProfile` ou `$orderby`).

-   **$filter=**  
    Um filtro é um mecanismo poderoso para limitar o tamanho dos resultados da pesquisa com base nos valores de atributos de documento específico. A `$filter` é avaliada em primeiro lugar, seguido de lógica de facetamento que gera os valores disponíveis e contagens correspondentes para cada valor

As expressões de pesquisa complexa diminuir o desempenho da consulta. Sempre que possível, utilize expressões de filtro bem construído para aumentar a precisão e melhorar o desempenho de consulta.

Para compreender melhor como um filtro adiciona mais precisão, compare uma expressão de pesquisa complexas de forma que inclui uma expressão de filtro:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Ambas as consultas são válidas, mas a segunda é superior se estiver procurando por não-cheios de baratas com estacionamento em Seattle.
-   A primeira consulta se baseia nessas palavras específicas que está a ser mencionados ou não mencionado em campos de cadeia de caracteres, como o nome, descrição e qualquer outro campo que contém dados pesquisáveis.
-   A segunda consulta procura por correspondências exatas em dados estruturados e é provável que seja muito mais precisa.

Em aplicativos que incluem a navegação por facetas, certifique-se de que cada ação do utilizador ao longo de uma estrutura de navegação por facetas seja acompanhada por uma restrição de resultados da pesquisa. Para restringir os resultados, utilize uma expressão de filtro.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Criar uma aplicação de navegação por facetas
Implementar a navegação por facetas com o Azure Search no código da aplicação que cria a solicitação de pesquisa. A navegação por facetas baseia-se em elementos no seu esquema que definiu anteriormente.

Predefinidos em sua pesquisa o índice é o `Facetable [true|false]` atributo de índice, definir nos campos selecionados para ativar ou desativar seu uso numa estrutura de navegação por facetas. Sem `"Facetable" = true`, um campo não pode ser utilizado na navegação de faceta.

A camada de apresentação em seu código fornece a experiência do usuário. Ele deve listar as partes constituintes do painel de navegação por facetas, como a etiqueta, valores, caixas de verificação e a contagem. A API de REST do Azure Search é independente de plataforma, por isso, utilize qualquer linguagem e plataforma que quiser. O importante é incluir elementos de interface do Usuário que suportam a atualização incremental, com o estado de interface do Usuário atualizada à medida que cada faceta adicional está selecionada. 

No momento da consulta, o código da aplicação cria um pedido que inclui `facet=[string]`, um parâmetro de pedido que fornece o campo de faceta pelo. Uma consulta pode ter várias facetas, tais como `&facet=color&facet=category&facet=rating`, cada um deles separados por um caractere e comercial (&).

Código do aplicativo também deve construir um `$filter` expressão para manipular os eventos de clique no painel de navegação por facetas. A `$filter` reduz os resultados da pesquisa, usando o valor de faceta como critérios de filtro.

O Azure Search devolve os resultados da pesquisa, com base num ou mais termos que introduzir, juntamente com as atualizações para a estrutura de navegação por facetas. No Azure Search, navegação por facetas é uma construção de nível de único, com valores de Faceta, contagens e quantos resultados são encontradas para cada um deles.

Nas seções a seguir, vamos dar uma olhada mais de perto como criar cada parte.

<a name="buildindex"></a>

## <a name="build-the-index"></a>Criar o índice
Facetamento é ativado numa base de campo por campo no índice, por meio desse atributo de índice: `"Facetable": true`.  
Todos os tipos de campo, possivelmente, poderiam ser usados no painel de navegação por facetas são `Facetable` por predefinição. Esses tipos de campo incluem `Edm.String`, `Edm.DateTimeOffset`e todos os tipos de campo numérico (essencialmente, todos os tipos de campo são facetável, exceto `Edm.GeographyPoint`, que não pode ser utilizado no painel de navegação por facetas). 

Ao criar um índice, uma prática recomendada para navegação por facetas é explicitamente desativar facetamento para os campos que nunca devem ser usados como um aspeto.  Em particular, os campos de cadeia de caracteres para os valores de singleton, como um nome de ID ou o produto devem ser definidos como `"Facetable": false` para evitar seu uso acidental (e ineficaz) no painel de navegação por facetas. A ativação da facetamento desativado em que não precisa ajuda a manter o tamanho do índice pequeno e normalmente melhora o desempenho.

Segue-se parte do esquema para a aplicação de exemplo da demonstração do Portal de tarefa, cortado de alguns atributos para reduzir o tamanho:

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

Como pode ver no esquema de exemplo, `Facetable` estiver desativado para campos de cadeia de caracteres que não devem ser usados como facetas, tais como valores de ID. A ativação da facetamento desativado em que não precisa ajuda a manter o tamanho do índice pequeno e normalmente melhora o desempenho.

> [!TIP]
> Como melhor prática, inclua o conjunto completo de atributos de índice para cada campo. Embora `Facetable` está ativada por predefinição para quase todos os campos, propositadamente definir cada atributo pode ajudá-lo a considerar as implicações de cada decisão de esquema. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Verifique os dados
A qualidade dos seus dados tem um efeito direto na se a estrutura de navegação por facetas materializa conforme espera. Também afeta a facilidade de criação de filtros para reduzir o conjunto de resultados.

Se quiser faceta por marca ou preço, cada documento deve conter valores para *BrandName* e *ProductPrice* que são válidas, consistente e produtivo como uma opção de filtro.

Aqui estão alguns lembretes do que limpar para:

* Para cada campo que pretende faceta por, pergunte-se se contém valores que são adequadas como filtros de pesquisa orientada. Os valores devem ser suficientemente distintas para oferecer uma escolha clara entre as opções de concorrentes, curto e descritivo.
* Erros de ortografia ou valores quase correspondentes. Se faceta na cor e os valores de campo incluem laranja e Ornage (um problema de ortografia), uma faceta com base no campo de cor escolheria a ambos.
* Texto de capitalização misto também pode causar estragos na navegação por facetas, com a orange e a cor de laranja serem exibidos como dois valores diferentes. 
* Versões de únicas e plurais do mesmo valor podem resultar numa faceta separada para cada.

Como pode imaginar, cuidado na preparação dos dados é um aspecto essencial de navegação por facetas em vigor.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>Criar a IU
Trabalho volta da camada de apresentação pode ajudá-lo a descobrir os requisitos que podem ser perdidos em caso contrário e compreender que capacidades são essenciais para a experiência de pesquisa.

Em termos de navegação por facetas, a sua página de aplicativo web ou apresenta a estrutura de navegação por facetas, Deteta a entrada do usuário na página e insere os elementos alterados. 

Para aplicações web, o AJAX normalmente é usado na camada de apresentação porque ela permite que Atualize as alterações incrementais. Também poderia usar o ASP.NET MVC ou qualquer outra plataforma de visualização que pode ligar a um serviço de Azure Search através de HTTP. O aplicativo de exemplo referenciado no decorrer deste artigo, o **demonstração de Portal do Azure Search tarefa** – é um aplicativo ASP.NET MVC.

No exemplo, navegação por facetas baseia-se para a página de resultados de pesquisa. O exemplo seguinte, retirado do `index.cshtml` página de resultados de arquivo do aplicativo de exemplo, mostra o HTML estático de estrutura para exibir a navegação por facetas na pesquisa. A lista de facetas é criada ou reconstruída dinamicamente quando submeter um termo de pesquisa, ou selecionar ou desmarcar uma faceta.

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

O seguinte fragmento de código do `index.cshtml` página cria o HTML para exibir a faceta primeiro, o título de negócios de forma dinâmica. Funções semelhantes criam dinamicamente o HTML para as outras facetas. Cada faceta tem um rótulo e uma contagem, que mostra o número de itens encontrados para esse resultado de faceta.

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
> Ao conceber a página de resultados de pesquisa, lembre-se de adicionar um mecanismo para limpar facetas. Se adicionar caixas de verificação, pode ver facilmente como limpar os filtros. Para outros layouts, poderá ter um padrão de navegação estrutural ou outra abordagem criativa. Por exemplo, no aplicativo de exemplo de Portal de pesquisa de tarefa, pode clicar no `[X]` após uma faceta selecionada para limpar a faceta.

<a name="buildquery"></a>

## <a name="build-the-query"></a>Criar a consulta
O código que escreve para a criação de consultas deve especificar todas as partes de uma consulta válida, incluindo as expressões de pesquisa, facetas, filtros, classificação perfis – nada utilizado para formular um pedido. Nesta secção, vamos explorar onde facetas cabem numa consulta e, como os filtros são utilizados com facetas para fornecer um conjunto de resultados reduzida.

Tenha em atenção que as facetas são integrais nesse aplicativo de exemplo. A experiência de pesquisa na demonstração de Portal tarefa destina-se em torno de filtros e navegação por facetas. O posicionamento proeminente de navegação por facetas na página demonstra sua importância. 

Um exemplo, muitas vezes, é um bom lugar para começar. O exemplo seguinte, retirado do `JobsSearch.cs` arquivo, um pedido que cria a navegação de faceta com base no título de negócio, localização, tipo de lançamento e salário de mínimo de compilações. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

Um parâmetro de consulta de faceta está definido como um campo e consoante o tipo de dados, pode ser ainda mais parametrizada por lista delimitada por vírgulas que inclua `count:<integer>`, `sort:<>`, `interval:<integer>`, e `values:<list>`. Uma lista de valores é suportada para dados numéricos, na configuração de intervalos. Ver [documentos sobre pesquisa (API do Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) para detalhes de utilização.

Juntamente com as facetas, a solicitação formulada pelo seu aplicativo também deve criar filtros para limitar o conjunto de documentos de candidatos com base numa seleção de valor de faceta. Para uma loja de bicicletas, navegação por facetas fornece pistas para perguntas, como *que as cores, os fabricantes e tipos de bicicletas estão disponíveis?*. Filtragem de respostas a perguntas como *quais bicicletas exatas são red, bicicletas de montanha, neste de preço intervalo?*. Quando clicar em "Red" para indicar que apenas os produtos de vermelhos devem ser mostrados, a consulta seguinte, a aplicação envia inclui `$filter=Color eq ‘Red’`.

O seguinte fragmento de código do `JobsSearch.cs` página adiciona o título de negócio selecionada para o filtro, se selecionar um valor de faceta o título de negócios.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Dicas e melhores práticas

### <a name="indexing-tips"></a>Sugestões de indexação
**Melhorar a eficiência de índice, se não usar uma caixa de pesquisa**

Se a sua aplicação utilizar a navegação por facetas exclusivamente (ou seja, nenhuma caixa de pesquisa), pode marcar o campo como `searchable=false`, `facetable=true` para produzir um índice mais compacto. Além disso, a indexação ocorre apenas nos valores de faceta todo, com nenhuma quebra de palavras ou indexação de partes componentes de um valor de várias palavra.

**Especifique os campos que podem ser utilizados como facetas**

Lembre-se de que o esquema do índice determina quais os campos que estão disponíveis para utilizar como um aspeto. Partindo do princípio de que um campo é facetável, a consulta Especifica quais campos a faceta pelo. O campo pelo qual está facetamento fornece os valores apresentados abaixo a etiqueta. 

Os valores que são apresentados em cada etiqueta são obtidos a partir do índice. Por exemplo, se for o campo de faceta *cor*, os valores disponíveis para filtragem adicionais são os valores para esse campo - vermelho, preto e assim por diante.

Para numéricos e de DateTime apenas valores, pode definir explicitamente valores no campo de faceta (por exemplo, `facet=Rating,values:1|2|3|4|5`). É permitida uma lista de valores para estes tipos de campo simplificar a separação dos resultados de faceta em intervalos contíguos (qualquer um dos intervalos com base em valores numéricos ou períodos de tempo). 

**Por predefinição, só pode ter um nível de navegação por facetas** 

Como observado, não há nenhum suporte direto para aninhar facetas numa hierarquia. Por predefinição, a navegação por facetas no Azure Search suporta apenas um nível de filtros. No entanto, existem soluções alternativas. Pode codificar uma estrutura hierárquica de faceta num `Collection(Edm.String)` com uma entrada do ponto por hierarquia. Implementar esta solução está além do escopo deste artigo. 

### <a name="querying-tips"></a>Sugestões de consulta
**Validar campos**

Se criar a lista de facetas dinamicamente com base na entrada do usuário não confiável, confirme que os nomes dos campos por facetas são válidos. Em alternativa, os nomes de escape durante a criação de URLs, utilizando um `Uri.EscapeDataString()` no .NET, ou o equivalente na sua plataforma de eleição.

### <a name="filtering-tips"></a>Dicas de filtragem
**Aumentar a precisão da pesquisa com filtros**

Utilize filtros. Se depende apenas as expressões de pesquisa sozinhas, Lematização pode fazer com que um documento a ser devolvida que não tem o valor de faceta precisa em qualquer um dos respetivos campos.

**Aumentar o desempenho de pesquisa com filtros**

Filtros de restringir o conjunto de documentos de Release candidate para pesquisa e excluí-los da classificação. Se tiver um grande conjunto de documentos, usar uma faceta seletiva desagregar, muitas vezes, fornece um melhor desempenho.
  
**Filtrar apenas os campos por facetas**

Desagregação por facetas, normalmente pretende incluir apenas os documentos com o valor de faceta num campo específico (por facetas), não em qualquer lugar em todos os campos pesquisáveis. Adicionar um filtro reforça o campo de destino ao direcionar o serviço para pesquisar apenas no campo por facetas para um valor correspondente.

**Cortar os resultados de faceta com mais de filtros**

Resultados de faceta são encontrados nos resultados da pesquisa que correspondem ao termo uma faceta de documentos. No exemplo a seguir, nos resultados de pesquisa para *computação em nuvem*, 254 itens têm também *especificação interna* como um tipo de conteúdo. Itens não são necessariamente mutuamente exclusivos. Se um item cumpre os critérios de ambos os filtros, esta contará em cada uma. Essa duplicação é possível quando facetamento no `Collection(Edm.String)` campos, que são frequentemente utilizados para implementar a marcação de documento.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

Em geral, se achar que os resultados de faceta consistentemente são demasiado grandes, recomendamos que adicione mais filtros para dar aos utilizadores mais opções para limitar a procura.

### <a name="tips-about-result-count"></a>Dicas sobre a contagem de resultados

**Limitar o número de itens na barra de navegação de faceta**

Para cada campo por facetas na árvore de navegação, existe um limite predefinido de 10 valores. Esse padrão faz sentido para estruturas de navegação porque mantém a lista de valores para um tamanho gerenciável. É possível substituir a predefinição atribuindo um valor para contar.

* `&facet=city,count:5` Especifica que apenas as primeiras cinco cidades encontradas na parte superior com a classificação de resultados são retornadas como um resultado de faceta. Considere uma consulta de exemplo com um termo de pesquisa de "aeroporto" e 32 correspondências. Se a consulta especifica `&facet=city,count:5`, apenas as primeiras cinco exclusivas cidades com mais documentos nos resultados da pesquisa são incluídas nos resultados de faceta.

Observe que a distinção entre os resultados de faceta e os resultados da pesquisa. Os resultados da pesquisa são todos os documentos que correspondam à consulta. Resultados de faceta são as correspondências para cada valor de faceta. No exemplo, os resultados da pesquisa incluem os nomes das cidades que não estão na lista de classificação de faceta (5 no nosso exemplo). Os resultados que são filtrados por meio de navegação por facetas ficam visíveis quando limpar facetas ou escolha outras facetas além de cidade. 

> [!NOTE]
> Discutindo `count` quando existe mais de um tipo pode ser confuso. A tabela a seguir oferece um breve resumo de como o termo é usado na API de pesquisa do Azure, o código de exemplo e documentação. 

* `@colorFacet.count`<br/>
  No código da apresentação, deverá ver um parâmetro de contagem na Faceta, utilizada para apresentar o número de resultados de faceta. Nos resultados de Faceta, contagem indica o número de documentos que correspondem ao termo de faceta ou um intervalo.
* `&facet=City,count:12`<br/>
  Numa consulta de Faceta, pode definir a contagem para um valor.  A predefinição é 10, mas pode definir o valor superior ou inferior. Definição `count:12` obtém a parte superior a 12 corresponde nos resultados de faceta por contagem de documentos.
* "`@odata.count`"<br/>
  Na resposta da consulta, este valor indica o número de itens correspondentes nos resultados da pesquisa. Em média, é maior do que a soma de todos os resultados de faceta combinado, devido à presença de itens que correspondem ao termo de pesquisa, mas não ter nenhuma correspondência do valor de faceta.

**Obtenha contagens nos resultados de faceta**

Quando adiciona um filtro a uma consulta por facetas, pode querer manter a declaração de faceta (por exemplo, `facet=Rating&$filter=Rating ge 4`). Tecnicamente, a faceta = classificação não é necessária, mas mantendo a devolve as contagens de valores de faceta para classificações 4 e superior. Por exemplo, se clicar em "4" e a consulta inclui um filtro para maior ou igual a "4", contagens são devolvidos para cada classificação de que é 4 e superior.  

**Certifique-se de que as contagens de faceta precisas**

Em determinadas circunstâncias, poderá considerar que as contagens de faceta não correspondem os conjuntos de resultados (consulte [navegação por facetas no Azure Search (mensagem de fórum)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Contagens de faceta podem estar incorretas devido à arquitetura de fragmentação. Todos os índices de pesquisa tem várias partições horizontais, e cada partição horizontal reporta as facetas de N principais por contagem de documentos, que, em seguida, é combinada num único resultado. Se algumas partições horizontais tem muitos valores correspondentes, enquanto outros têm menos, pode achar que alguns valores de faceta estão em falta ou em contado nos resultados.

Embora esse comportamento poderia alterar em qualquer altura, se encontrar este comportamento hoje em dia, pode utilizar uma solução artificialmente inflating a contagem:<number> para um grande número de impor a geração de relatórios completa de cada partição horizontal. Se o valor da contagem: é maior que ou igual ao número de valores exclusivos no campo, existe a garantia de resultados precisos. No entanto, quando as contagens de documentos são alta, há uma penalidade de desempenho, por isso, utilize esta opção com moderação.

### <a name="user-interface-tips"></a>Dicas de interface de utilizador
**Adicionar etiquetas para cada campo na navegação de faceta**

As etiquetas são normalmente definidas no HTML ou formulário (`index.cshtml` no aplicativo de exemplo). Não existe nenhuma API no Azure Search para etiquetas de navegação de faceta ou quaisquer outros metadados.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Filtrar com base num intervalo
Facetamento ao longo de intervalos de valores é um requisito de aplicativo de pesquisa comum. Intervalos são suportados para dados numéricos e os valores de DateTime. Pode ler mais sobre cada abordagem [documentos sobre pesquisa (API do Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

O Azure Search simplifica a construção de intervalo ao fornecer duas abordagens para um intervalo de computação. Em ambas as abordagens, o Azure Search cria os intervalos adequados, tendo em conta as entradas que forneceu. Por exemplo, se especificar valores de intervalo de 10 | 20 | 30, este cria automaticamente a intervalos de 0 a 10, 10 a 20, 20 a 30. Seu aplicativo, opcionalmente, pode remover quaisquer intervalos que estão vazios. 

**Abordagem 1: Utilize o parâmetro de intervalo**  
Para definir as facetas de preço em incrementos de US $10, tem de especificar: `&facet=price,interval:10`

**Abordagem 2: Utilize uma lista de valores**  
Para dados numéricos, pode utilizar uma lista de valores.  Considere o intervalo de faceta para um `listPrice` campo, composto da seguinte forma:

  ![Lista de valores de exemplo][5]

Para especificar um intervalo de aspeto semelhante ao mostrado na captura de ecrã anterior, utilize uma lista de valores:

    facet=listPrice,values:10|25|100|500|1000|2500

Baseia-se cada intervalo de 0 a utilizar como ponto de partida, um valor na lista como um ponto final e, em seguida, removido do intervalo anterior para criar intervalos de discretos. O Azure Search faz essas coisas como parte da navegação por facetas. Não é necessário escrever código para estruturar a cada intervalo.

### <a name="build-a-filter-for-a-range"></a>Criar um filtro para um intervalo
Para filtrar documentos com base num intervalo que selecionou, pode utilizar o `"ge"` e `"lt"` filtrar operadores numa expressão de duas partes que define os pontos de extremidade do intervalo. Por exemplo, se escolher o intervalo de 10 a 25 para um `listPrice` campo, o filtro seria `$filter=listPrice ge 10 and listPrice lt 25`. No código de exemplo, a expressão de filtro usa **priceFrom** e **priceTo** parâmetros para definir os pontos de extremidade. 

  ![Consulta para um intervalo de valores][6]

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Filtrar com base na distância
É comum ver filtra que o ajudam a escolher um arquivo, um restaurante ou uma destino com base na respetiva proximidade na sua localização atual. Este tipo de filtro poderá ser semelhante a navegação por facetas, mas é apenas um filtro. Mencionamos-o aqui para aqueles que procuram especificamente conselhos de implementação para esse problema de design específico.

Existem duas funções Geoespaciais no Azure Search, **geo.distance** e **geo.intersects**.

* O **geo.distance** função retorna a distância em quilômetros entre dois pontos. Um ponto é um campo e o outro é uma constante passada como parte do filtro. 
* O **geo.intersects** função devolve VERDADEIRO se um determinado ponto estiver dentro de um determinado polígono. O ponto é um campo e o polígono é especificado como uma lista de constante de coordenadas passadas como parte do filtro.

Pode encontrar exemplos de filtro [sintaxe da expressão OData (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Experimentar a demonstração
A demonstração de Portal do Azure Search tarefa contém exemplos referenciados neste artigo.

-   Ver e testar a demonstração de trabalho online no [demonstração de Portal do Azure Search tarefa](https://azjobsdemo.azurewebsites.net/).

-   Baixe o código a partir da [repositório de exemplos do Azure no GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

À medida que trabalha com os resultados da pesquisa, verá que as alterações na construção de consulta. Esta aplicação acontece a acrescentar facetas para o URI, à medida que seleciona cada um deles.

1. Para utilizar a funcionalidade de mapeamento da aplicação de demonstração, obtenha uma chave do mapas Bing do [Bing Maps Dev Center](https://www.bingmapsportal.com/). Cole-a sobre a chave existente no `index.cshtml` página. O `BingApiKey` definição `Web.config` ficheiro não é utilizado. 

2. Execute a aplicação. Faça a visita guiada opcional ou descartar a caixa de diálogo.
   
3. Introduza um termo de pesquisa, como "analista" e clique no ícone de pesquisa. A consulta for executada rapidamente.
   
   Uma estrutura de navegação por facetas também é devolvida com os resultados da pesquisa. Na página de resultados de pesquisa, a estrutura de navegação por facetas inclui contagens para cada resultado de faceta. Não existem facetas estiverem selecionadas, pelo que todos os resultados correspondentes são devolvidos.
   
   ![Resultados da pesquisa antes de selecionar facetas][11]

4. Clique num título de negócios, a localização ou o salário mínimo. Facetas eram nulas na pesquisa inicial, mas à medida que assumir valores, os resultados da pesquisa são recortados dos itens que já não correspondem.
   
   ![Resultados da pesquisa depois de selecionar facetas][12]

5. Para limpar a consulta por facetas, para que pode tentar comportamentos de consulta diferentes, clique no `[X]` após as facetas selecionadas para limpar as facetas.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Saiba mais
Assista [aprofunde-se o Azure Search](https://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). No 45:25, existe uma demonstração sobre como implementar facetas.

Para obter mais informações sobre os princípios de design para navegação por facetas, recomendamos as seguintes ligações:

* [Criação de uma pesquisa por facetas](http://www.uie.com/articles/faceted_search/)
* [Padrões de design: Navegação por facetas](https://alistapart.com/article/design-patterns-faceted-navigation)


<!--Anchors-->
[How to build it]: #howtobuildit
[Build the presentation layer]: #presentationlayer
[Build the index]: #buildindex
[Check for data quality]: #checkdata
[Build the query]: #buildquery
[Tips on how to control faceted navigation]: #tips
[Faceted navigation based on range values]: #rangefacets
[Faceted navigation based on GeoPoints]: #geofacets
[Try it out]: #tryitout

<!--Image references-->
[1]: ./media/search-faceted-navigation/azure-search-faceting-example.PNG
[2]: ./media/search-faceted-navigation/Facet-2-CSHTML.PNG
[3]: ./media/search-faceted-navigation/Facet-3-schema.PNG
[4]: ./media/search-faceted-navigation/Facet-4-SearchMethod.PNG
[5]: ./media/search-faceted-navigation/Facet-5-Prices.PNG
[6]: ./media/search-faceted-navigation/Facet-6-buildfilter.PNG
[7]: ./media/search-faceted-navigation/Facet-7-appstart.png
[8]: ./media/search-faceted-navigation/Facet-8-appbike.png
[9]: ./media/search-faceted-navigation/Facet-9-appbikefaceted.png
[10]: ./media/search-faceted-navigation/Facet-10-appTitle.png
[11]: ./media/search-faceted-navigation/faceted-search-before-facets.png
[12]: ./media/search-faceted-navigation/faceted-search-after-facets.png

<!--Link references-->
[Designing for Faceted Search]: http://www.uie.com/articles/faceted_search/
[Design Patterns: Faceted Navigation]: https://alistapart.com/article/design-patterns-faceted-navigation
[Create your first application]: search-create-first-solution.md
[OData expression syntax (Azure Search)]: https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search
[Azure Search Adventure Works Demo]: https://azuresearchadventureworksdemo.codeplex.com/
[https://www.odata.org/documentation/odata-version-2-0/overview/]: https://www.odata.org/documentation/odata-version-2-0/overview/ 
[Faceting on Azure Search forum post]: ../faceting-on-azure-search.md?forum=azuresearch
[Search Documents (Azure Search API)]: https://docs.microsoft.com/rest/api/searchservice/Search-Documents

