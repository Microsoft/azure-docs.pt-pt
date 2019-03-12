---
title: Formato de gramática - API de serviço de exploração de conhecimento
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre o formato de gramática no conhecimento exploração de serviço (KES) API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 844bd9a88c52fd398fc66c71e59da513c0d7d90d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544329"
---
# <a name="grammar-format"></a>Formato de gramática

A gramática é um arquivo XML que especifica o conjunto ponderado de consultas de linguagem natural que o serviço pode interpretar, bem como a forma como estas consultas de linguagem natural são traduzidas em expressões de consulta semântica.  A sintaxe de gramática baseia [SRGS](https://www.w3.org/TR/speech-grammar/), uma norma W3C para gramáticas de reconhecimento de voz, com extensões para suportar a integração de índice de dados e funções de semânticas.

O seguinte descreve cada um dos elementos sintáticos que podem ser usados numa gramática.  Ver [neste exemplo](#example) para uma gramática completa que demonstra a utilização destes elementos no contexto.

### <a name="grammar-element"></a>gramática de elemento

O `grammar` elemento é o elemento de nível superior na especificação de gramática XML.  Necessários `root` atributo especifica o nome da regra de raiz que define o ponto de partida da gramática.

```xml
<grammar root="GetPapers">
```

### <a name="import-element"></a>importar o elemento

O `import` elemento importa uma definição de esquema a partir de um arquivo externo para ativar as referências de atributo. O elemento tem de ser um filho de nível superior `grammar` elemento e aparecer antes de qualquer `attrref` elementos. Necessários `schema` atributo especifica o nome de um ficheiro de esquema localizado no mesmo diretório que o ficheiro XML de gramática. Necessários `name` elemento Especifica o esquema ou alias subsequente `attrref` elementos usam ao referenciar atributos definidos no esquema do.

```xml
  <import schema="academic.schema" name="academic"/>
```

### <a name="rule-element"></a>regra de elemento

O `rule` elemento define uma regra de gramática, uma unidade estrutural que especifica um conjunto de expressões de consulta que o sistema pode interpretar.  O elemento tem de ser um filho de nível superior `grammar` elemento.  Necessários `id` atributo especifica o nome da regra, o que é referenciado a partir de `grammar` ou `ruleref` elementos.

A `rule` elemento define um conjunto de expansões legais.  Tokens de texto correspondem com a consulta de entrada diretamente.  `item` elementos especifique repete e alterar as probabilidades de interpretação.  `one-of` elementos indicam opções alternativas.  `ruleref` elementos permitem a construção de expansões mais complexos, dos mais simples.  `attrref` elementos permitem correspondências em relação aos valores de atributo do índice.  `tag` elementos de especificar a semântica da interpretação e é podem alterar a probabilidade de interpretação.

```xml
<rule id="GetPapers">...</rule>
```

### <a name="example-element"></a>exemplo de elemento

O opcional `example` elemento Especifica expressões de exemplo que podem ser aceites pelo que contém `rule` definição.  Isso pode ser utilizado para a documentação e/ou testes automatizados.

```xml
<example>papers about machine learning by michael jordan</example>
```

### <a name="item-element"></a>item de elemento

O `item` elemento agrupa uma seqüência de construções de gramática.  Ele pode ser usado para indicar as repetições da sequência de expansão ou especificar alternativas em conjunto com o `one-of` elemento.

Quando um `item` elemento não é um filho de um `one-of` elemento, pode especificar a repetição da sequência de circunscritas atribuindo o `repeat` de atributo para um valor de contagem.  Um valor de contagem de "*n*" (em que *n* é um número inteiro) indica que a sequência de deve ocorrer exatamente *n* vezes.  Um valor de contagem de "*m*-*n*" permite que a sequência de aparecer entre *m* e *n* exceder o tempo, inclusivamente.  Um valor de contagem de "*m*-" Especifica que a sequência tem, pelo menos, de aparecer *m* vezes.  O opcional `repeat-logprob` atributo pode ser utilizado para alterar a probabilidade de interpretação para cada repetição adicional além do mínimo.

```xml
<item repeat="1-" repeat-logprob="-10">...</item>
```

Quando `item` elementos aparecem como filhos de um `one-of` elemento, elas definem o conjunto de alternativas de expansão.  Para esse fim, o opcional `logprob` atributo Especifica a probabilidade do registo relativa entre as diferentes opções.  Dada uma probabilidade *p* entre 0 e 1, a probabilidade de registo correspondente pode ser computada como log (*p*), em que log() é a função de registo natural.  Se não for especificado, `logprob` a predefinição é 0, o que não altera a probabilidade de interpretação.  Observe que a probabilidade de log é sempre um valor de vírgula flutuante negativo ou 0.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="one-of-element"></a>um-do elemento

O `one-of` elemento Especifica expansões alternativos entre um dos filhos `item` elementos.  Apenas `item` elementos podem aparecer dentro de um `one-of` elemento.  Probabilidades relativas entre as diferentes opções podem ser especificadas através da `logprob` atributo em cada filho `item`.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="ruleref-element"></a>ruleref elemento

O `ruleref` elemento Especifica expansões válidos através de referências para outro `rule` elemento.  Através da utilização de `ruleref` elementos, expressões mais complexas podem ser criadas mais simples das regras de.  Necessários `uri` atributo indica o nome do referenciado `rule` usando a sintaxe "#*rulename*".  Para capturar a saída semântica da regra referenciada, utilize o opcional `name` atributo para especificar o nome de uma variável para o qual é atribuída a saída de semântica.
 
```xml
<ruleref uri="#GetPaperYear" name="year"/>
```

### <a name="attrref-element"></a>attrref elemento

O `attrref` elemento faz referência a um atributo de índice, permitindo a correspondência em relação aos valores de atributo observado no índice.  Necessários `uri` atributo especifica o nome do esquema de índice e o nome de atributo utilizando a sintaxe "*schemaName*#*attrName*".  Tem de existir um precedente `import` elemento que importa o esquema com o nome *schemaName*.  O nome de atributo é o nome de um atributo definido no esquema do correspondente.

Para além de correspondência de entrada do usuário, o `attrref` elemento também retorna um objeto de consulta estruturadas como saída que seleciona o subconjunto de objetos no índice que correspondem o valor de entrada.  Utilizar o opcional `name` atributo para especificar o nome da variável em que a saída de objeto de consulta deve ser armazenada.  O objeto de consulta pode ser composto com outros objetos de consulta para formar mais expressões complexas.  Ver [interpretação semântica](SemanticInterpretation.md) para obter detalhes.  

```xml
<attrref uri="academic#Keyword" name="keyword"/>
```

#### <a name="query-completion"></a>Conclusão de consulta

Para suportar as conclusões de consulta ao interpretar consultas de utilizador parcial, cada atributo de referência tem de incluir "starts_with" como uma operação na definição do esquema.  Tendo em conta um prefixo de consulta de utilizador, `attrref` irá corresponder a todos os valores no índice que concluir o prefixo e resultar em cada valor concluída como uma interpretação separada da gramática.  

Exemplos:
* Correspondência `<attrref uri="academic#Keyword" name="keyword"/>` contra a consulta o prefixo "dados" gera uma interpretação para documentos sobre o "base de dados", uma interpretação para documentos sobre o "data mining", etc.
* Correspondência `<attrref uri="academic#Year" name="year"/>` contra a consulta o prefixo "200" gera uma interpretação para artigos "2000", uma interpretação para documentos em "2001", etc.

#### <a name="matching-operations"></a>Correspondência de operações

Além de correspondência exata, selecione os tipos de atributo também o prefixo de suporte e desigualdade corresponde ao através o opcional `op` atributo.  Se nenhum objeto no índice tem um valor correspondente, o caminho de gramática é bloqueado e o serviço não irá gerar qualquer interpretações atravessando sobre este caminho de gramática.   O `op` predefinições de atributo para "eq".

```xml
in <attrref uri="academic#Year" name="year"/>
before <attrref uri="academic#Year" op="lt" name="year"/
```

A tabela seguinte lista os suportados `op` valores para cada tipo de atributo.  Seu uso requer que a operação de índice correspondente a serem incluídos na definição do atributo de esquema.

| Tipo de Atributo | Valor de OP | Descrição | Operação de índice
|----|----|----|----|
| String | eq | Correspondência exata da cadeia de caracteres | é igual a |
| String | starts_with | Correspondência de prefixo de cadeia de caracteres | starts_with |
| Duplo de Int32, Int64, | eq |  Correspondência de igualdade numérico | é igual a |
| Duplo de Int32, Int64, | lt, le, gt, ge | Correspondência de desigualdade numérico (<, < =, >, > =) | is_between |
| Duplo de Int32, Int64, | starts_with | Correspondência de prefixo de valor na notação decimal | starts_with |

Exemplos:
* `<attrref uri="academic#Year" op="lt" name="year"/>` corresponde à cadeia de entrada "2000" e retorna todos os documentos publicados antes do ano 2000, exclusivamente.
* `<attrref uri="academic#Year" op="lt" name="year"/>` não corresponde a cadeia de entrada "20" porque não existem não existem documentos no índice publicado antes do ano 20.
* `<attrref uri="academic#Keyword" op="starts_with" name="keyword"/>` corresponde à cadeia de entrada "dados" e retorna em papers uma única interpretação sobre "base de dados", "data mining", etc.  Esse é um caso de utilização rara.
* `<attrref uri="academic#Year" op="starts_with" name="year"/>` correspondências de cadeia de entrada "20" e devolve em documentos de uma única interpretação publicado em 200 299, 2000-2999, etc.  Esse é um caso de utilização rara.

### <a name="tag-element"></a>etiqueta de elemento

O `tag` elemento Especifica como um caminho através a gramática é para ser interpretado.  Ela contém uma seqüência de instruções finalizada com ponto e vírgula.  Uma instrução pode ser uma atribuição de um literal ou uma variável a outra variável.  -Lo, também, pode atribuir a saída de uma função com 0 ou mais parâmetros a uma variável.  Cada parâmetro de função pode ser especificado com um literal ou uma variável.  Se a função não devolve quaisquer dados, a atribuição é omitida.  Escopo da variável seja local para a regra que contém.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Cada `rule` na gramática tem uma variável predefinida com o nome "de saída", que representa a saída semântica da regra.  Seu valor é calculado através da avaliação de cada uma das declarações a semânticas percorridas pelo caminho através do `rule` corresponder o utilizador a entrada de consulta.  O valor atribuído à variável "de saída" no final da avaliação é a saída semântica da regra.  A saída semântica da interpretação de uma consulta de utilizador em relação a gramática é a saída semântica da regra de raiz.

Algumas instruções podem alterar a probabilidade de um caminho de interpretação introduzindo um desvio de probabilidade de log suplementar.  Algumas instruções podem rejeitar a interpretação totalmente se for especificado não estão cumpridas condições.

Para obter uma lista de funções de semânticas suportadas, consulte [funções semântico](SemanticInterpretation.md#semantic-functions).

## <a name="interpretation-probability"></a>Probabilidade de interpretação

A probabilidade de um caminho de interpretação através a gramática é a probabilidade de log cumulativa de todos os `<item>` elementos e as funções semânticas encontradas ao longo do caminho.  Ele descreve a probabilidade relativa de correspondência de uma determinada seqüência de entrada.

Dada uma probabilidade *p* entre 0 e 1, a probabilidade de registo correspondente pode ser computada como log (*p*), em que log() é a função de registo natural.  Usar as probabilidades de log permite que o sistema acumular a probabilidade conjunta de um caminho de interpretação por meio da adição simples.  Isso também evita a capacidade reduzida de ponto flutuante comuns a esses cálculos de probabilidade conjunta.  Observe que, por design, a probabilidade de log é sempre um valor de vírgula flutuante negativo ou 0, onde os valores maiores indicam probabilidade mais alta.

## <a name="example"></a>Exemplo

Segue-se um exemplo XML do domínio publicações acadêmicas que demonstra os vários elementos de uma gramática:

```xml
<grammar root="GetPapers">

  <!-- Import academic data schema-->
  <import schema="academic.schema" name="academic"/>
  
  <!-- Define root rule-->
  <rule id="GetPapers">
    <example>papers about machine learning by michael jordan</example>
    
    papers
    <tag>
      yearOnce = false;
      isBeyondEndOfQuery = false;
      query = All();
    </tag>
  
    <item repeat="1-" repeat-logprob="-10">
      <!-- Do not complete additional attributes beyond end of query -->
      <tag>AssertEquals(isBeyondEndOfQuery, false);</tag>
        
      <one-of>
        <!-- about <keyword> -->
        <item logprob="-0.5">
          about <attrref uri="academic#Keyword" name="keyword"/>
          <tag>query = And(query, keyword);</tag>
        </item>
        
        <!-- by <authorName> [while at <authorAffiliation>] -->
        <item logprob="-1">
          by <attrref uri="academic#Author.Name" name="authorName"/>
          <tag>authorQuery = authorName;</tag>
          <item repeat="0-1" repeat-logprob="-1.5">
            while at <attrref uri="academic#Author.Affiliation" name="authorAffiliation"/>
            <tag>authorQuery = And(authorQuery, authorAffiliation);</tag>
          </item>
          <tag>
            authorQuery = Composite(authorQuery);
            query = And(query, authorQuery);
          </tag>
        </item>
        
        <!-- written (in|before|after) <year> -->
        <item logprob="-1.5">
          <!-- Allow this grammar path to be traversed only once -->
          <tag>
            AssertEquals(yearOnce, false);
            yearOnce = true;
          </tag>
          <ruleref uri="#GetPaperYear" name="year"/>
          <tag>query = And(query, year);</tag>
        </item>
      </one-of>

      <!-- Determine if current parse position is beyond end of query -->
      <tag>isBeyondEndOfQuery = GetVariable("IsBeyondEndOfQuery", "system");</tag>
    </item>
    <tag>out = query;</tag>
  </rule>
  
  <rule id="GetPaperYear">
    <tag>year = All();</tag>
    written
    <one-of>
      <item>
        in <attrref uri="academic#Year" name="year"/>
      </item>
      <item>
        before
        <one-of>
          <item>[year]</item>
          <item><attrref uri="academic#Year" op="lt" name="year"/></item>
        </one-of>
      </item>
      <item>
        after
        <one-of>
          <item>[year]</item>
          <item><attrref uri="academic#Year" op="gt" name="year"/></item>
        </one-of>
      </item>
    </one-of>
    <tag>out = year;</tag>
  </rule>
</grammar>
```
