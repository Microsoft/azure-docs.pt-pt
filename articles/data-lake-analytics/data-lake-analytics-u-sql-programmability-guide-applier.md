---
title: Guia de programabilidade definido pelo utilizador U-SQL para O Lago de Dados Azure
description: Saiba mais sobre o guia de programabilidade UDO UDO UDO UDO UDO - aplicador definido pelo utilizador.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 0842a2cfa021ef8ea45c19ec885c7dec371730de
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512642"
---
# <a name="use-user-defined-applier"></a>Utilize o aplicador definido pelo utilizador 

## <a name="u-sql-udo-user-defined-applier"></a>U-SQL UDO: aplicador definido pelo utilizador
Um aplicador definido pelo utilizador U-SQL permite-lhe invocar uma função C# personalizada para cada linha que é devolvida pela expressão exterior da tabela de uma consulta. A entrada direita é avaliada para cada linha a partir da entrada esquerda, e as linhas que são produzidas são combinadas para a saída final. A lista de colunas produzidas pelo operador APPLY é a combinação do conjunto de colunas à esquerda e à entrada direita.


## <a name="how-to-define-and-use-user-defined-applier"></a>Como definir e utilizar o aplicador definido pelo utilizador
O aplicador definido pelo utilizador está a ser invocado como parte da expressão USQL SELECT.

A chamada típica para o aplicador definido pelo utilizador parece ser a seguinte:

```usql
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Para obter mais informações sobre a utilização de aplicadores numa expressão SELECT, consulte [U-SQL SELECTIng from CROSS APPLY e OUTER APPLY](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply).

A definição de classe base de aplicação definida pelo utilizador é a seguinte:

```csharp
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

Para definir um aplicador definido pelo utilizador, precisamos de criar a `IApplier` interface com o atributo [ ] que é opcional para uma `SqlUserDefinedApplier` definição de aplicação definida pelo utilizador.

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
    public ParserApplier()
    {
        …
    }

    public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
    {
        …
    }
}
```

* A aplicação é chamada para cada linha da mesa exterior. Devolve o `IUpdatableRow` conjunto de saída.
* A classe Constructor é utilizada para passar parâmetros ao aplicador definido pelo utilizador.

**SqlUserDefinedApplier** indica que o tipo deve ser registado como um aplicador definido pelo utilizador. Esta classe não pode ser herdada.

**SqlUserDefinedApplier** é **opcional** para uma definição de aplicação definida pelo utilizador.


Os principais objetos de programabilidade são os seguintes:

```csharp
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Os conjuntos de linhas de entrada são passados como `IRow` entrada. As linhas de saída são geradas como `IUpdatableRow` interface de saída.

Os nomes das colunas individuais podem ser determinados chamando o `IRow` método Schema.

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Para obter os valores reais dos dados a partir da `IRow` entrada, utilizamos o método de `IRow` interface Get().

```csharp
mycolumn = row.Get<int>("mycolumn")
```

Ou usamos o nome da coluna de esquema:

```csharp
row.Get<int>(row.Schema[0].Name)
```

Os valores de saída devem ser definidos com `IUpdatableRow` saída:

```csharp
output.Set<int>("mycolumn", mycolumn)
```

É importante entender que os aplicadores personalizados apenas colunas e valores de saída que são definidos com `output.Set` chamada de método.

A saída real é desencadeada por `yield return output.AsReadOnly();` chamada.

Os parâmetros de aplicação definidos pelo utilizador podem ser passados para o construtor. O aplicador pode devolver um número variável de colunas que precisam de ser definidas durante a chamada de aplicação na base U-SQL Script.

```csharp
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Aqui está o exemplo do aplicador definido pelo utilizador:

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
private string parsingPart;

public ParserApplier(string parsingPart)
{
    if (parsingPart.ToUpper().Contains("ALL")
    || parsingPart.ToUpper().Contains("MAKE")
    || parsingPart.ToUpper().Contains("MODEL")
    || parsingPart.ToUpper().Contains("YEAR")
    || parsingPart.ToUpper().Contains("TYPE")
    || parsingPart.ToUpper().Contains("MILLAGE")
    )
    {
    this.parsingPart = parsingPart;
    }
    else
    {
    throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
    }
}

public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
{

    string[] properties = input.Get<string>("properties").Split(',');

    //  only process with correct number of properties
    if (properties.Count() == 5)
    {

    string make = properties[0];
    string model = properties[1];
    string year = properties[2];
    string type = properties[3];
    int millage = -1;

    // Only return millage if it is number, otherwise, -1
    if (!int.TryParse(properties[4], out millage))
    {
        millage = -1;
    }

    if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
    if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
    if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
    if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
    if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
    }
    yield return output.AsReadOnly();            
}
}
```

Segue-se o script U-SQL base para este aplicador definido pelo utilizador:

```usql
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

Neste cenário de utilização, o aplicador definido pelo utilizador atua como um parser de valor delimitado em vírgula para as propriedades da frota automóvel. As linhas de ficheiro de entrada parecem ser as seguintes:

```text
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

É um típico ficheiro TSV delimitado por separadores com uma coluna de propriedades que contém propriedades de carros, tais como a maquilhagem e o modelo. Essas propriedades devem ser analisadas nas colunas de mesa. O aplicador que é fornecido também permite gerar um número dinâmico de propriedades no conjunto de resultados, com base no parâmetro que passou. Você pode gerar todas as propriedades ou um conjunto específico de propriedades apenas.

```text
...USQL_Programmability.ParserApplier ("all")
...USQL_Programmability.ParserApplier ("make")
...USQL_Programmability.ParserApplier ("make&model")
```

O aplicador definido pelo utilizador pode ser chamado como uma nova instância de objeto aplicador:

```usql
CROSS APPLY new MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

Ou com a invocação de um método de fábrica de invólucro:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```


## <a name="next-steps"></a>Passos seguintes
* [Guia de programabilidade U-SQL - visão geral](data-lake-analytics-u-sql-programmability-guide.md)
* [Guia de programabilidade U-SQL - UDT e UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)