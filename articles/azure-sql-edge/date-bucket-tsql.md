---
title: Date_Bucket (Transact-SQL) - Borda SQL Azure (Pré-visualização)
description: Saiba mais sobre a utilização de Date_Bucket em Azure SQL Edge (Pré-visualização)
keywords: Date_Bucket, SQL Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 7cedc19c1b2a19fb26677a9426300d19c5396bd4
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682744"
---
# <a name="date_bucket-transact-sql"></a>Date_Bucket (Transact-SQL)

Esta função devolve o valor da data correspondente ao início de cada balde de data, a partir do valor de origem padrão de `1900-01-01 00:00:00.000` .

Consulte os tipos e funções de data de [data e hora &#40;&#41;Transact-SQL](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql/) para uma visão geral de todos os tipos e funções de data e hora transact-SQL.

[Convenções de Sintaxe Transact-SQL](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql/)

`DATE_BUCKET`utiliza um valor de data de origem padrão de `1900-01-01 00:00:00.000` 12:00 am na segunda-feira, 1 de janeiro de 1900.

## <a name="syntax"></a>Sintaxe

```sql
DATE_BUCKET (datePart, number, date)
```

## <a name="arguments"></a>Argumentos

*dataParte*

A parte da *data* que é utilizada com o parâmetro 'número'. Ex. Ano, mês, minuto, segundo etc.

> [!NOTE]
> `DATE_BUCKET`não aceita equivalentes variáveis definidos pelo utilizador para os argumentos *datepPart.*
  
|*dataParte*|Abreviaturas|  
|---|---|
|**dia**|**dd**, **d**|  
|**semana**|**wk,** **ww**|  
|**hora**|**hh**|  
|**minuto**|**mi,** **n**|  
|**segundo**|**ss,** **s**|  
|**milissegundo**|**ms**|  

*número*

O número inteiro que decide a largura do balde combinado com o argumento *dataPart.* Isto representa a largura dos dadosBaldes do tempo de origem. **`This argument cannot be a negative integer value`**. 

*data*

Uma expressão que pode resolver um dos seguintes valores:

+ **data**
+ **datetime**
+ **datacompensação**
+ **datetime2**
+ **tempo de data pequena**
+ **tempo**

Para *data,* `DATE_BUCKET` aceitará uma expressão, expressão ou variável definida pelo utilizador se resolverem qualquer um dos tipos de dados acima mencionados.

## <a name="return-type"></a>Tipo de devolução

O tipo de dados de valor de retorno para este método é dinâmico. O tipo de devolução depende do argumento fornecido para `date` . Se for fornecido um tipo de dados de entrada `date` válido, `DATE_BUCKET` deretfique o mesmo tipo de dados. `DATE_BUCKET`levanta um erro se for especificado um literal de cadeia para o `date` parâmetro.

## <a name="return-values"></a>Valores de Devolução

### <a name="understanding-the-output-from-date_bucket"></a>Compreender a saída de`DATE_BUCKET`

`Date_Bucket`devolve a data ou o valor da hora mais recente, correspondente à dataPart e parâmetro de número. Por exemplo, nas expressões abaixo, devolverá o valor de saída de , uma vez que `Date_Bucket` a saída é calculada com base em `2020-04-13 00:00:00.0000000` baldes de uma semana a partir do tempo de origem padrão de `1900-01-01 00:00:00.000` . O valor `2020-04-13 00:00:00.0000000` é de 6276 semanas a partir do valor de origem de `1900-01-01 00:00:00.000` . 

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 1, @date)
```

Para todas as expressões abaixo, o mesmo valor de saída `2020-04-13 00:00:00.0000000` será devolvido. Isto porque está a `2020-04-13 00:00:00.0000000` 6276 semanas da data de origem e 6276 é divisível por 2, 3, 4 e 6.

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 2, @date)
Select DATE_BUCKET(wk, 3, @date)
Select DATE_BUCKET(wk, 4, @date)
Select DATE_BUCKET(wk, 6, @date)
```

A saída para a expressão abaixo, que é de 6275 semanas a partir do tempo de origem.

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 5, @date)
```

## <a name="datepart-argument"></a>argumento da data parte

**dia do ano,** **dia,** e **dia da semana** retornar o mesmo valor. Cada *data e* as suas abreviaturas devolvem o mesmo valor.
  
## <a name="number-argument"></a>número Argumento

O argumento *número* não pode exceder o intervalo de valores **int** positivos. Nas seguintes declarações, o argumento para o *número* excede o intervalo de **int** por 1. A seguinte declaração devolve a seguinte mensagem de erro: "`Msg 8115, Level 16, State 2, Line 2. Arithmetic overflow error converting expression to data type int."`
  
```sql
declare @date datetime2 = '2020-04-30 00:00:00'
Select DATE_BUCKET(dd, 2147483648, @date)
```  

Se um valor negativo para o número for passado para a `Date_Bucket` função, o seguinte erro será devolvido. 

```sql
Msg 9834, Level 16, State 1, Line 1
Invalid bucket width value passed to date_bucket function. Only positive values are allowed.
````

## <a name="date-argument"></a>data Argumento  

`DATE_BUCKET`devolver o valor base correspondente ao tipo de dados do `date` argumento. No exemplo seguinte, é devolvido um valor de saída com datatime2 datatype. 

```sql
Select DATE_BUCKET(dd, 10, SYSUTCDATETIME())
```

## <a name="remarks"></a>Observações

Utilização `DATE_BUCKET` nas seguintes cláusulas:

+ GROUP BY
+ HAVING
+ ORDENAR POR
+ Lista selecionada \<>
+ WHERE

## <a name="examples"></a>Exemplos

### <a name="a-calculating-date_bucket-with-a-bucket-width-of-1-from-the-origin-time"></a>R. Cálculo Date_Bucket com uma largura de balde de 1 do tempo de origem

Cada uma destas declarações aumenta *date_bucket* com uma largura de balde de 1 a partir do tempo de origem:

```sql
declare @date datetime2 = '2020-04-30 21:21:21'
Select 'Week',  DATE_BUCKET(wk, 1, @date)
Union All
Select 'Day',  DATE_BUCKET(dd, 1, @date)
Union All
Select 'Hour',  DATE_BUCKET(hh, 1, @date)
Union All
Select 'Minutes',  DATE_BUCKET(mi, 1, @date)
Union All
Select 'Seconds',  DATE_BUCKET(ss, 1, @date)
```

Aqui está o conjunto de resultados.

```sql
Week    2020-04-27 00:00:00.0000000
Day     2020-04-30 00:00:00.0000000
Hour    2020-04-30 21:00:00.0000000
Minutes 2020-04-30 21:21:00.0000000
Seconds 2020-04-30 21:21:21.0000000
```

### <a name="b-using-expressions-as-arguments-for-the-number-and-date-parameters"></a>B. Usando expressões como argumentos para os parâmetros de número e data

Estes exemplos usam diferentes tipos de expressões como argumentos para os parâmetros de *número* e *data.* Estes exemplos são construídos utilizando a Base de Dados 'AdventureWorksDW2017'.
  
#### <a name="specifying-user-defined-variables-as-number-and-date"></a>Especificação de variáveis definidas pelo utilizador como número e data  

Este exemplo especifica variáveis definidas pelo utilizador como argumentos para *o número* e *data:*
  
```sql
DECLARE @days int = 365,
        @datetime datetime2 = '2000-01-01 01:01:01.1110000'; /* 2000 was a leap year */;  
SELECT Date_Bucket(day, @days, @datetime);
```

Aqui está o conjunto de resultados.

```sql
---------------------------
1999-12-08 00:00:00.0000000

(1 row affected)
```  

#### <a name="specifying-a-column-as-date"></a>Especificando uma coluna como data

No exemplo abaixo, estamos a calcular a soma da Quantidade de Encomendas e a soma da UnitPrice agruparada em baldes de data semanal.
  
```sql
SELECT
    Date_Bucket(week, 1 ,cast(Shipdate as datetime2)) AS ShippedDateBucket
    ,Sum(OrderQuantity)  As SumOrderQuantity
    ,Sum(UnitPrice) As SumUnitPrice
FROM dbo.FactInternetSales FIS
where Shipdate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
Group by Date_Bucket(week, 1 ,cast(Shipdate as datetime2))
order by 1
```  

Aqui está o conjunto de resultados.
  
```sql
ShippedDateBucket           SumOrderQuantity SumUnitPrice
--------------------------- ---------------- ---------------------
2011-01-03 00:00:00.0000000 21               65589.7546
2011-01-10 00:00:00.0000000 27               89938.5464
2011-01-17 00:00:00.0000000 31               104404.9064
2011-01-24 00:00:00.0000000 36               118525.6846
2011-01-31 00:00:00.0000000 39               123555.431
2011-02-07 00:00:00.0000000 35               109342.351
2011-02-14 00:00:00.0000000 32               107804.8964
2011-02-21 00:00:00.0000000 37               119456.3428
2011-02-28 00:00:00.0000000 9                28968.6982
```  

#### <a name="specifying-scalar-system-function-as-date"></a>Especificação da função do sistema escalar como data

Este exemplo especifica `SYSDATETIME` a *data*. O valor exato devolvido depende do dia e hora da execução da declaração:
  
```sql
SELECT Date_Bucket(wk, 10, SYSDATETIME());  
```  

Aqui está o conjunto de resultados.

```sql
---------------------------
2020-03-02 00:00:00.0000000

(1 row affected)
```  

#### <a name="specifying-scalar-subqueries-and-scalar-functions-as-number-and-date"></a>Especificação de subqueries e funções escalar como número e data

Este exemplo utiliza subqueries escalar, `MAX(OrderDate)` como argumentos para *número* e *data.* `(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100)`serve como um argumento artificial para o parâmetro de número, para mostrar como selecionar um argumento *numérica* de uma lista de valor.
  
```sql
SELECT DATE_BUCKET(week,(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100),  
    (SELECT MAX(OrderDate) FROM dbo.FactInternetSales));  
```  
  
#### <a name="specifying-numeric-expressions-and-scalar-system-functions-as-number-and-date"></a>Especificação de expressões numéricas e funções do sistema escalar como número e data

Este exemplo utiliza uma expressão numérica ((10/2)) e funções do sistema escalar (SYSDATETIME) como argumentos para o número e data.
  
```sql
SELECT Date_Bucket(week,(10/2), SYSDATETIME());
```

#### <a name="specifying-an-aggregate-window-function-as-number"></a>Especificando uma função de janela agregada como número

Este exemplo utiliza uma função de janela agregada como argumento para *o número*.
  
```sql
Select 
    DISTINCT DATE_BUCKET(day, 30, Cast([shipdate] as datetime2)) as DateBucket,
    First_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as First_Value_In_Bucket,
    Last_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as Last_Value_In_Bucket
    from [dbo].[FactInternetSales]
Where ShipDate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
order by DateBucket
GO  
``` 

## <a name="see-also"></a>Ver também

[CAST e CONVERT &#40;&#41;Transact-SQL](/sql/t-sql/functions/cast-and-convert-transact-sql/)
