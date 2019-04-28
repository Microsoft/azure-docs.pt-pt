---
title: Sintaxe de pesquisa lambda - API de conhecimento académico
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre a sintaxe de pesquisa Lambda, que pode utilizar a API de conhecimento académico.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 4d4c540e00794bfdf1df265457798cc13530c828
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61337793"
---
# <a name="lambda-search-syntax"></a>Sintaxe de pesquisa lambda

Cada *lambda* cadeia descreve um padrão de grafo de consulta de pesquisa. Uma consulta tem de ter, pelo menos, um nó de partida, especificando a partir do nó de gráfico que começamos a passagem. Para especificar um nó de partida, chame o *MAG. StartFrom()* método e passe o IDs de um ou mais nós ou uma consulta de objeto que especifica as restrições de pesquisa. O *StartFrom()* método tem três sobrecargas. Todos eles usam dois argumentos com sendo que a segunda é opcionais. O primeiro argumento pode ser um número inteiro longo, uma coleção enumerável e de número inteiro longo, ou uma cadeia de caracteres que representa um JSON de objeto, com a mesma semântica como na *json* pesquisa:
```
StartFrom(long cellid, IEnumerable<string> select = null)
StartFrom(IEnumerable<long> cellid, IEnumerable<string> select = null)
StartFrom(string queryObject, IEnumerable<string> select = null)
```

O processo de criar uma consulta de pesquisa lambda é orientá-lo de um nó para outro. Para especificar o tipo de limite para percorrer, utilize *FollowEdge()* e passar os tipos de borda pretendido. *FollowEdge()* usa um número arbitrário de argumentos de cadeia de caracteres:
```
FollowEdge(params string[] edgeTypes)
```
> [!NOTE]
> Se não nos preocupamos o tipo ou tipos de edge(s) a seguir, basta omitir *FollowEdge()* entre dois nós: a consulta irá guiá-todas as margens possíveis entre estes dois nós.

Podemos especificar as ações de transversal a tomar um nó através de *VisitNode()*, ou seja, se parar neste nó e retornar o caminho atual como o resultado ou continuar a explorar o gráfico.  O tipo de enumeração *ação* define dois tipos de ações: *Action.Return* e *Action.Continue*. Tal um valor de enumeração diretamente em que podemos transmitir *VisitNode()*, ou combiná-los com o bit a bit- e o operador "&". Quando a ação dois são combinados, significa que ambas as ações serão tomadas. Nota: não utilize totalmente- ou o operador ' |' em ações. Se o fizer, fará com que a consulta terminar sem devolver nada. A ignorar *VisitNode()* entre as duas *FollowEdge()* chamadas fará com que a consulta explorar incondicionalmente gráfico depois de chegar um nó.

```
VisitNode(Action action, IEnumerable<string> select = null)
```

Para *VisitNode()*, também pode passar uma expressão lambda do tipo *expressão\<Func\<Inodes, ação\>\>*, que leva um *Inodes* e retorna uma ação de transversal:

```
VisitNode(Expression<Func<INode, Action>> action, IEnumerable<string> select = null)
```

## <a name="inode"></a>*Inodes* 

*Inodes* fornece *só de leitura* interfaces e algumas funções auxiliares incorporada num nó de acesso a dados. 

### <a name="basic-data-access-interfaces"></a>Interfaces de acesso a dados básicos

##### <a name="long-cellid"></a>CellID longa

O ID de 64 bits do nó. 

##### <a name="t-getfieldtstring-fieldname"></a>T GetField\<T\>(string fieldName)

Obtém o valor da propriedade especificado. *T* é o tipo desejado, que o campo deve ser interpretado como. Conversão de tipo automática será tentada se o tipo desejado não é possível converter implicitamente do tipo do campo. Nota: quando a propriedade é com múltiplos valores *GetField\<cadeia\>*  fará com que a lista ser serializada para uma cadeia de caracteres do Json ["val1", "val2",...]. Se a propriedade não existir, ele irá lançar uma exceção e abortar a exploração de gráfico atual.

##### <a name="bool-containsfieldstring-fieldname"></a>bool ContainsField (fieldName de cadeia de caracteres)

Informa se existe um campo com o nome fornecido no nó atual.

##### <a name="string-getstring-fieldname"></a>cadeia de caracteres obter (fieldName de cadeia de caracteres)

Como funciona *GetField\<cadeia\>(fieldName)*. No entanto, ele não emite exceções quando o campo não for encontrado, ele retorna um string("") vazio em vez disso.

##### <a name="bool-hasstring-fieldname"></a>bool tem (fieldName de cadeia de caracteres)

Indica se a propriedade especificada existe no nó atual. Mesmo que *ContainsField(fieldName)*.

##### <a name="bool-hasstring-fieldname-string-value"></a>bool tem (fieldName de cadeia de caracteres, o valor de cadeia de caracteres)

Indica se a propriedade tem o valor especificado. 

##### <a name="int-countstring-fieldname"></a>Contagem de INT (fieldname de cadeia de caracteres)

Obter a contagem de valores de uma determinada propriedade. Quando a propriedade não existir, retorna 0.

### <a name="built-in-helper-functions"></a>Funções auxiliares internos

##### <a name="action-returnifbool-condition"></a>Ação return_if (condição bool)

Devolve *Action.Return* se a condição for *verdadeiro*. Se a condição for *false* e esta expressão não está associada com outras ações por um bit a bit- e o operador, a exploração do gráfico será abortada.

##### <a name="action-continueifbool-condition"></a>Ação continue_if (condição bool)

Devolve *Action.Continue* se a condição for *verdadeiro*. Se a condição for *false* e esta expressão não está associada com outras ações por um bit a bit- e o operador, a exploração do gráfico será abortada.

##### <a name="bool-dicedouble-p"></a>bool dice (double p)

Gera um número aleatório que é maior que ou igual a 0,0 e menor que 1,0. Esta função devolve *true* apenas se o número for menor ou igual a *p*.

Em comparação com *json* pesquisa, *lambda* pesquisa é mais expressiva: C#as expressões lambda podem ser utilizadas diretamente para especificar padrões de consulta. Aqui estão dois exemplos.

```
MAG.StartFrom(@"{
    type  : ""ConferenceSeries"",
    match : {
        FullName : ""graph""
    }
}", new List<string>{ "FullName", "ShortName" })
.FollowEdge("ConferenceInstanceIDs")
.VisitNode(v => v.return_if(v.GetField<DateTime>("StartDate").ToString().Contains("2014")),
        new List<string>{ "FullName", "StartDate" })
```

```
MAG.StartFrom(@"{
    type  : ""Affiliation"",
    match : {
        Name : ""microsoft""
    }
}").FollowEdge("PaperIDs")
.VisitNode(v => v.return_if(v.get("NormalizedTitle").Contains("graph") || v.GetField<int>("CitationCount") > 100),
        new List<string>{ "OriginalTitle", "CitationCount" })
```
