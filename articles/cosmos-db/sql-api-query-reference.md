---
title: Sintaxe de linguagem SQL no Azure Cosmos DB
description: Este artigo explica a sintaxe de linguagem de consulta SQL utilizada no Azure Cosmos DB, diferentes operadores e palavras-chave disponíveis neste idioma.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: f326c8608f92cc974a9decad3b010888c358c667
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56818532"
---
# <a name="sql-language-reference-for-azure-cosmos-db"></a>Referência de linguagem SQL para o Azure Cosmos DB 

Azure Cosmos DB suporta consultas de documentos através de uma consulta de SQL (Structured Query Language), como gramática através de documentos JSON hierárquicos sem a necessidade de esquema explícito ou criação de índices secundários. Este artigo fornece documentação para a sintaxe de linguagem de consulta SQL, que é compatível com contas da API de SQL. Para obter instruções de consultas SQL de exemplo, consulte [consultas SQL no Cosmos DB](how-to-sql-query.md).  
  
Visite o [recreio de consultas](https://www.documentdb.com/sql/demo) onde pode experimentar o Cosmos DB e executar consultas SQL no nosso conjunto de dados.  
  
## <a name="select-query"></a>Consulta SELECT  
Cada consulta é composta por uma cláusula SELECT e FROM opcional e cláusulas WHERE por padrões ANSI SQL. Normalmente, para cada consulta, a origem na cláusula FROM é enumerada. Em seguida, o filtro na cláusula WHERE é aplicado na origem para obter um subconjunto de documentos JSON. Por fim, a cláusula SELECT é utilizada para os valores JSON de pedidos na lista de seleção do projeto. As convenções usadas para descrever as instruções SELECT são apresentadas na secção de convenções de sintaxe. Para obter exemplos, consulte [exemplos de consulta SELECT](how-to-sql-query.md#SelectClause)
  
**Sintaxe**  
  
```sql
<select_query> ::=  
SELECT <select_specification>   
    [ FROM <from_specification>]   
    [ WHERE <filter_condition> ]  
    [ ORDER BY <sort_specification> ]  
```  
  
 **Observações**  
  
 Consulte o seguinte secções para obter detalhes sobre cada cláusula:  
  
-   [Cláusula SELECT](#bk_select_query)    
-   [Cláusula FROM](#bk_from_clause)    
-   [Cláusula WHERE](#bk_where_clause)    
-   [Cláusula ORDER BY](#bk_orderby_clause)  
  
As cláusulas na instrução SELECT tem de ser ordenadas conforme mostrado acima. Qualquer uma das cláusulas opcionais pode ser omitida. Mas quando cláusulas opcionais são usadas, eles devem aparecer na ordem correta.  
  
### <a name="logical-processing-order-of-the-select-statement"></a>Ordem de processamento de mensagens em fila lógica da instrução SELECT  
  
A ordem na qual são processadas cláusulas é:  

1.  [Cláusula FROM](#bk_from_clause)  
2.  [Cláusula WHERE](#bk_where_clause)  
3.  [Cláusula ORDER BY](#bk_orderby_clause)  
4.  [Cláusula SELECT](#bk_select_query)  

Tenha em atenção que isto é diferente da ordem em que aparecem na sintaxe. A ordenação é, de modo que todos os símbolos novo introduzidos por uma cláusula processada estão visíveis e podem ser utilizados nas cláusulas processadas mais tarde. Por exemplo, aliases declarados numa cláusula FROM estão acessíveis no WHERE e cláusulas SELECT.  

### <a name="whitespace-characters-and-comments"></a>Carateres de espaço em branco e seus comentários  

Todos os caracteres de espaço em branco que não fazem parte de uma cadeia de caracteres com aspas simples ou quoted identificador não fazem parte da gramática de idioma e serão ignorados durante a análise.  

A linguagem de consulta suporta comentários de estilo de T-SQL, como  

-   Instrução SQL `-- comment text [newline]`  

Enquanto os carateres de espaço em branco e os comentários não tem qualquer significado na gramática, deve ser utilizados para separar os tokens. Por exemplo: `-1e5` é um pouco de token, número único`: – 1 e5` é seguido de um token de subtração pelo número 1 e o identificador e5.  

##  <a name="bk_select_query"></a> Cláusula SELECT  
As cláusulas na instrução SELECT tem de ser ordenadas conforme mostrado acima. Qualquer uma das cláusulas opcionais pode ser omitida. Mas quando cláusulas opcionais são usadas, eles devem aparecer na ordem correta. Para obter exemplos, consulte [exemplos de consulta SELECT](how-to-sql-query.md#SelectClause).

**Sintaxe**  

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | <object_property_list>   
      | VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
 **Argumentos**  
  
- `<select_specification>`  

  Propriedades ou valor a ser selecionado para o conjunto de resultados.  
  
- `'*'`  

  Especifica que o valor deve ser obtido sem fazer quaisquer alterações. Especificamente, se o valor processado é um objeto, serão possível obter todas as propriedades.  
  
- `<object_property_list>`  
  
  Especifica a lista de propriedades a serem obtidas. Cada valor retornado será um objeto com as propriedades especificadas.  
  
- `VALUE`  

  Especifica que o valor JSON deve ser obtido em vez do objeto JSON completo. Isso, ao contrário `<property_list>` não envolve o valor previsto num objeto.  
  
- `<scalar_expression>`  

  Expressão que representa o valor a ser calculada. Ver [expressões escalares](#bk_scalar_expressions) secção para obter detalhes.  
  
**Observações**  
  
O `SELECT *` sintaxe só é válida se a cláusula FROM tem declarado exatamente um alias. `SELECT *` Fornece uma projeção de identidade, o que pode ser útil se não projeção é necessária. SELECIONE * só é válido se for especificado cláusula FROM e introduziu apenas uma única origem de entrada.  
  
Ambos `SELECT <select_list>` e `SELECT *` são "açúcar sintático" e pode ser expresso como alternativa ao utilizar as instruções SELECT simples, conforme mostrado abaixo.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   é equivalente a:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   é equivalente a:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
**Consulte também**  
  
[Expressões escalares](#bk_scalar_expressions)  
[Cláusula SELECT](#bk_select_query)  
  
##  <a name="bk_from_clause"></a> Cláusula FROM  
Especifica a origem ou associado ao origens. A cláusula FROM é opcional, a menos que a origem é filtrada ou prevista mais tarde na consulta. O objetivo essa cláusula é especificar a origem de dados no qual a consulta deve operar. Normalmente o contentor inteiro é a origem, mas um pode especificar um subconjunto do contentor em vez disso. Se essa cláusula não for especificada, as outras cláusulas ainda serão executadas como se a cláusula FROM fornecido um único documento. Para obter exemplos, consulte [dos exemplos de cláusula](how-to-sql-query.md#FromClause)
  
**Sintaxe**  
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=   
        ROOT   
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
**Argumentos**  
  
- `<from_source>`  
  
  Especifica uma origem de dados, com ou sem um alias. Se não for especificado o alias, ele irá ser inferido a partir do `<container_expression>` com os seguintes regras:  
  
  -  Se a expressão for um container_name, container_name será utilizado como um alias.  
  
  -  Se a expressão for `<container_expression>`, property_name, em seguida, property_name será utilizado como um alias. Se a expressão for um container_name, container_name será utilizado como um alias.  
  
- AS `input_alias`  
  
  Especifica que o `input_alias` é um conjunto de valores devolvidos pela expressão de contentor subjacente.  
 
- `input_alias` ÍNDIA  
  
  Especifica que o `input_alias` deve representar o conjunto de valores obtidos com a iteração sobre todos os elementos de matriz de cada matriz devolvida pela expressão de contentor subjacente. Qualquer valor devolvido pela expressão subjacente do contentor que não seja uma matriz é ignorada.  
  
- `<container_expression>`  
  
  Especifica a expressão de contentor a ser usada para recuperar os documentos.  
  
- `ROOT`  
  
  Especifica que esse documento deve ser obtido da predefinição, o contentor atualmente ligada.  
  
- `container_name`  
  
  Especifica que esse documento deve ser obtido a partir do contentor fornecido. O nome do contentor tem de corresponder ao nome do contentor ligadas atualmente ao.  
  
- `input_alias`  
  
  Especifica que esse documento deve ser obtido a partir de outra origem definida pelo alias fornecido.  
  
- `<container_expression> '.' property_`  
  
  Especifica esse documento deve ser obtido ao aceder a `property_name` especificada de propriedade ou array_index elemento de matriz para todos os documentos obtidos através de expressão de contentor.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Especifica esse documento deve ser obtido ao aceder a `property_name` especificada de propriedade ou array_index elemento de matriz para todos os documentos obtidos através de expressão de contentor.  
  
**Observações**  
  
Todos os aliases fornecido ou inferido no `<from_source>(`s) tem de ser exclusivo. A sintaxe `<container_expression>.`property_name é o mesmo que `<container_expression>' ['"property_name"']'`. No entanto, a sintaxe esse último pode ser utilizada se um nome de propriedade contém um caráter não identificador.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>manipulação em falta as propriedades, em falta elementos de matriz e os valores não definidos
  
Se uma expressão de contentor acessa as propriedades ou elementos de matriz e que valor não existir, esse valor será ignorado e não processado ainda mais.  
  
### <a name="container-expression-context-scoping"></a>O controlo de âmbito do contentor expressão contexto  
  
Uma expressão de contentor pode ser no âmbito do contentor ou no âmbito do documento:  
  
-   Uma expressão é o contentor no âmbito, se a origem subjacente da expressão de contentor é a raiz ou `container_name`. Tal uma expressão representa um conjunto de documentos obtidas diretamente a partir do contentor e não é dependente do processamento de outras expressões de contentor.  
  
-   Uma expressão é o documento no âmbito, se a origem subjacente da expressão de contentor é `input_alias` introduzida anteriormente a consulta. Tal uma expressão representa um conjunto de documentos obtido ao avaliar a expressão de contentor no âmbito de cada documento que pertencem ao conjunto associado ao contentor de um alias.  O conjunto resultante será uma União dos conjuntos de obteve ao avaliar a expressão de contentor para cada um dos documentos no conjunto de subjacente.  
  
### <a name="joins"></a>Associações 
  
Na versão atual, o Cosmos DB suporta associações internas. Capacidades de associação adicionais serão disponibilizadas. 

Associações internas resultam num produto cruzado completo dos conjuntos de participar na União. O resultado de uma junção N-vias é um conjunto de cadeias de identificação de elemento de N, onde cada valor na cadeia de identificação é associado o alias definido a participar na União e pode ser acedido ao consultar esse alias em outras cláusulas. Para obter exemplos, consulte [exemplos de palavra-chave de JUNÇÃO](how-to-sql-query.md#Joins)
  
A avaliação da associação depende do escopo de contexto dos conjuntos de participantes:  
  
-  Uma associação entre um conjunto de contentor e no âmbito do contentor definir que B, resulta num produto cruzado de todos os elementos em conjuntos de A e B.
  
-   Uma associação entre o conjunto A e no âmbito do documento conjunto B, resulta numa união de todos os conjuntos de obteve através da avaliação no âmbito do documento conjunto B para cada documento do conjunto A.  
  
 Na versão atual, um máximo de uma expressão no âmbito do contentor é suportado pelo processador de consultas.  
  
### <a name="examples-of-joins"></a>Exemplos de associações  
  
Vejamos o seguinte cláusula FROM: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Permitir que cada origem de definir `input_alias1, input_alias2, …, input_aliasN`. Essa cláusula FROM devolve um conjunto de tuplas de N (cadeia de identificação com valores de N). Cada tupla tem valores produzidos por fazendo a iteração de todos os aliases de contentor através de seus respectivos conjuntos.  
  
**Exemplo 1** -2 origens  
  
- Permitir que `<from_source1>` ser contentor no âmbito e representam o conjunto {A, B, C}.  
  
- Permitir que `<from_source2>` ser documentos no âmbito referenciar input_alias1 e representam conjuntos:  
  
    {1, 2} para `input_alias1 = A,`  
  
    {3} para `input_alias1 = B,`  
  
    {4, 5} para `input_alias1 = C,`  
  
- A cláusula FROM `<from_source1> JOIN <from_source2>` resultará em cadeias de identificação seguintes:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Exemplo 2** -3 origens  
  
- Permitir que `<from_source1>` ser contentor no âmbito e representam o conjunto {A, B, C}.  
  
- Permitem `<from_source2>` ser no âmbito do documento referenciar `input_alias1` e representam conjuntos:  
  
    {1, 2} para `input_alias1 = A,`  
  
    {3} para `input_alias1 = B,`  
  
    {4, 5} para `input_alias1 = C,`  
  
- Permitem `<from_source3>` ser no âmbito do documento referenciar `input_alias2` e representam conjuntos:  
  
    {100, 200} para `input_alias2 = 1,`  
  
    {300} para `input_alias2 = 3,`  
  
- A cláusula FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` resultará em cadeias de identificação seguintes:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > Falta de cadeias de identificação de outros valores de `input_alias1`, `input_alias2`, para o qual o `<from_source3>` não devolveu quaisquer valores.  
  
**Exemplo 3** -3 origens  
  
- Permitir que < from_source1 > ser no âmbito do contentor e representam conjunto {A, B, C}.  
  
- Permitir que `<from_source1>` ser contentor no âmbito e representam o conjunto {A, B, C}.  
  
- Permitir que < from_source2 > ser input_alias1 de referência no âmbito do documento e representam conjuntos:  
  
    {1, 2} para `input_alias1 = A,`  
  
    {3} para `input_alias1 = B,`  
  
    {4, 5} para `input_alias1 = C,`  
  
- Permitem `<from_source3>` confinados `input_alias1` e representam conjuntos:  
  
    {100, 200} para `input_alias2 = A,`  
  
    {300} para `input_alias2 = C,`  
  
- A cláusula FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` resultará em cadeias de identificação seguintes:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200),  (C, 4, 300) ,  (C, 5, 300)  
  
  > [!NOTE]
  > Isso resultou em produto cruzado entre `<from_source2>` e `<from_source3>` uma vez que ambos estão no âmbito da mesma `<from_source1>`.  Isso resultou em 4 (2 x 2) as tuplas ter valor A, as tuplas 0 ter valor B (1 x 0) e 2 (2 x 1) as tuplas ter valor C.  
  
**Consulte também**  
  
 [Cláusula SELECT](#bk_select_query)  
  
##  <a name="bk_where_clause"></a> Cláusula WHERE  
 Especifica a condição de pesquisa para os documentos devolvidos pela consulta. Para obter exemplos, consulte [exemplos de cláusula WHERE](how-to-sql-query.md#WhereClause)
  
 **Sintaxe**  
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
 **Argumentos**  
  
-   `<filter_condition>`  
  
     Especifica as condições para ser atendidas para que os documentos a serem retornados.  
  
-   `<scalar_expression>`  
  
     Expressão que representa o valor a ser calculada. Consulte a [expressões escalares](#bk_scalar_expressions) secção para obter detalhes.  
  
 **Observações**  
  
 Para que o documento a ser devolvida uma expressão especificada como filtro de condição deve ser avaliado como true. Apenas o valor booleano true satisfaça a condição, qualquer outro valor: indefinido, nulo, FALSO, número, matriz ou objeto não satisfaçam a condição.  
  
##  <a name="bk_orderby_clause"></a> Cláusula ORDER BY  
 Especifica a ordem de classificação para obter os resultados devolvidos pela consulta. Para obter exemplos, consulte [exemplos de cláusula ORDER BY](how-to-sql-query.md#OrderByClause)
  
 **Sintaxe**  
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= <scalar_expression> [ASC | DESC]  
  
```  
  
 **Argumentos**  
  
-   `<sort_specification>`  
  
     Especifica uma expressão no qual ordenar o conjunto de resultados de consulta ou de propriedade. Uma coluna de ordenação pode ser especificada como um alias de coluna ou nome.  
  
     Podem ser especificadas várias colunas de ordenação. Os nomes das colunas tem de ser exclusivos. A seqüência das colunas na cláusula ORDER BY ordenação define a organização do conjunto de resultados classificados. Ou seja, o conjunto de resultados é ordenado pela primeira propriedade e, em seguida, essa lista ordenada é ordenada pela propriedade segundo e assim por diante.  
  
     Os nomes de coluna referenciados na cláusula ORDER BY tem de corresponder a uma coluna na lista de seleção ou a uma coluna definida numa tabela especificada na cláusula FROM sem qualquer ambigüidade.  
  
-   `<sort_expression>`  
  
     Especifica uma única propriedade ou uma expressão no qual ordenar o conjunto de resultados de consulta.  
  
-   `<scalar_expression>`  
  
     Consulte a [expressões escalares](#bk_scalar_expressions) secção para obter detalhes.  
  
-   `ASC | DESC`  
  
     Especifica que os valores na coluna especificada devem ser classificados em ordem ascendente ou descendente. ASC ordena a partir do valor mais baixo valor mais alto. DESC ordena a partir do valor mais alto valor mais baixo. ASC é a ordem de classificação padrão. Valores nulos são tratados como os valores possíveis mais baixos.  
  
 **Observações**  
  
 Embora a gramática de consulta oferece suporte a vários ordem por propriedades, o tempo de execução de consulta do Cosmos DB suporta a classificação apenas em relação a uma única propriedade e somente nos nomes de propriedade (não contra as propriedades calculadas). Classificação também requer que a política de indexação inclui um índice do intervalo para a propriedade e o tipo especificado, com a precisão máxima. Consulte a documentação de política de indexação para obter mais detalhes.  
  
##  <a name="bk_scalar_expressions"></a> Expressões escalares  
 Uma expressão escalar é uma combinação de símbolos e operadores que podem ser avaliados como para obter um valor único. Expressões simples podem ser constantes, referências de propriedade, referências de elemento de matriz, referências de alias ou chamadas de função. Expressões simples podem ser combinadas em expressões complexas usando operadores. Para obter exemplos, consulte [exemplos de expressões escalares](how-to-sql-query.md#scalar-expressions)
  
 Para obter detalhes sobre os valores que pode ter expressão escalar, consulte [constantes](#bk_constants) secção.  
  
 **Sintaxe**  
  
```sql  
<scalar_expression> ::=  
       <constant>   
     | input_alias   
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>   
     | <create_array_expression>  
     | (<scalar_expression>)   
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```  
  
 **Argumentos**  
  
-   `<constant>`  
  
     Representa um valor constante. Ver [constantes](#bk_constants) secção para obter detalhes.  
  
-   `input_alias`  
  
     Representa um valor definido pela `input_alias` introduzida no `FROM` cláusula.  
    Este valor é garantido que não seja **indefinido** –**indefinido** valores na entrada são ignoradas.  
  
-   `<scalar_expression>.property_name`  
  
     Representa um valor da propriedade de um objeto. Se a propriedade não existe ou propriedade é referenciada num valor que não é um objeto, em seguida, a expressão avalia **indefinido** valor.  
  
-   `<scalar_expression>'['"property_name"|array_index']'`  
  
     Representa um valor da propriedade com o nome `property_name` ou o elemento de matriz com índice `array_index` de uma matriz/objeto. Se o índice da matriz/propriedade não existe ou o índice da propriedade/matriz é referenciado num valor que não é uma matriz/objeto, em seguida, a expressão é avaliada como valor indefinido.  
  
-   `unary_operator <scalar_expression>`  
  
     Representa um operador que é aplicado a um valor único. Ver [operadores](#bk_operators) secção para obter detalhes.  
  
-   `<scalar_expression> binary_operator <scalar_expression>`  
  
     Representa um operador que é aplicado aos dois valores. Ver [operadores](#bk_operators) secção para obter detalhes.  
  
-   `<scalar_function_expression>`  
  
     Representa um valor definido por um resultado de uma chamada de função.  
  
-   `udf_scalar_function`  
  
     Nome da função escalar definida pelo utilizador.  
  
-   `builtin_scalar_function`  
  
     Nome da função escalar incorporada.  
  
-   `<create_object_expression>`  
  
     Representa um valor de obteve ao criar um novo objeto com propriedades especificadas e os respetivos valores.  
  
-   `<create_array_expression>`  
  
     Representa um valor de obteve ao criar uma nova matriz com os valores especificados como elementos  
  
-   `parameter_name`  
  
     Representa um valor do nome do parâmetro especificado. Os nomes dos parâmetros têm de ter um único \@ como o primeiro caráter.  
  
 **Observações**  
  
 Ao chamar uma função escalar incorporada ou definidas pelo utilizador, todos os argumentos devem ser definidos. Se qualquer um dos argumentos não está definido, a função não será chamada e o resultado será indefinido.  
  
 Ao criar um objeto, qualquer propriedade que é atribuída o valor indefinido será ignorada e não incluída no objeto criado.  
  
 Quando a criação de uma matriz, qualquer valor de elemento que é atribuído **indefinido** valor será ignorado e não incluído no objeto criado. Isso fará com que o próximo elemento definido que ocupará seu lugar, de forma que a matriz criada não irá ter ignorado índices.  
  
##  <a name="bk_operators"></a> Operadores  
 Esta secção descreve os operadores suportados. Cada operador pode ser atribuída a exatamente uma categoria.  
  
 Ver **categorias de operador** a tabela abaixo, para obter detalhes sobre a manipulação de **indefinido** valores, requisitos de tipo de valores de entrada e tratamento dos valores com tipos não correspondentes.  
  
 **Categorias de operador:**  
  
|**Categoria**|**Detalhes**|  
|-|-|  
|**Operações aritméticas**|Operador espera input(s) ser número (s). Saída também é um número. Se qualquer uma das entradas estiver **indefinido** ou o tipo que não seja o número, em seguida, o resultado é **indefinido**.|  
|**bit a bit**|Operador espera input(s) como inteiro de 32 bits assinado número (s). Saída também é inteiro de 32 bits assinado número.<br /><br /> Qualquer valor não inteiro será arredondado. Um valor positivo será arredondado para baixo, negativos valores arredondados.<br /><br /> Qualquer valor que está fora do intervalo de número inteiro de 32 bits será convertido, efetuando os últimos 32-bits da notação de complemento do suas duas.<br /><br /> Se qualquer uma das entradas estiver **indefinido** ou escreva outro número, em seguida, o resultado é **indefinido**.<br /><br /> **Nota:** O comportamento acima é compatível com o comportamento do operador bit a bit de JavaScript.|  
|**logical**|Operador espera input(s) ser Boolean(s). Saída também é um booleano.<br />Se qualquer uma das entradas estiver **indefinido** ou escreva senão Boolean, em seguida, o resultado será **indefinido**.|  
|**comparison**|Operador espera input(s) para ter o mesmo tipo e não será definida. O resultado é um booleano.<br /><br /> Se qualquer uma das entradas estiver **indefinido** ou as entradas têm diferentes tipos, em seguida, o resultado é **indefinido**.<br /><br /> Ver **ordenação de valores para comparação** tabela para ordenação detalhes de valor.|  
|**string**|Operador espera input(s) ser String(s). Saída também é uma cadeia de caracteres.<br />Se qualquer uma das entradas estiver **indefinido** ou escreva senão a cadeia de caracteres, em seguida, o resultado é **indefinido**.|  
  
 **Operadores unários:**  
  
|**Nome**|**Operador**|**Detalhes**|  
|-|-|-|  
|**Operações aritméticas**|+<br /><br /> -|Devolve o valor numérico.<br /><br /> Negação bit a bit. Valor de número devolve negadas.|  
|**bit a bit**|~|Complemento dos recursos. Devolve um complemento de um valor numérico.|  
|**Lógica**|**NÃO**|Negação. Devolve negadas valor booleano.|  
  
 **Operadores binários:**  
  
|**Nome**|**Operador**|**Detalhes**|  
|-|-|-|  
|**Operações aritméticas**|+<br /><br /> -<br /><br /> *<br /><br /> /<br /><br /> %|Adição.<br /><br /> Subtração.<br /><br /> Multiplicação.<br /><br /> Divisão.<br /><br /> Modulação.|  
|**bit a bit**|&#124;<br /><br /> &<br /><br /> ^<br /><br /> <<<br /><br /> >><br /><br /> >>>|OR bit a bit.<br /><br /> E bit a bit<br /><br /> XOR bit a bit.<br /><br /> Esquerda Shift.<br /><br /> Shift certo.<br /><br /> Mudança de direito de preenchimento de zero.|  
|**logical**|**E**<br /><br /> **OR**|Conjunção lógica. Devolve **true** se ambos os argumentos forem **true**, devolve **false** caso contrário.<br /><br /> Disjunção lógica. Devolve **true** se quaisquer argumentos forem **true**, devolve **false** caso contrário.|  
|**comparison**|**=**<br /><br /> **!=, <>**<br /><br /> **>**<br /><br /> **>=**<br /><br /> **<**<br /><br /> **<=**<br /><br /> **??**|É igual a. Devolve **true** se argumentos forem iguais, devolve **falso** caso contrário.<br /><br /> Não é igual a. Devolve **true** se argumentos não forem iguais, devolve **falso** caso contrário.<br /><br /> Maior que. Devolve **true** se o primeiro argumento for maior que o segundo, devolver **falso** caso contrário.<br /><br /> Maior que ou igual a. Devolve **true** se o primeiro argumento for maior ou igual a segunda, devolver **falso** caso contrário.<br /><br /> Menor que. Devolve **true** se o primeiro argumento for menor do que o segundo ponto, retorno **falso** caso contrário.<br /><br /> Menor ou igual a. Devolve **true** se o primeiro argumento for menor ou igual a segunda, devolver **falso** caso contrário.<br /><br /> Coalesce. Devolve o segundo argumento, se o primeiro argumento é um **indefinido** valor.|  
|**Cadeia de caracteres**|**&#124;&#124;**|Concatenação. Devolve uma concatenação de ambos os argumentos.|  
  
 **Operadores ternária:**  
  
|Operador Ternário|?|Devolve o segundo argumento, se o primeiro argumento for avaliada como **true**; caso contrário, a devolver o terceiro argumento.|  
|-|-|-|  
  
 **Ordenação de valores de comparação**  
  
|**Tipo**|**Ordem de valores**|  
|-|-|  
|**Não definido**|Não é comparável.|  
|**Nulo**|Único valor: **nulo**|  
|**Número**|Número de real natural.<br /><br /> Valor de infinito negativo é menor do que qualquer outro valor numérico.<br /><br /> Valor de infinito positivo é maior do que qualquer outro valor numérico. **NaN** valor não é comparável. Comparar com **NaN** resultará na **indefinido** valor.|  
|**Cadeia de caracteres**|Ordem lexicográficas.|  
|**Matriz**|Não existem ordenação, mas equitable.|  
|**Objeto**|Não existem ordenação, mas equitable.|  
  
 **Observações**  
  
 No Cosmos DB, os tipos de valores, muitas vezes, não são conhecidos até que eles são obtidos a partir da base de dados. Para oferecer suporte à execução eficiente de consultas, a maioria dos operadores têm requisitos de tipo rígida. Também operadores por si só não efetuar conversões implícitas.  
  
 Isso significa que uma consulta como: SELECIONAR * da raiz r onde r.Age = 21 só irá devolver os documentos com a propriedade idade igual ao número 21. Documentos com a propriedade idade iguais à cadeia de caracteres "21" ou a cadeia de caracteres "0021" não sejam correspondentes, como a expressão "21" = 21 avalia a indefinido. Isso permite um melhor uso de índices, uma vez que a pesquisa de um valor específico (por exemplo, o número de 21) é mais rápida do que procurar um número indefinido de potencial corresponde a (o número de 21 ou cadeias de caracteres "21", "021", "21.0"...). Isso é diferente da forma como o JavaScript avalia os operadores em valores de diferentes tipos.  
  
 **E comparação de igualdade de matrizes e objetos**  
  
 Comparação de valores de matriz ou um objeto usando operadores de intervalo (>, > =, <, < =) irá resultar no não definida como não há não definido no objeto ou matriz de valores de ordem. No entanto usando operadores de igualdade/desigualdade (=,! =, <>) é suportada e valores serão comparado com estruturalmente.  
  
 As matrizes são iguais, se ambas as matrizes de tem o mesmo número de elementos e elementos das posições de correspondência também são iguais. Se a comparação entre qualquer par de resultados de elementos no indefinido, o resultado de comparação de matriz é indefinido.  
  
 Os objetos são iguais se ambos os objetos têm as mesmas propriedades definidas e valores de propriedades de correspondentes também são iguais. Se a comparação entre qualquer par de resultados de valores de propriedade no indefinido, o resultado da comparação de objeto não está definido.  
  
##  <a name="bk_constants"></a> Constantes  
 Uma constante, também conhecido como um literal ou um valor escalar, é um símbolo que representa um valor de dados específicos. O formato de uma constante depende do valor representa o tipo de dados.  
  
 **Tipos de dados escalares suportados:**  
  
|**Tipo**|**Ordem de valores**|  
|-|-|  
|**Não definido**|Único valor: **indefinido**|  
|**Nulo**|Único valor: **nulo**|  
|**valor booleano**|Valores: **false**, **verdadeiro**.|  
|**Número**|Um precisão dupla número de vírgula flutuante, padrão IEEE 754.|  
|**Cadeia de caracteres**|Uma seqüência de caracteres Unicode de zero ou mais. Cadeias de caracteres devem estar entre aspas simples ou duplas.|  
|**Matriz**|Uma seqüência de elementos de zero ou mais. Cada elemento pode ser um valor de qualquer tipo de dados escalares, exceto indefinido.|  
|**Objeto**|Um conjunto fora de ordem de zero ou mais pares de nome/valor. Nome é uma cadeia de caracteres Unicode, o valor pode ser de qualquer tipo de dados escalares, exceto **indefinido**.|  
  
 **Sintaxe**  
  
```sql  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
 **Argumentos**  
  
* `<undefined_constant>; undefined`  
  
  Valor de representa não definida do tipo indefinido.  
  
* `<null_constant>; null`  
  
  Representa **nulo** valor do tipo **nulo**.  
  
* `<boolean_constant>`  
  
  Representa a constante de tipo Booleano.  
  
* `false`  
  
  Representa **false** valor do tipo Booleano.  
  
* `true`  
  
  Representa **true** valor do tipo Booleano.  
  
* `<number_constant>`  
  
  Representa uma constante.  
  
* `decimal_literal`  
  
  Literais decimais são números representados através de notação decimal ou notação científica.  
  
* `hexadecimal_literal`  
  
  Hexadecimal literais são números apresentados com o prefixo "0x" seguido de um ou mais dígitos hexadecimais.  
  
* `<string_constant>`  
  
  Representa uma constante do tipo cadeia.  
  
* `string _literal`  
  
  Literais de cadeia de caracteres são cadeias de caracteres Unicode representadas por uma seqüência de zero ou mais carateres Unicode ou seqüências de escape. Literais de cadeia de caracteres são inseridos entre aspas (apóstrofe: ") ou as aspas duplas (aspas de fecho:").  
  
 São permitidas os seguintes sequências de escape:  
  
|**Sequência de escape**|**Descrição**|**Caráter Unicode**|  
|-|-|-|  
|\\'|apóstrofe (')|U+0027|  
|\\"|aspas (")|U+0022|  
|\\\ |solidus inversa (\\)|U+0B95 + 005C|  
|\\/|solidus (/)|U+0B95 + 002F|  
|\b|RETROCESSO|U+0008|  
|\f|formulário de feed|U+000C|  
|\n|avanço de linha|U+000A|  
|\r|retorno de carro|U+0B95 + 000D|  
|\t|tabulação|U+0009|  
|\uXXXX|Um caráter Unicode definido por 4 dígitos hexadecimais.|U+XXXX|  
  
##  <a name="bk_query_perf_guidelines"></a> Diretrizes de desempenho de consulta  
 Para uma consulta a ser executada com eficiência para um contentor grande, ele deve utilizar filtros que podem ser atendidos por meio de um ou mais índices.  
  
 Os seguintes filtros serão considerados para a pesquisa do índice:  
  
-   Utilize o operador de igualdade (=) com uma expressão de caminho do documento e uma constante.  
  
-   Usar os operadores de intervalo (<, \<=, >, > =) com uma expressão de caminho do documento e constantes de números.  
  
-   Expressão de caminho do documento significa qualquer expressão que identifica um caminho constante nos documentos do contêiner da base de dados referenciada.  
  
 **Expressão de caminho do documento**  
  
 Expressões de caminho do documento são expressões que um caminho de propriedade ou matriz assessores de indexador ao longo de um documento proveniente de documentos do contentor de base de dados. Este caminho pode ser utilizado para identificar a localização de valores referenciada num filtro diretamente dentro dos documentos no contentor de base de dados.  
  
 Para uma expressão ser considerado uma expressão de caminho do documento, deveria:  
  
1.  Referência diretamente a raiz do contentor.  
  
2.  Indexador de matriz de propriedade ou constante de referência de uma expressão de caminho do documento  
  
3.  Um alias, que representa uma expressão de caminho do documento de referência.  
  
     **Convenções de sintaxe**  
  
     A tabela seguinte descreve as convenções usadas para descrever a sintaxe na referência do SQL seguinte.  
  
    |**Convenção**|**Utilizado para**|  
    |-|-|    
    |LETRAS MAIÚSCULAS|Palavras-chave maiúsculas de minúsculas.|  
    |Em minúsculas|Palavras-chave diferencia maiúsculas de minúsculas.|  
    |\<nonterminal>|Nonterminal, definido em separado.|  
    |\<nonterminal >:: =|Definição de sintaxe do nonterminal.|  
    |other_terminal|O terminal (token), descrito detalhadamente nas palavras.|  
    |Identificador|Identificador. Permite apenas caracteres a seguir: caráter de _First-z A-Z 0-9 não pode ser um dígito.|  
    |"string"|Cadeia de caracteres com aspas simples. Permite que qualquer cadeia de caracteres válida. Ver descrição de um string_literal.|  
    |"símbolo"|Símbolo de literal que faz parte da sintaxe.|  
    |&#124;(barra vertical)|Alternativas para itens de sintaxe. Pode utilizar apenas um dos itens especificados.|  
    |[] /(brackets)|Parênteses Retos coloque um ou mais itens opcionais.|  
    |[,... n]|Indica o que item anterior pode ser n repetidas diversas vezes. As ocorrências são separadas por vírgulas.|  
    |[... n]|Indica o que item anterior pode ser n repetidas diversas vezes. As ocorrências são separadas por espaços em branco.|  
  
##  <a name="bk_built_in_functions"></a> Funções incorporadas  
 O cosmos DB fornece muitas funções internas do SQL. As categorias de funções internas são listadas abaixo.  
  
|Função|Descrição|  
|--------------|-----------------|  
|[Funções matemáticas](#bk_mathematical_functions)|As funções matemáticas realizar um cálculo, normalmente com base em valores de entrada que são fornecidos como argumentos e devolvem um valor numérico.|  
|[As funções de verificação de tipo](#bk_type_checking_functions)|As funções de verificação de tipo permitem-lhe verificar o tipo de uma expressão dentro de consultas SQL.|  
|[Funções de cadeia](#bk_string_functions)|As funções de cadeia de caracteres realizar uma operação num valor de entrada de cadeia de caracteres e retornam uma cadeia de caracteres, o valor numérico ou booleano.|  
|[Funções de matriz](#bk_array_functions)|As funções de matriz executam uma operação num valor de entrada de matriz e o valor de matriz, booleano ou numérico de retorno.|  
|[Funções espaciais](#bk_spatial_functions)|As funções geográficos realizar uma operação num valor de entrada do objeto espacial e retornam um valor numérico ou booleano.|  
  
###  <a name="bk_mathematical_functions"></a> Funções matemáticas  
 As funções seguintes realizar um cálculo, normalmente com base em valores de entrada que são fornecidos como argumentos e devolvem um valor numérico.  
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[LIMITE](#bk_ceiling)|  
|[COS](#bk_cos)|[COT](#bk_cot)|[GRAUS](#bk_degrees)|  
|[EXP](#bk_exp)|[FLOOR](#bk_floor)|[LOG](#bk_log)|  
|[LOG10](#bk_log10)|[PI](#bk_pi)|[POWER](#bk_power)|  
|[RADIANOS](#bk_radians)|[ARREDONDAR](#bk_round)|[SIN](#bk_sin)|  
|[SQRT](#bk_sqrt)|[SQUARE](#bk_square)|[INÍCIO DE SESSÃO](#bk_sign)|  
|[TAN](#bk_tan)|[TRUNC](#bk_trunc)||  
  
####  <a name="bk_abs"></a> ABS  
 Devolve o valor absoluto (positivo) da expressão especificada numérico.  
  
 **Sintaxe**  
  
```  
ABS (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra os resultados usando a função ABS em três números diferentes.  
  
```  
SELECT ABS(-1), ABS(0), ABS(1)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{$1: 1, $2: 0, $3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 Devolve o ângulo em radianos, cujo co-seno é a expressão numérica especificada; Também chamado de arco de cosseno.  
  
 **Sintaxe**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve as funções ACOS de -1.  
  
```  
SELECT ACOS(-1)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 Devolve o ângulo em radianos, cujo seno é a expressão numérica especificada. Isso também é chamado o arco de seno.  
  
 **Sintaxe**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve o ASIN de -1.  
  
```  
SELECT ASIN(-1)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 Devolve o ângulo em radianos, cuja tangente é a expressão numérica especificada. Isso também é chamado o arco de tangente.  
  
 **Sintaxe**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve ATAN do valor especificado.  
  
```  
SELECT ATAN(-45.01)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 Devolve o valor principal do arco tangente de y / x, expressado em radianos.  
  
 **Sintaxe**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte calcula ATN2 especificado para x e y componentes.  
  
```  
SELECT ATN2(35.175643, 129.44)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> LIMITE  
 Devolve o menor valor de número inteiro maior que ou igual a, a expressão numérica especificada.  
  
 **Sintaxe**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra numérico positivo, negativo e valores zero com a função de limite.  
  
```  
SELECT CEILING(123.45), CEILING(-123.45), CEILING(0.0)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{$1: 124, $2: -123, $3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 Devolve o cosseno trigonométricos do ângulo especificado, em radianos, a expressão especificada.  
  
 **Sintaxe**  
  
```  
COS(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte calcula COS do ângulo especificado.  
  
```  
SELECT COS(14.78)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 Devolve a co-tangente trigonométricos do ângulo especificado, em radianos, a expressão numérica especificado.  
  
 **Sintaxe**  
  
```  
COT(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte calcula COT do ângulo especificado.  
  
```  
SELECT COT(124.1332)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> GRAUS  
 Devolve o ângulo correspondente em graus para um ângulo especificado em radianos.  
  
 **Sintaxe**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve o número de graus num ângulo de radianos de PI/2.  
  
```  
SELECT DEGREES(PI()/2)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": 90}]  
```  
  
####  <a name="bk_floor"></a> PISO  
 Devolve o maior número inteiro menor ou igual a expressão numérica especificada.  
  
 **Sintaxe**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra numérico positivo, negativo e valores zero com a função de piso.  
  
```  
SELECT FLOOR(123.45), FLOOR(-123.45), FLOOR(0.0)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{$1: 123, $2: -124, $3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 Devolve o valor exponencial da expressão especificada numérico.  
  
 **Sintaxe**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Observações**  
  
 A constante **i** (2.718281...), é a base do logaritmos naturais.  
  
 O expoente de um número é a constante **i** elevado à potência do número. Por exemplo, EXP(1.0) = e ^ 1.0 = 2.71828182845905 e EXP(10) = e ^ 10 = 22026.4657948067.  
  
 Exponencial do logaritmo natural de um número é o número em si: EXP (LOG (n)) = n. E o logaritmo natural de exponencial de um número é o número em si: REGISTO (EXP (n)) = n.  
  
 **Exemplos**  
  
 O exemplo seguinte declara uma variável e devolve o valor exponencial da variável especificada (10).  
  
```  
SELECT EXP(10)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{$1: 22026.465794806718}]  
```  
  
 O exemplo seguinte devolve o valor exponencial de natural logarithm de 20 e o logaritmo natural de exponencial de 20. Uma vez que estas funções são funções inversas entre si, o valor de retorno com o arredondamento para matemática de ponto flutuante em ambos os casos é 20.  
  
```  
SELECT EXP(LOG(20)), LOG(EXP(20))  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{$1: 19.999999999999996, $2: 20}]  
```  
  
####  <a name="bk_log"></a> REGISTO  
 Devolve o logaritmo natural da expressão numérica especificado.  
  
 **Sintaxe**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
-   `base`  
  
     Argumento numérico opcional que define a base para o logaritmo.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Observações**  
  
 Por predefinição, o LOG() devolve o logaritmo natural. Pode alterar a base do logaritmo a outro valor utilizando o parâmetro de base opcional.  
  
 O logaritmo natural é o logaritmo para a base **i**, onde **i** é uma constante irracional aproximadamente igual a 2.718281828.  
  
 O logaritmo natural de exponencial de um número é o número em si: REGISTO (EXP (n)) = n. E exponencial do logaritmo natural de um número é o número em si: EXP (LOG (n)) = n.  
  
 **Exemplos**  
  
 O exemplo seguinte declara uma variável e devolve o valor de logaritmo da variável especificada (10).  
  
```  
SELECT LOG(10)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{$1: 2.3025850929940459}]  
```  
  
 O exemplo seguinte calcula o registo para o expoente de um número.  
  
```  
SELECT EXP(LOG(10))  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{$1: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 Devolve o logaritmo base 10 da expressão especificada numérico.  
  
 **Sintaxe**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Observações**  
  
 As funções LOG10 e POWER inversamente estão relacionadas entre si. Por exemplo, 10 ^ LOG10(n) = n.  
  
 **Exemplos**  
  
 O exemplo seguinte declara uma variável e devolve o valor de LOG10 da variável especificada (100).  
  
```  
SELECT LOG10(100)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{$1: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 Devolve o valor da constante de PI.  
  
 **Sintaxe**  
  
```  
PI ()  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve o valor de PI.  
  
```  
SELECT PI()  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> POWER  
 Devolve o valor da expressão especificada à potência especificada.  
  
 **Sintaxe**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
-   `y`  
  
     É o poder de que elevar `numeric_expression`.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte demonstra a gerar um número à potência de 3 (o cubo do número).  
  
```  
SELECT POWER(2, 3), POWER(2.5, 3)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{$1: 8, $2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> RADIANOS  
 Devolve o radianos quando uma expressão numérica, em graus, que é introduzida.  
  
 **Sintaxe**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo a seguir usa alguns ângulos como entrada e retorna os valores de radian correspondentes.  
  
```  
SELECT RADIANS(-45.01), RADIANS(-181.01), RADIANS(0), RADIANS(0.1472738), RADIANS(197.1099392)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{  
       "$1": -0.7855726963226477,  
       "$2": -3.1592204790349356,  
       "$3": 0,  
       "$4": 0.0025704127119236249,  
       "$5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> ARREDONDAR  
 Devolve um valor numérico, arredondado para o valor de número inteiro mais próximo.  
  
 **Sintaxe**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte arredonda os números positivos e negativos a seguir para o número inteiro mais próximo.  
  
```  
SELECT ROUND(2.4), ROUND(2.6), ROUND(2.5), ROUND(-2.4), ROUND(-2.6)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{$1: 2, $2: 3, $3: 3, $4: -2, $5: -3}]  
```  
  
####  <a name="bk_sign"></a> INÍCIO DE SESSÃO  
 Devolve o positivo (+ 1), zero (0) ou sinal negativo de (-1) da expressão especificada numérico.  
  
 **Sintaxe**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve os valores de início de sessão de números de -2 de 2.  
  
```  
SELECT SIGN(-2), SIGN(-1), SIGN(0), SIGN(1), SIGN(2)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{$1: -1, $2: -1, $3: 0, $4: 1, $5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 Devolve o seno trigonométricos do ângulo especificado, em radianos, a expressão especificada.  
  
 **Sintaxe**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte calcula o PECADO do ângulo especificado.  
  
```  
SELECT SIN(45.175643)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 Devolve a raiz quadrada do valor numérico especificado.  
  
 **Sintaxe**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve as raízes quadradas de números de 1 a 3.  
  
```  
SELECT SQRT(1), SQRT(2.0), SQRT(3)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{$1: 1, $2: 1.4142135623730952, $3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> QUADRADO  
 Devolve o quadrado do valor numérico especificado.  
  
 **Sintaxe**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve os quadrados de números de 1 a 3.  
  
```  
SELECT SQUARE(1), SQUARE(2.0), SQUARE(3)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{$1: 1, $2: 4, $3: 9}]  
```  
  
####  <a name="bk_tan"></a> TAN  
 Devolve a tangente do ângulo especificado, em radianos, a expressão especificada.  
  
 **Sintaxe**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte calcula a tangente do instalador de plataforma (-) / 2.  
  
```  
SELECT TAN(PI()/2);  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> TRUNC  
 Devolve um valor numérico, truncado para o valor de número inteiro mais próximo.  
  
 **Sintaxe**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte trunca os números positivos e negativos a seguir para o valor de número inteiro mais próximo.  
  
```  
SELECT TRUNC(2.4), TRUNC(2.6), TRUNC(2.5), TRUNC(-2.4), TRUNC(-2.6)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{$1: 2, $2: 2, $3: 2, $4: -2, $5: -2}]  
```  
  
###  <a name="bk_type_checking_functions"></a> As funções de verificação de tipo  
 As seguintes funções suportam o tipo de verificação em relação aos valores de entrada e cada uma devolve um valor booleano.  
  
||||  
|-|-|-|  
|[IS_ARRAY](#bk_is_array)|[IS_BOOL](#bk_is_bool)|[IS_DEFINED](#bk_is_defined)|  
|[IS_NULL](#bk_is_null)|[IS_NUMBER](#bk_is_number)|[IS_OBJECT](#bk_is_object)|  
|[IS_PRIMITIVE](#bk_is_primitive)|[IS_STRING](#bk_is_string)||  
  
####  <a name="bk_is_array"></a> IS_ARRAY  
 Devolve um valor Booleano indicando se o tipo da expressão especificada é uma matriz.  
  
 **Sintaxe**  
  
```  
IS_ARRAY(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     É qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão booleana.  
  
 **Exemplos**  
  
 O exemplo seguinte verifica os objetos de JSON booleano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_ARRAY.  
  
```  
SELECT   
 IS_ARRAY(true),   
 IS_ARRAY(1),  
 IS_ARRAY("value"),  
 IS_ARRAY(null),  
 IS_ARRAY({prop: "value"}),   
 IS_ARRAY([1, 2, 3]),  
 IS_ARRAY({prop: "value"}.prop2)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: false, $6: true}]  
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 Devolve um valor Booleano indicando se o tipo da expressão especificada é um booleano.  
  
 **Sintaxe**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     É qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão booleana.  
  
 **Exemplos**  
  
 O exemplo seguinte verifica os objetos de JSON booleano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_BOOL.  
  
```  
SELECT   
    IS_BOOL(true),   
    IS_BOOL(1),  
    IS_BOOL("value"),   
    IS_BOOL(null),  
    IS_BOOL({prop: "value"}),   
    IS_BOOL([1, 2, 3]),  
    IS_BOOL({prop: "value"}.prop2)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{$1: true, $2: false, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 Devolve um valor booleano que indica se a propriedade foi atribuída um valor.  
  
 **Sintaxe**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     É qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão booleana.  
  
 **Exemplos**  
  
 O exemplo seguinte verifica a presença de uma propriedade dentro do documento JSON especificado. O primeiro método retorna true, uma vez que "a" está presente, mas o segundo devolve false, uma vez que "b" está ausente.  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a), IS_DEFINED({ "a" : 5 }.b)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{  
       "$1": true,    
       "$2": false   
   }]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 Devolve um valor Booleano indicando se o tipo da expressão especificada é nulo.  
  
 **Sintaxe**  
  
```  
IS_NULL(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     É qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão booleana.  
  
 **Exemplos**  
  
 O exemplo seguinte verifica os objetos de JSON booleano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_NULL.  
  
```  
SELECT   
    IS_NULL(true),   
    IS_NULL(1),  
    IS_NULL("value"),   
    IS_NULL(null),  
    IS_NULL({prop: "value"}),   
    IS_NULL([1, 2, 3]),  
    IS_NULL({prop: "value"}.prop2)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{$1: false, $2: false, $3: false, $4: true, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 Devolve um valor Booleano indicando se o tipo da expressão especificada é um número.  
  
 **Sintaxe**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     É qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão booleana.  
  
 **Exemplos**  
  
 O exemplo seguinte verifica os objetos de JSON booleano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_NULL.  
  
```  
SELECT   
    IS_NUMBER(true),   
    IS_NUMBER(1),  
    IS_NUMBER("value"),   
    IS_NUMBER(null),  
    IS_NUMBER({prop: "value"}),   
    IS_NUMBER([1, 2, 3]),  
    IS_NUMBER({prop: "value"}.prop2)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{$1: false, $2: true, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT  
 Devolve um valor Booleano indicando se o tipo da expressão especificada é um objeto JSON.  
  
 **Sintaxe**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     É qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão booleana.  
  
 **Exemplos**  
  
 O exemplo seguinte verifica os objetos de JSON booleano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_OBJECT.  
  
```  
SELECT   
    IS_OBJECT(true),   
    IS_OBJECT(1),  
    IS_OBJECT("value"),   
    IS_OBJECT(null),  
    IS_OBJECT({prop: "value"}),   
    IS_OBJECT([1, 2, 3]),  
    IS_OBJECT({prop: "value"}.prop2)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: true, $6: false}]  
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 Devolve um valor Booleano indicando se o tipo da expressão especificada é um primitivo (string, booleano, numéricos ou nulo).  
  
 **Sintaxe**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     É qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão booleana.  
  
 **Exemplos**  
  
 O exemplo seguinte verifica os objetos de JSON booleano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_PRIMITIVE.  
  
```  
SELECT   
           IS_PRIMITIVE(true),   
           IS_PRIMITIVE(1),  
           IS_PRIMITIVE("value"),   
           IS_PRIMITIVE(null),  
           IS_PRIMITIVE({prop: "value"}),   
           IS_PRIMITIVE([1, 2, 3]),  
           IS_PRIMITIVE({prop: "value"}.prop2)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": true, "$2": true, "$3": true, "$4": true, "$5": false, "$6": false, "$7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 Devolve um valor Booleano indicando se o tipo da expressão especificada é uma cadeia de caracteres.  
  
 **Sintaxe**  
  
```  
IS_STRING(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     É qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão booleana.  
  
 **Exemplos**  
  
 O exemplo seguinte verifica os objetos de JSON booleano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_STRING.  
  
```  
SELECT   
       IS_STRING(true),   
       IS_STRING(1),  
       IS_STRING("value"),   
       IS_STRING(null),  
       IS_STRING({prop: "value"}),   
       IS_STRING([1, 2, 3]),  
       IS_STRING({prop: "value"}.prop2)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{$1: false, $2: false, $3: true, $4: false, $5: false, $6: false}]  
```  
  
###  <a name="bk_string_functions"></a> Funções de cadeia  
 As seguintes funções escalares realizar uma operação num valor de entrada de cadeia de caracteres e retornam uma cadeia de caracteres, o valor numérico ou booleano.  
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[CONTÉM](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[À ESQUERDA](#bk_left)|[LENGTH](#bk_length)|  
|[INFERIOR](#bk_lower)|[LTRIM](#bk_ltrim)|[SUBSTITUIR](#bk_replace)|  
|[REPLICAR](#bk_replicate)|[INVERTER](#bk_reverse)|[DIREITA](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[StringToArray](#bk_stringtoarray)|
|[StringToBoolean](#bk_stringtoboolean)|[StringToNull](#bk_stringtonull)|[StringToNumber](#bk_stringtonumber)|
|[StringToObject](#bk_stringtoobject)|[SUBCADEIA](#bk_substring)|[ToString](#bk_tostring)|
|[TRIM](#bk_trim)|[SUPERIOR](#bk_upper)||| 
  
####  <a name="bk_concat"></a> CONCAT  
 Devolve uma cadeia que é o resultado da concatenação de dois ou mais valores de cadeia de caracteres.  
  
 **Sintaxe**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve a cadeia de caracteres concatenada dos valores especificados.  
  
```  
SELECT CONCAT("abc", "def")  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> CONTÉM  
 Retorna um Booleano indicando se a primeira cadeia de expressão contém o segundo.  
  
 **Sintaxe**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão booleana.  
  
 **Exemplos**  
  
 O exemplo a seguir verifica se "abc" contém "ab" e "d".  
  
```  
SELECT CONTAINS("abc", "ab"), CONTAINS("abc", "d")  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": true, "$2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 Retorna um Booleano indicando se a primeira expressão de cadeia termina com a segunda.  
  
 **Sintaxe**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão booleana.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve "abc" termina com "b" e "bc".  
  
```  
SELECT ENDSWITH("abc", "b"), ENDSWITH("abc", "bc")  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 Devolve a posição inicial da primeira ocorrência da segunda cadeia de expressão dentro da primeira expressão de cadeia especificada ou -1 se a cadeia de caracteres não foi encontrada.  
  
 **Sintaxe**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve o índice de várias subseqüências dentro de "abc".  
  
```  
SELECT INDEX_OF("abc", "ab"), INDEX_OF("abc", "b"), INDEX_OF("abc", "c")  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": 0, "$2": 1, "$3": -1}]  
```  
  
####  <a name="bk_left"></a> À ESQUERDA  
 Devolve a parte esquerda de uma cadeia com o número especificado de carateres.  
  
 **Sintaxe**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
-   `num_expr`  
  
     É qualquer expressão numérica válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve a parte esquerda de "abc" para vários valores de comprimento.  
  
```  
SELECT LEFT("abc", 1), LEFT("abc", 2)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": "a", "$2": "ab"}]  
```  
  
####  <a name="bk_length"></a> COMPRIMENTO  
 Devolve o número de carateres da expressão de cadeia especificada.  
  
 **Sintaxe**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve o comprimento de uma cadeia de caracteres.  
  
```  
SELECT LENGTH("abc")  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_lower"></a> INFERIOR  
 Devolve uma expressão de cadeia de caracteres após a conversão de dados de caráter em maiúsculas em minúsculas.  
  
 **Sintaxe**  
  
```  
LOWER(<str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra como utilizar o mais baixo numa consulta.  
  
```  
SELECT LOWER("Abc")  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> LTRIM  
 Devolve uma expressão de cadeia de caracteres depois que ele remove espaços em branco à esquerda.  
  
 **Sintaxe**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra como utilizar LTRIM dentro de uma consulta.  
  
```  
SELECT LTRIM("  abc"), LTRIM("abc"), LTRIM("abc   ")  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": "abc", "$2": "abc", "$3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> SUBSTITUIR  
 Substitui todas as ocorrências de um valor de cadeia especificada com outro valor de cadeia de caracteres.  
  
 **Sintaxe**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra como usar REPLACE numa consulta.  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk")  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLICAR  
 Repete-se um valor de cadeia de caracteres um número de vezes especificado.  
  
 **Sintaxe**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
-   `num_expr`  
  
     É qualquer expressão numérica válida. Se num_expr é negativo ou não infinite, o resultado é indefinido.

  > [!NOTE]
  > O comprimento máximo do resultado é 10.000 carateres, ou seja, (length(str_expr) * num_expr) < = 10 000.
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra como usar a REPLICAR numa consulta.  
  
```  
SELECT REPLICATE("a", 3)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> INVERTER  
 Devolve a ordem inversa de um valor de cadeia de caracteres.  
  
 **Sintaxe**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra como utilizar o INVERSO numa consulta.  
  
```  
SELECT REVERSE("Abc")  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": "cbA"}]  
```  
  
####  <a name="bk_right"></a> DIREITA  
 Devolve a parte direita de uma cadeia de caracteres com o número especificado de carateres.  
  
 **Sintaxe**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
-   `num_expr`  
  
     É qualquer expressão numérica válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve a parte direita de "abc" para vários valores de comprimento.  
  
```  
SELECT RIGHT("abc", 1), RIGHT("abc", 2)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": "c", "$2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> RTRIM  
 Devolve uma expressão de cadeia de caracteres depois que ele remove espaços em branco à direita.  
  
 **Sintaxe**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra como utilizar RTRIM dentro de uma consulta.  
  
```  
SELECT RTRIM("  abc"), RTRIM("abc"), RTRIM("abc   ")  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": "   abc", "$2": "abc", "$3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 Retorna um Booleano indicando se a primeira expressão de cadeia começa com a segunda.  
  
 **Sintaxe**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão booleana.  
  
 **Exemplos**  
  
 O exemplo a seguir verifica se a cadeia de caracteres "abc" começa com "b" e "a".  
  
```  
SELECT STARTSWITH("abc", "b"), STARTSWITH("abc", "a")  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": false, "$2": true}]  
```  

  ####  <a name="bk_stringtoarray"></a> StringToArray  
 Devolve a expressão convertido numa matriz. Se a expressão não pode ser convertida, retornará indefinida.  
  
 **Sintaxe**  
  
```  
StringToArray(<expr>)  
```  
  
 **Argumentos**  
  
-   `expr`  
  
     É qualquer expressão de matriz JSON válido. Tenha em atenção que os valores de cadeia de caracteres devem ser escritas com aspas duplas para ser válida. Para obter detalhes sobre o formato JSON, veja [json.org](https://json.org/)
  
 **Tipos de retorno**  
  
 Devolve uma expressão de matriz ou não definida.  
  
 **Exemplos**  
  
O exemplo seguinte mostra como StringToArray se comporta vários tipos diferentes. 
  
```  
SELECT 
StringToArray('[]'), 
StringToArray("[1,2,3]"),
StringToArray("[\"str\",2,3]"),
IS_ARRAY(StringToArray("[['5','6','7'],['8'],['9']]")), 
IS_ARRAY(StringToArray('[["5","6","7"],["8"],["9"]]')),
StringToArray('[1,2,3, "[4,5,6]",[7,8]]'),
StringToArray("[1,2,3, '[4,5,6]',[7,8]]"),
StringToArray(false), 
StringToArray(undefined),
StringToArray(NaN), 
StringToArray("[")
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": [], "$2": [1,2,3], "$3": ["str",2,3], "$4": false, "$5": true, "$6": [1,2,3,"[4,5,6]",[7,8]]}]
```  

####  <a name="bk_stringtoboolean"></a> StringToBoolean  
 Devolve a expressão convertido para booleano. Se a expressão não pode ser convertida, retornará indefinida.  
  
 **Sintaxe**  
  
```  
StringToBoolean(<expr>)  
```  
  
 **Argumentos**  
  
-   `expr`  
  
     É qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão booleana ou não definida.  
  
 **Exemplos**  
  
O exemplo seguinte mostra como StringToBoolean se comporta vários tipos diferentes. 
  
```  
SELECT 
StringToBoolean("true"), 
StringToBoolean("    false"),
IS_BOOL(StringToBoolean("false")), 
StringToBoolean("null"),
StringToBoolean(undefined),
StringToBoolean(NaN), 
StringToBoolean(false), 
StringToBoolean(true), 
StringToBoolean("TRUE"),
StringToBoolean("False")
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": true, "$2": false, "$3": true}]
```  

####  <a name="bk_stringtonull"></a> StringToNull  
 Devolve a expressão traduzido como null. Se a expressão não pode ser convertida, retornará indefinida.  
  
 **Sintaxe**  
  
```  
StringToNull(<expr>)  
```  
  
 **Argumentos**  
  
-   `expr`  
  
     É qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de nulo ou indefinido.  
  
 **Exemplos**  
  
O exemplo seguinte mostra como StringToNull se comporta vários tipos diferentes. 
  
```  
SELECT 
StringToNull("null"), 
StringToNull("  null "),
IS_NULL(StringToNull("null")), 
StringToNull("true"), 
StringToNull(false), 
StringToNull(undefined),
StringToNull(NaN), 
StringToNull("NULL"),
StringToNull("Null")
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": null, "$2": null, "$3": true}]
```  

####  <a name="bk_stringtonumber"></a> StringToNumber  
 Devolve a expressão traduzida para um número. Se a expressão não pode ser convertida, retornará indefinida.  
  
 **Sintaxe**  
  
```  
StringToNumber(<expr>)  
```  
  
 **Argumentos**  
  
-   `expr`  
  
     É qualquer expressão de número de JSON válido. Números no JSON tem de ser um número inteiro ou um ponto flutuante. Para obter detalhes sobre o formato JSON, veja [json.org](https://json.org/)  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de número ou não definida.  
  
 **Exemplos**  
  
O exemplo seguinte mostra como StringToNumber se comporta vários tipos diferentes. 
  
```  
SELECT 
StringToNumber("1.000000"), 
StringToNumber("3.14"),
IS_NUMBER(StringToNumber("   60   ")), 
StringToNumber("0xF"),
StringToNumber("-1.79769e+308"),
IS_STRING(StringToNumber("2")),
StringToNumber(undefined),
StringToNumber("99     54"), 
StringToNumber("false"), 
StringToNumber(false),
StringToNumber(" "),
StringToNumber(NaN)
```  
  
 Aqui está o conjunto de resultados.  
  
```  
{{"$1": 1, "$2": 3.14, "$3": true, "$5": -1.79769e+308, "$6": false}}
```  

####  <a name="bk_stringtoobject"></a> StringToObject  
 Devolve a expressão traduzida para um objeto. Se a expressão não pode ser convertida, retornará indefinida.  
  
 **Sintaxe**  
  
```  
StringToObject(<expr>)  
```  
  
 **Argumentos**  
  
-   `expr`  
  
     É qualquer expressão de objeto JSON válido. Tenha em atenção que os valores de cadeia de caracteres devem ser escritas com aspas duplas para ser válida. Para obter detalhes sobre o formato JSON, veja [json.org](https://json.org/)  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de objeto ou não definida.  
  
 **Exemplos**  
  
O exemplo seguinte mostra como StringToObject se comporta vários tipos diferentes. 
  
```  
SELECT 
StringToObject("{}"), 
StringToObject('{"a":[1,2,3]}'),
StringToObject("{'a':[1,2,3]}"),
StringToObject("{a:[1,2,3]}"),
IS_OBJECT(StringToObject('{"obj":[{"b":[5,6,7]},{"c":8},{"d":9}]}')), 
IS_OBJECT(StringToObject("{\"obj\":[{\"b\":[5,6,7]},{\"c\":8},{\"d\":9}]}")), 
IS_OBJECT(StringToObject("{'obj':[{'b':[5,6,7]},{'c':8},{'d':9}]}")), 
StringToObject(false), 
StringToObject(undefined),
StringToObject(NaN), 
StringToObject("{")
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": {}, "$2": {"a": [1,2,3]}, "$5": true, "$6": true, "$7": false}]
```  

####  <a name="bk_substring"></a> SUBCADEIA  
 Devolve a parte de uma expressão de cadeia de caracteres a partir da posição caractere especificado baseado em zero e continua ao comprimento especificado, ou ao fim da cadeia de caracteres.  
  
 **Sintaxe**  
  
```  
SUBSTRING(<str_expr>, <num_expr>, <num_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
-   `num_expr`  
  
     É qualquer expressão numérica válida para denotar o caráter de início e de fim.    
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte devolve a subcadeia de "abc" começando em 1 e durante um período de 1 caráter.  
  
```  
SELECT SUBSTRING("abc", 1, 1)  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": "b"}]  
```  
####  <a name="bk_tostring"></a> ToString  
 Devolve uma representação de cadeia de expressão escalar. 
  
 **Sintaxe**  
  
```  
ToString(<expr>)
```  
  
 **Argumentos**  
  
-   `expr`  
  
     É qualquer expressão escalar válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra como ToString se comporta vários tipos diferentes.   
  
```  
SELECT ToString(1.0000), ToString("Hello World"), ToString(NaN), ToString(Infinity),
ToString(IS_STRING(ToString(undefined))), IS_STRING(ToString(0.1234), ToString(false), ToString(undefined))
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": "1", "$2": "Hello World", "$3": "NaN", "$4": "Infinity", "$5": "false", "$6": true, "$7": "false"}]  
```  
 Tendo em conta a seguinte entrada:
```  
{"Products":[{"ProductID":1,"Weight":4,"WeightUnits":"lb"},{"ProductID":2,"Weight":32,"WeightUnits":"kg"},{"ProductID":3,"Weight":400,"WeightUnits":"g"},{"ProductID":4,"Weight":8999,"WeightUnits":"mg"}]}
```    
 O exemplo seguinte mostra como ToString pode ser usada com outras funções de cadeia de caracteres como CONCAT.   
 
```  
SELECT 
CONCAT(ToString(p.Weight), p.WeightUnits) 
FROM p in c.Products 
```  

 Aqui está o conjunto de resultados.  
  
```  
[{"$1":"4lb" },
 {"$1":"32kg"},
 {"$1":"400g" },
 {"$1":"8999mg" }]

```  
Tendo em conta a seguinte entrada.
```
{"id":"08259","description":"Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX","nutrients":[{"id":"305","description":"Caffeine","units":"mg"},{"id":"306","description":"Cholesterol, HDL","nutritionValue":30,"units":"mg"},{"id":"307","description":"Sodium, NA","nutritionValue":612,"units":"mg"},{"id":"308","description":"Protein, ABP","nutritionValue":60,"units":"mg"},{"id":"309","description":"Zinc, ZN","nutritionValue":null,"units":"mg"}]}
```
 O exemplo seguinte mostra como ToString pode ser usada com outras funções de cadeia de caracteres, como substituir.   
```
SELECT 
    n.id AS nutrientID,
    REPLACE(ToString(n.nutritionValue), "6", "9") AS nutritionVal
FROM food 
JOIN n IN food.nutrients
```
 Aqui está o conjunto de resultados.  
 ```
[{"nutrientID":"305"},
{"nutrientID":"306","nutritionVal":"30"},
{"nutrientID":"307","nutritionVal":"912"},
{"nutrientID":"308","nutritionVal":"90"},
{"nutrientID":"309","nutritionVal":"null"}]
 ``` 
 
####  <a name="bk_trim"></a> TRIM  
 Devolve uma expressão de cadeia de caracteres depois que ele remove e à direita espaços em branco.  
  
 **Sintaxe**  
  
```  
TRIM(<str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra como utilizar TRIM dentro de uma consulta.  
  
```  
SELECT TRIM("   abc"), TRIM("   abc   "), TRIM("abc   "), TRIM("abc")   
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": "abc", "$2": "abc", "$3": "abc", "$4": "abc"}]  
``` 
####  <a name="bk_upper"></a> SUPERIOR  
 Devolve uma expressão de cadeia de caracteres após a conversão de dados de caráter em minúsculas em maiúsculas.  
  
 **Sintaxe**  
  
```  
UPPER(<str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de cadeia.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra como usar o canto superior numa consulta  
  
```  
SELECT UPPER("Abc")  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": "ABC"}]  
```  
  
###  <a name="bk_array_functions"></a> Funções de matriz  
 As seguintes funções escalares realizar uma operação num valor de entrada de matriz e a numérico de retorno, o valor de booleano ou matriz  
  
||||  
|-|-|-|  
|[ARRAY_CONCAT](#bk_array_concat)|[ARRAY_CONTAINS](#bk_array_contains)|[ARRAY_LENGTH](#bk_array_length)|  
|[ARRAY_SLICE](#bk_array_slice)|||  
  
####  <a name="bk_array_concat"></a> ARRAY_CONCAT  
 Devolve uma matriz que é o resultado da concatenação de dois ou mais valores de matriz.  
  
 **Sintaxe**  
  
```  
ARRAY_CONCAT (<arr_expr>, <arr_expr> [, <arr_expr>])  
```  
  
 **Argumentos**  
  
-   `arr_expr`  
  
     É qualquer expressão de matriz válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão de matriz.  
  
 **Exemplos**  
  
 O exemplo a seguir como concatenar duas matrizes.  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"])  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a> ARRAY_CONTAINS  
Devolve um valor booleano que indica se a matriz contém o valor especificado. Pode procurar uma correspondência parcial ou completa de um objeto ao utilizar uma expressão booleana dentro do comando. 

 **Sintaxe**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **Argumentos**  
  
-   `arr_expr`  
  
     É qualquer expressão de matriz válida.  
  
-   `expr`  
  
     É qualquer expressão válida.  

-   `bool_expr`  
  
     É qualquer expressão booleana. Se estiver definido como ' true'and se o valor de pesquisa especificada é um objeto, que o comando verifica a existência de uma correspondência parcial (o objeto de pesquisa é um subconjunto de um dos objetos). Se estiver definido como 'false', o comando verifica a existência de uma correspondência completa de todos os objetos dentro da matriz. O valor predefinido se não for especificado é false. 
  
 **Tipos de retorno**  
  
 Devolve um valor booleano.  
  
 **Exemplos**  
  
 O exemplo a seguir como verificar a existência de associação numa matriz usando ARRAY_CONTAINS.  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples"),  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes")  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": true, "$2": false}]  
```  

 O exemplo a seguir como verificar a existência de uma correspondência parcial de um JSON numa matriz usando ARRAY_CONTAINS.  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true), 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}),
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) 
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{
  "$1": true,
  "$2": false,
  "$3": false
}] 
```  
  
####  <a name="bk_array_length"></a> ARRAY_LENGTH  
 Devolve o número de elementos da expressão de matriz especificada.  
  
 **Sintaxe**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **Argumentos**  
  
-   `arr_expr`  
  
     É qualquer expressão de matriz válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo a seguir como obter o comprimento de uma matriz usando ARRAY_LENGTH.  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"])  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_array_slice"></a> ARRAY_SLICE  
 Devolve a parte de uma expressão de matriz.
  
 **Sintaxe**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argumentos**  
  
-   `arr_expr`  
  
     É qualquer expressão de matriz válida.  
  
-   `num_expr`  
  
     Índice numérico baseado em zero na qual deve começar a matriz. Valores negativos podem ser utilizados para especificar o índice de início em relação ao último elemento de matriz, ou seja, -1 referencia o último elemento na matriz.  

-   `num_expr`  

     Número máximo de elementos na matriz resultante.    

 **Tipos de retorno**  
  
 Devolve uma expressão de matriz.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra como obter setores diferentes de uma matriz usando ARRAY_SLICE.  
  
```  
SELECT   
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1),  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1),
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1),
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2),
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0),
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000),
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100)      
  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{  
           "$1": ["strawberries", "bananas"],   
           "$2": ["strawberries"],
           "$3": ["strawberries"],  
           "$4": ["strawberries", "bananas"], 
           "$5": [],
           "$6": ["strawberries", "bananas"],
           "$7": [] 
}]  
```  
 
###  <a name="bk_spatial_functions"></a> Funções espaciais  
 As seguintes funções escalares realizar uma operação num valor de entrada do objeto espacial e retornam um valor numérico ou booleano.  
  
||||  
|-|-|-|  
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|  
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)|||  
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 Devolve a distância entre as duas expressões LineString, Polygon ou GeoJSON ponto.  
  
 **Sintaxe**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumentos**  
  
-   `spatial_expr`  
  
     É qualquer expressão de objeto GeoJSON ponto, Polygon ou LineString válida.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão numérica, que contém a distância. Este valor é expresso em metros para o sistema de referência do padrão.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra como devolver todos os documentos de famílias que estão dentro de 30 km de distância do local especificado usando a função incorporada ST_DISTANCE. .  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{  
  "id": "WakefieldFamily"  
}]  
```  
  
####  <a name="bk_st_within"></a> ST_WITHIN  
 Devolve uma expressão booleana que indica se o objeto de GeoJSON (ponto, polígono ou LineString) especificado no primeiro argumento é dentro GeoJSON (ponto, polígono ou LineString) no segundo argumento.  
  
 **Sintaxe**  
  
```  
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumentos**  
  
-   `spatial_expr`  
  
     É qualquer expressão de objeto GeoJSON ponto, Polygon ou LineString válida.  
 
-   `spatial_expr`  
  
     É qualquer expressão de objeto GeoJSON ponto, Polygon ou LineString válida.  
  
 **Tipos de retorno**  
  
 Devolve um valor booleano.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra como encontrar todos os documentos famílias dentro de um polígono com ST_WITHIN.  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{ "id": "WakefieldFamily" }]  
```  

####  <a name="bk_st_intersects"></a> ST_INTERSECTS  
 Devolve uma expressão booleana que indica se o objeto de GeoJSON (ponto, polígono ou LineString) especificado no primeiro argumento intersetar-GeoJSON (ponto, polígono ou LineString) no segundo argumento.  
  
 **Sintaxe**  
  
```  
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumentos**  
  
-   `spatial_expr`  
  
     É qualquer expressão de objeto GeoJSON ponto, Polygon ou LineString válida.  
 
-   `spatial_expr`  
  
     É qualquer expressão de objeto GeoJSON ponto, Polygon ou LineString válida.  
  
 **Tipos de retorno**  
  
 Devolve um valor booleano.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra como localizar todas as áreas que fazem interseção com determinado polígono.  
  
```  
SELECT a.id   
FROM Areas a   
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{ "id": "IntersectingPolygon" }]  
```  
  
####  <a name="bk_st_isvalid"></a> ST_ISVALID  
 Devolve um valor booleano que indica se a expressão de LineString, Polygon ou GeoJSON ponto especificada é válida.  
  
 **Sintaxe**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argumentos**  
  
-   `spatial_expr`  
  
     É qualquer expressão LineString, Polygon ou GeoJSON ponto válido.  
  
 **Tipos de retorno**  
  
 Devolve uma expressão booleana.  
  
 **Exemplos**  
  
 O exemplo seguinte mostra como verificar se um ponto é válido quando utiliza ST_VALID.  
  
 Por exemplo, este ponto tem um valor de latitude não for no intervalo válido de valores [-90, 90], por isso, a consulta devolve false.  
  
 Para polígonos, a especificação GeoJSON requer que o último par coordenado fornecido deve ser o mesmo que o primeiro, para criar uma forma fechada. Pontos de dentro de um polígono tem de ser especificados na ordem no sentido. Um polígono especificado por ordem para a direita representa o inverso da região na mesma.  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{ "$1": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 Devolve um valor JSON que contém um valor booleano valor se a expressão de LineString, Polygon ou GeoJSON ponto especificada é válida e se for inválido, além do motivo pelo qual como um valor de cadeia de caracteres.  
  
 **Sintaxe**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argumentos**  
  
-   `spatial_expr`  
  
     É qualquer expressão de ponto ou de polígono de GeoJSON válida.  
  
 **Tipos de retorno**  
  
 Devolve um valor JSON que contém um valor booleano valor se a expressão de ponto ou de polígono de GeoJSON especificada é válida e se for inválido, além do motivo pelo qual como um valor de cadeia de caracteres.  
  
 **Exemplos**  
  
 O exemplo a seguir como verificar a validade (com detalhes) usando ST_ISVALIDDETAILED.  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
})  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{  
  "$1": {   
    "valid": false,   
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  
  
## <a name="next-steps"></a>Passos Seguintes  

- [Sintaxe SQL e consulta SQL para o Cosmos DB](how-to-sql-query.md)

- [Documentação do cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)  
