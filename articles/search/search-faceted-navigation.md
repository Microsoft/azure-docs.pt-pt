---
title: Adicione uma hierarquia de categoria de navegação facetada
titleSuffix: Azure Cognitive Search
description: Adicione navegação frontal para filtragem auto-dirigida em aplicações de pesquisa que se integram com Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 05be5295ae5f8c73c916a21bba7dbc98ab0c5e87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89002798"
---
# <a name="how-to-implement-faceted-navigation-in-azure-cognitive-search"></a>Como implementar a navegação facetada na Pesquisa Cognitiva do Azure

A navegação frontal é um mecanismo de filtragem que fornece navegação auto-dirigida em aplicações de pesquisa. O termo "navegação facial" pode não ser familiar, mas provavelmente já a usou antes. Como mostra o exemplo a seguir, a navegação com face não é nada mais do que as categorias utilizadas para filtrar resultados.

 ![Demonstração do Portal de Pesquisa Cognitiva de Azure](media/search-faceted-navigation/azure-search-faceting-example.png "Demonstração do Portal de Pesquisa Cognitiva de Azure")

A navegação com face é um ponto de entrada alternativo para pesquisar. Oferece uma alternativa conveniente para escrever expressões complexas de pesquisa à mão. As facetas podem ajudá-lo a encontrar o que procura, garantindo ao mesmo tempo que não obtém resultados nulos. Como desenvolvedor, as facetas permitem expor os critérios de pesquisa mais úteis para navegar no seu índice de pesquisa. Nas aplicações de retalho online, a navegação facial é muitas vezes construída sobre marcas, departamentos (sapatos infantis), tamanho, preço, popularidade e classificações. 

A implementação da navegação faceda difere entre as tecnologias de pesquisa. Em Azure Cognitive Search, a navegação facetada é construída no tempo de consulta, usando campos que você anteriormente atribuiu no seu esquema.

-   Nas consultas que a sua aplicação constrói, uma consulta deve enviar *parâmetros de consulta faceta* para obter os valores de filtro de faceta disponíveis para o conjunto de resultados do documento.

-   Para aparar o conjunto de resultados do documento, a aplicação também deve aplicar uma `$filter` expressão.

No desenvolvimento da sua aplicação, o código de escrita que constrói consultas constitui a maior parte da obra. Muitos dos comportamentos de aplicação que seria de esperar da navegação frontal são fornecidos pelo serviço, incluindo suporte incorporado para definir gamas e obter contagens para resultados facetas. O serviço também inclui incumprimentos sensatos que o ajudam a evitar estruturas de navegação descontroladas. 

## <a name="sample-code-and-demo"></a>Código de amostra e demonstração
Este artigo usa um portal de pesquisa de emprego como exemplo. O exemplo é implementado como uma aplicação de MVC ASP.NET.

- Veja e teste a demonstração de trabalho on-line na [Azure Cognitive Search Job Portal Demo](https://aka.ms/azjobsdemo).

- Descarregue o código a partir do [repo Azure-Samples no GitHub.](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)

## <a name="get-started"></a>Introdução
Se você é novo no desenvolvimento da pesquisa, a melhor maneira de pensar na navegação facetada é que ele mostra as possibilidades de pesquisa auto-dirigida. É um tipo de experiência de pesquisa de perfuração, baseada em filtros predefinidos, usados para reduzir rapidamente os resultados de pesquisa através de ações de ponto e clique. 

### <a name="interaction-model"></a>Modelo de interação

A experiência de pesquisa para navegação facetada é iterativa, por isso vamos começar por compreendê-la como uma sequência de consultas que se desenrolam em resposta às ações do utilizador.

O ponto de partida é uma página de aplicação que fornece navegação facial, tipicamente colocada na periferia. A navegação com face é frequentemente uma estrutura de árvores, com caixas de verificação para cada valor, ou texto clicável. 

1. Uma consulta enviada para a Azure Cognitive Search especifica a estrutura de navegação facetada através de um ou mais parâmetros de consulta faceta. Por exemplo, a consulta pode incluir `facet=Rating` , talvez com uma ou `:values` `:sort` opção para aperfeiçoar ainda mais a apresentação.
2. A camada de apresentação torna uma página de pesquisa que proporciona navegação facial, utilizando as facetas especificadas no pedido.
3. Dada uma estrutura de navegação facetada que inclui rating, clique em "4" para indicar que apenas produtos com uma classificação igual ou superior devem ser mostrados. 
4. Em resposta, a aplicação envia uma consulta que inclui `$filter=Rating ge 4` 
5. A camada de apresentação atualiza a página, mostrando um conjunto de resultados reduzido, contendo apenas os itens que satisfazem os novos critérios (neste caso, produtos avaliados 4 e acima).

Uma faceta é um parâmetro de consulta, mas não o confunda com entrada de consulta. Nunca é utilizado como critério de seleção numa consulta. Em vez disso, pense nos parâmetros de consulta de faceta como entradas para a estrutura de navegação que volta na resposta. Para cada parâmetro de consulta faceta que fornece, a Azure Cognitive Search avalia quantos documentos estão nos resultados parciais para cada valor faceta.

Reparem `$filter` no passo 4. O filtro é um aspeto importante da navegação facetada. Embora as facetas e filtros sejam independentes na API, você precisa de ambos para entregar a experiência que pretende. 

### <a name="app-design-pattern"></a>Padrão de design de aplicativo

No código de aplicação, o padrão é usar parâmetros de consulta faceta para devolver a estrutura de navegação facetada juntamente com os resultados facetas, além de uma expressão $filter.  A expressão do filtro lida com o evento de clique no valor da faceta. Pense na `$filter` expressão como o código por trás do corte real dos resultados de pesquisa devolvidos à camada de apresentação. Dada a faceta Colors, clicar na cor Vermelha é implementado através de uma `$filter` expressão que seleciona apenas os itens que têm uma cor de vermelho. 

### <a name="query-basics"></a>Básicos de consulta

Na Pesquisa Cognitiva Azure, um pedido é especificado através de um ou mais parâmetros de consulta (ver [Documentos](/rest/api/searchservice/Search-Documents) de Busca para uma descrição de cada um). Nenhum dos parâmetros de consulta são necessários, mas deve ter pelo menos um para que uma consulta seja válida.

A precisão, entendida como a capacidade de filtrar acessos irrelevantes, é conseguida através de uma ou ambas as expressões:

-   **pesquisa=**  
    O valor deste parâmetro constitui a expressão de pesquisa. Pode ser um único pedaço de texto, ou uma expressão de pesquisa complexa que inclui vários termos e operadores. No servidor, é utilizada uma expressão de pesquisa para pesquisa de texto completo, consultando campos pes pesjáveis no índice para termos correspondentes, devolvendo resultados na ordem de classificação. Se definir `search` para nulo, a execução de consulta é superior a todo o índice (isto é, `search=*` ). Neste caso, outros elementos da consulta, como um `$filter` perfil de pontuação ou de pontuação, são os principais fatores que afetam os documentos devolvidos) `($filter` e em que ordem `scoringProfile` (ou `$orderby` ).

-   **$filter=**  
    Um filtro é um mecanismo poderoso para limitar o tamanho dos resultados de pesquisa com base nos valores de atributos específicos do documento. A `$filter` é avaliado em primeiro lugar, seguido por uma lógica de faceta que gera os valores disponíveis e contagens correspondentes para cada valor

Expressões de pesquisa complexas diminuem o desempenho da consulta. Sempre que possível, utilize expressões de filtro bem construídas para aumentar a precisão e melhorar o desempenho da consulta.

Para entender melhor como um filtro adiciona mais precisão, compare uma expressão de pesquisa complexa com uma que inclui uma expressão de filtro:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq 'Seattle' and Parking and Type ne 'motel'`

Ambas as consultas são válidas, mas a segunda é superior se você estiver procurando não-motéis com estacionamento em Seattle.
-   A primeira consulta baseia-se nessas palavras específicas que são mencionadas ou não mencionadas em campos de cordas como Nome, Descrição e qualquer outro campo que contenha dados pesmáveis.
-   A segunda consulta procura correspondências precisas em dados estruturados e é provável que seja muito mais precisa.

Nas aplicações que incluem navegação presencial, certifique-se de que cada ação do utilizador sobre uma estrutura de navegação facetada é acompanhada por um estreitamento dos resultados da pesquisa. Para reduzir os resultados, utilize uma expressão de filtro.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Construir uma aplicação de navegação com faceta
Implementa navegação facetada com Azure Cognitive Search no seu código de aplicação que constrói o pedido de pesquisa. A navegação facetada baseia-se em elementos do seu esquema que definiu anteriormente.

Predefinido no seu índice de pesquisa é o `Facetable [true|false]` atributo índice, definido em campos selecionados para permitir ou desativar a sua utilização numa estrutura de navegação facetada. Sem `"Facetable" = true` , um campo não pode ser usado na navegação faceta.

A camada de apresentação no seu código proporciona a experiência do utilizador. Deve listar as partes constituintes da navegação facetada, tais como a etiqueta, valores, caixas de verificação e a contagem. O Azure Cognitive Search REST API é agnóstico de plataforma, por isso use qualquer linguagem e plataforma que queira. O importante é incluir elementos de UI que suportam atualização incremental, com o estado de UI atualizado à medida que cada faceta adicional é selecionada. 

Na hora da consulta, o seu código de aplicação cria um pedido que `facet=[string]` inclui, um parâmetro de pedido que fornece o campo para facetar. Uma consulta pode ter múltiplas facetas, tais `&facet=color&facet=category&facet=rating` como, cada uma separada por um caráter ampersand (&).

O código de aplicação também deve construir uma `$filter` expressão para lidar com os eventos de clique na navegação presencial. A `$filter` reduz os resultados da pesquisa, utilizando o valor da faceta como critérios de filtro.

A Azure Cognitive Search devolve os resultados da pesquisa, com base num ou mais termos que introduz, juntamente com atualizações à estrutura de navegação facetada. Em Azure Cognitive Search, a navegação frontal é uma construção de um só nível, com valores de faceta, e conta quantos resultados são encontrados para cada um.

Nas secções seguintes, damos uma olhada mais atenta na forma de construir cada peça.

<a name="buildindex"></a>

## <a name="build-the-index"></a>Construir o índice
A faceta é ativada numa base de campo a campo no índice, através deste atributo de índice: `"Facetable": true` .  
Todos os tipos de campo que possam ser utilizados na navegação facial são `Facetable` por defeito. Estes tipos de campo incluem `Edm.String` , e todos os tipos de campo `Edm.DateTimeOffset` numérico (essencialmente, todos os tipos de campo são `Edm.GeographyPoint` facetable, exceto , que não podem ser usados na navegação faceta). 

Ao construir um índice, uma melhor prática para a navegação facetada é ligar explicitamente a cara para campos que nunca devem ser usados como uma faceta.  Em particular, os campos de cordas para valores singleton, como um ID ou nome do produto, devem ser definidos `"Facetable": false` para evitar a sua utilização acidental (e ineficaz) na navegação facetada. Virar a cara onde não precisa ajuda a manter o tamanho do índice pequeno e normalmente melhora o desempenho.

Segue-se o esquema da aplicação de amostras Job Portal Demo, aparada de alguns atributos para reduzir o tamanho:

```json
{
  ...
  "name": "nycjobs",
  "fields": [
    { "name": "id",                 "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { "name": "job_id",             "type": "Edm.String",              "searchable": false, "filterable": false, ... "facetable": false, ... },
    { "name": "agency",              "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "posting_type",        "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "num_of_positions",    "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "business_title",      "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "civil_service_title", "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "title_code_no",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "level",               "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_range_from",   "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_range_to",     "type": "Edm.Int32",              "searchable": false, "filterable": true, ...  "facetable": true, ...  },
    { "name": "salary_frequency",    "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
    { "name": "work_location",       "type": "Edm.String",             "searchable": true,  "filterable": true, ...  "facetable": true, ...  },
…
    { "name": "geo_location",        "type": "Edm.GeographyPoint",     "searchable": false, "filterable": true, ...  "facetable": false, ... },
    { "name": "tags",                "type": "Collection(Edm.String)", "searchable": true,  "filterable": true, ...  "facetable": true, ...  }
  ],
…
}
```

Como pode ver no esquema de amostra, `Facetable` é desligado para campos de cordas que não devem ser usados como facetas, tais como valores de ID. Virar a cara onde não precisa ajuda a manter o tamanho do índice pequeno e normalmente melhora o desempenho.

> [!TIP]
> Como uma boa prática, inclua o conjunto completo de atributos de índice para cada campo. Embora `Facetable` esteja ligado por padrão para quase todos os campos, definir propositadamente cada atributo pode ajudá-lo a pensar através das implicações de cada decisão de esquema. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Verifique os dados
A qualidade dos seus dados tem um efeito direto sobre se a estrutura de navegação facetada se materializa como espera. Também afeta a facilidade de construir filtros para reduzir o conjunto de resultados.

Se quiser ser confrontado por Marca ou Preço, cada documento deve conter valores para *BrandName* e *ProductPrice* que são válidos, consistentes e produtivos como opção de filtro.

Aqui estão algumas lembranças do que esfregar:

* Para cada campo que pretende ser confrontado, pergunte-se se contém valores adequados como filtros na pesquisa auto-dirigida. Os valores devem ser curtos, descritivos e suficientemente distintos para oferecer uma escolha clara entre opções concorrentes.
* Erros ortográficos ou valores quase correspondentes. Se você facetar em Color, e os valores de campo incluem Orange e Ornage (um erro ortográfico), uma faceta baseada no campo Color iria pegar ambos.
* O texto de caso misto também pode causar estragos na navegação facetada, com laranja e laranja aparecendo como dois valores diferentes. 
* Versões simples e plural do mesmo valor podem resultar numa faceta separada para cada um.

Como pode imaginar, a diligência na preparação dos dados é um aspeto essencial da navegação efetiva facetada.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>Criar a IU
Trabalhar a partir da camada de apresentação pode ajudá-lo a descobrir requisitos que podem ser perdidos de outra forma, e entender quais as capacidades essenciais para a experiência de pesquisa.

Em termos de navegação facetada, a sua página web ou de aplicação exibe a estrutura de navegação facetada, deteta a entrada do utilizador na página e insere os elementos alterados. 

Para aplicações web, o AJAX é comumente usado na camada de apresentação porque permite-lhe refrescar alterações incrementais. Também pode utilizar ASP.NET MVC ou qualquer outra plataforma de visualização que possa ligar-se a um serviço de Pesquisa Cognitiva Azure em HTTP. A aplicação da amostra referenciada ao longo deste artigo -- a **Demo do Portal de Pesquisa Cognitiva Azure** - é uma aplicação ASP.NET MVC.

Na amostra, a navegação facial é incorporada na página de resultados de pesquisa. O exemplo a seguir, retirado do `index.cshtml` ficheiro da aplicação da amostra, mostra a estrutura estática html para exibir navegação facial na página de resultados de pesquisa. A lista de facetas é construída ou reconstruída dinamicamente quando submete um termo de pesquisa, ou seleciona ou limpa uma faceta.

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

O seguinte corte de código da `index.cshtml` página constrói dinamicamente o HTML para exibir a primeira faceta, Título de Negócio. Funções semelhantes constroem dinamicamente o HTML para as outras facetas. Cada faceta tem uma etiqueta e uma contagem, que mostra o número de itens encontrados para o resultado da faceta.

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
> Quando desenhar a página de resultados da pesquisa, lembre-se de adicionar um mecanismo para limpar as facetas. Se adicionar caixas de verificação, pode facilmente ver como limpar os filtros. Para outros layouts, você pode precisar de um padrão de migalhas de pão ou outra abordagem criativa. Por exemplo, na aplicação de amostra do Portal de Pesquisa de Emprego, pode clicar na `[X]` faceta selecionada para limpar a faceta.

<a name="buildquery"></a>

## <a name="build-the-query"></a>Construir a consulta
O código que escreve para consultas de construção deve especificar todas as partes de uma consulta válida, incluindo expressões de pesquisa, facetas, filtros, perfis de pontuação, qualquer coisa usada para formular um pedido. Nesta secção, exploramos onde as facetas se encaixam numa consulta, e como os filtros são usados com facetas para fornecer um conjunto de resultados reduzido.

Note que as facetas são integrais nesta aplicação de amostra. A experiência de pesquisa na Demo do Portal de Trabalho é projetada em torno da navegação e filtros com cara. A colocação proeminente da navegação facetada na página demonstra a sua importância. 

Um exemplo é muitas vezes um bom lugar para começar. O exemplo seguinte, retirado do `JobsSearch.cs` ficheiro, constrói um pedido que cria navegação faceta com base no Título de Negócios, Localização, Tipo de Registo e Salário Mínimo. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

Um parâmetro de consulta faceta é definido para um campo e, dependendo do tipo de dados, pode ser ainda mais parametrizado por lista delimitada por vírgula que `count:<integer>` `sort:<>` inclui, `interval:<integer>` e `values:<list>` . Uma lista de valores é suportada para dados numéricos ao configurar intervalos. Consulte [documentos de pesquisa (Azure Cognitive Search API)](/rest/api/searchservice/Search-Documents) para obter detalhes de utilização.

Juntamente com as facetas, o pedido formulado pela sua aplicação também deve construir filtros para reduzir o conjunto de documentos candidatos com base numa seleção de valor faceta. Para uma loja de bicicletas, a navegação com face fornece pistas para questões como *Que cores, fabricantes e tipos de bicicletas estão disponíveis?* Filtrar responde a perguntas como *Quais as bicicletas exatas vermelhas, de montanha, nesta gama de preços?* Quando clica em "Vermelho" para indicar que apenas os produtos vermelhos devem ser mostrados, a próxima consulta que a aplicação envia inclui `$filter=Color eq 'Red'` .

O seguinte corte de código da `JobsSearch.cs` página adiciona o Título de Negócios selecionado ao filtro se selecionar um valor a partir da faceta do Título de Negócio.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Sugestões e melhores práticas

### <a name="indexing-tips"></a>Dicas de indexação
**Melhore a eficiência do índice se não utilizar uma caixa de pesquisa**

Se a sua aplicação utilizar a navegação facial exclusivamente (isto é, sem caixa de pesquisa), pode marcar o campo como `searchable=false` , para produzir um índice mais `facetable=true` compacto. Além disso, a indexação ocorre apenas em valores de facetas inteiras, sem quebra de palavras ou indexação das partes componentes de um valor multi-palavra.

**Especificar quais os campos que podem ser usados como facetas**

Lembre-se que o esquema do índice determina quais os campos disponíveis para usar como faceta. Assumindo que um campo é tão caravel, a consulta especifica quais os campos a enfrentar. O campo pelo qual está a enfrentar fornece os valores que aparecem abaixo da etiqueta. 

Os valores que aparecem em cada rótulo são recuperados do índice. Por exemplo, se o campo faceta for *Color,* os valores disponíveis para filtragem adicional são os valores para esse campo - Vermelho, Preto, e assim por diante.

Apenas para valores numéricos e datetime, pode definir explicitamente valores no campo faceta (por exemplo, `facet=Rating,values:1|2|3|4|5` ). É permitida uma lista de valores para que estes tipos de campo simplifiquem a separação dos resultados facetas em gamas contíguas (ou gamas baseadas em valores numéricos ou períodos de tempo). 

**Por padrão, só pode ter um nível de navegação faceted** 

Como notado, não há apoio direto para nidificar facetas numa hierarquia. Por predefinição, a navegação facetada na Pesquisa Cognitiva Azure suporta apenas um nível de filtros. No entanto, existem soluções alternativas. Pode codificar uma estrutura de faceta hierárquica num `Collection(Edm.String)` ponto de entrada por hierarquia. A implementação desta solução está fora do âmbito deste artigo. 

### <a name="querying-tips"></a>Dicas de consulta
**Validar campos**

Se construir a lista de facetas dinamicamente baseadas na entrada de utilizador não fided quanto, valide que os nomes dos campos facetados são válidos. Ou, escape os nomes ao construir URLs usando ou `Uri.EscapeDataString()` em .NET, ou o equivalente na sua plataforma de eleição.

### <a name="filtering-tips"></a>Dicas de filtragem
**Aumentar a precisão de pesquisa com filtros**

Use filtros. Se você depender apenas de expressões de pesquisa apenas, o caule pode fazer com que um documento seja devolvido que não tenha o valor facial preciso em nenhum dos seus campos.

**Aumentar o desempenho da pesquisa com filtros**

Os filtros reduzem o conjunto de documentos candidatos para pesquisa e excluem-nos do ranking. Se tiver um grande conjunto de documentos, usar uma broca de faceta seletiva muitas vezes dá-lhe um melhor desempenho.
  
**Filtrar apenas os campos com face**

Em brotos facetados, normalmente só pretende incluir documentos que tenham o valor da face num campo específico (faceted), e não em qualquer lugar em todos os campos pesquisáveis. A adição de um filtro reforça o campo alvo direcionando o serviço para procurar apenas no campo virado para o lado por um valor correspondente.

**Corte os resultados faceta com mais filtros**

Os resultados faceta são documentos encontrados nos resultados da pesquisa que correspondem a um termo faceta. No exemplo seguinte, nos resultados de pesquisa para *computação* em nuvem, 254 itens também têm *especificação interna* como tipo de conteúdo. Os itens não são necessariamente mutuamente exclusivos. Se um item satisfaça os critérios de ambos os filtros, é contado em cada um deles. Esta duplicação é possível quando se enfrenta em `Collection(Edm.String)` campos, que são frequentemente utilizados para implementar a marcação de documentos.

```output
Search term: "cloud computing"
Content type
   Internal specification (254)
   Video (10)
```

Em geral, se descobrir que os resultados das facetas são consistentemente demasiado grandes, recomendamos que adicione mais filtros para dar aos utilizadores mais opções para reduzir a pesquisa.

### <a name="tips-about-result-count"></a>Dicas sobre contagem de resultados

**Limitar o número de itens na navegação faceta**

Para cada campo virado na árvore de navegação, existe um limite padrão de 10 valores. Este padrão faz sentido para as estruturas de navegação porque mantém a lista de valores para um tamanho manejável. Pode anular o padrão atribuindo um valor a contar.

* `&facet=city,count:5` especifica que apenas as cinco primeiras cidades encontradas nos resultados mais bem classificados são devolvidas como resultado faceta. Considere uma consulta de amostra com um termo de pesquisa de "aeroporto" e 32 fósforos. Se a consulta `&facet=city,count:5` especificar, apenas as cinco primeiras cidades únicas com mais documentos nos resultados da pesquisa estão incluídas nos resultados da faceta.

Note a distinção entre resultados de faceta e resultados de pesquisa. Os resultados da pesquisa são todos os documentos que correspondem à consulta. Os resultados faceta são os fósforos para cada valor faceta. No exemplo, os resultados da pesquisa incluem nomes do City que não estão na lista de classificação faceta (5 no nosso exemplo). Os resultados que são filtrados através da navegação facial tornam-se visíveis quando limpam as facetas, ou escolhem outras facetas para além do City. 

> [!NOTE]
> Discutir `count` quando há mais de um tipo pode ser confuso. A tabela seguinte oferece um breve resumo de como o termo é usado na API de Pesquisa Cognitiva Azure, código de amostra e documentação. 

* `@colorFacet.count`<br/>
  No código de apresentação, deve ver um parâmetro de contagem na faceta, utilizado para mostrar o número de resultados facetas. Nos resultados facetas, a contagem indica o número de documentos que correspondem no termo ou intervalo da faceta.
* `&facet=City,count:12`<br/>
  Numa consulta de faceta, pode definir a contagem para um valor.  O padrão é 10, mas pode defini-lo mais ou menos. A definição `count:12` obtém os 12 melhores jogos em resultados facetas por contagem de documentos.
* "`@odata.count`"<br/>
  Na resposta de consulta, este valor indica o número de itens correspondentes nos resultados da pesquisa. Em média, é maior do que a soma de todos os resultados facetas combinados, devido à presença de itens que correspondem ao termo de pesquisa, mas não têm correspondências de valor faceta.

**Obtenha contagem nos resultados facetas**

Quando adicionar um filtro a uma consulta facetada, é melhor manter a declaração faceta (por exemplo, `facet=Rating&$filter=Rating ge 4` ). Tecnicamente, a facet=Rating não é necessária, mas mantê-la devolve as contagens de valores facetas para classificações 4 e superiores. Por exemplo, se clicar em "4" e a consulta incluir um filtro para maior ou igual a "4", as contagens são devolvidas para cada classificação que seja 4 e superior.  

**Certifique-se de obter contagens de facetas precisas**

Em certas circunstâncias, poderá descobrir que as contagens de faceta não correspondem aos conjuntos de resultados (ver [navegação facetada em Azure Cognitive Search (Microsoft Q&Página de perguntas)](/answers/topics/azure-cognitive-search.html)).

As contagens de faceta podem ser imprecisas devido à arquitetura de fragmentos. Cada índice de pesquisa tem vários fragmentos, e cada fragmento relata as facetas N superiores por contagem de documentos, que é então combinado em um único resultado. Se alguns fragmentos tiverem muitos valores correspondentes, enquanto outros têm menos, poderá descobrir que alguns valores de faceta estão em falta ou sub-contados nos resultados.

Embora este comportamento possa mudar a qualquer momento, se encontrar este comportamento hoje, pode trabalhar em torno dele inflacionando artificialmente a contagem: \<number> para um grande número para impor relatórios completos de cada fragmento. Se o valor da contagem: for maior ou igual ao número de valores únicos no campo, é garantido um resultado preciso. No entanto, quando as contagens de documentos são altas, há uma penalidade de desempenho, por isso use esta opção criteriosamente.

### <a name="user-interface-tips"></a>Dicas de interface de utilizador
**Adicione etiquetas para cada campo na navegação faceta**

As etiquetas são tipicamente definidas no HTML ou na forma `index.cshtml` (na aplicação da amostra). Não existe API na Azure Cognitive Search para etiquetas de navegação faceta ou quaisquer outros metadados.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Filtro baseado numa gama
Encarar os intervalos de valores é um requisito comum de aplicação de pesquisa. As gamas são suportadas para dados numéricos e valores datetime. Pode ler mais sobre cada abordagem em [Documentos de Busca (Azure Cognitive Search API)](/rest/api/searchservice/Search-Documents).

A Azure Cognitive Search simplifica a construção do alcance, fornecendo duas abordagens para a computação de uma gama. Para ambas as abordagens, a Azure Cognitive Search cria as gamas apropriadas dadas as entradas que forneceu. Por exemplo, se especificar valores de gama de 10|20|30, cria automaticamente intervalos de 0-10, 10-20, 20-30. A sua aplicação pode remover opcionalmente quaisquer intervalos que estejam vazios. 

**Abordagem 1: Utilize o parâmetro de intervalo**  
Para definir as facetas de preço em incrementos de $10, especificaria: `&facet=price,interval:10`

**Abordagem 2: Utilize uma lista de valores**  
Para dados numéricos, pode utilizar uma lista de valores.  Considere o alcance da faceta de um `listPrice` campo, prestado da seguinte forma:

  ![Lista de valores de amostra](media/search-faceted-navigation/Facet-5-Prices.PNG "Lista de valores de amostra")

Para especificar uma gama de facetas como a da imagem anterior, utilize uma lista de valores:

> `facet=listPrice,values:10|25|100|500|1000|2500`

Cada gama é construída usando 0 como ponto de partida, um valor da lista como ponto final, e depois aparado da gama anterior para criar intervalos discretos. A Azure Cognitive Search faz estas coisas como parte da navegação facetada. Não é necessário escrever código para estruturar cada intervalo.

### <a name="build-a-filter-for-a-range"></a>Construa um filtro para uma gama
Para filtrar documentos com base numa gama selecionada, pode utilizar os `"ge"` operadores e `"lt"` filtrar numa expressão em duas partes que define os pontos finais da gama. Por exemplo, se escolher o intervalo 10-25 para um `listPrice` campo, o filtro será `$filter=listPrice ge 10 and listPrice lt 25` . No código de amostra, a expressão do filtro utiliza **os parâmetros preçoFrom** e **priceTo** para definir os pontos finais. 

  ![Consulta para uma gama de valores](media/search-faceted-navigation/Facet-6-buildfilter.PNG "Consulta para uma gama de valores")

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Filtro à base de distância
É comum ver filtros que o ajudam a escolher uma loja, restaurante ou destino com base na sua proximidade com a sua localização atual. Embora este tipo de filtro possa parecer navegação facial, é apenas um filtro. Mencionamos aqui para aqueles que procuram especificamente conselhos de implementação para esse problema específico de conceção.

Existem duas funções Geoespaciais em Azure Cognitive Search, **geo.distance** e **geo.intersects**.

* A função **geo.distância** devolve a distância em quilómetros entre dois pontos. Um ponto é um campo e outro é uma constante passada como parte do filtro. 
* A função **geo.intersecta retornos verdadeiros** se um determinado ponto estiver dentro de um determinado polígono. O ponto é um campo e o polígono é especificado como uma lista constante de coordenadas passadas como parte do filtro.

Pode encontrar exemplos de filtro na sintaxe de [expressão OData (Azure Cognitive Search)](query-odata-filter-orderby-syntax.md).

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Experimente a demonstração
A Demo do Portal de Pesquisa Cognitiva Azure contém os exemplos referenciados neste artigo.

-   Veja e teste a demonstração de trabalho on-line na [Azure Cognitive Search Job Portal Demo](https://aka.ms/azjobsdemo).

-   Descarregue o código a partir do [repo Azure-Samples no GitHub.](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)

Enquanto trabalha com os resultados da pesquisa, observe o URL para alterações na construção de consultas. Esta aplicação acontece para anexar facetas ao URI à medida que seleciona cada uma.

1. Para utilizar a funcionalidade de mapeamento da app de demonstração, obtenha uma chave Bing Maps do [Bing Maps Dev Center](https://www.bingmapsportal.com/). Cole-a sobre a chave existente na `index.cshtml` página. A `BingApiKey` definição no `Web.config` ficheiro não é utilizada. 

2. Execute a aplicação. Faça o passeio opcional, ou despedaque a caixa de diálogo.
   
3. Introduza um termo de pesquisa, como "analista", e clique no ícone 'Pesquisar'. A consulta executa rapidamente.
   
   Uma estrutura de navegação facetada também é devolvida com os resultados da pesquisa. Na página de resultados de pesquisa, a estrutura de navegação faceta inclui contagens para cada resultado facet. Não são selecionadas as facetas, pelo que todos os resultados correspondentes são devolvidos.
   
   ![Pesquisar resultados antes de selecionar facetas](media/search-faceted-navigation/faceted-search-before-facets.png "Pesquisar resultados antes de selecionar facetas")

4. Clique num Título de Negócio, Localização ou Salário Mínimo. As facetas foram nulas na pesquisa inicial, mas à medida que assumem valores, os resultados da pesquisa são aparados de itens que já não correspondem.
   
   ![Resultados da pesquisa após a seleção de facetas](media/search-faceted-navigation/faceted-search-after-facets.png "Resultados da pesquisa após a seleção de facetas")

5. Para limpar a consulta facetada para que possa experimentar diferentes comportamentos de consulta, clique nas `[X]` facetas selecionadas para limpar as facetas.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Saber mais
Assista [Azure Cognitive Search Deep Dive](https://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). Às 45:25, há uma demonstração sobre como implementar as facetas.

Para obter mais informações sobre os princípios de design para a navegação com face, recomendamos os seguintes links:

* [Padrões de design: Navegação com face](https://alistapart.com/article/design-patterns-faceted-navigation)
* [Preocupações frontais ao implementar a procura facetada – Parte 1](https://articles.uie.com/faceted_search2/)