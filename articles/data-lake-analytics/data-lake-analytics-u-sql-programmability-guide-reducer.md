---
title: Guia de programabilidade definido pelo utilizador U-SQL para O Lago de Dados Azure
description: Saiba mais sobre o guia de programabilidade UDO UDO UDO UDO UDO - redutor definido pelo utilizador.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 52d44685678c3e89dc820042a7925d284500cef8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512630"
---
# <a name="use-user-defined-reducer"></a>Utilizar o redutor definido pelo utilizador

## <a name="u-sql-udo-user-defined-reducer"></a>U-SQL UDO: redutor definido pelo utilizador

O U-SQL permite-lhe escrever redutores personalizados em C# utilizando a estrutura de extensibilidade do operador definida pelo utilizador e implementando uma interface IReducer.

O redutor definido pelo utilizador, ou UDR, pode ser usado para eliminar linhas desnecessárias durante a extração de dados (importação). Também pode ser usado para manipular e avaliar linhas e colunas. Com base na lógica de programabilidade, também pode definir quais linhas precisam de ser extraídas.

## <a name="how-to-define-and-use-user-defined-reducer"></a>Como definir e utilizar o redutor definido pelo utilizador
Para definir uma classe UDR, precisamos criar uma `IReducer` interface com um atributo `SqlUserDefinedReducer` opcional.

Esta interface de classe deve conter uma definição para o sobreposição de `IEnumerable` linha de interface.

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

O atributo **SqlUserDefinedReducer** indica que o tipo deve ser registado como um redutor definido pelo utilizador. Esta classe não pode ser herdada.
**SqlUserDefinedReducer** é um atributo opcional para uma definição de redução definida pelo utilizador. É usado para definir propriedade IsRecursive.

* bool IsRecursive    
* **verdadeiro**  = Indica se este Redutor é associativo e comutativo

Os principais objetos de programabilidade são **a entrada** e **a saída.** O objeto de entrada é utilizado para enumerar linhas de entrada. A saída é utilizada para definir linhas de saída como resultado da redução da atividade.

Para a enumeração das linhas de entrada, usamos o `Row.Get` método.

```csharp
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

O parâmetro para o `Row.Get` método é uma coluna que é passada como parte da classe da declaração do script base `PRODUCE` `REDUCE` U-SQL. Precisamos usar o tipo de dados correto aqui também.

Para saída, utilize o `output.Set` método.

É importante entender que o redutor personalizado apenas produz valores que são definidos com a `output.Set` chamada do método.

```csharp
output.Set<string>("mycolumn", guid);
```

A saída do redutor real é desencadeada através da chamada `yield return output.AsReadOnly();` .

Segue-se um exemplo redutor:

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
    string guid;
    DateTime dt;
    string user;
    string des;

    foreach (IRow row in input.Rows)
    {
        guid = row.Get<string>("guid");
        dt = row.Get<DateTime>("dt");
        user = row.Get<string>("user");
        des = row.Get<string>("des");

        if (user.Length > 0)
        {
        output.Set<string>("guid", guid);
        output.Set<DateTime>("dt", dt);
        output.Set<string>("user", user);
        output.Set<string>("des", des);

        yield return output.AsReadOnly();
        }
    }
    }

}
```

Neste cenário de utilização, o redutor está a saltar linhas com um nome de utilizador vazio. Para cada linha em linha, lê cada coluna requerida e, em seguida, avalia o comprimento do nome de utilizador. Só produz a linha real se o comprimento do valor do nome de utilizador for superior a 0.

Segue-se o script base U-SQL que utiliza um redutor personalizado:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

## <a name="next-steps"></a>Passos seguintes
* [Guia de programabilidade U-SQL - visão geral](data-lake-analytics-u-sql-programmability-guide.md)
* [Guia de programabilidade U-SQL - UDT e UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)