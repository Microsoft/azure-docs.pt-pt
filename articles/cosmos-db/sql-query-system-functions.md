---
title: Funções de sistema
description: Saiba mais sobre as funções de sistema do SQL no Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: 11a6fdad187670bcb5af4c56198fd7343680690d
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342657"
---
# <a name="system-functions"></a>Funções de sistema

 O cosmos DB fornece muitas funções internas do SQL. As categorias de funções internas são listadas abaixo.  
  
|Função|Descrição|  
|--------------|-----------------|  
|[Funções matemáticas](#mathematical-functions)|As funções matemáticas realizar um cálculo, normalmente com base em valores de entrada que são fornecidos como argumentos e devolvem um valor numérico.|  
|[As funções de verificação de tipo](#type-checking-functions)|As funções de verificação de tipo permitem-lhe verificar o tipo de uma expressão dentro de consultas SQL.|  
|[Funções de cadeia](#string-functions)|As funções de cadeia de caracteres realizar uma operação num valor de entrada de cadeia de caracteres e retornam uma cadeia de caracteres, o valor numérico ou booleano.|  
|[Funções de matriz](#array-functions)|As funções de matriz executam uma operação num valor de entrada de matriz e o valor de matriz, booleano ou numérico de retorno.|
|[Funções de data e hora](#date-time-functions)|As funções de data e hora permitem-lhe obter o UTC data e hora atuais em dois formatos; um carimbo numérico cujo valor é a época de Unix em milissegundos ou como uma cadeia que está em conformidade com o formato ISO 8601.|
|[Funções espaciais](#spatial-functions)|As funções geográficos realizar uma operação num valor de entrada do objeto espacial e retornam um valor numérico ou booleano.|  

Seguem-se uma lista de funções dentro de cada categoria:

| Grupo de função | Operações |
|---------|----------|
| Funções matemáticas | ABS, LIMITE, EXP, ANDAR, LOG, LOG10, ENERGIA, ROUND, INÍCIO DE SESSÃO, SQRT, QUADRADO, TRUNC, FUNÇÕES ACOS, ASIN, ATAN, ATN2, COS, COT, GRAUS, PI, RADIANOS, SIN, TAN |
| Verificação do tipo de funções | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED, IS_PRIMITIVE |
| Funções de cadeia | CONCAT, CONTÉM, ENDSWITH, INDEX_OF, À ESQUERDA, COMPRIMENTO, MAIS BAIXO, LTRIM, SUBSTITUIR, REPLICAR, INVERSO, CERTO, RTRIM, STARTSWITH, SUBSTRING, SUPERIOR |
| Funções de matriz | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH e ARRAY_SLICE |
| Funções de data e hora | GETCURRENTDATETIME, GETCURRENTTIMESTAMP,  |
| Funções espaciais | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID, ST_ISVALIDDETAILED |

Se estiver a utilizar atualmente uma função definida pelo utilizador (UDF) para o qual uma função incorporada está agora disponível, a função interna correspondente será mais rápida executar e mais eficiente.

A principal diferença entre as funções do Cosmos DB e as funções ANSI SQL é que as funções do Cosmos DB foram concebidas para funcionar bem com os dados sem esquemas e esquema misto. Por exemplo, se uma propriedade está em falta ou tem um valor não numéricos como `unknown`, o item é ignorado em vez de retornar um erro.

##  <a name="mathematical-functions"></a> Funções matemáticas  

As funções matemáticas realizar um cálculo, com base nos valores de entrada que são fornecidos como argumentos e devolvem um valor numérico.

Pode executar consultas semelhante ao seguinte exemplo:

```sql
    SELECT VALUE ABS(-4)
```

O resultado é:

```json
    [4]
```

Aqui está uma tabela de funções matemáticas incorporadas.
  
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
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo seguinte mostra os resultados usando a função ABS em três números diferentes.  
  
```  
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{abs1: 1, abs2: 0, abs3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 Devolve o ângulo em radianos, cujo co-seno é a expressão numérica especificada; Também chamado de arco de cosseno.  
  
 **Sintaxe**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo seguinte devolve as funções ACOS de -1.  
  
```  
SELECT ACOS(-1) AS acos 
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"acos": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 Devolve o ângulo em radianos, cujo seno é a expressão numérica especificada. Isso também é chamado o arco de seno.  
  
 **Sintaxe**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo seguinte devolve o ASIN de -1.  
  
```  
SELECT ASIN(-1) AS asin  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"asin": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 Devolve o ângulo em radianos, cuja tangente é a expressão numérica especificada. Isso também é chamado o arco de tangente.  
  
 **Sintaxe**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo seguinte devolve ATAN do valor especificado.  
  
```  
SELECT ATAN(-45.01) AS atan  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"atan": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 Devolve o valor principal do arco tangente de y / x, expressado em radianos.  
  
 **Sintaxe**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo seguinte calcula ATN2 especificado para x e y componentes.  
  
```  
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"atn2": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> LIMITE  
 Devolve o menor valor de número inteiro maior que ou igual a, a expressão numérica especificada.  
  
 **Sintaxe**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo seguinte mostra numérico positivo, negativo e valores zero com a função de limite.  
  
```  
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{c1: 124, c2: -123, c3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 Devolve o cosseno trigonométricos do ângulo especificado, em radianos, a expressão especificada.  
  
 **Sintaxe**  
  
```  
COS(<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo seguinte calcula COS do ângulo especificado.  
  
```  
SELECT COS(14.78) AS cos  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"cos": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 Devolve a co-tangente trigonométricos do ângulo especificado, em radianos, a expressão numérica especificado.  
  
 **Sintaxe**  
  
```  
COT(<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo seguinte calcula COT do ângulo especificado.  
  
```  
SELECT COT(124.1332) AS cot  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"cot": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> GRAUS  
 Devolve o ângulo correspondente em graus para um ângulo especificado em radianos.  
  
 **Sintaxe**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo seguinte devolve o número de graus num ângulo de radianos de PI/2.  
  
```  
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"degrees": 90}]  
```  
  
####  <a name="bk_floor"></a> PISO  
 Devolve o maior número inteiro menor ou igual a expressão numérica especificada.  
  
 **Sintaxe**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo seguinte mostra numérico positivo, negativo e valores zero com a função de piso.  
  
```  
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{fl1: 123, fl2: -124, fl3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 Devolve o valor exponencial da expressão especificada numérico.  
  
 **Sintaxe**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
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
SELECT EXP(10) AS exp  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{exp: 22026.465794806718}]  
```  
  
 O exemplo seguinte devolve o valor exponencial de natural logarithm de 20 e o logaritmo natural de exponencial de 20. Uma vez que estas funções são funções inversas entre si, o valor de retorno com o arredondamento para matemática de ponto flutuante em ambos os casos é 20.  
  
```  
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{exp1: 19.999999999999996, exp2: 20}]  
```  
  
####  <a name="bk_log"></a> REGISTO  
 Devolve o logaritmo natural da expressão numérica especificado.  
  
 **Sintaxe**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
- `base`  
  
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
SELECT LOG(10) AS log  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{log: 2.3025850929940459}]  
```  
  
 O exemplo seguinte calcula o registo para o expoente de um número.  
  
```  
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{expLog: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 Devolve o logaritmo base 10 da expressão especificada numérico.  
  
 **Sintaxe**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão numérica.  
  
  **Observações**  
  
  As funções LOG10 e POWER inversamente estão relacionadas entre si. Por exemplo, 10 ^ LOG10(n) = n.  
  
  **Exemplos**  
  
  O exemplo seguinte declara uma variável e devolve o valor de LOG10 da variável especificada (100).  
  
```  
SELECT LOG10(100) AS log10 
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{log10: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 Devolve o valor da constante de PI.  
  
 **Sintaxe**  
  
```  
PI ()  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo seguinte devolve o valor de PI.  
  
```  
SELECT PI() AS pi 
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"pi": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> POWER  
 Devolve o valor da expressão especificada à potência especificada.  
  
 **Sintaxe**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
- `y`  
  
   É o poder de que elevar `numeric_expression`.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo seguinte demonstra a gerar um número à potência de 3 (o cubo do número).  
  
```  
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{pow1: 8, pow2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> RADIANOS  
 Devolve o radianos quando uma expressão numérica, em graus, que é introduzida.  
  
 **Sintaxe**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo a seguir usa alguns ângulos como entrada e retorna os valores de radian correspondentes.  
  
```  
SELECT RADIANS(-45.01) AS r1, RADIANS(-181.01) AS r2, RADIANS(0) AS r3, RADIANS(0.1472738) AS r4, RADIANS(197.1099392) AS r5  
```  
  
  Aqui está o conjunto de resultados.  
  
```  
[{  
       "r1": -0.7855726963226477,  
       "r2": -3.1592204790349356,  
       "r3": 0,  
       "r4": 0.0025704127119236249,  
       "r5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> ARREDONDAR  
 Devolve um valor numérico, arredondado para o valor de número inteiro mais próximo.  
  
 **Sintaxe**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão numérica.  
  
  **Observações**
  
  A operação de arredondamento realizada segue o ponto médio arredondamento zero. Se a entrada for uma expressão numérica que cai exatamente entre dois números inteiros, em seguida, o resultado será o valor de número inteiro mais próximo zero.  
  
  |<numeric_expression>|Arredondado|
  |-|-|
  |-6.5000|-7|
  |-0.5|-1|
  |0,5|1|
  |6.5000|7||
  
  **Exemplos**  
  
  O exemplo seguinte arredonda os números positivos e negativos a seguir para o número inteiro mais próximo.  
  
```  
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Aqui está o conjunto de resultados.  
  
```  
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  
  
####  <a name="bk_sign"></a> INÍCIO DE SESSÃO  
 Devolve o positivo (+ 1), zero (0) ou sinal negativo de (-1) da expressão especificada numérico.  
  
 **Sintaxe**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo seguinte devolve os valores de início de sessão de números de -2 de 2.  
  
```  
SELECT SIGN(-2) AS s1, SIGN(-1) AS s2, SIGN(0) AS s3, SIGN(1) AS s4, SIGN(2) AS s5  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{s1: -1, s2: -1, s3: 0, s4: 1, s5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 Devolve o seno trigonométricos do ângulo especificado, em radianos, a expressão especificada.  
  
 **Sintaxe**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo seguinte calcula o PECADO do ângulo especificado.  
  
```  
SELECT SIN(45.175643) AS sin  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"sin": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 Devolve a raiz quadrada do valor numérico especificado.  
  
 **Sintaxe**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo seguinte devolve as raízes quadradas de números de 1 a 3.  
  
```  
SELECT SQRT(1) AS s1, SQRT(2.0) AS s2, SQRT(3) AS s3  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{s1: 1, s2: 1.4142135623730952, s3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> QUADRADO  
 Devolve o quadrado do valor numérico especificado.  
  
 **Sintaxe**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo seguinte devolve os quadrados de números de 1 a 3.  
  
```  
SELECT SQUARE(1) AS s1, SQUARE(2.0) AS s2, SQUARE(3) AS s3  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{s1: 1, s2: 4, s3: 9}]  
```  
  
####  <a name="bk_tan"></a> TAN  
 Devolve a tangente do ângulo especificado, em radianos, a expressão especificada.  
  
 **Sintaxe**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo seguinte calcula a tangente do instalador de plataforma (-) / 2.  
  
```  
SELECT TAN(PI()/2) AS tan 
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"tan": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> TRUNC  
 Devolve um valor numérico, truncado para o valor de número inteiro mais próximo.  
  
 **Sintaxe**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo seguinte trunca os números positivos e negativos a seguir para o valor de número inteiro mais próximo.  
  
```  
SELECT TRUNC(2.4) AS t1, TRUNC(2.6) AS t2, TRUNC(2.5) AS t3, TRUNC(-2.4) AS t4, TRUNC(-2.6) AS t5  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{t1: 2, t2: 2, t3: 2, t4: -2, t5: -2}]  
```

## <a id="type-checking-functions"></a>As funções de verificação de tipo

As funções de verificação de tipo permitem-lhe verificar o tipo de uma expressão dentro de uma consulta SQL. Pode utilizar funções de verificação de tipo para determinar os tipos de propriedades em itens rapidamente, quando estiverem variável ou desconhecido. Esta é uma tabela de suportadas funções internas de verificação de tipo:

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
  
- `expression`  
  
   É qualquer expressão válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão booleana.  
  
  **Exemplos**  
  
  O exemplo seguinte verifica os objetos de JSON booleano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_ARRAY.  
  
```  
SELECT   
 IS_ARRAY(true) AS isArray1,   
 IS_ARRAY(1) AS isArray2,  
 IS_ARRAY("value") AS isArray3,  
 IS_ARRAY(null) AS isArray4,  
 IS_ARRAY({prop: "value"}) AS isArray5,   
 IS_ARRAY([1, 2, 3]) AS isArray6,  
 IS_ARRAY({prop: "value"}.prop2) AS isArray7  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"isArray1":false,"isArray2":false,"isArray3":false,"isArray4":false,"isArray5":false,"isArray6":true,"isArray7":false}]
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 Devolve um valor Booleano indicando se o tipo da expressão especificada é um booleano.  
  
 **Sintaxe**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **Argumentos**  
  
- `expression`  
  
   É qualquer expressão válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão booleana.  
  
  **Exemplos**  
  
  O exemplo seguinte verifica os objetos de JSON booleano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_BOOL.  
  
```  
SELECT   
    IS_BOOL(true) AS isBool1,   
    IS_BOOL(1) AS isBool2,  
    IS_BOOL("value") AS isBool3,   
    IS_BOOL(null) AS isBool4,  
    IS_BOOL({prop: "value"}) AS isBool5,   
    IS_BOOL([1, 2, 3]) AS isBool6,  
    IS_BOOL({prop: "value"}.prop2) AS isBool7  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"isBool1":true,"isBool2":false,"isBool3":false,"isBool4":false,"isBool5":false,"isBool6":false,"isBool7":false}]
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 Devolve um valor booleano que indica se a propriedade foi atribuída um valor.  
  
 **Sintaxe**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **Argumentos**  
  
- `expression`  
  
   É qualquer expressão válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão booleana.  
  
  **Exemplos**  
  
  O exemplo seguinte verifica a presença de uma propriedade dentro do documento JSON especificado. O primeiro método retorna true, uma vez que "a" está presente, mas o segundo devolve false, uma vez que "b" está ausente.  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"isDefined1":true,"isDefined2":false}]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 Devolve um valor Booleano indicando se o tipo da expressão especificada é nulo.  
  
 **Sintaxe**  
  
```  
IS_NULL(<expression>)  
```  
  
 **Argumentos**  
  
- `expression`  
  
   É qualquer expressão válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão booleana.  
  
  **Exemplos**  
  
  O exemplo seguinte verifica os objetos de JSON booleano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_NULL.  
  
```  
SELECT   
    IS_NULL(true) AS isNull1,   
    IS_NULL(1) AS isNull2,  
    IS_NULL("value") AS isNull3,   
    IS_NULL(null) AS isNull4,  
    IS_NULL({prop: "value"}) AS isNull5,   
    IS_NULL([1, 2, 3]) AS isNull6,  
    IS_NULL({prop: "value"}.prop2) AS isNull7  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"isNull1":false,"isNull2":false,"isNull3":false,"isNull4":true,"isNull5":false,"isNull6":false,"isNull7":false}]
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 Devolve um valor Booleano indicando se o tipo da expressão especificada é um número.  
  
 **Sintaxe**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **Argumentos**  
  
- `expression`  
  
   É qualquer expressão válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão booleana.  
  
  **Exemplos**  
  
  O exemplo seguinte verifica os objetos de JSON booleano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_NULL.  
  
```  
SELECT   
    IS_NUMBER(true) AS isNum1,   
    IS_NUMBER(1) AS isNum2,  
    IS_NUMBER("value") AS isNum3,   
    IS_NUMBER(null) AS isNum4,  
    IS_NUMBER({prop: "value"}) AS isNum5,   
    IS_NUMBER([1, 2, 3]) AS isNum6,  
    IS_NUMBER({prop: "value"}.prop2) AS isNum7  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"isNum1":false,"isNum2":true,"isNum3":false,"isNum4":false,"isNum5":false,"isNum6":false,"isNum7":false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT  
 Devolve um valor Booleano indicando se o tipo da expressão especificada é um objeto JSON.  
  
 **Sintaxe**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **Argumentos**  
  
- `expression`  
  
   É qualquer expressão válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão booleana.  
  
  **Exemplos**  
  
  O exemplo seguinte verifica os objetos de JSON booleano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_OBJECT.  
  
```  
SELECT   
    IS_OBJECT(true) AS isObj1,   
    IS_OBJECT(1) AS isObj2,  
    IS_OBJECT("value") AS isObj3,   
    IS_OBJECT(null) AS isObj4,  
    IS_OBJECT({prop: "value"}) AS isObj5,   
    IS_OBJECT([1, 2, 3]) AS isObj6,  
    IS_OBJECT({prop: "value"}.prop2) AS isObj7  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"isObj1":false,"isObj2":false,"isObj3":false,"isObj4":false,"isObj5":true,"isObj6":false,"isObj7":false}]
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 Devolve um valor Booleano indicando se o tipo da expressão especificada é um primitivo (string, booleano, numéricos ou nulo).  
  
 **Sintaxe**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **Argumentos**  
  
- `expression`  
  
   É qualquer expressão válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão booleana.  
  
  **Exemplos**  
  
  O exemplo seguinte verifica os objetos de JSON booleano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_PRIMITIVE.  
  
```  
SELECT   
           IS_PRIMITIVE(true) AS isPrim1,   
           IS_PRIMITIVE(1) AS isPrim2,  
           IS_PRIMITIVE("value") AS isPrim3,   
           IS_PRIMITIVE(null) AS isPrim4,  
           IS_PRIMITIVE({prop: "value"}) AS isPrim5,   
           IS_PRIMITIVE([1, 2, 3]) AS isPrim6,  
           IS_PRIMITIVE({prop: "value"}.prop2) AS isPrim7  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"isPrim1": true, "isPrim2": true, "isPrim3": true, "isPrim4": true, "isPrim5": false, "isPrim6": false, "isPrim7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 Devolve um valor Booleano indicando se o tipo da expressão especificada é uma cadeia de caracteres.  
  
 **Sintaxe**  
  
```  
IS_STRING(<expression>)  
```  
  
 **Argumentos**  
  
- `expression`  
  
   É qualquer expressão válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão booleana.  
  
  **Exemplos**  
  
  O exemplo seguinte verifica os objetos de JSON booleano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_STRING.  
  
```  
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  

## <a id="string-functions"></a>Funções de cadeia

As seguintes funções escalares realizar uma operação num valor de entrada de cadeia de caracteres e retornam um valor de cadeia, numéricos ou booleanos:
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[CONTÉM](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[À ESQUERDA](#bk_left)|[LENGTH](#bk_length)|  
|[INFERIOR](#bk_lower)|[LTRIM](#bk_ltrim)|[SUBSTITUIR](#bk_replace)|  
|[REPLICAR](#bk_replicate)|[INVERTER](#bk_reverse)|[DIREITA](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[StringToArray](#bk_stringtoarray)|
|[StringToBoolean](#bk_stringtoboolean)|[StringToNull](#bk_stringtonull)|[StringToNumber](#bk_stringtonumber)|
|[StringToObject](#bk_stringtoobject)|[SUBCADEIA](#bk_substring)|[ToString](#bk_tostring)|
|[TRIM](#bk_trim)|[SUPERIOR](#bk_upper)||
  
####  <a name="bk_concat"></a> CONCAT  
 Devolve uma cadeia que é o resultado da concatenação de dois ou mais valores de cadeia de caracteres.  
  
 **Sintaxe**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão de cadeia.  
  
  **Exemplos**  
  
  O exemplo seguinte devolve a cadeia de caracteres concatenada dos valores especificados.  
  
```  
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"concat": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> CONTÉM  
 Retorna um Booleano indicando se a primeira cadeia de expressão contém o segundo.  
  
 **Sintaxe**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão booleana.  
  
  **Exemplos**  
  
  O exemplo a seguir verifica se "abc" contém "ab" e "d".  
  
```  
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"c1": true, "c2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 Retorna um Booleano indicando se a primeira expressão de cadeia termina com a segunda.  
  
 **Sintaxe**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão booleana.  
  
  **Exemplos**  
  
  O exemplo seguinte devolve "abc" termina com "b" e "bc".  
  
```  
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"e1": false, "e2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 Devolve a posição inicial da primeira ocorrência da segunda cadeia de expressão dentro da primeira expressão de cadeia especificada ou -1 se a cadeia de caracteres não foi encontrada.  
  
 **Sintaxe**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo seguinte devolve o índice de várias subseqüências dentro de "abc".  
  
```  
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"i1": 0, "i2": 1, "i3": -1}]  
```  
  
####  <a name="bk_left"></a> À ESQUERDA  
 Devolve a parte esquerda de uma cadeia com o número especificado de carateres.  
  
 **Sintaxe**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
- `num_expr`  
  
   É qualquer expressão numérica válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão de cadeia.  
  
  **Exemplos**  
  
  O exemplo seguinte devolve a parte esquerda de "abc" para vários valores de comprimento.  
  
```  
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"l1": "a", "l2": "ab"}]  
```  
  
####  <a name="bk_length"></a> COMPRIMENTO  
 Devolve o número de carateres da expressão de cadeia especificada.  
  
 **Sintaxe**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão de cadeia.  
  
  **Exemplos**  
  
  O exemplo seguinte devolve o comprimento de uma cadeia de caracteres.  
  
```  
SELECT LENGTH("abc") AS len 
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_lower"></a> INFERIOR  
 Devolve uma expressão de cadeia de caracteres após a conversão de dados de caráter em maiúsculas em minúsculas.  
  
 **Sintaxe**  
  
```  
LOWER(<str_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão de cadeia.  
  
  **Exemplos**  
  
  O exemplo seguinte mostra como utilizar o mais baixo numa consulta.  
  
```  
SELECT LOWER("Abc") AS lower
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"lower": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> LTRIM  
 Devolve uma expressão de cadeia de caracteres depois que ele remove espaços em branco à esquerda.  
  
 **Sintaxe**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão de cadeia.  
  
  **Exemplos**  
  
  O exemplo seguinte mostra como utilizar LTRIM dentro de uma consulta.  
  
```  
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> SUBSTITUIR  
 Substitui todas as ocorrências de um valor de cadeia especificada com outro valor de cadeia de caracteres.  
  
 **Sintaxe**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão de cadeia.  
  
  **Exemplos**  
  
  O exemplo seguinte mostra como usar REPLACE numa consulta.  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"replace": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLICAR  
 Repete-se um valor de cadeia de caracteres um número de vezes especificado.  
  
 **Sintaxe**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
- `num_expr`  
  
   É qualquer expressão numérica válida. Se num_expr é negativo ou não infinite, o resultado é indefinido.

  > [!NOTE]
  > O comprimento máximo do resultado é 10.000 carateres, ou seja, (length(str_expr) * num_expr) < = 10 000.
  
  **Tipos de retorno**  
  
  Devolve uma expressão de cadeia.  
  
  **Exemplos**  
  
  O exemplo seguinte mostra como usar a REPLICAR numa consulta.  
  
```  
SELECT REPLICATE("a", 3) AS replicate  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"replicate": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> INVERTER  
 Devolve a ordem inversa de um valor de cadeia de caracteres.  
  
 **Sintaxe**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão de cadeia.  
  
  **Exemplos**  
  
  O exemplo seguinte mostra como utilizar o INVERSO numa consulta.  
  
```  
SELECT REVERSE("Abc") AS reverse  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"reverse": "cbA"}]  
```  
  
####  <a name="bk_right"></a> DIREITA  
 Devolve a parte direita de uma cadeia de caracteres com o número especificado de carateres.  
  
 **Sintaxe**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
- `num_expr`  
  
   É qualquer expressão numérica válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão de cadeia.  
  
  **Exemplos**  
  
  O exemplo seguinte devolve a parte direita de "abc" para vários valores de comprimento.  
  
```  
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"r1": "c", "r2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> RTRIM  
 Devolve uma expressão de cadeia de caracteres depois que ele remove espaços em branco à direita.  
  
 **Sintaxe**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão de cadeia.  
  
  **Exemplos**  
  
  O exemplo seguinte mostra como utilizar RTRIM dentro de uma consulta.  
  
```  
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 Retorna um Booleano indicando se a primeira expressão de cadeia começa com a segunda.  
  
 **Sintaxe**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão booleana.  
  
  **Exemplos**  
  
  O exemplo a seguir verifica se a cadeia de caracteres "abc" começa com "b" e "a".  
  
```  
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"s1": false, "s2": true}]  
```  

  ####  <a name="bk_stringtoarray"></a> StringToArray  
 Devolve a expressão convertido numa matriz. Se a expressão não pode ser convertida, retornará indefinida.  
  
 **Sintaxe**  
  
```  
StringToArray(<expr>)  
```  
  
 **Argumentos**  
  
- `expr`  
  
   É qualquer expressão escalar válida para ser avaliada como uma expressão de matriz JSON. Tenha em atenção que os valores de cadeia de caracteres aninhados têm de ser escritos com aspas duplas para ser válida. Para obter detalhes sobre o formato JSON, veja [json.org](https://json.org/)
  
  **Tipos de retorno**  
  
  Devolve uma expressão de matriz ou não definida.  
  
  **Exemplos**  
  
  O exemplo seguinte mostra como StringToArray se comporta vários tipos diferentes. 
  
 Seguem-se exemplos com uma entrada válida.

```
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Aqui está o conjunto de resultados.

```
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Segue-se um exemplo de entrada inválida. 
   
 As aspas simples dentro da matriz não são um JSON válido.
Mesmo que sejam válidas dentro de uma consulta, não irá analisar a matrizes válidas. Também devem ser escritas cadeias de caracteres dentro da cadeia de matriz "[\\"\\"]" ou a citação ao redor tem de ser única "[" "]".

```
SELECT
    StringToArray("['5','6','7']")
```

Aqui está o conjunto de resultados.

```
[{}]
```

Seguem-se exemplos de entrada inválido.
   
 A expressão passada será analisada como uma matriz JSON; o seguinte não avalia para o tipo de matriz e, portanto, retornar indefinido.
   
```
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

Aqui está o conjunto de resultados.

```
[{}]
```

####  <a name="bk_stringtoboolean"></a> StringToBoolean  
 Devolve a expressão convertido para booleano. Se a expressão não pode ser convertida, retornará indefinida.  
  
 **Sintaxe**  
  
```  
StringToBoolean(<expr>)  
```  
  
 **Argumentos**  
  
- `expr`  
  
   É qualquer expressão escalar válida para ser avaliada como uma expressão booleana.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão booleana ou não definida.  
  
  **Exemplos**  
  
  O exemplo seguinte mostra como StringToBoolean se comporta vários tipos diferentes. 
 
 Seguem-se exemplos com uma entrada válida.

Espaço em branco é permitido apenas antes ou depois de "true"/ "false".

```  
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"b1": true, "b2": false, "b3": false}]
```  

Seguem-se exemplos com entrada inválida.

 Booleanos são sensíveis a maiúsculas e minúsculas e têm de ser escritos com todos os carateres em minúsculas, ou seja, "true" e "falsos".

```  
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Aqui está o conjunto de resultados.  
  
```  
[{}]
``` 

A expressão passada será analisada como uma expressão booleana; Estas entradas não avaliam tipo Booleano e, portanto, retornar indefinido.

```  
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

Aqui está o conjunto de resultados.  
  
```  
[{}]
```  

####  <a name="bk_stringtonull"></a> StringToNull  
 Devolve a expressão traduzido como null. Se a expressão não pode ser convertida, retornará indefinida.  
  
 **Sintaxe**  
  
```  
StringToNull(<expr>)  
```  
  
 **Argumentos**  
  
- `expr`  
  
   É qualquer expressão escalar válida para ser avaliada como uma expressão nulo.
  
  **Tipos de retorno**  
  
  Devolve uma expressão de nulo ou indefinido.  
  
  **Exemplos**  
  
  O exemplo seguinte mostra como StringToNull se comporta vários tipos diferentes. 

Seguem-se exemplos com uma entrada válida.

 Espaço em branco é permitido apenas antes ou depois de "null".

```  
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"n1": null, "n2": null, "n3": true}]
```  

Seguem-se exemplos com entrada inválida.

NULL diferencia maiúsculas de minúsculas e têm de ser escrito com todos os carateres em minúsculas, ou seja, "nulos".

```  
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{}]
```  

A expressão passada será analisada como uma expressão nulo; Estas entradas não avaliam escreva nulo e, portanto, retornar indefinido.

```  
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{}]
```  

####  <a name="bk_stringtonumber"></a> StringToNumber  
 Devolve a expressão traduzida para um número. Se a expressão não pode ser convertida, retornará indefinida.  
  
 **Sintaxe**  
  
```  
StringToNumber(<expr>)  
```  
  
 **Argumentos**  
  
- `expr`  
  
   É qualquer expressão escalar válida para ser avaliada como uma expressão de número de JSON. Números no JSON tem de ser um número inteiro ou um ponto flutuante. Para obter detalhes sobre o formato JSON, veja [json.org](https://json.org/)  
  
  **Tipos de retorno**  
  
  Devolve uma expressão de número ou não definida.  
  
  **Exemplos**  
  
  O exemplo seguinte mostra como StringToNumber se comporta vários tipos diferentes. 

Espaço em branco é permitido apenas antes ou depois do número.

```  
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Aqui está o conjunto de resultados.  
  
```  
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

Em JSON, um número válido tem de ser optar por ser um número inteiro ou flutuante ponto número.

```  
SELECT   
    StringToNumber("0xF")
```  
  
 Aqui está o conjunto de resultados.  
  
```  
{{}}
```  

A expressão passada será analisada como uma expressão numérica; Estas entradas não avaliam escreva o número e, portanto, retornar indefinido. 

```  
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 Aqui está o conjunto de resultados.  
  
```  
{{}}
```  

####  <a name="bk_stringtoobject"></a> StringToObject  
 Devolve a expressão traduzida para um objeto. Se a expressão não pode ser convertida, retornará indefinida.  
  
 **Sintaxe**  
  
```  
StringToObject(<expr>)  
```  
  
 **Argumentos**  
  
- `expr`  
  
   É qualquer expressão escalar válida para ser avaliada como uma expressão de objeto JSON. Tenha em atenção que os valores de cadeia de caracteres aninhados têm de ser escritos com aspas duplas para ser válida. Para obter detalhes sobre o formato JSON, veja [json.org](https://json.org/)  
  
  **Tipos de retorno**  
  
  Devolve uma expressão de objeto ou não definida.  
  
  **Exemplos**  
  
  O exemplo seguinte mostra como StringToObject se comporta vários tipos diferentes. 
  
 Seguem-se exemplos com uma entrada válida.

``` 
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

Aqui está o conjunto de resultados.

```
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 Seguem-se exemplos com entrada inválida.
Mesmo que sejam válidas dentro de uma consulta, não irá analisar a objetos válidos. Também devem ser escritas cadeias de caracteres na cadeia do objeto "{\\" uma\\":\\" str\\"}" ou a citação ao redor tem de ser única "{"a":"str"}".

Aspas simples ao redor de nomes de propriedades não são um JSON válido.

``` 
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Aqui está o conjunto de resultados.

```  
[{}]
```  

Nomes de propriedade sem aspas ao redor não são um JSON válido.

``` 
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Aqui está o conjunto de resultados.

```  
[{}]
``` 

Seguem-se exemplos com entrada inválida.

 A expressão passada será analisada como um objeto JSON; Estas entradas não avaliam para o tipo de objeto e, portanto, retornar indefinido.

``` 
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 Aqui está o conjunto de resultados.

```
[{}]
```

####  <a name="bk_substring"></a> SUBCADEIA  
 Devolve a parte de uma expressão de cadeia de caracteres a partir da posição caractere especificado baseado em zero e continua ao comprimento especificado, ou ao fim da cadeia de caracteres.  
  
 **Sintaxe**  
  
```  
SUBSTRING(<str_expr>, <num_expr>, <num_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
- `num_expr`  
  
   É qualquer expressão numérica válida para denotar o caráter de início e de fim.    
  
  **Tipos de retorno**  
  
  Devolve uma expressão de cadeia.  
  
  **Exemplos**  
  
  O exemplo seguinte devolve a subcadeia de "abc" começando em 1 e durante um período de 1 caráter.  
  
```  
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"substring": "b"}]  
```  
####  <a name="bk_tostring"></a> ToString  
 Devolve uma representação de cadeia de expressão escalar. 
  
 **Sintaxe**  
  
```  
ToString(<expr>)
```  
  
 **Argumentos**  
  
- `expr`  
  
   É qualquer expressão escalar válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão de cadeia.  
  
  **Exemplos**  
  
  O exemplo seguinte mostra como ToString se comporta vários tipos diferentes.   
  
```  
SELECT 
    ToString(1.0000) AS str1, 
    ToString("Hello World") AS str2, 
    ToString(NaN) AS str3, 
    ToString(Infinity) AS str4,
    ToString(IS_STRING(ToString(undefined))) AS str5, 
    ToString(0.1234) AS str6, 
    ToString(false) AS str7, 
    ToString(undefined) AS str8
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"str1": "1", "str2": "Hello World", "str3": "NaN", "str4": "Infinity", "str5": "false", "str6": "0.1234", "str7": "false"}]  
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
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão de cadeia.  
  
  **Exemplos**  
  
  O exemplo seguinte mostra como utilizar TRIM dentro de uma consulta.  
  
```  
SELECT TRIM("   abc") AS t1, TRIM("   abc   ") AS t2, TRIM("abc   ") AS t3, TRIM("abc") AS t4
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"t1": "abc", "t2": "abc", "t3": "abc", "t4": "abc"}]  
``` 
####  <a name="bk_upper"></a> SUPERIOR  
 Devolve uma expressão de cadeia de caracteres após a conversão de dados de caráter em minúsculas em maiúsculas.  
  
 **Sintaxe**  
  
```  
UPPER(<str_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão de cadeia.  
  
  **Exemplos**  
  
  O exemplo seguinte mostra como usar o canto superior numa consulta  
  
```  
SELECT UPPER("Abc") AS upper  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"upper": "ABC"}]  
```

## <a id="array-functions"></a>Funções de matriz

As seguintes funções escalares realizar uma operação num valor de entrada de matriz e a numérico de retorno, o valor de booleano ou matriz:
  
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
  
- `arr_expr`  
  
   É qualquer expressão de matriz válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão de matriz.  
  
  **Exemplos**  
  
  O exemplo a seguir como concatenar duas matrizes.  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a> ARRAY_CONTAINS  
Devolve um valor booleano que indica se a matriz contém o valor especificado. Pode procurar uma correspondência parcial ou completa de um objeto ao utilizar uma expressão booleana dentro do comando. 

**Sintaxe**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **Argumentos**  
  
- `arr_expr`  
  
   É qualquer expressão de matriz válida.  
  
- `expr`  
  
   É qualquer expressão válida.  

- `bool_expr`  
  
   É qualquer expressão booleana. Se estiver definido como ' true'and se o valor de pesquisa especificada é um objeto, que o comando verifica a existência de uma correspondência parcial (o objeto de pesquisa é um subconjunto de um dos objetos). Se estiver definido como 'false', o comando verifica a existência de uma correspondência completa de todos os objetos dentro da matriz. O valor predefinido se não for especificado é false. 
  
  **Tipos de retorno**  
  
  Devolve um valor booleano.  
  
  **Exemplos**  
  
  O exemplo a seguir como verificar a existência de associação numa matriz usando ARRAY_CONTAINS.  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"b1": true, "b2": false}]  
```  

O exemplo a seguir como verificar a existência de uma correspondência parcial de um JSON numa matriz usando ARRAY_CONTAINS.  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{
  "b1": true,
  "b2": false,
  "b3": false
}] 
```  
  
####  <a name="bk_array_length"></a> ARRAY_LENGTH  
 Devolve o número de elementos da expressão de matriz especificada.  
  
 **Sintaxe**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **Argumentos**  
  
- `arr_expr`  
  
   É qualquer expressão de matriz válida.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo a seguir como obter o comprimento de uma matriz usando ARRAY_LENGTH.  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_array_slice"></a> ARRAY_SLICE  
 Devolve a parte de uma expressão de matriz.
  
 **Sintaxe**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argumentos**  
  
- `arr_expr`  
  
   É qualquer expressão de matriz válida.  
  
- `num_expr`  
  
   Índice numérico baseado em zero na qual deve começar a matriz. Valores negativos podem ser utilizados para especificar o índice de início em relação ao último elemento de matriz, ou seja, -1 referencia o último elemento na matriz.  

- `num_expr`  

   Número máximo de elementos na matriz resultante.    

  **Tipos de retorno**  
  
  Devolve uma expressão de matriz.  
  
  **Exemplos**  
  
  O exemplo seguinte mostra como obter setores diferentes de uma matriz usando ARRAY_SLICE.  
  
```  
SELECT
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  
## <a id="date-time-functions"></a>Data e a função de tempo

As seguintes funções escalares permitem-lhe obter o UTC data e hora atuais em dois formatos; um carimbo numérico cujo valor é a época de Unix em milissegundos ou como uma cadeia que está em conformidade com o formato ISO 8601. 

|||
|-|-|
|[GETCURRENTDATETIME](#bk_get_current_date_time)|[GETCURRENTTIMESTAMP](#bk_get_current_timestamp)||

####  <a name="bk_get_current_date_time"></a> GETCURRENTDATETIME
 Devolve o UTC data e hora atuais como uma cadeia de caracteres ISO 8601.
  
 **Sintaxe**
  
```
GETCURRENTDATETIME ()
```
  
  **Tipos de retorno**
  
  Devolve o atual UTC data e hora ISO 8601 valor da cadeia. 

  Este valor é expresso no formato AAAA-MM-DDThh:mm:ss.sssZ onde:
  
  |||
  |-|-|
  |AAAA|ano de quatro dígitos|
  |MM|mês de dois dígitos (01 = Janeiro, etc.)|
  |DD|dia de dois dígitos do mês (01 a 31)|
  |T|signifier da partir de elementos de tempo|
  |hh|hora de dois dígitos (00 a 23)|
  |mm|minutos de dois dígitos (00 a 59)|
  |ss|segundos de dois dígitos (00 a 59)|
  |.sss|três dígitos do número decimal frações de segundo|
  |Z|Designador de UTC (hora de Universal Coordenada)||
  
  Para obter mais detalhes sobre o formato ISO 8601, consulte [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

  **Observações**

  GETCURRENTDATETIME é uma função não determinística. 
  
  O resultado retornado é UTC (hora de Universal Coordenada).

  **Exemplos**  
  
  O exemplo seguinte mostra como obter a hora de data UTC atual usando a função incorporada GetCurrentDateTime.
  
```  
SELECT GETCURRENTDATETIME() AS currentUtcDateTime
```  
  
 Aqui está um conjunto de resultados de exemplo.
  
```  
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.784Z"
}]  
```  

####  <a name="bk_get_current_timestamp"></a> GETCURRENTTIMESTAMP
 Devolve o número de milissegundos decorridos desde 00:00:00 Quinta-feira, 1 de Janeiro de 1970. 
  
 **Sintaxe**  
  
```  
GETCURRENTTIMESTAMP ()  
```  
  
  **Tipos de retorno**  
  
  Devolve um valor numérico, o número atual de milissegundos decorridos desde a época de Unix, ou seja, o número de milissegundos decorridos desde 00:00:00 Quinta-feira, 1 de Janeiro de 1970.

  **Observações**

  GETCURRENTTIMESTAMP é uma função não determinística.
  
  O resultado retornado é UTC (hora de Universal Coordenada).

  **Exemplos**  
  
  O exemplo seguinte mostra como obter o timestamp atual usando a função incorporada GetCurrentTimestamp.
  
```  
SELECT GETCURRENTTIMESTAMP() AS currentUtcTimestamp
```  
  
 Aqui está um conjunto de resultados de exemplo.
  
```  
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a id="spatial-functions"></a>Funções espaciais

O cosmos DB suporta as seguintes funções internas de Open Geoespacial Consortium (OGC) para consultar dados geoespaciais. As seguintes funções escalares realizar uma operação num valor de entrada do objeto espacial e retornam um valor numérico ou booleano.  
  
|||||
|-|-|-|-|
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)||||
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 Devolve a distância entre as duas expressões LineString, Polygon ou GeoJSON ponto.  
  
 **Sintaxe**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumentos**  
  
- `spatial_expr`  
  
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
  
- `spatial_expr`  
  
   É qualquer expressão de objeto GeoJSON ponto, Polygon ou LineString válida.  
 
- `spatial_expr`  
  
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
  
- `spatial_expr`  
  
   É qualquer expressão de objeto GeoJSON ponto, Polygon ou LineString válida.  
 
- `spatial_expr`  
  
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
  
- `spatial_expr`  
  
   É qualquer expressão LineString, Polygon ou GeoJSON ponto válido.  
  
  **Tipos de retorno**  
  
  Devolve uma expressão booleana.  
  
  **Exemplos**  
  
  O exemplo seguinte mostra como verificar se um ponto é válido quando utiliza ST_VALID.  
  
  Por exemplo, este ponto tem um valor de latitude não for no intervalo válido de valores [-90, 90], por isso, a consulta devolve false.  
  
  Para polígonos, a especificação GeoJSON requer que o último par coordenado fornecido deve ser o mesmo que o primeiro, para criar uma forma fechada. Pontos de dentro de um polígono tem de ser especificados na ordem no sentido. Um polígono especificado por ordem para a direita representa o inverso da região na mesma.  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{ "b": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 Devolve um valor JSON que contém um valor booleano valor se a expressão de LineString, Polygon ou GeoJSON ponto especificada é válida e se for inválido, além do motivo pelo qual como um valor de cadeia de caracteres.  
  
 **Sintaxe**  
  
```  
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
 **Argumentos**  
  
- `spatial_expr`  
  
   É qualquer expressão de ponto ou de polígono de GeoJSON válida.  
  
  **Tipos de retorno**  
  
  Devolve um valor JSON que contém um valor booleano valor se a expressão de ponto ou de polígono de GeoJSON especificada é válida e se for inválido, além do motivo pelo qual como um valor de cadeia de caracteres.  
  
  **Exemplos**  
  
  O exemplo a seguir como verificar a validade (com detalhes) usando ST_ISVALIDDETAILED.  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 Aqui está o conjunto de resultados.  
  
```  
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>Passos Seguintes

- [Introdução ao Azure Cosmos DB](introduction.md)
- [UDFs](sql-query-udfs.md)
- [Agregados](sql-query-aggregates.md)