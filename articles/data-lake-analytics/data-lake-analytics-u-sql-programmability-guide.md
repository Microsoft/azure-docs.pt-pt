---
title: Guia de programabilidade U-SQL para O Lago de Dados Azure
description: Conheça o conjunto de serviços no Azure Data Lake Analytics que lhe permitem criar uma plataforma de big data baseada na nuvem.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 1a0e914b5f36d58ec3aab24099c8b3787f0cf648
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92215455"
---
# <a name="u-sql-programmability-guide"></a>Guia de programabilidade U-SQL

U-SQL é uma linguagem de consulta que é projetada para grandes cargas de trabalho tipo dado. Uma das características únicas do U-SQL é a combinação da linguagem declarativa semelhante ao SQL com a extensibilidade e programabilidade que é fornecida por C#. Neste guia, concentramo-nos na extensibilidade e programabilidade da linguagem U-SQL que é ativada por C#.

## <a name="requirements"></a>Requisitos

Descarregue e instale [ferramentas do Lago de Dados Azure para o Estúdio Visual.](https://www.microsoft.com/download/details.aspx?id=49504)

## <a name="get-started-with-u-sql"></a>Introdução ao U-SQL  

Veja o seguinte script U-SQL:

```usql
@a  = 
  SELECT * FROM 
    (VALUES
       ("Contoso",   1500.0, "2017-03-39"),
       ("Woodgrove", 2700.0, "2017-04-10")
    ) AS D( customer, amount, date );

@results =
  SELECT
    customer,
    amount,
    date
  FROM @a;    
```

Este script define dois Linhas de Partidas: `@a` e `@results` . O RowSet `@results` é definido a partir de `@a` .

## <a name="c-types-and-expressions-in-u-sql-script"></a>Tipos e expressões C# no script U-SQL

Uma Expressão U-SQL é uma expressão C# combinada com operações lógicas U-SQL tais `AND` `OR` , e `NOT` . As expressões U-SQL podem ser utilizadas com SELECT, EXTRACT, WHERE, HAVING, GROUP BY e DECLARE. Por exemplo, o seguinte script analisa uma cadeia como um valor DateTime.

```usql
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

O seguinte corte analisa uma cadeia como valor DateTime numa declaração DECLARA.

```usql
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>Utilizar expressões C# para conversões de tipo de dados

O exemplo a seguir demonstra como pode fazer uma conversão de dados de data, utilizando expressões C#. Neste cenário específico, os dados da data de data de cordas são convertidos para data padrão com a notação de hora da meia-noite 00:00:00.

```usql
DECLARE @dt = "2016-07-06 10:23:15";

@rs1 =
  SELECT 
    Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
    dt AS olddt
  FROM @rs0;

OUTPUT @rs1 
  TO @output_file 
  USING Outputters.Text();
```

### <a name="use-c-expressions-for-todays-date"></a>Use expressões C# para a data de hoje

Para puxar a data de hoje, podemos usar a seguinte expressão C#: `DateTime.Now.ToString("M/d/yyyy")`

Aqui está um exemplo de como usar esta expressão num script:

```usql
@rs1 =
  SELECT
    MAX(guid) AS start_id,
    MIN(dt) AS start_time,
    MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
    MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
    DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
    user,
    des
  FROM @rs0
  GROUP BY user, des;
```
## <a name="using-net-assemblies"></a>Utilização de conjuntos .NET

O modelo de extensibilidade da U-SQL depende fortemente da capacidade de adicionar código personalizado a partir de conjuntos .NET. 

### <a name="register-a-net-assembly"></a>Registar uma montagem .NET

Utilize a `CREATE ASSEMBLY` declaração para colocar um conjunto .NET numa Base de Dados U-SQL. Posteriormente, os scripts U-SQL podem utilizar esses conjuntos utilizando a `REFERENCE ASSEMBLY` declaração. 

O seguinte código mostra como registar uma montagem:

```usql
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

O seguinte código mostra como fazer referência a um conjunto:

```usql
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Consulte as [instruções de inscrição](/archive/blogs/azuredatalake/how-to-register-u-sql-assemblies-in-your-u-sql-catalog) de montagem que cobrem este tema com maior detalhe.


### <a name="use-assembly-versioning"></a>Utilize a versão de montagem
Atualmente, o U-SQL utiliza a versão .NET Framework 4.7.2. Por isso, certifique-se de que os seus próprios conjuntos são compatíveis com a versão do tempo de execução.

Como mencionado anteriormente, o U-SQL executa o código num formato de 64 bits (x64). Por isso, certifique-se de que o seu código é compilado para funcionar em x64. Caso contrário, obtém-se o erro de formato incorreto apresentado anteriormente.

Cada conjunto carregado DLL e ficheiro de recursos, como um tempo de execução diferente, um conjunto nativo, ou um ficheiro config, pode ser no máximo 400 MB. A dimensão total dos recursos mobilizados, quer através do RECURSO DEPLOY, quer através de referências a conjuntos e seus ficheiros adicionais, não pode exceder 3 GB.

Finalmente, note que cada base de dados U-SQL só pode conter uma versão de qualquer conjunto. Por exemplo, se precisar da versão 7 e da versão 8 da biblioteca newtonSoft Json.NET, tem de as registar em duas bases de dados diferentes. Além disso, cada script só pode referir-se a uma versão de um dado conjunto DLL. A este respeito, a U-SQL segue a gestão da montagem C# e a semântica de versões.

## <a name="use-user-defined-functions-udf"></a>Utilizar funções definidas pelo utilizador: UDF
As funções definidas pelo utilizador U-SQL, ou UDF, são rotinas de programação que aceitam parâmetros, realizam uma ação (como um cálculo complexo) e devolvem o resultado dessa ação como um valor. O valor de retorno da UDF só pode ser um único escalão. U-SQL UDF pode ser chamado em script base U-SQL como qualquer outra função de escala C#.

Recomendamos que inicialize funções definidas pelo utilizador U-SQL como **públicas** e **estáticas.**

```usql
public static string MyFunction(string param1)
{
    return "my result";
}
```

Primeiro vamos olhar para o exemplo simples de criar um UDF.

Neste cenário de utilização, temos de determinar o período fiscal, incluindo o trimestre fiscal e o mês fiscal do primeiro início de sing-in para o utilizador específico. O primeiro mês fiscal do ano no nosso cenário é junho.

Para calcular o período fiscal, introduzimos a seguinte função C#:

```usql
public static string GetFiscalPeriod(DateTime dt)
{
    int FiscalMonth=0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter=0;
    if (FiscalMonth >=1 && FiscalMonth<=3)
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

    return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
}
```

Simplesmente calcula o mês e o trimestre fiscal e devolve um valor de cadeia. Para junho, primeiro mês do primeiro trimestre fiscal, usamos "Q1:P1". Para julho, usamos "Q1:P2", e assim por diante.

Esta é uma função C# regular que vamos usar no nosso projeto U-SQL.

Eis como a secção de código-por trás parece neste cenário:

```usql
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
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

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }
    }
}
```

Agora vamos chamar esta função do script U-SQL base. Para isso, temos de fornecer um nome totalmente qualificado para a função, incluindo o espaço de nome, que neste caso é NameSpace.Class.Function(parâmetro).
```usql
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Segue-se o roteiro de base U-SQL:
```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt DateTime,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Segue-se o ficheiro de saída da execução do guião:

```output
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Este exemplo demonstra uma utilização simples de UDF em U-SQL.

### <a name="keep-state-between-udf-invocations"></a>Mantenha o estado entre as invocações da UDF
Os objetos de programabilidade U-SQL C# podem ser mais sofisticados, utilizando a interatividade através das variáveis globais por detrás do código. Vejamos o seguinte cenário de uso de negócios.

Nas grandes organizações, os utilizadores podem alternar entre variedades de aplicações internas. Estes podem incluir Microsoft Dynamics CRM, Power BI, e assim por diante. Os clientes podem querer aplicar uma análise de telemetria de como os utilizadores mudam entre diferentes aplicações, quais são as tendências de utilização, e assim por diante. O objetivo do negócio é otimizar o uso da aplicação. Também podem querer combinar diferentes aplicações ou rotinas específicas de inscrição.

Para atingir este objetivo, temos de determinar os IDs da sessão e o tempo de desfasamento entre a última sessão que ocorreu.

Precisamos de encontrar um início de sessão anterior e, em seguida, atribuir este início de sessão a todas as sessões que estão a ser geradas para a mesma aplicação. O primeiro desafio é que o script base U-SQL não nos permite aplicar cálculos sobre colunas já calculadas com função LAG. O segundo desafio é que temos de manter a sessão específica para todas as sessões dentro do mesmo período de tempo.

Para resolver este problema, utilizamos uma variável global dentro de uma secção de códigos: `static public string globalSession;` .

Esta variável global é aplicada a todo o conjunto de linhas durante a execução do nosso script.

Aqui está a secção de código-de-trás do nosso programa U-SQL:

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60) {return Session;}
                else {return Guid.NewGuid().ToString();}
            }
            else {return Guid.NewGuid().ToString();}

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session)) { globalSession = Session; }
            return globalSession;
        }

    }
}
```

Este exemplo mostra a variável global `static public string globalSession;` utilizada dentro da `getStampUserSession` função e a reinitializar-se cada vez que o parâmetro Session é alterado.

O script base U-SQL é o seguinte:

```usql
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT 
        EventDateTime,
        UserName,
    LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
        string.IsNullOrEmpty(LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
        USQLApplication21.UserSession.StampUserSession
           (
            EventDateTime,
            LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
            LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           ) AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT 
        EventDateTime,
        UserName,
        LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
        string.IsNullOrEmpty( LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
        USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
    TO @out2
    ORDER BY UserName, EventDateTime ASC
    USING Outputters.Csv();
```

A função `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` é chamada aqui durante o segundo cálculo do conjunto de linhas de memória. Passa a `UserSessionTimestamp` coluna e devolve o valor até que tenha `UserSessionTimestamp` mudado.

O ficheiro de saída é o seguinte:

```output
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

Este exemplo demonstra um cenário de caso de uso mais complicado em que usamos uma variável global dentro de uma secção de código-atrás que é aplicada a todo o conjunto de linhas de memória.

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

* Adicione os seguintes espaços de nome:

```csharp
using Microsoft.Analytics.Interfaces
using System.IO;
```

* Adicione `Microsoft.Analytics.Interfaces` , que é necessário para as interfaces UDT. Além disso, `System.IO` poderá ser necessário definir a interface IFormatter.

* Defina um tipo definido com atributo SqlUserDefinedType.

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

## <a name="use-user-defined-objects-udo"></a>Utilize objetos definidos pelo utilizador: UDO
O U-SQL permite-lhe definir objetos de programabilidade personalizados, que são chamados objetos definidos pelo utilizador ou UDO.

Segue-se uma lista de UDO em U-SQL:

* Extratores definidos pelo utilizador
    * Extrair linha por linha
    * Usado para implementar a extração de dados a partir de ficheiros estruturados personalizados

* Saídadores definidos pelo utilizador
    * Linha de saída por linha
    * Usado para desatar tipos de dados personalizados ou formatos de ficheiros personalizados

* Processadores definidos pelo utilizador
    * Pegue uma linha e produza uma linha
    * Usado para reduzir o número de colunas ou produzir novas colunas com valores derivados de um conjunto de colunas existente

* Aplicadores definidos pelo utilizador
    * Pegue uma linha e produza 0 a n linhas
    * Usado com OUTER/CROSS APPLY

* Combinadores definidos pelo utilizador
    * Combina conjuntos de linhas -- JOINs definidos pelo utilizador

* Redutores definidos pelo utilizador
    * Tome n linhas e produza uma linha
    * Usado para reduzir o número de linhas

UDO é tipicamente chamado explicitamente no script U-SQL como parte das seguintes declarações U-SQL:

* EXTRAIR
* SAÍDA
* PROCESSO
* COMBINAR
* REDUZIR

> [!NOTE]  
> Os UDO's limitam-se a consumir 0,5Gb de memória.  Esta limitação de memória não se aplica às execuções locais.

## <a name="use-user-defined-extractors"></a>Utilize extratores definidos pelo utilizador
O U-SQL permite-lhe importar dados externos utilizando uma declaração Extrata. Uma declaração Extrata pode utilizar extratores de UDO incorporados:  

* *Extractadores.Text()*: Fornece extração de ficheiros de texto delimitados de diferentes codificações.

* *Extractors.Csv:)*: Fornece a extração de ficheiros de valor separados de vírgula (CSV) de diferentes codificações.

* *Extratores.Tsv()*: Fornece a extração de ficheiros de valor separados (TSV) de diferentes codificações.

Pode ser útil desenvolver um extrator personalizado. Isto pode ser útil durante a importação de dados se quisermos fazer qualquer uma das seguintes tarefas:

* Modifique os dados de entrada dividindo colunas e modificando valores individuais. A funcionalidade PROCESSADOR é melhor para combinar colunas.
* Parse dados não estruturados, tais como páginas Web e e-mails, ou dados semi-estruturados como XML/JSON.
* Parse dados em codificação não suportada.

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

## <a name="use-user-defined-outputters"></a>Utilize outputters definidos pelo utilizador
O outputter definido pelo utilizador é outro U-SQL UDO que lhe permite alargar a funcionalidade U-SQL incorporada. Semelhante ao extrator, existem vários saídadores embutidos.

* *Outputters.Text()*: Escreve dados para ficheiros de texto delimitados de diferentes codificações.
* *Outputters.Csv()*: Escreve dados para ficheiros de valor separados por vírgula (CSV) de diferentes codificações.
* *Outputters.Tsv()*: Escreve dados para ficheiros de valor separados por separados (TSV) de diferentes codificações.

O outputter personalizado permite-lhe escrever dados num formato definido sob medida. Isto pode ser útil para as seguintes tarefas:

* Escrever dados para ficheiros semi-estruturados ou não estruturados.
* A escrita de dados não suportados codificações.
* Modificar os dados de saída ou adicionar atributos personalizados.

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

## <a name="use-user-defined-processors"></a>Utilize processadores definidos pelo utilizador
O processador definido pelo utilizador, ou UDP, é um tipo de UDO UDO UDO U-SQL que lhe permite processar as linhas de entrada aplicando funcionalidades de programabilidade. O UDP permite-lhe combinar colunas, modificar valores e adicionar novas colunas, se necessário. Basicamente, ajuda a processar um conjunto de linhas para produzir elementos de dados necessários.

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

## <a name="use-user-defined-appliers"></a>Utilize os aplicadores definidos pelo utilizador
Um aplicador definido pelo utilizador U-SQL permite-lhe invocar uma função C# personalizada para cada linha que é devolvida pela expressão exterior da tabela de uma consulta. A entrada direita é avaliada para cada linha a partir da entrada esquerda, e as linhas que são produzidas são combinadas para a saída final. A lista de colunas produzidas pelo operador APPLY é a combinação do conjunto de colunas à esquerda e à entrada direita.

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

## <a name="use-user-defined-combiners"></a>Utilize combinadores definidos pelo utilizador
O combinador definido pelo utilizador, ou UDC, permite-lhe combinar linhas de linhas esquerda e direita, com base na lógica personalizada. O combinador definido pelo utilizador é usado com a expressão COMBINE.

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

## <a name="use-user-defined-reducers"></a>Utilize redutores definidos pelo utilizador

O U-SQL permite-lhe escrever redutores personalizados em C# utilizando a estrutura de extensibilidade do operador definida pelo utilizador e implementando uma interface IReducer.

O redutor definido pelo utilizador, ou UDR, pode ser usado para eliminar linhas desnecessárias durante a extração de dados (importação). Também pode ser usado para manipular e avaliar linhas e colunas. Com base na lógica de programabilidade, também pode definir quais linhas precisam de ser extraídas.

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