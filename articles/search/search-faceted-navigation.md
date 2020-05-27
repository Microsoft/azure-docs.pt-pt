---
title: Adicione uma hierarquia de categoria de navegação facetada
titleSuffix: Azure Cognitive Search
description: Adicione navegação facetada para filtragem auto-direcionada em aplicações de pesquisa que se integram com a Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4d2ee2bccf94dca933981c3070323b659eab6cfa
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836095"
---
# <a name="how-to-implement-faceted-navigation-in-azure-cognitive-search"></a>Como implementar navegação faceada na Pesquisa Cognitiva Azure

A navegação facetada é um mecanismo de filtragem que fornece navegação de perfuração auto-direcionada em aplicações de pesquisa. O termo "navegação facetada" pode não ser familiar, mas provavelmente já o usou antes. Como mostra o exemplo seguinte, a navegação facetada não é mais do que as categorias utilizadas para filtrar resultados.

 ![Demonstração do Portal de Trabalho de Pesquisa Cognitiva Azure](media/search-faceted-navigation/azure-search-faceting-example.png "Demonstração do Portal de Trabalho de Pesquisa Cognitiva Azure")

A navegação facetada é um ponto de entrada alternativo à procura. Oferece uma alternativa conveniente à dactilografia de expressões de pesquisa complexas à mão. As facetas podem ajudá-lo a encontrar o que procura, garantindo que não obtém resultados nulos. Como desenvolvedor, as facetas permitem-lhe expor os critérios de pesquisa mais úteis para navegar no seu índice de pesquisa. Nas aplicações de retalho online, a navegação facetada é muitas vezes construída sobre marcas, departamentos (sapatos infantis), tamanho, preço, popularidade e classificações. 

A implementação da navegação faceada difere em todas as tecnologias de pesquisa. Na Pesquisa Cognitiva Azure, a navegação facetada é construída no momento da consulta, utilizando campos que atribuiu anteriormente no seu esquema.

-   Nas consultas que a sua aplicação constrói, uma consulta deve enviar parâmetros de consulta de *facet* para obter os valores de filtro de faceta disponíveis para esse conjunto de resultados do documento.

-   Para realmente aparar o conjunto de resultados do documento, a aplicação também deve aplicar uma `$filter` expressão.

No desenvolvimento da sua aplicação, o código de escrita que constrói consultas constitui a maior parte da obra. Muitos dos comportamentos de aplicação que seria de esperar da navegação facetada são fornecidos pelo serviço, incluindo suporte incorporado para definir gamas e obter contagens para resultados de faceta. O serviço também inclui incumprimentos sensatos que o ajudam a evitar estruturas de navegação desequilibradas. 

## <a name="sample-code-and-demo"></a>Código de amostra e demonstração
Este artigo usa um portal de pesquisa de emprego como exemplo. O exemplo é implementado como uma aplicação ASP.NET MVC.

- Consulte e teste a demonstração de trabalho online na Demo do Portal de Trabalho de [Pesquisa Cognitiva Azure.](https://aka.ms/azjobsdemo)

- Descarregue o código do [repo Azure-Samples no GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

## <a name="get-started"></a>Introdução
Se você é novo para pesquisar desenvolvimento, a melhor maneira de pensar na navegação faceada é que mostra as possibilidades de pesquisa auto-dirigida. É um tipo de experiência de pesquisa de perfuração, baseada em filtros predefinidos, usado para reduzir rapidamente os resultados de pesquisa através de ações de ponto e clique. 

### <a name="interaction-model"></a>Modelo de interação

A experiência de pesquisa para navegação facetada é iterativa, por isso vamos começar por compreendê-la como uma sequência de consultas que se desenrolam em resposta às ações do utilizador.

O ponto de partida é uma página de aplicação que fornece navegação faceada, tipicamente colocada na periferia. A navegação facetada é muitas vezes uma estrutura de árvore, com caixas de verificação para cada valor, ou texto clicável. 

1. Uma consulta enviada para a Pesquisa Cognitiva Azure especifica a estrutura de navegação faceada através de um ou mais parâmetros de consulta de faceta. Por exemplo, a consulta pode `facet=Rating` incluir, talvez com uma `:values` ou `:sort` opção para aperfeiçoar ainda mais a apresentação.
2. A camada de apresentação torna uma página de pesquisa que fornece navegação faceada, utilizando as facetas especificadas no pedido.
3. Dada uma estrutura de navegação facetada que inclui rating, clique em "4" para indicar que apenas devem ser mostrados produtos com uma classificação de 4 ou superior. 
4. Em resposta, a aplicação envia uma consulta que inclui`$filter=Rating ge 4` 
5. A camada de apresentação atualiza a página, mostrando um conjunto de resultados reduzido, contendo apenas os itens que satisfazem os novos critérios (neste caso, os produtos avaliados em 4 e acima).

Uma faceta é um parâmetro de consulta, mas não a confunde com a entrada de consulta. Nunca é usado como critério de seleção numa consulta. Em vez disso, pense nos parâmetros de consulta de faceta como inputs para a estrutura de navegação que volta na resposta. Para cada parâmetro de consulta facial que fornece, a Pesquisa Cognitiva Azure avalia quantos documentos estão nos resultados parciais para cada valor faceta.

Reparem no `$filter` passo 4. O filtro é um aspeto importante da navegação faceada. Embora as facetas e filtros sejam independentes na API, você precisa de ambos para fornecer a experiência que pretende. 

### <a name="app-design-pattern"></a>Padrão de design de aplicativos

No código de aplicação, o padrão é usar parâmetros de consulta de faceta para devolver a estrutura de navegação facetada juntamente com os resultados da faceta, além de uma expressão $filter.  A expressão do filtro trata do clique no valor da faceta. Pense na `$filter` expressão como o código por trás do corte real dos resultados da pesquisa voltou à camada de apresentação. Dada a faceta das Cores, clicar na cor Vermelha é implementada através de uma `$filter` expressão que seleciona apenas os itens que têm uma cor de vermelho. 

### <a name="query-basics"></a>Básicos de consulta

Na Pesquisa Cognitiva Azure, um pedido é especificado através de um ou mais parâmetros de consulta (ver [Documentos](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) de Pesquisa para uma descrição de cada um). Nenhum dos parâmetros de consulta é necessário, mas deve ter pelo menos um para que uma consulta seja válida.

A precisão, entendida como a capacidade de filtrar sucessos irrelevantes, é conseguida através de uma ou ambas as expressões:

-   **pesquisa=**  
    O valor deste parâmetro constitui a expressão de pesquisa. Pode ser uma única peça de texto, ou uma expressão de pesquisa complexa que inclui vários termos e operadores. No servidor, uma expressão de pesquisa é usada para pesquisa de texto completo, consultando campos pesquisáveis no índice para termos correspondentes, devolvendo resultados na ordem de classificação. Se você se definir para nulo, a execução da `search` consulta é sobre todo o índice (isto é, `search=*` ). Neste caso, outros elementos da consulta, como um `$filter` ou perfil de pontuação, são os principais fatores que afetam os documentos devolvidos `($filter` e em que ordem ( ou `scoringProfile` `$orderby` ).

-   **$filter=**  
    Um filtro é um mecanismo poderoso para limitar o tamanho dos resultados de pesquisa com base nos valores de atributos específicos do documento. A `$filter` é avaliado primeiro, seguido por uma lógica de faceta que gera os valores disponíveis e as contagens correspondentes para cada valor

Expressões de pesquisa complexas diminuem o desempenho da consulta. Sempre que possível, utilize expressões de filtro bem construídas para aumentar a precisão e melhorar o desempenho da consulta.

Para entender melhor como um filtro adiciona mais precisão, compare uma expressão de pesquisa complexa com uma que inclui uma expressão de filtro:

-   `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`
-   `GET /indexes/hotel/docs?search=lodging&$filter=City eq 'Seattle' and Parking and Type ne 'motel'`

Ambas as consultas são válidas, mas a segunda é superior se você estiver procurando não-motéis com estacionamento em Seattle.
-   A primeira consulta baseia-se nessas palavras específicas que são mencionadas ou não mencionadas em campos de cordas como Nome, Descrição e qualquer outro campo que contenha dados pesquisáveis.
-   A segunda consulta procura correspondências precisas em dados estruturados e é provável que seja muito mais precisa.

Nas aplicações que incluem navegação facetada, certifique-se de que cada ação do utilizador sobre uma estrutura de navegação facetada é acompanhada por um estreitamento dos resultados de pesquisa. Para reduzir os resultados, utilize uma expressão de filtro.

<a name="howtobuildit"></a>

## <a name="build-a-faceted-navigation-app"></a>Construir uma aplicação de navegação facetada
Implementa navegação faceada com a Pesquisa Cognitiva Azure no seu código de aplicação que constrói o pedido de pesquisa. A navegação facetada baseia-se em elementos do seu esquema que definiu anteriormente.

Predefinido no seu índice de pesquisa é o `Facetable [true|false]` atributo indexado, definido em campos selecionados para permitir ou desativar a sua utilização numa estrutura de navegação facetada. `"Facetable" = true`Sem, um campo não pode ser utilizado na navegação de faceta.

A camada de apresentação do seu código proporciona a experiência do utilizador. Deve enumerar as partes constituintes da navegação facetada, tais como o rótulo, os valores, as caixas de verificação e a contagem. A API de pesquisa cognitiva Azure É agnóstica de plataforma, por isso use qualquer idioma e plataforma que queira. O importante é incluir elementos ui que suportem a atualização incremental, com estado ui atualizado à medida que cada faceta adicional é selecionada. 

No momento da consulta, o seu código de candidatura cria um pedido que inclui , um parâmetro de `facet=[string]` pedido que fornece o campo para enfrentar. Uma consulta pode ter múltiplas facetas, tais `&facet=color&facet=category&facet=rating` como, cada uma separada por um caráter ampersand (&).

O código de aplicação também deve construir uma `$filter` expressão para lidar com os eventos de clique na navegação faceada. A `$filter` reduz os resultados da pesquisa, utilizando o valor faceta como critérios de filtro.

A Pesquisa Cognitiva Azure devolve os resultados da pesquisa, com base num ou mais termos em que entra, juntamente com atualizações para a estrutura de navegação facetada. Na Pesquisa Cognitiva Azure, a navegação facetada é uma construção de nível único, com valores de faceta, e conta quantos resultados são encontrados para cada um.

Nas seguintes secções, olhamos mais de perto como construir cada parte.

<a name="buildindex"></a>

## <a name="build-the-index"></a>Construir o índice
O faceting é ativado numa base de campo a campo no índice, através deste atributo indicador: `"Facetable": true` .  
Todos os tipos de campo que possam ser utilizados na navegação faceada são `Facetable` por defeito. Tais tipos de campo incluem, e todos os tipos de `Edm.String` `Edm.DateTimeOffset` campo numéricos (essencialmente, todos os tipos de campo são frontáveis `Edm.GeographyPoint` exceto , que não podem ser usados na navegação facial). 

Ao construir um índice, a melhor prática para a navegação facetada é desligar explicitamente os campos que nunca devem ser usados como faceta.  Em especial, devem ser definidos campos de cordas para valores singleton, tais como identificação ou nome do produto, para evitar a `"Facetable": false` sua utilização acidental (e ineficaz) na navegação facial. Desligar-se onde não precisa ajuda a manter o tamanho do índice pequeno, e tipicamente melhora o desempenho.

Segue-se parte do esquema para a aplicação de amostra seletiva Do Portal de Trabalho, aparada de alguns atributos para reduzir o tamanho:

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

Como pode ver no esquema da amostra, é desligado para campos de `Facetable` cordas que não devem ser usados como facetas, como valores de identificação. Desligar-se onde não precisa ajuda a manter o tamanho do índice pequeno, e tipicamente melhora o desempenho.

> [!TIP]
> Como uma boa prática, inclua o conjunto completo de atributos de índice para cada campo. Embora `Facetable` esteja ligado por padrão para quase todos os campos, definir propositadamente cada atributo pode ajudá-lo a pensar através das implicações de cada decisão do esquema. 

<a name="checkdata"></a>

## <a name="check-the-data"></a>Verifique os dados
A qualidade dos seus dados tem um efeito direto sobre se a estrutura de navegação faceada se materializa como espera. Também afeta a facilidade de construção de filtros para reduzir o conjunto de resultados.

Se quiser enfrentar a Marca ou o Preço, cada documento deve conter valores para *BrandName* e *ProductPrice* que sejam válidos, consistentes e produtivos como opção de filtro.

Aqui estão algumas lembranças do que esfregar para:

* Para cada campo que pretende enfrentar, pergunte-se se contém valores adequados como filtros em pesquisa auto-dirigida. Os valores devem ser curtos, descritivos e suficientemente distintos para oferecer uma escolha clara entre opções concorrentes.
* Erros ortográficos ou valores quase iguais. Se você encarar a Cor, e os valores de campo incluem Laranja e Ornage (um erro ortográfico), uma faceta baseada no campo Color pegaria ambos.
* O texto de caso misto também pode causar estragos na navegação faceada, com laranja e laranja aaparecerem como dois valores diferentes. 
* Versões únicas e plurais do mesmo valor podem resultar numa faceta separada para cada um.

Como pode imaginar, a diligência na preparação dos dados é um aspeto essencial da navegação facial eficaz.

<a name="presentationlayer"></a>

## <a name="build-the-ui"></a>Criar a IU
Trabalhar a partir da camada de apresentação pode ajudá-lo a descobrir requisitos que podem ser perdidos de outra forma, e entender quais as capacidades essenciais para a experiência de pesquisa.

Em termos de navegação faceada, a sua página web ou aplicação exibe a estrutura de navegação facetada, deteta a entrada do utilizador na página e insere os elementos alterados. 

Para aplicações web, o AJAX é comumente usado na camada de apresentação porque permite atualizar alterações incrementais. Também pode utilizar ASP.NET MVC ou qualquer outra plataforma de visualização que possa ligar-se a um serviço de Pesquisa Cognitiva Azure em HTTP. A aplicação da amostra referenciada ao longo deste artigo -- a Demonstração do Portal de Trabalho de **Pesquisa Cognitiva Azure** - passa a ser uma aplicação ASP.NET MVC.

Na amostra, a navegação facetada é incorporada na página de resultados da pesquisa. O exemplo seguinte, retirado do `index.cshtml` ficheiro da aplicação da amostra, mostra a estrutura html estática para exibir navegação facetada na página de resultados da pesquisa. A lista de facetas é construída ou reconstruída dinamicamente quando submete um termo de pesquisa, ou selecione ou limpe uma faceta.

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

O seguinte código da `index.cshtml` página constrói dinamicamente o HTML para exibir a primeira faceta, Título de Negócios. Funções semelhantes constroem dinamicamente o HTML para as outras facetas. Cada faceta tem uma etiqueta e uma contagem, que mostra o número de itens encontrados para esse resultado faceta.

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
> Quando desenhar a página de resultados da pesquisa, lembre-se de adicionar um mecanismo para limpar facetas. Se adicionar caixas de verificação, pode facilmente ver como limpar os filtros. Para outros layouts, você pode precisar de um padrão de migalhas de pão ou outra abordagem criativa. Por exemplo, na aplicação de amostra do Portal de Pesquisa de Emprego, pode clicar na `[X]` faceta selecionada para limpar a faceta.

<a name="buildquery"></a>

## <a name="build-the-query"></a>Construa a consulta
O código que escreve para consultas de construção deve especificar todas as partes de uma consulta válida, incluindo expressões de pesquisa, facetas, filtros, perfis de pontuação, qualquer coisa usada para formular um pedido. Nesta secção, exploramos onde as facetas se encaixam numa consulta, e como os filtros são usados com facetas para entregar um conjunto de resultados reduzido.

Note que as facetas são integrais nesta aplicação de amostra. A experiência de pesquisa na Demo do Portal de Trabalho é projetada em torno de navegação e filtros facetados. A colocação proeminente da navegação faceada na página demonstra a sua importância. 

Um exemplo é muitas vezes um bom lugar para começar. O exemplo seguinte, retirado do `JobsSearch.cs` ficheiro, constrói um pedido que cria navegação faceta com base no Título de Negócio, Localização, Tipo de Publicação e Salário Mínimo. 

```cs
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "business_title", "posting_type", "level", "salary_range_from,interval:50000" },
};
```

Um parâmetro de consulta facial é definido para um campo e, dependendo do tipo de dados, pode ser ainda parametrizado por lista de comma-delimitada que `count:<integer>` inclui, `sort:<>` , , e `interval:<integer>` `values:<list>` . Uma lista de valores é suportada para dados numéricos ao configurar intervalos. Consulte documentos de [pesquisa (API de pesquisa cognitiva azure)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) para obter detalhes de utilização.

Juntamente com as facetas, o pedido formulado pela sua aplicação também deve construir filtros para reduzir o conjunto de documentos candidatos com base numa seleção de valor faceta. Para uma loja de bicicletas, a navegação facetada fornece pistas para questões como *que cores, fabricantes e tipos de bicicletas estão disponíveis?* Filtrar responde a perguntas como *quais bicicletas exatas são vermelhas, bicicletas de montanha, nesta gama de preços?* Quando clica em "Vermelho" para indicar que apenas devem ser mostrados produtos Vermelhos, a próxima consulta que a aplicação envia inclui `$filter=Color eq 'Red'` .

O seguinte código da `JobsSearch.cs` página adiciona o Título de Negócio selecionado ao filtro se selecionar um valor a partir da faceta do Título de Negócio.

```cs
if (businessTitleFacet != "")
  filter = "business_title eq '" + businessTitleFacet + "'";
```

<a name="tips"></a> 

## <a name="tips-and-best-practices"></a>Sugestões e melhores práticas

### <a name="indexing-tips"></a>Dicas de indexação
**Melhore a eficiência do índice se não utilizar uma caixa de pesquisa**

Se a sua aplicação utilizar a navegação facetada exclusivamente (isto é, sem caixa de pesquisa), pode marcar o campo `searchable=false` como, `facetable=true` para produzir um índice mais compacto. Além disso, a indexação ocorre apenas em valores inteiros de faceta, sem quebra de palavras ou indexação das partes componentes de um valor de várias palavras.

**Especificar quais os campos que podem ser usados como facetas**

Recorde-se que o esquema do índice determina quais os campos disponíveis para serem utilizados como faceta. Assumindo que um campo é facetável, a consulta especifica quais os campos a enfrentar. O campo pelo qual está a enfrentar fornece os valores que aparecem abaixo do rótulo. 

Os valores que aparecem sob cada rótulo são recuperados do índice. Por exemplo, se o campo de faceta for *Color,* os valores disponíveis para filtragem adicional são os valores para esse campo - Vermelho, Preto e assim por diante.

Apenas para valores numéricos e dateTime, pode definir explicitamente valores no campo faceta (por exemplo, `facet=Rating,values:1|2|3|4|5` ). É permitida uma lista de valores para que estes tipos de campo simplifiquem a separação dos resultados da faceta em intervalos contíguos (quer as gamas baseadas em valores numéricos ou períodos de tempo). 

**Por padrão, só pode ter um nível de navegação faceada** 

Como notado, não há apoio direto para as facetas nidificantes numa hierarquia. Por padrão, a navegação facetada na Pesquisa Cognitiva Azure suporta apenas um nível de filtros. No entanto, existem sobras. Pode codificar uma estrutura de faceta hierárquica numa `Collection(Edm.String)` com um ponto de entrada por hierarquia. A implementação desta suver é fora do âmbito deste artigo. 

### <a name="querying-tips"></a>Dicas de consulta
**Verlidar campos**

Se construir a lista de facetas dinamicamente com base na entrada de utilizador não confiável, valide que os nomes dos campos facetados são válidos. Ou, escapar aos nomes ao construir URLs utilizando `Uri.EscapeDataString()` ou em .NET, ou o equivalente na sua plataforma de eleição.

### <a name="filtering-tips"></a>Dicas de filtragem
**Aumentar a precisão de pesquisa com filtros**

Utilize filtros. Se confiar apenas em expressões de pesquisa, a criação pode fazer com que um documento seja devolvido que não tenha o valor facial preciso em nenhum dos seus campos.

**Aumentar o desempenho da pesquisa com filtros**

Os filtros reduzem o conjunto de documentos candidatos para pesquisa e excluem-nos do ranking. Se tiver um grande conjunto de documentos, usar uma broca de faceta seletiva muitas vezes dá-lhe um melhor desempenho.
  
**Filtrar apenas os campos encarados**

Em perfuração facetada, você normalmente quer incluir apenas documentos que tenham o valor facial em um campo específico (faceed), não em qualquer lugar em todos os campos pesquisáveis. A adição de um filtro reforça o campo alvo, direcionando o serviço para procurar apenas no campo facetado por um valor correspondente.

**Aparar resultados da faceta com mais filtros**

Os resultados da faceta são documentos encontrados nos resultados da pesquisa que correspondem a um termo faceta. No exemplo seguinte, nos resultados de pesquisa para *computação em nuvem,* 254 itens também têm *especificação interna* como tipo de conteúdo. Os itens não são necessariamente mutuamente exclusivos. Se um item satisfaz os critérios de ambos os filtros, é contado em cada um. Esta duplicação é possível quando se enfrenta em `Collection(Edm.String)` campos, que são frequentemente utilizados para implementar a marcação de documentos.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

Em geral, se descobrir que os resultados da faceta são consistentemente demasiado grandes, recomendamos que adicione mais filtros para dar aos utilizadores mais opções para reduzir a pesquisa.

### <a name="tips-about-result-count"></a>Dicas sobre a contagem de resultados

**Limite o número de itens na navegação faceta**

Para cada campo facetado na árvore de navegação, há um limite padrão de 10 valores. Este padrão faz sentido para as estruturas de navegação porque mantém a lista de valores num tamanho manejável. Pode anular o padrão atribuindo um valor a contar.

* `&facet=city,count:5`especifica que apenas as cinco primeiras cidades encontradas nos resultados mais bem classificados são devolvidas como resultado faceta. Considere uma consulta de amostra com um termo de pesquisa de "aeroporto" e 32 fósforos. Se a consulta especificar `&facet=city,count:5` , apenas as cinco primeiras cidades únicas com mais documentos nos resultados da pesquisa estão incluídas nos resultados da faceta.

Note a distinção entre resultados de faceta e resultados de pesquisa. Os resultados da pesquisa são todos os documentos que coincidem com a consulta. Os resultados da faceta são os fósforos para cada valor faceta. No exemplo, os resultados da pesquisa incluem nomes da Cidade que não estão na lista de classificação faceta (5 no nosso exemplo). Os resultados filtrados através da navegação faceta tornam-se visíveis quando se limpam as facetas, ou escolhem outras facetas para além da Cidade. 

> [!NOTE]
> Discutir `count` quando há mais do que um tipo pode ser confuso. A tabela seguinte oferece um breve resumo de como o termo é usado em API de pesquisa cognitiva azure, código de amostra e documentação. 

* `@colorFacet.count`<br/>
  No código de apresentação, deve ver um parâmetro de contagem na faceta, utilizado para apresentar o número de resultados da faceta. Nos resultados da faceta, a contagem indica o número de documentos que correspondem ao termo ou intervalo faceta.
* `&facet=City,count:12`<br/>
  Numa consulta de rosto, pode definir a contagem para um valor.  O padrão é de 10, mas pode defini-lo mais alto ou inferior. A definição `count:12` obtém os 12 melhores jogos em resultados de faceta por contagem de documentos.
* "`@odata.count`"<br/>
  Na resposta à consulta, este valor indica o número de itens correspondentes nos resultados da pesquisa. Em média, é maior do que a soma de todos os resultados da faceta combinados, devido à presença de itens que correspondem ao termo de pesquisa, mas não têm correspondência de valor faceta.

**Obtenha contagens nos resultados da faceta**

Quando adicionar um filtro a uma consulta facetada, é melhor reter a declaração de faceta (por exemplo, `facet=Rating&$filter=Rating ge 4` ). Tecnicamente, facet=Rating não é necessário, mas mantê-lo devolve as contagens de valores faciais para classificações 4 e superiores. Por exemplo, se clicar em "4" e a consulta incluir um filtro maior ou igual a "4", as contagens são devolvidas para cada classificação que seja 4 e superior.  

**Certifique-se de obter contas precisas de faceta**

Em certas circunstâncias, pode descobrir que as contagens de faceta não correspondem aos conjuntos de resultados (ver [navegação facetada em Pesquisa Cognitiva Azure (Microsoft Q&uma página](https://docs.microsoft.com/answers/topics/azure-cognitive-search.html)de perguntas).

As contagens de faceta podem ser imprecisas devido à arquitetura sharding. Cada índice de pesquisa tem vários fragmentos, e cada fragmento reporta as facetas N superiores por contagem de documentos, que é então combinado em um único resultado. Se alguns fragmentos tiverem muitos valores correspondentes, enquanto outros têm menos, pode descobrir que alguns valores de faceta estão em falta ou sub-contados nos resultados.

Embora este comportamento possa mudar a qualquer momento, se você encontrar este comportamento hoje, você pode trabalhar em torno dele inflacionando artificialmente a contagem: \< número> para um grande número para impor relatórios completos de cada fragmento. Se o valor da contagem: é maior ou igual ao número de valores únicos no campo, é garantido resultados precisos. No entanto, quando as contagens de documentos são elevadas, há uma penalidade de desempenho, por isso use esta opção criteriosamente.

### <a name="user-interface-tips"></a>Dicas de interface do utilizador
**Adicione etiquetas para cada campo na navegação faceta**

As etiquetas são tipicamente definidas no HTML ou na forma `index.cshtml` (na aplicação da amostra). Não existe API na Pesquisa Cognitiva Azure para etiquetas de navegação de facetas ou quaisquer outros metadados.

<a name="rangefacets"></a>

## <a name="filter-based-on-a-range"></a>Filtro com base numa gama
Enfrentar uma série de valores é um requisito comum de aplicação de pesquisa. As gamas são suportadas para dados numéricos e valores dateTime. Pode ler mais sobre cada abordagem em Documentos de [Pesquisa (API de Pesquisa Cognitiva Azure)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

A Pesquisa Cognitiva Azure simplifica a construção de gama, fornecendo duas abordagens para a computação de uma gama. Para ambas as abordagens, a Pesquisa Cognitiva Azure cria as gamas adequadas dadas as inputs que forneceu. Por exemplo, se especificar valores de intervalo de 10/20/30, cria automaticamente intervalos de 0-10, 10-20, 20-30. A sua aplicação pode remover opcionalmente quaisquer intervalos que estejam vazios. 

**Abordagem 1: Utilize o parâmetro de intervalo**  
Para definir facetas de preço em incrementos de $10, especificaria:`&facet=price,interval:10`

**Abordagem 2: Utilize uma lista de valores**  
Para dados numéricos, pode utilizar uma lista de valores.  Considere a gama de facetas para um `listPrice` campo, prestado da seguinte forma:

  ![Lista de valores da amostra](media/search-faceted-navigation/Facet-5-Prices.PNG "Lista de valores da amostra")

Para especificar uma gama de facetas como a da imagem anterior, utilize uma lista de valores:

    facet=listPrice,values:10|25|100|500|1000|2500

Cada gama é construída usando 0 como ponto de partida, um valor da lista como ponto final, e depois aparada da gama anterior para criar intervalos discretos. A Pesquisa Cognitiva Azure faz estas coisas como parte da navegação facial. Não é preciso escrever código para estruturar cada intervalo.

### <a name="build-a-filter-for-a-range"></a>Construa um filtro para uma gama
Para filtrar documentos com base numa gama selecionada, pode utilizar os `"ge"` operadores e `"lt"` filtrar numa expressão em duas partes que define os pontos finais da gama. Por exemplo, se escolher o intervalo 10-25 para um `listPrice` campo, o filtro seria `$filter=listPrice ge 10 and listPrice lt 25` . No código da amostra, a expressão do filtro utiliza **preçosFrom** e **preçosPara** definir os pontos finais. 

  ![Consulta para uma gama de valores](media/search-faceted-navigation/Facet-6-buildfilter.PNG "Consulta para uma gama de valores")

<a name="geofacets"></a> 

## <a name="filter-based-on-distance"></a>Filtro com base na distância
É comum ver filtros que o ajudam a escolher uma loja, restaurante ou destino com base na sua proximidade com a sua localização atual. Embora este tipo de filtro possa parecer uma navegação facial, é apenas um filtro. Mencionamos-o aqui para aqueles que procuram especificamente aconselhamento de implementação para esse problema específico de conceção.

Existem duas funções geoespaciais em Azure Cognitive Search, **geo.distance** e **geo.intersects.**

* A função **geo.distância** devolve a distância em quilómetros entre dois pontos. Um ponto é um campo e o outro é uma constante passada como parte do filtro. 
* A função **geo.intersecta** retorna verdadeira se um determinado ponto estiver dentro de um determinado polígono. O ponto é um campo e o polígono é especificado como uma lista constante de coordenadas passadas como parte do filtro.

Pode encontrar exemplos de filtro na [sintaxe de expressão OData (Pesquisa Cognitiva Azure)](query-odata-filter-orderby-syntax.md).

<a name="tryitout"></a>

## <a name="try-the-demo"></a>Tente a demonstração.
A Demo do Portal de Trabalho de Pesquisa Cognitiva Azure contém os exemplos referenciados neste artigo.

-   Consulte e teste a demonstração de trabalho online na Demo do Portal de Trabalho de [Pesquisa Cognitiva Azure.](https://aka.ms/azjobsdemo)

-   Descarregue o código do [repo Azure-Samples no GitHub](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs).

Enquanto trabalha com os resultados da pesquisa, observe o URL para obter alterações na construção de consultas. Esta aplicação acontece para anexar facetas ao URI à medida que seleciona cada uma.

1. Para utilizar a funcionalidade de mapeamento da aplicação de demonstração, obtenha uma chave Bing Maps do [Bing Maps Dev Center](https://www.bingmapsportal.com/). Colá-lo sobre a chave existente na `index.cshtml` página. A `BingApiKey` definição do `Web.config` ficheiro não é utilizada. 

2. Execute a aplicação. Faça o passeio opcional ou dispense a caixa de diálogo.
   
3. Introduza um termo de pesquisa, como "analista", e clique no ícone 'Pesquisa'. A consulta executa rapidamente.
   
   Uma estrutura de navegação facetada também é devolvida com os resultados da pesquisa. Na página de resultados de pesquisa, a estrutura de navegação facetada inclui contagens para cada resultado faceta. Não são selecionadas facetas, pelo que todos os resultados correspondentes são devolvidos.
   
   ![Pesquisar resultados antes de selecionar facetas](media/search-faceted-navigation/faceted-search-before-facets.png "Pesquisar resultados antes de selecionar facetas")

4. Clique num Título de Negócio, Localização ou Salário Mínimo. As facetas foram nulas na pesquisa inicial, mas à medida que assumem valores, os resultados da pesquisa são aparados de itens que já não correspondem.
   
   ![Pesquisar resultados após selecionar facetas](media/search-faceted-navigation/faceted-search-after-facets.png "Pesquisar resultados após selecionar facetas")

5. Para limpar a consulta facetada para que possa experimentar diferentes comportamentos de consulta, clique no `[X]` seguinte as facetas selecionadas para limpar as facetas.
   
<a name="nextstep"></a>

## <a name="learn-more"></a>Saiba mais
Assista [azure Cognitive Search Deep Dive](https://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). Às 45:25, há uma demonstração sobre como implementar facetas.

Para obter mais informações sobre os princípios de design para a navegação facetada, recomendamos os seguintes links:

* [Padrões de Design: Navegação Faceted](https://alistapart.com/article/design-patterns-faceted-navigation)
* [Preocupações frontais ao implementar pesquisa facetada - Parte 1](https://articles.uie.com/faceted_search2/)

