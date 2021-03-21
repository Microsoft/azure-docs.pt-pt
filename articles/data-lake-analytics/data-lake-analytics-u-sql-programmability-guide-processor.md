---
title: Guia de programabilidade do processador definido pelo utilizador U-SQL para O Lago de Dados Azure
description: Saiba mais sobre o guia de programabilidade UDO UDO UDO UDO - processador definido pelo utilizador.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 6ff45c577e94a8c63bd7cb1e6603e4d5519af5c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96512690"
---
# <a name="use-user-defined-processor"></a>Utilizar o processador definido pelo utilizador

## <a name="u-sql-udo-user-defined-processor"></a>U-SQL UDO: processador definido pelo utilizador
O processador definido pelo utilizador, ou UDP, é um tipo de UDO U-SQL que lhe permite processar as linhas de entrada aplicando funcionalidades de programabilidade. O UDP permite-lhe combinar colunas, modificar valores e adicionar novas colunas, se necessário. Basicamente, ajuda a processar um conjunto de linhas para produzir elementos de dados necessários.

## <a name="how-to-define-and-use-user-defined-processor"></a>Como definir e utilizar o processador definido pelo utilizador
Para definir uma UDP, precisamos de criar uma `IProcessor` interface com o `SqlUserDefinedProcessor` atributo, que é opcional para a UDP.

Esta interface deve conter a definição para o sobreposição de `IRow` linha de interface, como mostra o seguinte exemplo:

```csharp
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**SqlUserDefinedProcessor** indica que o tipo deve ser registado como um processador definido pelo utilizador. Esta classe não pode ser herdada.

O atributo SqlUserDefinedProcessor é **opcional** para a definição de UDP.

Os principais objetos de programabilidade são **a entrada** e **a saída.** O objeto de entrada é utilizado para enumerar colunas de entrada e saída e para definir dados de saída como resultado da atividade do processador.

Para a enumeração das colunas de entrada, usamos o `input.Get` método.

```csharp
string column_name = input.Get<string>("column_name");
```

O parâmetro para `input.Get` o método é uma coluna que é passada como parte da cláusula da declaração do script base `PRODUCE` `PROCESS` U-SQL. Precisamos usar o tipo de dados correto aqui.

Para saída, utilize o `output.Set` método.

É importante notar que o produtor personalizado apenas produz colunas e valores que são definidos com a `output.Set` chamada do método.

```csharp
output.Set<string>("mycolumn", mycolumn);
```

A saída real do processador é desencadeada através da chamada `return output.AsReadOnly();` .

Segue-se um exemplo de processador:

```csharp
[SqlUserDefinedProcessor]
public class FullDescriptionProcessor : IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
     string user = input.Get<string>("user");
     string des = input.Get<string>("des");
     string full_description = user.ToUpper() + "=>" + des;
     output.Set<string>("dt", input.Get<string>("dt"));
     output.Set<string>("full_description", full_description);
     output.Set<Guid>("new_guid", Guid.NewGuid());
     output.Set<Guid>("guid", input.Get<Guid>("guid"));
     return output.AsReadOnly();
 }
}
```

Neste cenário de utilização, o processador está a gerar uma nova coluna chamada "full_description" combinando as colunas existentes -- neste caso, "utilizador" em maiúsão e "des". Também regenera um GUID e devolve os valores originais e novos GUID.

Como pode ver pelo exemplo anterior, pode chamar métodos C# durante a `output.Set` chamada de método.

Segue-se um exemplo de script U-SQL base que utiliza um processador personalizado:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```


## <a name="next-steps"></a>Passos seguintes
* [Guia de programabilidade U-SQL - visão geral](data-lake-analytics-u-sql-programmability-guide.md)
* [Guia de programabilidade U-SQL - UDT e UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)