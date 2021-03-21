---
title: Guia de programabilidade definido pelo utilizador U-SQL para o Lago de Dados Azure
description: Saiba mais sobre o guia de programabilidade UDO UDO UDO UDO - exaustor definido pelo utilizador.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: ad7f6336753903533771033de21aec8262425a61
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97608022"
---
# <a name="use-user-defined-extractor"></a>Utilizar o extrator definido pelo utilizador

## <a name="u-sql-udo-user-defined-extractor"></a>U-SQL UDO: extrator definido pelo utilizador
O U-SQL permite-lhe importar dados externos utilizando uma declaração Extrata. Uma declaração Extrata pode utilizar extratores de UDO incorporados:  

* *Extractadores.Text()*: Fornece extração de ficheiros de texto delimitados de diferentes codificações.

* *Extractors.Csv:)*: Fornece a extração de ficheiros de valor separados de vírgula (CSV) de diferentes codificações.

* *Extratores.Tsv()*: Fornece a extração de ficheiros de valor separados (TSV) de diferentes codificações.

Pode ser útil desenvolver um extrator personalizado. Isto pode ser útil durante a importação de dados se quisermos fazer qualquer uma das seguintes tarefas:

* Modifique os dados de entrada dividindo colunas e modificando valores individuais. A funcionalidade PROCESSADOR é melhor para combinar colunas.
* Parse dados não estruturados, tais como páginas Web e e-mails, ou dados semi-estruturados como XML/JSON.
* Parse dados em codificação não suportada.

## <a name="how-to-define-and-use-user-defined-extractor"></a>Como definir e utilizar o extrator definido pelo utilizador
Para definir um extrator definido pelo utilizador, ou UDE, precisamos criar uma `IExtractor` interface. Todos os parâmetros de entrada para o exaustor, tais como limagem coluna/linha, e codificação, devem ser definidos no construtor da classe. A `IExtractor`  interface deve também conter uma definição para a `IEnumerable<IRow>` sobreposição da seguinte forma:

```csharp
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

O atributo **SqlUserDefinedExtractor** indica que o tipo deve ser registado como um extrator definido pelo utilizador. Esta classe não pode ser herdada.

SqlUserExtractor É um atributo opcional para a definição de UDE. Foi usado para definir a propriedade AtomicFileProcessing para o objeto UDE.

* bool AtomicFileProcessing   

* **verdadeiro** = Indica que este extrator requer ficheiros de entrada atómica (JSON, XML, ...)
* **falso** = Indica que este extrator pode lidar com ficheiros divididos /distribuídos (CSV, SEQ, ...)

Os principais objetos de programabilidade UDE são **a entrada** e **a saída.** O objeto de entrada é utilizado para enumerar dados de entrada como `IUnstructuredReader` . O objeto de saída é utilizado para definir dados de saída como resultado da atividade do extrator.

Os dados de entrada são acedidos `System.IO.Stream` através e `System.IO.StreamReader` .

Para a enumeração das colunas de entrada, dividimos primeiro o fluxo de entrada usando um delimiter de linha.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Em seguida, divida ainda mais a linha de entrada em partes de colunas.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

Para definir dados de saída, usamos o `output.Set` método.

É importante entender que o extrator personalizado apenas produz colunas e valores que são definidos com a saída. Definir chamada de método.

```csharp
output.Set<string>(count, part);
```

A saída do extrator real é desencadeada por chamada `yield return output.AsReadOnly();` .

Segue-se o exemplo do extrator:

```csharp
[SqlUserDefinedExtractor(AtomicFileProcessing = true)]
public class FullDescriptionExtractor : IExtractor
{
     private Encoding _encoding;
     private byte[] _row_delim;
     private char _col_delim;

    public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
    {
         this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
         this._row_delim = this._encoding.GetBytes(row_delim);
         this._col_delim = col_delim;

    }

    public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
    {
         string line;
         //Read the input line by line
         foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
         {
        using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
         {
             line = streamReader.ReadToEnd().Trim();
             //Split the input by the column delimiter
             string[] parts = line.Split(this._col_delim);
             int count = 0; // start with first column
             foreach (string part in parts)
             {
    if (count == 0)
             {  // for column “guid”, re-generated guid
                 Guid new_guid = Guid.NewGuid();
                 output.Set<Guid>(count, new_guid);
             }
             else if (count == 2)
             {
                 // for column “user”, convert to UPPER case
                 output.Set<string>(count, part.ToUpper());

             }
             else
             {
                 // keep the rest of the columns as-is
                 output.Set<string>(count, part);
             }
             count += 1;
             }

         }
         yield return output.AsReadOnly();
         }
         yield break;
     }
}
```

Neste cenário de utilização, o exaustor regenera o GUID para coluna "guid" e converte os valores da coluna "user" para a caixa superior. Os extratores personalizados podem produzir resultados mais complicados através da análise dos dados de entrada e da sua manipulação.

Segue-se o script base U-SQL que utiliza um extrator personalizado:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt String,
        user String,
        des String
    FROM @input_file
        USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```


## <a name="next-steps"></a>Passos seguintes
* [Guia de programabilidade U-SQL - visão geral](data-lake-analytics-u-sql-programmability-guide.md)
* [Guia de programabilidade U-SQL - UDT e UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)