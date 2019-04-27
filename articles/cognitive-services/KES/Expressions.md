---
title: Expressões de consulta estruturadas - API de serviço de exploração de conhecimento
titlesuffix: Azure Cognitive Services
description: Saiba como utilizar expressões de consulta estruturadas no conhecimento exploração de serviço (KES) API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: a544cdca1ef4be56fcf368a39040f4ee85076a9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60815112"
---
# <a name="structured-query-expression"></a>Expressão de consulta estruturadas

Uma expressão de consulta estruturadas Especifica um conjunto de operações para avaliar contra o índice de dados.  Ele consiste em expressões de consulta de atributo e funções de nível mais alto.  Utilize o [ *avaliar* ](evaluateMethod.md) método para computar os objetos que correspondam a expressão.  Segue-se um exemplo do domínio publicações acadêmicas que retorna publicações criadas por Jaime Teevan desde o ano de 2013.

`And(Composite(Author.Name=='jaime teevan'),Y>=2013)`

Expressões de consulta estruturadas podem ser obtidas a partir [ *interpretar* ](interpretMethod.md) pedidos, em que a saída de semântica de cada interpretação seja uma expressão de consulta estruturadas que devolve os objetos de índice correspondente a consulta de entrada de linguagem natural.  Em alternativa, eles podem ser criados manualmente utilizando a sintaxe descrita nesta secção.

## <a name="attribute-query-expression"></a>Expressão de consulta do atributo

Uma expressão de consulta do atributo identifica um conjunto de objetos com base na correspondência em relação a um atributo específico.  Diferentes operações correspondentes são suportadas consoante o tipo de atributo e operação indexada especificado na [esquema](SchemaFormat.md):

| Type | Operação | Exemplos |
|------|-------------|------------|
| String | é igual a | Title = "latente semântico analysis" (canônico + sinónimos) |
| String | é igual a | Dos Author.Name=='susan t dumais (canônico apenas)|
| String | starts_with | Title = "s latente"... |
| Int32/Int64/Double | é igual a | Year=2000 |
| Int32/Int64/Double | starts_with | Ano = "20"... (qualquer valor decimal a partir do "20") |
| Int32/Int64/Double | is_between | Ano&lt;2000 <br/> Ano&lt;= 2000 <br/> Ano&gt;2000 <br/> Ano&gt;= 2000 <br/> Year=[2010,2012) *(inclui o valor de limite esquerdo única: 2010, 2011)* <br/> Ano = [2000,2012] *(inclui ambos os valores de limite: 2010, 2011, 2012)* |
| Date | é igual a | BirthDate='1984-05-14' |
| Date | is_between | BirthDate&lt;='2008/03/14' <br/> PublishDate=['2000-01-01','2009-12-31'] |
| Guid | é igual a | Id='602DD052-CC47-4B23-A16A-26B52D30C05B' |


Por exemplo, a expressão "Title ="s latente"..." corresponde a todas as publicações acadêmicas cujo título começa com a cadeia de caracteres "latente s".  Fim de avaliar esta expressão, o atributo Title tem de especificar a operação de "starts_with" no esquema utilizado para criar o índice.

Para atributos com sinónimos associados, uma expressão de consulta pode especificar objetos cujo valor canônico corresponde a uma determinada seqüência de caracteres usando o operador "= =", ou objetos em que qualquer um dos seus valores de canônico/sinónimo correspondem usando o operador "=".  Ambas exigem o operador de "igual a" seja especificado na definição do atributo.


## <a name="functions"></a>Funções

Existe um conjunto interno de funções que permite a construção de expressões de consulta mais sofisticadas de consultas de atributos básicos.

### <a name="and-function"></a>E a função

`And(expr1, expr2)`

Devolve a interseção entre duas expressões de consulta de entrada.

O exemplo seguinte devolve publicações acadêmicas publicadas no ano 2000 sobre a obtenção de informações:

`And(Year=2000, Keyword=='information retrieval')`

### <a name="or-function"></a>Ou uma função

`Or(expr1, expr2)`

Devolve a União de duas expressões de consulta de entrada.

O exemplo seguinte devolve publicações acadêmicas publicadas no ano 2000 sobre a obtenção de informações ou a Modelagem de utilizador:

`And(Year=2000, Or(Keyword='information retrieval', Keyword='user modeling'))`

### <a name="composite-function"></a>Função composta

`Composite(expr)`

Devolve uma expressão que encapsula uma expressão interna é composto por consultas nos atributos de frações de um atributo composto comuns.  O encapsulamento requer o atributo composto de qualquer objeto de dados correspondente para ter, pelo menos, um valor que individualmente satisfaz a expressão interna.  Tenha em atenção que uma expressão de consulta em atributos de frações de um atributo composto tem de ser encapsuladas com a função de Composite() antes de ele pode ser combinado com outras expressões de consulta.

Por exemplo, a seguinte expressão retorna publicações acadêmicas por "harry shum" enquanto esteve na "microsoft":

```
Composite(And(Author.Name="harry shum", 
              Author.Affiliation="microsoft"))
```

Por outro lado, a seguinte expressão retorna publicações acadêmicas em que é um dos autores "harry shum" e uma das afiliações é "microsoft":

```
And(Composite(Author.Name="harry shum"), 
    Composite(Author.Affiliation="microsoft"))
```
