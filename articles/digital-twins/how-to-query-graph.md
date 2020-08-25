---
title: Consultar o grafo duplo
titleSuffix: Azure Digital Twins
description: Veja como consultar o gráfico gémeo Azure Digital Twins para obter informações.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: e7be96fcab0807ac8c6500c3b360f9380b4d2b28
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88824955"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Consulta o gráfico gémeo Azure Digital Twins

Este artigo oferece exemplos e mais detalhes para usar a [linguagem de consulta Azure Digital Twins](concepts-query-language.md) para consultar o gráfico [gémeo](concepts-twins-graph.md) para obter informações. Execute consultas no gráfico utilizando as [**APIs de Consulta**](how-to-use-apis-sdks.md)de Gémeos Digitais Azure .

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

O resto deste artigo fornece exemplos de como utilizar estas operações.

## <a name="query-syntax"></a>Sintaxe de consulta

Esta secção contém consultas de amostra que ilustram a estrutura linguística da consulta e realizam possíveis operações de consulta.

Obtenha [gémeos digitais](concepts-twins-graph.md) por propriedades (incluindo ID e metadados):
```sql
SELECT  * 
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

> [!TIP]
> A identificação de um gémeo digital é consultada utilizando o campo de `$dtId` metadados.

Você também pode obter gémeos pelas suas propriedades *de tag,* como descrito em [Adicionar tags a gémeos digitais](how-to-use-tags.md):
```sql
select * from digitaltwins where is_defined(tags.red) 
```

### <a name="select-top-items"></a>Selecione itens de topo

Pode selecionar os vários itens "top" numa consulta utilizando a `Select TOP` cláusula.

```sql
SELECT TOP (5)
FROM DIGITALTWINS
WHERE property = 42
```

### <a name="query-by-model"></a>Consulta por modelo

O `IS_OF_MODEL` operador pode ser utilizado para filtrar com base no [modelo](concepts-models.md)do gémeo. Suporta a herança e tem várias opções de sobrecarga.

A utilização mais simples `IS_OF_MODEL` requer apenas um `twinTypeName` parâmetro: `IS_OF_MODEL(twinTypeName)` .
Aqui está um exemplo de consulta que passa um valor neste parâmetro:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:sample:thing;1')
```

Para especificar uma coleção dupla para procurar quando há mais de uma (como quando uma `JOIN` é usada), adicione o `twinCollection` parâmetro: `IS_OF_MODEL(twinCollection, twinTypeName)` .
Aqui está um exemplo de consulta que acrescenta um valor para este parâmetro:

```sql
SELECT * FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:sample:thing;1')
```

Para fazer uma correspondência exata, adicione o `exact` parâmetro: `IS_OF_MODEL(twinTypeName, exact)` .
Aqui está um exemplo de consulta que acrescenta um valor para este parâmetro:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:sample:thing;1', exact)
```

Também pode passar os três argumentos juntos: `IS_OF_MODEL(twinCollection, twinTypeName, exact)` .
Aqui está um exemplo de consulta especificando um valor para os três parâmetros:

```sql
SELECT ROOM FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:sample:thing;1', exact)
```

### <a name="query-based-on-relationships"></a>Consulta baseada em relacionamentos

Ao consultar com base nas relações dos gémeos digitais, a linguagem de consulta Azure Digital Twins tem uma sintaxe especial.

As relações são puxadas para o âmbito de consulta na `FROM` cláusula. Uma distinção importante das línguas "clássicas" do tipo SQL é que cada expressão nesta `FROM` cláusula não é uma tabela; pelo contrário, a cláusula expressa uma relação transversal `FROM` transversal, e é escrita com uma versão Azure Digital Twins de `JOIN` . 

Recorde-se que com as capacidades do [modelo](concepts-models.md) Azure Digital Twins, as relações não existem independentemente dos gémeos. Isto significa que a linguagem de consulta Azure Digital Twins `JOIN` é um pouco diferente da SQL geral, uma vez que as `JOIN` relações aqui não podem ser consultadas de forma independente e devem ser ligadas a um gémeo.
Para incorporar esta diferença, a palavra-chave `RELATED` é usada na cláusula para `JOIN` referenciar o conjunto de relacionamentos de um gémeo. 

A secção seguinte dá vários exemplos do que isto parece.

> [!TIP]
> Conceptualmente, esta funcionalidade imita a funcionalidade centrada no documento do CosmosDB, onde `JOIN` pode ser realizada em objetos infantis dentro de um documento. CosmosDB usa a `IN` palavra-chave para indicar que se `JOIN` destina a iterar sobre elementos de matriz dentro do documento de contexto atual.

#### <a name="relationship-based-query-examples"></a>Exemplos de consulta baseados em relacionamentos

Para obter um conjunto de dados que inclua relacionamentos, use uma única `FROM` declaração seguida de `JOIN` declarações N, onde as `JOIN` declarações expressam relações sobre o resultado de um `FROM` anterior ou `JOIN` declaração.

Aqui está uma consulta baseada em relacionamentos. Este código seleciona todos os gémeos digitais com uma propriedade de *ID* de 'ABC', e todos os gémeos digitais relacionados com estes gémeos digitais através de uma relação *contenha.* 

```sql
SELECT T, CT
FROM DIGITALTWINS T
JOIN CT RELATED T.contains
WHERE T.$dtId = 'ABC' 
```

>[!NOTE] 
> O desenvolvedor não precisa de correlacionar isto `JOIN` com um valor chave na cláusula `WHERE` (ou especificar um valor chave em linha com a `JOIN` definição). Esta correlação é calculada automaticamente pelo sistema, uma vez que as propriedades da própria relação identificam a entidade-alvo.

#### <a name="query-the-properties-of-a-relationship"></a>Consultar as propriedades de uma relação

Da mesma forma que os gémeos digitais têm propriedades descritas via DTDL, as relações também podem ter propriedades. A linguagem de consulta Azure Digital Twins permite filtrar e projeção de relacionamentos, atribuindo um pseudónimo à relação dentro da `JOIN` cláusula. 

Como exemplo, considere uma relação *servicedBy* que tenha uma propriedade *reportada deCondição.* Na consulta abaixo, esta relação é dada um pseudónimo de 'R' a fim de referenciar a sua propriedade.

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC' 
AND R.reportedCondition = 'clean'
```

No exemplo acima, note como *a comunicaçãoCondition* é uma propriedade da própria relação *de ServiçodBy* (NÃO de algum gémeo digital que tem uma relação *de ServiçodBy).*

### <a name="query-with-multiple-joins"></a>Consulta com vários JOINs

Atualmente em pré-visualização, até cinco `JOIN` s são suportados numa única consulta. Isto permite-lhe percorrer vários níveis de relacionamentos ao mesmo tempo.

Aqui está um exemplo de uma consulta multi-junção, que recebe todas as lâmpadas contidas nos painéis de luz nas salas 1 e 2.

```sql
SELECT LightBulb 
FROM DIGITALTWINS Room 
JOIN LightPanel RELATED Room.contains 
JOIN LightBulb RELATED LightPanel.contains 
WHERE IS_OF_MODEL(LightPanel, ‘dtmi:contoso:com:lightpanel;1’) 
AND IS_OF_MODEL(LightBulb, ‘dtmi:contoso:com:lightbulb ;1’) 
AND Room.$dtId IN [‘room1’, ‘room2’] 
```

## <a name="run-queries-with-an-api-call"></a>Executar consultas com uma chamada de API

Uma vez decidido uma cadeia de consulta, executa-a fazendo uma chamada para a **API de Consulta**.
O seguinte código snippet ilustra esta chamada da aplicação do cliente:

```csharp
var client = new AzureDigitalTwinsAPIClient(<your-credentials>);
client.BaseUri = new Uri(<your-Azure-Digital-Twins-instance-URL>);

QuerySpecification spec = new QuerySpecification("SELECT * FROM digitaltwins");
QueryResult result = await client.Query.QueryTwinsAsync(spec);
```

Esta consulta de retorna de chamada resulta na forma de um objeto QueryResult. 

Consulta chama de suporte de paging. Aqui está um exemplo completo com manipulação de erros e paging:

```csharp
string query = "SELECT * FROM digitaltwins";
try
{
    AsyncPageable<string> qresult = client.QueryAsync(query);
    await foreach (string item in qresult) 
    {
        // Do something with each result
    }
}
catch (RequestFailedException e)
{
    Log.Error($"Error {e.Status}: {e.Message}");
    return null;
}
```

## <a name="query-limitations"></a>Limitações de consulta

Pode haver um atraso de até 10 segundos antes que as alterações no seu caso sejam refletidas em consultas. Por exemplo, se completar uma operação como criar ou eliminar gémeos com a API DigitalTwins, o resultado pode não ser imediatamente refletido nos pedidos da API de Consulta. Esperar por um curto período deve ser suficiente para ser resolvido.

Existem limitações adicionais na utilização `JOIN` durante a pré-visualização.
* Não há subqueries suportados dentro da `FROM` declaração.
* `OUTER JOIN` a semântica não é suportada, o que significa que se a relação tem uma classificação de zero, então toda a "linha" é eliminada do conjunto de resultados de saída.
* Durante a pré-visualização, a profundidade transversal do gráfico é limitada a cinco `JOIN` níveis por consulta.
* A fonte de `JOIN` operações é restrita: a consulta deve declarar os gémeos onde a consulta começa.

## <a name="query-best-practices"></a>Melhores práticas de consulta

Abaixo estão algumas dicas para consulta com Azure Digital Twins.

* Considere o padrão de consulta durante a fase de conceção do modelo. Tente garantir que as relações que precisam de ser respondidas numa única consulta são modeladas como uma relação de nível único.
* Desenhe propriedades de forma a evitar grandes conjuntos de resultados do graf traversal.
* Você pode reduzir significativamente o número de consultas que você precisa construindo uma variedade de gémeos e consultando com o `IN` operador. Por exemplo, considere um cenário em que *os edifícios* contenham *Pisos* e Pisos contenham *Quartos.* *Rooms* Para procurar quartos dentro de um edifício que são quentes, você pode:

    1. Encontre pisos no edifício com base na `contains` relação
        ```sql
        SELECT Floor
        FROM DIGITALTWINS Building
        JOIN Floor RELATED Building.contains
        WHERE Building.$dtId = @buildingId
        ``` 
    2. Para encontrar quartos, em vez de considerar os pisos um a um e executar uma `JOIN` consulta para encontrar os quartos para cada um, você pode consultar com uma coleção dos pisos do edifício (chamado *Floor* na consulta abaixo).

        Na aplicação do cliente:
        ```csharp
        var floors = "['floor1','floor2', ..'floorn']"; 
        ```
        Em consulta:
        ```sql
        SELECT Room
        FROM DIGITALTWINS Floor
        JOIN Room RELATED Floor.contains
        WHERE Floor.$dtId IN ['floor1','floor2', ..'floorn']
        AND Room. Temperature > 72
        AND IS_OF_MODEL(Room, 'dtmi:com:contoso:Room;1')
        ```
* Os nomes e valores dos imóveis são sensíveis ao caso, por isso tenha cuidado para usar os nomes exatos definidos nos modelos. Se os nomes dos imóveis estiverem mal escritos ou mal arquivados, o conjunto de resultados está vazio sem erros devolvidos.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as [APIs e SDKs das Gémeas Digitais Azure,](how-to-use-apis-sdks.md)incluindo a API de Consulta, que é usada para executar as consultas deste artigo.
