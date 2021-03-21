---
title: Guia de programabilidade de combinação definido pelo utilizador U-SQL para O Lago de Dados Azure
description: Saiba mais sobre o guia de programabilidade UDO UDO UDO UDO - combinador definido pelo utilizador.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: a6c560cf4ec11197183711656d69024591e7008c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96512697"
---
# <a name="use-user-defined-combiner"></a>Utilizar o combinador definido pelo utilizador

## <a name="u-sql-udo-user-defined-combiner"></a>U-SQL UDO: combinador definido pelo utilizador
O combinador definido pelo utilizador, ou UDC, permite-lhe combinar linhas de linhas esquerda e direita, com base na lógica personalizada. O combinador definido pelo utilizador é usado com a expressão COMBINE.

## <a name="how-to-define-and-use-user-defined-combiner"></a>Como definir e usar o combinador definido pelo utilizador

Um combinador está sendo invocado com a expressão COMBINE que fornece as informações necessárias sobre ambos os conjuntos de entradas, as colunas de agrupamento, o esquema de resultados esperado, e informações adicionais.

Para chamar um combinador num script U-SQL base, usamos a seguinte sintaxe:

```usql
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

Para obter mais informações, consulte [a expressão COMBINE (U-SQL)](/u-sql/statements-and-expressions/combine-expression).

Para definir um combinador definido pelo utilizador, precisamos criar a `ICombiner` interface com o atributo [ ] , que é opcional para uma `SqlUserDefinedCombiner` definição de Combiner definida pelo utilizador.

Definição da `ICombiner` classe base:

```csharp
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

A implementação personalizada de uma `ICombiner` interface deve conter a definição para uma `IEnumerable<IRow>` sobreposição de Combine.

```csharp
[SqlUserDefinedCombiner]
public class MyCombiner : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    …
}
}
```

O atributo **SqlUserDefinedCombiner** indica que o tipo deve ser registado como um combinador definido pelo utilizador. Esta classe não pode ser herdada.

**SqlUserDefinedCombiner** é usado para definir a propriedade do modo Combiner. É um atributo opcional para uma definição de combinação definida pelo utilizador.

Modo CombinerMode

CombinerMode enum pode assumir os seguintes valores:

* Full (0) Cada linha de saída depende potencialmente de todas as linhas de entrada da esquerda e da direita com o mesmo valor de chave.

* Esquerda (1) Cada linha de saída depende de uma única linha de entrada a partir da esquerda (e potencialmente todas as linhas da direita com o mesmo valor chave).

* Direita (2) Cada linha de saída depende de uma única linha de entrada da direita (e potencialmente todas as linhas da esquerda com o mesmo valor chave).

* Interior (3) Cada linha de saída depende de uma única linha de entrada da esquerda e da direita com o mesmo valor.

Exemplo: `SqlUserDefinedCombiner(Mode=CombinerMode.Left)` [ ]


Os principais objetos de programabilidade são:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Os conjuntos de linhas de entrada são passados como o tipo de interface **esquerdo** e **direito.** `IRowset` Ambos os conjuntos de linha devem ser enumerados para o processamento. Só se pode enumerar cada interface uma vez, por isso temos de enumerar e cache se necessário.

Para fins de caching, podemos criar um \<T\> tipo de lista de estrutura de memória como resultado de uma execução de consulta LINQ, especificamente List<`IRow`>. O tipo de dados anónimo também pode ser usado durante a enumeração.

Consulte [a Introdução às Consultas LINQ (C#)](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) para obter mais informações sobre consultas linq e [ \<T\> interface ienumerable](/dotnet/api/system.collections.generic.ienumerable-1) para obter mais informações sobre a interface IEnumerable. \<T\>

Para obter os valores reais dos dados a partir da `IRowset` entrada, utilizamos o método de `IRow` interface Get().

```csharp
mycolumn = row.Get<int>("mycolumn")
```

Os nomes das colunas individuais podem ser determinados chamando o `IRow` método Schema.

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Ou usando o nome da coluna de esquema:

```csharp
c# row.Get<int>(row.Schema[0].Name)
```

A enumeração geral com LINQ parece ser a seguinte:

```csharp
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Depois de enumerar os dois conjuntos de linhas, vamos dar a volta por todas as linhas. Para cada linha no rowset esquerdo, vamos encontrar todas as linhas que satisfaçam a condição do nosso combinador.

Os valores de saída devem ser definidos com `IUpdatableRow` saída.

```csharp
output.Set<int>("mycolumn", mycolumn)
```

A saída real é desencadeada por chamadas para `yield return output.AsReadOnly();` .

Segue-se um exemplo combinador:

```csharp
[SqlUserDefinedCombiner]
public class CombineSales : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    var internetSales =
    (from row in left.Rows
          select new
          {
              ProductKey = row.Get<int>("ProductKey"),
              OrderDateKey = row.Get<int>("OrderDateKey"),
              SalesAmount = row.Get<decimal>("SalesAmount"),
              TaxAmt = row.Get<decimal>("TaxAmt")
          }).ToList();

    var resellerSales =
    (from row in right.Rows
     select new
     {
     ProductKey = row.Get<int>("ProductKey"),
     OrderDateKey = row.Get<int>("OrderDateKey"),
     SalesAmount = row.Get<decimal>("SalesAmount"),
     TaxAmt = row.Get<decimal>("TaxAmt")
     }).ToList();

    foreach (var row_i in internetSales)
    {
    foreach (var row_r in resellerSales)
    {

        if (
        row_i.OrderDateKey > 0
        && row_i.OrderDateKey < row_r.OrderDateKey
        && row_i.OrderDateKey == 20010701
        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
        {
        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
        output.Set<int>("ProductKey", row_i.ProductKey);
        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
        }

    }
    }
    yield return output.AsReadOnly();
}
}
```

Neste cenário de utilização, estamos a construir um relatório de análise para o retalhista. O objetivo é encontrar todos os produtos que custam mais de $20.000 e que vendem através do site mais rapidamente do que através do retalhista regular dentro de um determinado prazo.

Aqui está o roteiro base U-SQL. Pode comparar a lógica entre um JOIN regular e um combinador:

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

Um combinador definido pelo utilizador pode ser chamado como uma nova instância do objeto aplicador:

```csharp
USING new MyNameSpace.MyCombiner();
```


Ou com a invocação de um método de fábrica de invólucro:

```csharp
USING MyNameSpace.MyCombiner();
```

## <a name="next-steps"></a>Passos seguintes
* [Guia de programabilidade U-SQL - visão geral](data-lake-analytics-u-sql-programmability-guide.md)
* [Guia de programabilidade U-SQL - UDT e UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)