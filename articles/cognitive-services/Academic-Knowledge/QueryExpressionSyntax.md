---
title: Sintaxe de expressão de consulta-API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Saiba como usar a sintaxe de expressão de consulta na API Conhecimento Acadêmico.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 3b87e04c2d6380a0ee4157e73db0cd4057fadee1
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704915"
---
# <a name="query-expression-syntax"></a>Sintaxe de expressão de consulta

Vimos que a resposta a uma solicitação de **interpretação** inclui uma expressão de consulta. A gramática que interpretau a consulta do usuário criou uma expressão de consulta para cada interpretação. Uma expressão de consulta pode ser usada para emitir uma solicitação de **avaliação** para recuperar os resultados da pesquisa de entidade.

Você também pode construir suas próprias expressões de consulta e usá-las em uma solicitação de **avaliação** . Isso pode ser útil se você estiver criando sua própria interface do usuário, que cria uma expressão de consulta em resposta às ações do usuário. Para fazer isso, você precisa saber a sintaxe para expressões de consulta.  

Cada atributo de entidade que pode ser incluído em uma expressão de consulta tem um tipo de dados específico e um conjunto de possíveis operadores de consulta. O conjunto de atributos de entidade e os operadores com suporte para cada atributo é especificado em [atributos de entidade](EntityAttributes.md). Uma consulta de valor único requer que o atributo dê suporte à operação Equals. Uma consulta de prefixo requer que o atributo dê suporte à operação *StartsWith* . As consultas de intervalo numérico exigem o atributo para dar suporte à operação isBetween.

Alguns dos dados da entidade são armazenados como atributos compostos, conforme indicado por um ponto '. ' no nome do atributo. Por exemplo, as informações de autor/afiliação são representadas como um atributo composto. Ele contém 4 componentes: AuN, AuId, AfN, AfId. Esses componentes são partes separadas de dados que formam um único valor de atributo de entidade.


**Atributo de cadeia de caracteres: Único valor** (inclui correspondências contra sinônimos)  
Ti = ' indexação por análise semântica latente '  
Composto (AA. AuN = ' Suzana Dumais ')

**Atributo de cadeia de caracteres: Valor** único exato (corresponde apenas a valores canônicos)  
Ti = = ' indexação por análise semântica latente '  
Composto (AA. AuN = = ' Susan t Dumais ')
     
**Atributo de cadeia de caracteres: Valor do prefixo**   
Ti = ' indexação por Seman latente '...  
Composto (AA. AuN = ' Suzana du '...)

**Atributo numérico: Valor único**  
Y=2010
 
**Atributo numérico: Valor do intervalo**  
Y>2005  
Y>=2005  
Y<2010  
Y<=2010  
Y =\[2010, 2012\) (inclui somente o valor de limite esquerdo: 2010, 2011)  
Y =\[2010, 2012\] (inclui ambos os valores de limite: 2010, 2011, 2012)
 
**Atributo numérico: Valor do prefixo**  
Y = ' 19 '... (qualquer valor numérico que comece com 19) 
 
**Atributo de data: Valor único**  
D='2010-02-04'

**Atributo de data: Valor do intervalo**  
D > ' 2010-02-03 '  
D=['2010-02-03','2010-02-05']

**Consultas e/ou:**  
E (Y = 1985, ti = ' sistemas eletrônicos desordenados ')  
Ou (ti = ' sistemas eletrônicos desordenados ', ti = ' princípios de tolerância a falhas e prática ')  
And (or (Y = 1985, Y = 2008), ti = ' sistemas eletrônicos desordenados ')
 
**Consultas compostas:**  
Para consultar os componentes de um atributo composto, você precisa colocar a parte da expressão de consulta que se refere ao atributo composto na função Composite (). 

Por exemplo, para consultar documentos por nome de autor, use a seguinte consulta:
```
Composite(AA.AuN='mike smith')
```
<br>Para consultar documentos por um autor específico enquanto o autor estava em uma instituição específica, use a seguinte consulta:
```
Composite(And(AA.AuN='mike smith',AA.AfN='harvard university'))
```
<br>A função Composite () vincula as duas partes do atributo composto ao mesmo tempo. Isso significa que só obtemos artigos onde um dos autores é "Mike Smith" enquanto ele estava na Harvard. 

Para consultar documentos por um autor específico em afiliações com autores (outros) de uma instituição específica, use a seguinte consulta:
```
And(Composite(AA.AuN='mike smith'),Composite(AA.AfN='harvard university'))
```
<br>Nesta versão, como Composite () é aplicado ao autor e à afiliação individualmente antes e (), obtemos todos os documentos em que um dos autores é "Mike Smith" e uma das afiliações dos autores é "Harvard". Isso parece semelhante ao exemplo de consulta anterior, mas não é a mesma coisa.

Em geral, considere o seguinte exemplo: Temos um atributo composto C que tem dois componentes A e B. Uma entidade pode ter vários valores para C. Estas são nossas entidades:
```
E1: C={A=1, B=1}  C={A=1,B=2}  C={A=2,B=3}
E2: C={A=1, B=3}  C={A=3,B=2}
```

<br>A consulta 
```
Composite(And(C.A=1, C.B=2))
```

<br>corresponde somente a entidades que têm um valor de C em que o componente C. a é 1 e o componente C. B é 2. Somente E1 corresponde a essa consulta.

A consulta 
```
And(Composite(C.A=1), Composite(C.B=2))
```
<br>faz a correspondência de entidades que têm um valor de C em que c. A é 1 e também têm um valor para C, em que C. B é 2. O E1 e o E2 correspondem a essa consulta.

Atenção:  
- Você não pode fazer referência a uma parte de um atributo composto fora de uma função composta ().
- Você não pode fazer referência a partes de dois atributos compostos diferentes dentro da mesma função Composite ().
- Você não pode fazer referência a um atributo que não faz parte de um atributo composto dentro de uma função Composite ().
