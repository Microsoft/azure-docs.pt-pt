---
title: Guia de programabilidade U-SQL UDT e UDAGG para O Lago de Dados Azure
description: Conheça a programabilidade U-SQL UDT e UDAGG no Azure Data Lake Analytics para permitir a criação de um bom script USQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: bf2e1e1bc30aeb3306d0a643eca4725d8765edac
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512714"
---
# <a name="u-sql-programmability-guide---udt-and-udagg"></a>Guia de programabilidade U-SQL - UDT e UDAGG



## <a name="use-user-defined-types-udt"></a>Utilizar tipos definidos pelo utilizador: UDT
Os tipos definidos pelo utilizador, ou UDT, é outra característica de programabilidade do U-SQL. U-SQL UDT age como um tipo regular definido pelo utilizador C#. C# é uma linguagem fortemente dactilografada que permite a utilização de tipos incorporados e personalizados definidos pelo utilizador.

U-SQL não pode implicitamente serializar ou des-serializar UDTs arbitrários quando o UDT é passado entre vértices em linhas. Isto significa que o utilizador tem de fornecer um formato explícito utilizando a interface IFormatter. Isto fornece u-SQL com os métodos serialize e des-serialize para o UDT.

> [!NOTE]
> Atualmente, os extratores e outputters incorporados da U-SQL não podem serializar ou des serializar dados UDT de ou para ficheiros, mesmo com o conjunto IFormatter. Por isso, quando estiver a escrever dados de UDT para um ficheiro com a declaração OUTPUT, ou lê-lo com um extrator, tem de o passar como uma matriz de cordas ou byte. Em seguida, você chama o código de serialização e dessaserialização (isto é, o método toString() da UDT explicitamente. Os extratores e os desornadores definidos pelo utilizador, por outro lado, podem ler e escrever UDTs.

Se tentarmos utilizar o UDT no EXTRATOR ou OUTPUTTER (fora do SELECT anterior), como mostrado aqui:

```usql
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Recebemos o seguinte erro:

```output
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

Para trabalhar com a UDT no outputter, ou temos que serializá-lo para cadeia com o método ToString ou criar um outputter personalizado.

Atualmente, os UDTs não podem ser utilizados no GROUP BY. Se a UDT for utilizada no GROUP BY, é lançado o seguinte erro:

```output
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

Para definir uma UDT, temos que:

1. Adicione os seguintes espaços de nome:

```csharp
using Microsoft.Analytics.Interfaces
using System.IO;
```

2. Adicione `Microsoft.Analytics.Interfaces` , que é necessário para as interfaces UDT. Além disso, `System.IO` poderá ser necessário definir a interface IFormatter.

3. Defina um tipo definido com atributo SqlUserDefinedType.

**SqlUserDefinedType** é usado para marcar uma definição de tipo em um conjunto como um tipo definido pelo utilizador (UDT) em U-SQL. As propriedades no atributo refletem as características físicas da UDT. Esta classe não pode ser herdada.

SqlUserDefinedType é um atributo necessário para a definição de UDT.

O construtor da classe:  

* SqlUserDefinedTypeAttribute (tipo formatter)

* Tipo de formatter: Parâmetro necessário para definir um formatter UDT -- especificamente, o tipo de `IFormatter` interface deve ser passado aqui.

```csharp
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* O UDT típico também requer a definição da interface IFormatter, como mostra o seguinte exemplo:

```csharp
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

A `IFormatter` interface serializa e des-serializa um gráfico de objeto com o tipo de raiz de \<typeparamref name="T"> .

\<typeparam name="T">O tipo de raiz para o gráfico do objeto para serializar e des-serializar.

* **Deserializar:** Des-serializa os dados no fluxo fornecido e reconstitui o gráfico dos objetos.

* **Serialize**: Serializa um objeto, ou gráfico de objetos, com a raiz dada ao fluxo fornecido.

`MyType` exemplo: Instância do tipo.  
`IColumnWriter``IColumnReader`escritor/leitor: O fluxo de coluna subjacente.  
`ISerializationContext` contexto: Enum que define um conjunto de bandeiras que especifica o contexto de origem ou destino para o fluxo durante a serialização.

* **Intermédio**: Especifica que o contexto de origem ou destino não é uma loja persistiu.

* **Persistência**: Especifica que o contexto de origem ou destino é uma loja persistiu.

Como um tipo C# regular, uma definição U-SQL UDT pode incluir sobreposições para operadores como +/====. Também pode incluir métodos estáticos. Por exemplo, se vamos usar este UDT como parâmetro para uma função agregada U-SQL MIN, temos que definir < sobreposição do operador.

No início deste guia, demonstrámos um exemplo para a identificação do período fiscal a partir da data específica no formato `Qn:Pn (Q1:P10)` . O exemplo a seguir mostra como definir um tipo personalizado para valores do período fiscal.

Segue-se um exemplo de uma secção por trás de códigos com interface UDT e IFormatter personalizado:

```csharp
[SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
public struct FiscalPeriod
{
    public int Quarter { get; private set; }

    public int Month { get; private set; }

    public FiscalPeriod(int quarter, int month):this()
    {
    this.Quarter = quarter;
    this.Month = month;
    }

    public override bool Equals(object obj)
    {
    if (ReferenceEquals(null, obj))
    {
        return false;
    }

    return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
    }

    public bool Equals(FiscalPeriod other)
    {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
    }

    public bool GreaterThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
    }

    public bool LessThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
    }

    public override int GetHashCode()
    {
    unchecked
    {
        return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
    }
    }

    public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
    {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
    }

    public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.Equals(c2);
    }

    public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
    {
    return !c1.Equals(c2);
    }
    public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.GreaterThan(c2);
    }
    public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.LessThan(c2);
    }
    public override string ToString()
    {
    return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
    }

}

public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
{
    public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
    {
    using (var binaryWriter = new BinaryWriter(writer.BaseStream))
    {
        binaryWriter.Write(instance.Quarter);
        binaryWriter.Write(instance.Month);
        binaryWriter.Flush();
    }
    }

    public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
    {
    using (var binaryReader = new BinaryReader(reader.BaseStream))
    {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
        return result;
    }
    }
}
```

O tipo definido inclui dois números: trimestre e mês. Os operadores `==/!=/>/<` e o método estático são `ToString()` definidos aqui.

Como mencionado anteriormente, o UDT pode ser usado em expressões SELECT, mas não pode ser usado em OUTPUTTER/EXTRATOR sem serialização personalizada. Tem de ser serializado como uma corda com `ToString()` ou usado com um OUTPUTTER/EXTRATOR personalizado.

Agora vamos discutir o uso da UDT. Numa secção de code-behind, mudamos a nossa função GetFiscalPeriod para o seguinte:

```csharp
public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
{
    int FiscalMonth = 0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter = 0;
    if (FiscalMonth >= 1 && FiscalMonth <= 3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return new FiscalPeriod(FiscalQuarter, FiscalMonth);
}
```

Como pode ver, devolve o valor do nosso tipo FiscalPeriod.

Aqui fornecemos um exemplo de como usá-lo ainda mais no script base U-SQL. Este exemplo demonstra diferentes formas de invocação UDT do script U-SQL.

```usql
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT 
        guid AS start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
        user,
        des
    FROM @rs0;

@rs2 =
    SELECT 
           start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           fiscalquarter,
           fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

Aqui está um exemplo de uma secção completa de código-de-trás:

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>Utilizar agregados definidos pelo utilizador: UDAGG
Os agregados definidos pelo utilizador são quaisquer funções relacionadas com agregação que não são enviadas para fora da caixa com U-SQL. O exemplo pode ser um agregado para realizar cálculos de matemática personalizados, concatenações de cordas, manipulações com cordas, e assim por diante.

A definição de classe de base agregada definida pelo utilizador é a seguinte:

```csharp
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** indica que o tipo deve ser registado como um agregado definido pelo utilizador. Esta classe não pode ser herdada.

O atributo SqlUserDefinedType é **opcional** para a definição UDAGG.


A classe base permite-lhe passar três parâmetros abstratos: dois como parâmetros de entrada e um como resultado. Os tipos de dados são variáveis e devem ser definidos durante a herança de classe.

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    { … }

    public override void Accumulate(string guid, string user)
    { … }

    public override string Terminate()
    { … }
}
```

* **Init** invoca uma vez para cada grupo durante a computação. Fornece uma rotina de inicialização para cada grupo de agregação.  
* **A acumulação** é executada uma vez por cada valor. Fornece a principal funcionalidade para o algoritmo de agregação. Pode ser usado para agregar valores com vários tipos de dados que são definidos durante a herança de classe. Pode aceitar dois parâmetros de tipos de dados variáveis.
* **O fim** é executado uma vez por grupo de agregação no final do processamento para produzir o resultado para cada grupo.

Para declarar os tipos corretos de dados de entrada e saída, utilize a definição de classe da seguinte forma:

```csharp
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: Primeiro parâmetro a acumular
* T2: Segundo parâmetro a acumular
* TResult: Tipo de retorno de terminação

Por exemplo:

```csharp
public class GuidAggregate : IAggregate<string, int, int>
```

ou

```csharp
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>Use UDAGG em U-SQL
Para utilizar o UDAGG, primeiro defina-o em code-behind ou referencia-lo a partir da programabilidade existente DLL, como discutido anteriormente.

Em seguida, utilize a seguinte sintaxe:

```csharp
AGG<UDAGG_functionname>(param1,param2)
```

Aqui está um exemplo de UDAGG:

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    {
        guid_agg = "";
    }

    public override void Accumulate(string guid, string user)
    {
        if (user.ToUpper()== "USER1")
        {
        guid_agg += "{" + guid + "}";
        }
    }

    public override string Terminate()
    {
        return guid_agg;
    }

}
```

E o guião base U-SQL:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

Neste cenário de utilização, concatenamos GUIDs de classe para os utilizadores específicos.

## <a name="next-steps"></a>Passos seguintes
* [Guia de programabilidade U-SQL - visão geral](data-lake-analytics-u-sql-programmability-guide.md)
* [Guia de programabilidade U-SQL - UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)
