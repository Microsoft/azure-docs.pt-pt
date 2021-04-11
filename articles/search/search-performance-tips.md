---
title: Sugestões de desempenho
titleSuffix: Azure Cognitive Search
description: Saiba mais dicas e boas práticas para maximizar o desempenho num serviço de pesquisa.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 28325a1bbda1b2d4a4bb060ae3e79057275ee42a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582348"
---
# <a name="tips-for-better-performance-in-azure-cognitive-search"></a>Dicas para um melhor desempenho na Pesquisa Cognitiva Azure

Este artigo é uma coleção de dicas e boas práticas que são frequentemente recomendadas para aumentar o desempenho. Saber quais os fatores mais propensos a afetar o desempenho da pesquisa pode ajudá-lo a evitar ineficiências e tirar o máximo partido do seu serviço de pesquisa. Alguns factores-chave incluem:

+ Composição do índice (esquema e tamanho)
+ Tipos de consultas
+ Capacidade de serviço (nível e número de réplicas e divisórias)

## <a name="index-size-and-schema"></a>Tamanho e esquema do índice

As consultas são mais rápidas em índices menores. Esta é em parte uma função de ter menos campos para digitalizar, mas também é devido à forma como o sistema caches conteúdo para futuras consultas. Após a primeira consulta, alguns conteúdos permanecem na memória onde é pesquisado de forma mais eficiente. Como o tamanho do índice tende a crescer ao longo do tempo, uma das melhores práticas é revisitar periodicamente a composição do índice, tanto o esquema como os documentos, para procurar oportunidades de redução de conteúdos. No entanto, se o índice for de tamanho certo, a única outra calibração que pode fazer é aumentar a capacidade: quer [adicionando réplicas](search-capacity-planning.md#adjust-capacity) quer melhorando o nível de serviço. A secção ["Dica: Upgrade para um nível Standard S2"]](#tip-upgrade-to-a-standard-s2-tier) mostra-lhe como avaliar a escala acima versus decisão de escala.

A complexidade do esquema também pode afetar negativamente o indexação e o desempenho da consulta. A atribuição excessiva de campo baseia-se em limitações e requisitos de processamento. [Tipos complexos](search-howto-complex-data-types.md) demoram mais tempo a indexar e consultar. As próximas secções exploram cada condição.

### <a name="tip-be-selective-in-field-attribution"></a>Dica: Seja seletivo na atribuição de campo

Um erro comum que os administradores e desenvolvedores cometem ao criar um índice de pesquisa é selecionar todas as propriedades disponíveis para os campos, em oposição a apenas selecionar apenas as propriedades que são necessárias. Por exemplo, se um campo não precisar de ser o texto completo pesmável, salte esse campo ao definir o atributo pesmável.

:::image type="content" source="media/search-performance/perf-selective-field-attributes.png" alt-text="Atribuição seletiva" border="true":::

O suporte para filtros, facetas e triagem pode quadruplicar os requisitos de armazenamento. Se adicionar sugestivos, os requisitos de armazenamento aumentam ainda mais. Para obter uma ilustração sobre o impacto dos atributos no armazenamento, consulte [Atributos e tamanho do índice](search-what-is-an-index.md#index-size).

Resumindo, as ramificações da sobre-atribuição incluem:

+ Degradação do desempenho de indexação devido ao trabalho extra necessário para processar o conteúdo no campo e, em seguida, armazená-lo dentro do índice invertido de pesquisa (definir o atributo "pesmável" apenas em campos que contenham conteúdo pesmável).

+ Cria uma superfície maior que cada consulta tem de cobrir. Todos os campos marcados como pescandizáveis são analisados numa pesquisa por texto completo.

+ Aumenta os custos operacionais devido ao armazenamento extra. A filtragem e a triagem requerem espaço adicional para armazenar cordas originais (não analisadas). Evite colocar a definição filtral ou ordenada em campos que não necessitam.

+ Em muitos casos, a atribuição limita as capacidades do campo. Por exemplo, se um campo for tão caravel, filtrado e pescável, só pode armazenar 16 KB de texto dentro de um campo, enquanto um campo pes pescável pode conter até 16 MB de texto.

> [!NOTE]
> Apenas deve ser evitada a atribuição desnecessária. Filtros e facetas são muitas vezes essenciais para a experiência de pesquisa, e nos casos em que os filtros são usados, você precisa frequentemente de triagem para que você possa encomendar os resultados (os filtros por si só retornam em um conjunto não ordenado).

### <a name="tip-consider-alternatives-to-complex-types"></a>Dica: Considere alternativas a tipos complexos

Os tipos de dados complexos são úteis quando os dados têm uma estrutura aninhada complicada, como os elementos pai-filho encontrados em documentos JSON. A desvantagem dos tipos complexos são os requisitos de armazenamento extra e os recursos adicionais necessários para indexar o conteúdo, em comparação com os tipos de dados não complexos. 

Em alguns casos, pode evitar estas trocas mapeando uma estrutura de dados complexa para um tipo de campo mais simples, como uma Coleção. Em alternativa, pode optar por aplanar uma hierarquia de campo em campos individuais de nível de raiz.

:::image type="content" source="media/search-performance/perf-flattened-field-hierarchy.png" alt-text="estrutura de campo achatado" border="true":::

## <a name="types-of-queries"></a>Tipos de consultas

Os tipos de consultas que envia são um dos fatores mais importantes para o desempenho, e a otimização de consultas pode melhorar drasticamente o desempenho. Ao desenhar consultas, pense nos seguintes pontos:

+ **Número de campos pescáveis.** Cada campo pesmável adicional requer trabalho adicional pelo serviço de pesquisa. Pode limitar os campos que estão a ser pesquisados no tempo de consulta utilizando o parâmetro "searchFields". É melhor especificar apenas os campos com os quais se preocupa para melhorar o desempenho.

+ **Quantidade de dados a ser devolvido.** Recuperar muitos conteúdos pode tornar as consultas mais lentas. Ao estruturar uma consulta, devolva apenas os campos que necessita para estornizar a página de resultados e, em seguida, recupere os campos restantes usando a [API do Lookup](/rest/api/searchservice/lookup-document) assim que um utilizador selecione uma correspondência.

+ **Uso de pesquisas parciais a prazo.** [As pesquisas a prazo parcial](search-query-partial-matching.md), tais como pesquisa de prefixos, pesquisa de informação e pesquisa regular de expressão, são mais computacionalmente caras do que as pesquisas típicas de palavras-chave, uma vez que requerem exames completos de índice para produzir resultados.

+ **Número de facetas.** Adicionar facetas às consultas requer agregações para cada consulta. Em geral, adicione apenas as facetas que pretende renderizar na sua aplicação.

+ **Limite os altos campos de cardinalidade.**  Um *campo de alto escalão* refere-se a um campo facetável ou filtrado que tem um número significativo de valores únicos e, como resultado, consome recursos significativos ao calcular resultados. Por exemplo, definir um campo de identificação ou descrição do produto como facetable e filtrado contaria como cardeal elevado, porque a maioria dos valores de documento para documento são únicos.

### <a name="tip-use-search-functions-instead-overloading-filter-criteria"></a>Sugestão: Utilize funções de pesquisa em vez de sobrecarregar critérios de filtro

À medida que uma consulta utiliza critérios de filtro cada vez mais [complexos,](search-query-odata-filter.md#filter-size-limitations)o desempenho da consulta de pesquisa irá degradar-se. Considere o seguinte exemplo que demonstra a utilização de filtros para aparar resultados com base na identidade do utilizador:

```json
$filter= userid eq 123 or userid eq 234 or userid eq 345 or userid eq 456 or userid eq 567
```

Neste caso, as expressões do filtro são utilizadas para verificar se um único campo em cada documento é igual a um dos muitos valores possíveis de uma identidade do utilizador. É mais provável que encontre este padrão em aplicações que [implementem aparas de segurança](search-security-trimming-for-azure-search.md) (verificando um campo contendo um ou mais IDs principais contra uma lista de IDs principais que representam o utilizador que emite a consulta).

Uma forma mais eficiente de executar filtros que contenham um grande número de valores é utilizar a [ `search.in` função,](search-query-odata-search-in-function.md)como mostra este exemplo:

```json
search.in(userid, '123,234,345,456,567', ',')
```

### <a name="tip-add-partitions-for-slow-individual-queries"></a>Dica: Adicione divisórias para consultas individuais lentas

Quando o desempenho da consulta está a abrandar em geral, adicionar mais réplicas resolve frequentemente o problema. Mas e se o problema for uma única consulta que demora muito tempo a ser completada? Neste cenário, adicionar réplicas não vai ajudar, mas divisórias adicionais podem. Uma partição divide dados através de recursos de computação extra. Duas divisórias dividem dados ao meio, uma terceira divisória divide-o em terços, e assim por diante. 

Um efeito colateral positivo da adição de divisórias é que as consultas mais lentas às vezes funcionam mais rapidamente devido à computação paralela. Temos notado a paralelização em consultas de baixa seletividade, tais como consultas que combinam com muitos documentos, ou facetas que fornecem contagens sobre um grande número de documentos. Uma vez que o cálculo significativo é necessário para marcar a relevância dos documentos, ou para contar o número de documentos, adicionar divisórias extras ajuda as consultas a completar mais rapidamente.  

Para adicionar divisórias, utilize o [portal Azure,](search-create-service-portal.md) [PowerShell,](search-manage-powershell.md) [Azure CLI](search-manage-azure-cli.md)ou um SDK de gestão.

## <a name="service-capacity"></a>Capacidade de serviço

Um serviço é sobrecarregado quando as consultas demoram muito tempo ou quando o serviço começa a deixar cair pedidos. Se isso acontecer, pode resolver o problema através da atualização do serviço ou adicionando capacidade.

O nível do seu serviço de pesquisa e o número de réplicas/divisórias também têm um grande impacto no desempenho. Cada nível mais alto fornece CPUs mais rápidos e mais memória, ambos com um impacto positivo no desempenho.

### <a name="tip-upgrade-to-a-standard-s2-tier"></a>Dica: Upgrade para um nível S2 standard

O nível de pesquisa Standard S1 é muitas vezes onde os clientes começam. Um padrão comum para os serviços S1 é que os índices crescem ao longo do tempo, o que requer mais divisórias. Mais divisórias levam a tempos de resposta mais lentos, para que mais réplicas sejam adicionadas para lidar com a carga de consulta. Como pode imaginar, o custo de execução de um serviço S1 progrediu agora para níveis além da configuração inicial.

Neste momento, uma questão importante a colocar é se seria benéfico passar para um nível mais elevado, em oposição ao aumento progressiva do número de divisórias ou réplicas do serviço atual. 

Considere a seguinte topologia como um exemplo de um serviço que assumiu níveis crescentes de capacidade:

+ Nível padrão S1
+ Tamanho do índice: 190 GB
+ Contagem de partição: 8 (em S1, o tamanho da partição é de 25 GB por partição)
+ Contagem de réplicas: 2
+ Unidades totais de pesquisa: 16 (8 divisórias x 2 réplicas)
+ Preço hipotético de venda ao público: ~$4.000 USD /mês (assuma $250 USD x 16 unidades de pesquisa)

Suponha que o administrador de serviço ainda esteja a ver taxas de latência mais altas e está a considerar adicionar outra réplica. Isto mudaria a contagem de réplicas de 2 para 3 e, consequentemente, mudaria a contagem da Unidade de Busca para 24 e um preço resultante de USD/mês de USD/mês.

No entanto, se o administrador optasse por mudar para um nível Standard S2, a topologia seria:

+ Nível S2 standard
+ Tamanho do índice: 190 GB
+ Contagem de partição: 2 (em S2, o tamanho da partição é de 100 GB por partição)
+ Contagem de réplicas: 2
+ Unidades totais de pesquisa: 4 (2 divisórias x 2 réplicas)
+ Preço hipotético de venda ao público: ~$4.000 USD /mês ($1000 USD x 4 unidades de pesquisa)

Como este cenário hipotético ilustra, pode ter configurações em níveis inferiores que resultam em custos semelhantes, como se tivesse optado por um nível mais elevado em primeiro lugar. No entanto, os níveis mais elevados vêm com armazenamento premium, o que torna a indexação mais rápida. Os níveis mais altos também têm muito mais poder de computação, bem como memória extra. Pelos mesmos custos, poderia ter infraestruturas mais poderosas apoiando o mesmo índice.

Um benefício importante da memória adicionada é que mais do índice pode ser cached, resultando em menor latência de pesquisa, e um maior número de consultas por segundo. Com esta potência extra, o administrador pode nem precisar de aumentar a contagem de réplicas e pode potencialmente pagar menos do que permanecendo no serviço S1.

## <a name="next-steps"></a>Passos seguintes

Reveja estes artigos adicionais relacionados com o desempenho do serviço.

+ [Analisar o desempenho](search-performance-analysis.md)
+ [Escolha um nível de serviço](search-sku-tier.md)
+ [Adicionar capacidade (réplicas e divisórias)](search-capacity-planning.md#adjust-capacity)
