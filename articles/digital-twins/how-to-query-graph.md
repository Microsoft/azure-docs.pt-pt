---
title: Consultar o grafo duplo
titleSuffix: Azure Digital Twins
description: Veja como consultar o gráfico gémeo Azure Digital Twins para obter informações.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 6d15e2b8bfcddfd1f554ab2a27083fe5256e9e2b
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226333"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Consulta o gráfico gémeo Azure Digital Twins

Este artigo oferece exemplos de consulta e instruções mais detalhadas para usar a **linguagem de consulta Azure Digital Twins** para consultar o seu gráfico [gémeo](concepts-twins-graph.md) para obter informações. (Para uma introdução à linguagem de consulta e uma lista completa das suas [*características, consulte Conceitos: Linguagem de consulta*](concepts-query-language.md).)

Este artigo começa com consultas de amostra que ilustram a estrutura linguística de consulta e operações de consulta comuns para gémeos digitais. Em seguida, descreve como executar as suas consultas depois de as ter escrito, utilizando a [API](/rest/api/digital-twins/dataplane/query) de Consulta de Gémeos Digitais Azure ou um [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis).

> [!NOTE]
> Se estiver a executar as consultas de amostra abaixo com uma chamada API ou SDK, terá de condensar o texto de consulta numa única linha.

## <a name="show-all-digital-twins"></a>Mostre todos os gémeos digitais

Aqui está a consulta básica que devolverá uma lista de todos os gémeos digitais no caso:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

## <a name="query-by-property"></a>Consulta por propriedade

Obtenha gémeos digitais por **propriedades** (incluindo ID e metadados):

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty1":::

Como mostrado na consulta acima, o ID de um gémeo digital é consultado usando o campo de metadados `$dtId` .

>[!TIP]
> Se estiver a usar a Cloud Shell para executar uma consulta com campos de metadados que começam `$` com , deve escapar com um `$` backtick para que a Cloud Shell saiba que não é uma variável e deve ser consumida como literal no texto de consulta.

Você também pode obter gémeos com base em **se uma determinada propriedade é definida**. Aqui está uma consulta que obtém gémeos que têm uma propriedade *de Localização* definida:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty2":::

Isto pode ajudá-lo a obter gémeos pelas suas propriedades de *tags,* como descrito em [Adicionar tags a gémeos digitais.](how-to-use-tags.md) Aqui está uma consulta que deixa todos os gémeos marcados com *vermelho:*

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Você também pode obter gémeos com base no **tipo de propriedade.** Aqui está uma consulta que recebe gémeos cuja propriedade *temperatura* é um número:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty3":::

>[!TIP]
> Se uma propriedade for do `Map` tipo, pode utilizar as chaves e valores do mapa diretamente na consulta, como esta:
> :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty4":::

## <a name="query-by-model"></a>Consulta por modelo

O `IS_OF_MODEL` operador pode ser utilizado para filtrar com base no [**modelo**](concepts-models.md)do gémeo.

Considera [a herança](concepts-models.md#model-inheritance) e [a versão de](how-to-manage-model.md#update-models)modelo, e avalia a **verdade** para um dado gémeo se o gémeo cumprir qualquer uma destas condições:

* O twin implementa diretamente o modelo `IS_OF_MODEL()` fornecido, e o número de versão do modelo no twin é *maior ou igual ao* número de versão do modelo fornecido
* O twin implementa um modelo que *alarga* o modelo `IS_OF_MODEL()` fornecido, e o número de versão do modelo alargado da gémea é *maior ou igual ao* número de versão do modelo fornecido

Assim, por exemplo, se consultar os gémeos do `dtmi:example:widget;4` modelo, a consulta devolverá todos os gémeos com base na **versão 4 ou superior** do modelo **widget,** e também gémeos com base na versão **4 ou maior** de quaisquer **modelos que herdem do widget.**

`IS_OF_MODEL` pode tomar vários parâmetros diferentes, e o resto desta secção é dedicado às suas diferentes opções de sobrecarga.

A utilização mais simples `IS_OF_MODEL` requer apenas um `twinTypeName` parâmetro: `IS_OF_MODEL(twinTypeName)` .
Aqui está um exemplo de consulta que passa um valor neste parâmetro:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel1":::

Para especificar uma coleção dupla para procurar quando há mais de uma (como quando uma `JOIN` é usada), adicione o `twinCollection` parâmetro: `IS_OF_MODEL(twinCollection, twinTypeName)` .
Aqui está um exemplo de consulta que acrescenta um valor para este parâmetro:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel2":::

Para fazer uma correspondência exata, adicione o `exact` parâmetro: `IS_OF_MODEL(twinTypeName, exact)` .
Aqui está um exemplo de consulta que acrescenta um valor para este parâmetro:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel3":::

Também pode passar os três argumentos juntos: `IS_OF_MODEL(twinCollection, twinTypeName, exact)` .
Aqui está um exemplo de consulta especificando um valor para os três parâmetros:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel4":::

## <a name="query-by-relationship"></a>Consulta por relacionamento

Ao consultar com base nas **relações** dos gémeos digitais, a linguagem de consulta Azure Digital Twins tem uma sintaxe especial.

As relações são solicitadas para o âmbito de pesquisa na cláusula `FROM`. Ao contrário de línguas "clássicas" do tipo SQL, cada expressão nesta `FROM` cláusula não é uma tabela; pelo contrário, a cláusula expressa uma relação `FROM` transversal. Para atravessar relações, a Azure Digital Twins usa uma versão personalizada de `JOIN` .

Recorde-se que com as capacidades do [modelo](concepts-models.md) Azure Digital Twins, as relações não existem independentemente dos gémeos. Isto significa que as relações aqui não podem ser questionadas independentemente e devem estar ligadas a um gémeo.
Para lidar com isto, a palavra-chave `RELATED` é usada na cláusula para puxar o conjunto de um certo tipo de relação proveniente da coleção de `JOIN` gémeos. A consulta deve então filtrar na cláusula que os `WHERE` gémeos específicos devem utilizar na consulta de relacionamento (utilizando os valores dos `$dtId` gémeos).

As seguintes secções dão exemplos do que isto parece.

### <a name="basic-relationship-query"></a>Consulta básica de relacionamento

Aqui está uma consulta baseada em relacionamentos. Este código seleciona todos os gémeos digitais com uma propriedade de *ID* de 'ABC', e todos os gémeos digitais relacionados com estes gémeos digitais através de uma relação *contenha.*

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship1":::

> [!NOTE]
> O desenvolvedor não precisa de correlacionar isto `JOIN` com um valor chave na cláusula `WHERE` (ou especificar um valor chave em linha com a `JOIN` definição). Esta correlação é calculada automaticamente pelo sistema, uma vez que as próprias propriedades de relação identificam a entidade de destino.

### <a name="query-by-the-source-or-target-of-a-relationship"></a>Consulta pela fonte ou alvo de uma relação

Pode usar a estrutura de consulta de relacionamento para identificar um gémeo digital que é a fonte ou o alvo de uma relação.

Por exemplo, pode começar com um gémeo de origem e seguir as suas relações para encontrar os gémeos-alvo das relações. Aqui está um exemplo de uma consulta que encontra os gémeos alvo das *relações de feeds provenientes* da *fonte* gémea gémea.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationshipSource":::

Você também pode começar com o alvo da relação e rastrear a relação de volta para encontrar o gémeo de origem. Aqui está um exemplo de uma consulta que encontra a origem gémea de uma relação *de feeds* com o *gémeo-gémeo.*

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationshipTarget":::

### <a name="query-the-properties-of-a-relationship"></a>Consultar as propriedades de uma relação

Da mesma forma que os duplos digitais têm propriedades descritas através de DTDL, as relações também podem ter propriedades. Pode consultar gémeos **com base nas propriedades das suas relações.**
A linguagem de consulta Azure Digital Twins permite filtrar e projeção de relacionamentos, atribuindo um pseudónimo à relação dentro da `JOIN` cláusula.

Como exemplo, considere uma relação *servicedBy* que tenha uma propriedade *reportada deCondição.* Na consulta abaixo, esta relação é dada um pseudónimo de 'R' a fim de referenciar a sua propriedade.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship2":::

No exemplo acima, note como *a comunicaçãoCondition* é uma propriedade da própria relação *de ServiçodBy* (NÃO de algum gémeo digital que tem uma relação *de ServiçodBy).*

### <a name="query-with-multiple-joins"></a>Consulta com vários JOINs

Até cinco `JOIN` s são suportados numa única consulta. Isto permite-lhe percorrer vários níveis de relacionamentos ao mesmo tempo. 

Para consultar vários níveis de relacionamento, use uma única `FROM` declaração seguida de declarações de `JOIN` N, onde as `JOIN` declarações expressam relações sobre o resultado de uma `FROM` declaração anterior ou `JOIN` afirmação.

Aqui está um exemplo de uma consulta multi-junção, que recebe todas as lâmpadas contidas nos painéis de luz nas salas 1 e 2.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship3":::

## <a name="count-items"></a>Contar itens

Pode contar o número de itens num conjunto de resultados utilizando a `Select COUNT` cláusula:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount1":::

Adicione uma `WHERE` cláusula para contar o número de itens que satisfazem determinados critérios. Aqui estão alguns exemplos de contagem com um filtro aplicado baseado no tipo de modelo gémeo (para mais informações sobre esta sintaxe, ver [*Consulta por modelo*](#query-by-model) abaixo):

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount2":::

Também pode usar `COUNT` juntamente com a `JOIN` cláusula. Aqui está uma consulta que conta todas as lâmpadas contidas nos painéis luminosos das salas 1 e 2:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount3":::

## <a name="filter-results-select-top-items"></a>Resultados do filtro: selecione itens de topo

Pode selecionar os vários itens "top" numa consulta utilizando a `Select TOP` cláusula.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectTop":::

## <a name="filter-results-specify-return-set-with-projections"></a>Resultados do filtro: especifique o conjunto de retorno com as projeções

Ao utilizar projeções na `SELECT` declaração, pode escolher quais as colunas que uma consulta irá devolver.

>[!NOTE]
>Neste momento, propriedades complexas não são suportadas. Para garantir que as propriedades de projeção são válidas, combine as projeções com uma `IS_PRIMITIVE` verificação.

Aqui está um exemplo de uma consulta que usa a projeção para devolver gémeos e relacionamentos. A seguinte consulta projeta o *Consumidor,* *Fábrica* e *Borda* a partir de um cenário em que uma Fábrica com uma *Identificação* de *ABC* está relacionada com o *Consumidor* através de uma relação de *Factory.customer,* e essa relação é apresentada como a *Borda.*

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections1":::

Você também pode usar a projeção para devolver uma propriedade de um gémeo. A seguinte consulta projeta o *nome* propriedade dos *Consumidores* que estão relacionados com a *Fábrica* com uma identificação de *ABC* através de uma relação de *Factory.customer*.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections2":::

Você também pode usar a projeção para devolver uma propriedade de uma relação. Tal como no exemplo anterior, a seguinte consulta projeta o Nome do *Imóvel* dos *Consumidores* relacionado com a *Fábrica* com uma Identificação de *ABC* através de uma relação de *Factory.customer;* mas agora também devolve duas propriedades dessa relação, *prop1* e *prop2.* Fá-lo nomeando a relação *Edge* e reunindo as suas propriedades.  

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections3":::

Também pode usar pseudónimos para simplificar consultas com projeção.

A seguinte consulta faz as mesmas operações que o exemplo anterior, mas alia os nomes dos imóveis `consumerName` `first` para, `second` e `factoryArea` .

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections4":::

Aqui está uma consulta semelhante que consulta o mesmo conjunto que acima, mas projeta apenas a *propriedade Consumer.name* como , e projeta `consumerName` a *Fábrica* completa como um gémeo.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections5":::

## <a name="build-efficient-queries-with-the-in-operator"></a>Construa consultas eficientes com o operador IN

Você pode reduzir significativamente o número de consultas que você precisa construindo uma variedade de gémeos e consultando com o `IN` operador. 

Por exemplo, considere um cenário em que *os edifícios* contenham *Pisos* e Pisos contenham *Quartos.*  Para procurar quartos dentro de um edifício que são quentes, uma maneira é seguir estes passos.

1. Encontre pisos no prédio baseado na `contains` relação.

    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="INOperatorWithout":::

2. Para encontrar quartos, em vez de considerar os pisos um a um e executar uma `JOIN` consulta para encontrar os quartos para cada um, você pode consultar com uma coleção dos pisos do edifício (chamado *Floor* na consulta abaixo).

    Na aplicação do cliente:
    
    ```csharp
    var floors = "['floor1','floor2', ..'floorn']"; 
    ```
    
    Em consulta:
    
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="INOperatorWith":::

## <a name="other-compound-query-examples"></a>Outros exemplos de consulta composta

Pode **combinar** qualquer um dos tipos de consulta acima, utilizando operadores de combinação para incluir mais detalhes numa única consulta. Aqui estão alguns exemplos adicionais de consultas compostas que consultam mais de um tipo de descritor gémeo ao mesmo tempo.

* Fora dos dispositivos que a *Sala 123* tem, devolva os dispositivos MxChip que servem o papel de Operador
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples1":::
* Arranja gémeos que têm uma relação chamada *Contém* com outro gémeo que tem uma ID de *id1*
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples2":::
* Obtenha todos os quartos deste modelo de quarto que são contidos pelo *piso 11*
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples3":::

## <a name="run-queries-with-the-api"></a>Executar consultas com a API

Uma vez decidido uma cadeia de consulta, executa-a fazendo uma chamada para a [**API de Consulta**](/rest/api/digital-twins/dataplane/query).

Pode ligar diretamente para a API ou utilizar um dos [SDKs](how-to-use-apis-sdks.md#overview-data-plane-apis) disponíveis para a Azure Digital Twins.

O seguinte código snippet ilustra a chamada [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client) de uma aplicação do cliente:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="RunQuery":::

A consulta utilizada nesta chamada devolve uma lista de gémeos digitais, que o exemplo acima representa com objetos [BasicDigitalTwin.](/dotnet/api/azure.digitaltwins.core.basicdigitaltwin) O tipo de devolução dos seus dados para cada consulta dependerá dos termos especificados com a `SELECT` declaração:
* As consultas que começam com `SELECT * FROM ...` uma lista de gémeos digitais (que podem ser serializados como `BasicDigitalTwin` objetos, ou outros tipos de gémeos digitais personalizados que possa ter criado).
* As consultas que começam no formato `SELECT <A>, <B>, <C> FROM ...` devolverão um dicionário com teclas, `<A>` e `<B>` `<C>` .
* Outros formatos de `SELECT` declarações podem ser concebidos para devolver dados personalizados. Pode considerar criar as suas próprias aulas para lidar com conjuntos de resultados muito personalizados. 

### <a name="query-with-paging"></a>Consulta com paging

Consulta chama de suporte de paging. Aqui está um exemplo completo usando `BasicDigitalTwin` como tipo de resultado de consulta com manipulação de erros e paging:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as [APIs e SDKs das Gémeas Digitais Azure,](how-to-use-apis-sdks.md)incluindo a API de Consulta que é usada para executar as consultas deste artigo.
