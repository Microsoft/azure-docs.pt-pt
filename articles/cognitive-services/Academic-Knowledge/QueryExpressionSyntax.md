---
title: Sintaxe de expressão de consulta - API de conhecimento académico
titlesuffix: Azure Cognitive Services
description: Saiba como utilizar a sintaxe de expressão de consulta na API de conhecimento académico.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 95c2e9d3f54f967b3ebb434c742f69251b80573e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61336761"
---
# <a name="query-expression-syntax"></a>Sintaxe de expressão de consulta

Temos visto que a resposta para uma **interpretar** pedido inclui uma expressão de consulta. A gramática que interpretados consulta do utilizador criar uma expressão de consulta para cada interpretação. Em seguida, pode ser utilizada uma expressão de consulta a emitir uma **avaliar** pedido para obter os resultados da pesquisa de entidades.

Também pode construir seu próprio expressões de consulta e utilizá-las num **avaliar** pedido. Isso pode ser útil se estiver a criar sua própria interface do usuário que cria uma expressão de consulta em resposta às ações do usuário. Para fazer isso, precisa saber a sintaxe para expressões de consulta.  

Cada atributo de entidade que pode ser incluído numa expressão de consulta tem um tipo de dados específico e um conjunto de operadores de consulta possível. O conjunto de atributos de entidade e os operadores suportados para cada atributo é especificado no [atributos de entidade](EntityAttributes.md). Uma consulta de valor único requer o atributo para suportar o *é igual a* operação. Uma consulta de prefixo requer o atributo para suportar o *StartsWith* operação. Consultas de intervalo numérico requer o atributo para suportar o *IsBetween* operação.

Alguns dos dados de entidade são armazenadas como atributos compostos, como indicado por um ponto "." no nome do atributo. Por exemplo, informações de autor/afiliação são representadas como um atributo composto. Ele contém componentes de 4: AuN, AuId, AfN, AfId. Esses componentes são partes separadas de dados que formam um valor de atributo de entidade única.


**Atributo de cadeia de caracteres: Único valor** (inclui correspondências em relação a sinónimos)  
Te = 'indexação pela análise semântico latente'  
Composição (AA. AuN = "processar judicialmente dumais")

**Atributo de cadeia de caracteres: Valor único de EXACT** (corresponde apenas valores canónicos)  
Te = = 'indexação pela análise semântico latente'  
Composição (AA. AuN = = "susan t dumais")
     
**Atributo de cadeia de caracteres: Valor de prefixo**   
Te = "indexação por seman latente"...  
Composição (AA. AuN = "processar judicialmente du"...)

**Atributos numéricos: Valor único**  
Y=2010
 
**Atributos numéricos: Valor de intervalo**  
Y>2005  
Y>=2005  
Y<2010  
Y<=2010  
Y =\[2010, 2012\) (inclui o valor de limite esquerdo única: 2010, 2011)  
Y =\[2010, 2012\] (inclui ambos os valores de limite: 2010, 2011, 2012)
 
**Atributos numéricos: Valor de prefixo**  
Y = '19'... (qualquer valor numérico que começa com 19) 
 
**Atributo de data: Valor único**  
D='2010-02-04'

**Atributo de data: Valor de intervalo**  
D>'2010-02-03'  
D=['2010-02-03','2010-02-05']

**E/ou consultas:**  
E (Y = 1985, te = 'disordered eletrônicos sistemas')  
Ou (te 'disordered eletrônicos sistemas', te de = = "princípios de tolerância a falhas e prática")  
And(or(Y=1985,Y=2008), te = 'disordered eletrônicos sistemas')
 
**Consultas compostas:**  
Componentes de consulta de um atributo composto, tem de incluir a parte da expressão de consulta que se refere ao atributo composto na função Composite(). 

Por exemplo, para consultar documentos por nome de autor, utilize a seguinte consulta:
```
Composite(AA.AuN='mike smith')
```
<br>Para consultar documentos de um autor específico ao autor foi uma instituição específica, utilize a seguinte consulta:
```
Composite(And(AA.AuN='mike smith',AA.AfN='harvard university'))
```
<br>A função de Composite() junta as duas partes do atributo composto. Isso significa que podemos apenas obter documentos em que um dos autores é "Mike Smith" enquanto esteve na Harvard. 

Para consultar documentos de um autor específico no afiliações com (outros) os autores de uma instituição específica, utilize a seguinte consulta:
```
And(Composite(AA.AuN='mike smith'),Composite(AA.AfN='harvard university'))
```
<br>Nesta versão, porque Composite() é aplicada ao autor e afiliação individualmente antes And(), obtemos todos os documentos em que é um dos autores "Mike Smith" e é um dos afiliações dos autores "Harvard". Isso parece semelhante ao exemplo anterior de consulta, mas não é a mesma coisa.

Em geral, considere o exemplo a seguir: Temos um atributo composto C que tem dois componentes A e B. Uma entidade pode ter vários valores para C. Esses são nossos entidades:
```
E1: C={A=1, B=1}  C={A=1,B=2}  C={A=2,B=3}
E2: C={A=1, B=3}  C={A=3,B=2}
```

<br>A consulta 
```
Composite(And(C.A=1, C.B=2))
```

<br>corresponde a apenas as entidades que tenham um valor para C em que o componente C.A é 1 e o componente C.B é 2. Apenas E1 corresponde a esta consulta.

A consulta 
```
And(Composite(C.A=1), Composite(C.B=2))
```
<br>corresponde a entidades que têm um valor para C onde C.A é 1 e também de ter um valor para C onde C.B é 2. E1 e E2 correspondem a esta consulta.

Atenção:  
- Não é possível referenciar uma parte de um atributo composto fora de uma função de Composite().
- Não é possível referenciar a partes de dois atributos compostos diferentes dentro da mesma função de Composite().
- Não é possível referenciar um atributo que não faz parte de um atributo composto dentro de uma função de Composite().
