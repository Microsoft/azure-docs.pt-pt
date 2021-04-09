---
title: Guia de programabilidade definido pelo utilizador U-SQL para O Lago de Dados Azure
description: Saiba mais sobre o guia de programabilidade UDO UDO do utilizador definido.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 56b104b5cc8f8923445455c71fe2418e39539b8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512637"
---
# <a name="use-user-defined-outputter"></a>Utilizar o dispositivo de saída definido pelo utilizador

## <a name="u-sql-udo-user-defined-outputter"></a>U-SQL UDO: outputter definido pelo utilizador
O outputter definido pelo utilizador é outro U-SQL UDO que lhe permite alargar a funcionalidade U-SQL incorporada. Semelhante ao extrator, existem vários saídadores embutidos.

* *Outputters.Text()*: Escreve dados para ficheiros de texto delimitados de diferentes codificações.
* *Outputters.Csv()*: Escreve dados para ficheiros de valor separados por vírgula (CSV) de diferentes codificações.
* *Outputters.Tsv()*: Escreve dados para ficheiros de valor separados por separados (TSV) de diferentes codificações.

O outputter personalizado permite-lhe escrever dados num formato definido sob medida. Isto pode ser útil para as seguintes tarefas:

* Escrever dados para ficheiros semi-estruturados ou não estruturados.
* A escrita de dados não suportados codificações.
* Modificar os dados de saída ou adicionar atributos personalizados.

## <a name="how-to-define-and-use-user-defined-outputter"></a>Como definir e utilizar o outputter definido pelo utilizador
Para definir o outputter definido pelo utilizador, precisamos de criar a `IOutputter` interface.

Segue-se a implementação da `IOutputter` classe base:

```csharp
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Todos os parâmetros de entrada para o deslimiterador, tais como delimiters de coluna/linha, codificação, e assim por diante, precisam de ser definidos no construtor da classe. A `IOutputter` interface também deve conter uma definição para `void Output` substituição. O atributo `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` pode ser configurado opcionalmente para o processamento de ficheiros atómicos. Para mais informações, consulte os seguintes detalhes.

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class MyOutputter : IOutputter
{

    public MyOutputter(myparam1, myparam2)
    {
      …
    }

    public override void Close()
    {
      …
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
      …
    }
}
```

* `Output` é chamado para cada linha de entrada. Devolve o `IUnstructuredWriter output` conjunto de linhas.
* A classe Constructor é utilizada para passar parâmetros para o outputter definido pelo utilizador.
* `Close` é usado para substituir opcionalmente para libertar estado caro ou determinar quando a última linha foi escrita.

O atributo **SqlUserDefinedOutputter** indica que o tipo deve ser registado como um outputter definido pelo utilizador. Esta classe não pode ser herdada.

SqlUserDefinedOutputter é um atributo opcional para uma definição de outputter definida pelo utilizador. É usado para definir a propriedade AtomicFileProcessing.

* bool AtomicFileProcessing   

* **verdadeiro** = Indica que este outputter requer ficheiros de saída atómica (JSON, XML, ...)
* **falso** = Indica que este outputter pode lidar com ficheiros divididos /distribuídos (CSV, SEQ, ...)

Os principais objetos de programabilidade são **a linha** e **a saída.** O objeto **de linha** é usado para enumerar dados de saída como `IRow` interface. **A saída** é usada para definir dados de saída para o ficheiro-alvo.

Os dados de saída são acedidos através da `IRow` interface. Os dados de saída são passados uma linha de cada vez.

Os valores individuais são enumerados chamando o método Get da interface IRow:

```csharp
row.Get<string>("column_name")
```

Os nomes das colunas individuais podem ser determinados `row.Schema` chamando:

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Esta abordagem permite-lhe construir um outputter flexível para qualquer esquema de metadados.

Os dados de saída são escritos para arquivar utilizando `System.IO.StreamWriter` . O parâmetro de fluxo é definido `output.BaseStream` como parte de `IUnstructuredWriter output` .

Note que é importante descarregar o tampão de dados para o ficheiro após a iteração de cada linha. Além disso, o `StreamWriter` objeto deve ser utilizado com o atributo descartável ativado (predefinido) e com a palavra-chave de **utilização:**

```csharp
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

Caso contrário, ligue para o método Flush() explicitamente após cada iteração. Mostramos isto no exemplo seguinte.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Definir cabeçalhos e rodapés para o outputter definido pelo utilizador
Para definir um cabeçalho, utilize um fluxo de execução de iteração única.

```csharp
public override void Output(IRow row, IUnstructuredWriter output)
{
 …
if (isHeaderRow)
{
    …                
}

 …
if (isHeaderRow)
{
    isHeaderRow = false;
}
 …
}
}
```

O código do primeiro `if (isHeaderRow)` bloco é executado apenas uma vez.

Para o rodapé, utilize a referência à instância do `System.IO.Stream` objeto `output.BaseStream` (). Escreva o rodapé no método Close() da `IOutputter` interface.  (Para mais informações, consulte o seguinte exemplo.)

Segue-se um exemplo de um outputter definido pelo utilizador:

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class HTMLOutputter : IOutputter
{
    // Local variables initialization
    private string row_delimiter;
    private char col_delimiter;
    private bool isHeaderRow;
    private Encoding encoding;
    private bool IsTableHeader = true;
    private Stream g_writer;

    // Parameters definition            
    public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    this.isHeaderRow = isHeader;
    this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
    }

    // The Close method is used to write the footer to the file. It's executed only once, after all rows
    public override void Close()
    {
    //Reference to IO.Stream object - g_writer
    StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
    streamWriter.Write("</table>");
    streamWriter.Flush();
    streamWriter.Close();
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
    System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

    // Metadata schema initialization to enumerate column names
    ISchema schema = row.Schema;

    // This is a data-independent header--HTML table definition
    if (IsTableHeader)
    {
        streamWriter.Write("<table border=1>");
        IsTableHeader = false;
    }

    // HTML table attributes
    string header_wrapper_on = "<th>";
    string header_wrapper_off = "</th>";
    string data_wrapper_on = "<td>";
    string data_wrapper_off = "</td>";

    // Header row output--runs only once
    if (isHeaderRow)
    {
        streamWriter.Write("<tr>");
        for (int i = 0; i < schema.Count(); i++)
        {
        var col = schema[i];
        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
        }
        streamWriter.Write("</tr>");
    }

    // Data row output
    streamWriter.Write("<tr>");                
    for (int i = 0; i < schema.Count(); i++)
    {
        var col = schema[i];
        string val = "";
        try
        {
        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
        switch (col.Type.Name.ToString().ToLower())
        {
            case "string": val = row.Get<string>(col.Name).ToString(); break;
            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
            default: break;
        }
        }
        // Handling NULL values--keeping them empty
        catch (System.NullReferenceException)
        {
        }
        streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
    }
    streamWriter.Write("</tr>");

    if (isHeaderRow)
    {
        isHeaderRow = false;
    }
    // Reference to the instance of the IO.Stream object for footer generation
    g_writer = output.BaseStream;
    streamWriter.Flush();
    }
}

// Define the factory classes
public static class Factory
{
    public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    return new HTMLOutputter(isHeader, encoding);
    }
}
```

E o guião base U-SQL:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
         FROM @input_file
         USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 
    TO @output_file 
    USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Este é um outputter HTML, que cria um ficheiro HTML com dados de tabela.

### <a name="call-outputter-from-u-sql-base-script"></a>Saídador de chamadas a partir do script base U-SQL
Para chamar um outputter personalizado a partir do script base U-SQL, o novo exemplo do objeto de saída tem de ser criado.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Para evitar a criação de um exemplo do objeto no script base, podemos criar um invólucro de função, como mostra o nosso exemplo anterior:

```csharp
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

Neste caso, a chamada original parece ser a seguinte:

```usql
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="next-steps"></a>Passos seguintes
* [Guia de programabilidade U-SQL - visão geral](data-lake-analytics-u-sql-programmability-guide.md)
* [Guia de programabilidade U-SQL - UDT e UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)