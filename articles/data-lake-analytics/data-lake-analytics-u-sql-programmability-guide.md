---
title: Guia de programabilidade U-SQL para O Lago de Dados Azure
description: Conheça o conjunto de serviços no Azure Data Lake Analytics que lhe permitem criar uma plataforma de big data baseada na nuvem.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.topic: conceptual
ms.date: 06/30/2017
ms.openlocfilehash: dc55615d7a5c6ae9a393ed4fd5f49cd92aedc0f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73162576"
---
# <a name="u-sql-programmability-guide"></a>Guia de programabilidade U-SQL

U-SQL é uma linguagem de consulta que é projetada para grande tipo de trabalho de dados. Uma das características únicas do U-SQL é a combinação da linguagem declarativa semelhante ao SQL com a extebilidade e programabilidade que é fornecida por C#. Neste guia, concentramo-nos na extibilidade e programabilidade da linguagem U-SQL que é ativada por C#.

## <a name="requirements"></a>Requisitos

Descarregue e [instale ferramentas do Lago de Dados Azure para estúdio visual.](https://www.microsoft.com/download/details.aspx?id=49504)

## <a name="get-started-with-u-sql"></a>Introdução ao U-SQL  

Veja o seguinte script U-SQL:

```
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

Este script define dois RowSets: `@a` e `@results`. RowSet `@results` é `@a`definido a partir de .

## <a name="c-types-and-expressions-in-u-sql-script"></a>Tipos e expressões C# no script U-SQL

Uma expressão U-SQL é uma expressão C# combinada com `AND` `OR`operações `NOT`lógicas U-SQL tais, e . Expressões U-SQL podem ser usadas com SELECT, EXTRACT, WHERE, HAVING, GROUP BY e DECLARE. Por exemplo, o seguinte script analisa uma corda como um valor DateTime.

```
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

O seguinte corte analisa uma corda como valor DateTime numa declaração de DECLARE.

```
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>Utilize expressões C# para conversões de tipo de dados

O exemplo que se segue demonstra como pode fazer uma conversão de dados em data utilizando expressões C#. Neste cenário em particular, os dados da data da data da corda são convertidos para data padrão com a notação da hora das 00:00:00.

```
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

Para puxar a data de hoje, podemos usar a seguinte expressão C#:`DateTime.Now.ToString("M/d/yyyy")`

Aqui está um exemplo de como usar esta expressão num roteiro:

```
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

O modelo de extensibility da U-SQL depende fortemente da capacidade de adicionar código personalizado a partir de conjuntos .NET. 

### <a name="register-a-net-assembly"></a>Registe um conjunto .NET

Utilize `CREATE ASSEMBLY` a declaração para colocar um conjunto .NET numa base de dados U-SQL. Em seguida, os scripts U-SQL podem usar `REFERENCE ASSEMBLY` esses conjuntos usando a declaração. 

O seguinte código mostra como registar um conjunto:

```
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

O seguinte código mostra como fazer referência a um conjunto:

```
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Consulte mais pormenorizadamente as [instruções](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/) de registo de montagem que abranja este tema.


### <a name="use-assembly-versioning"></a>Utilizar a versão de montagem
Atualmente, a U-SQL utiliza a versão .NET Framework 4.5. Por isso, certifique-se de que as suas próprias assembleias são compatíveis com a versão do tempo de execução.

Como mencionado anteriormente, o U-SQL executa o código num formato de 64 bits (x64). Por isso, certifique-se de que o seu código está compilado para funcionar em x64. Caso contrário, obtém-se o erro de formato incorreto mostrado anteriormente.

Cada dLL de montagem carregado e ficheiro de recursos, tais como um tempo de execução diferente, um conjunto nativo, ou um ficheiro de config, podem ser no máximo 400 MB. A dimensão total dos recursos implantados, seja através do DEPLOY RESOURCE ou através de referências aos conjuntos e aos seus ficheiros adicionais, não pode exceder 3 GB.

Por último, note que cada base de dados U-SQL só pode conter uma versão de qualquer conjunto. Por exemplo, se precisar tanto da versão 7 como da versão 8 da biblioteca Json.NET NewtonSoft, tem de as registar em duas bases de dados diferentes. Além disso, cada script só pode se referir a uma versão de um dLL de montagem. A este respeito, a U-SQL segue a gestão de montagem c# e a semântica de versão.

## <a name="use-user-defined-functions-udf"></a>Utilizar funções definidas pelo utilizador: UDF
As funções definidas pelo utilizador U-SQL, ou UDF, são rotinas de programação que aceitam parâmetros, realizam uma ação (como um cálculo complexo), e devolvem o resultado dessa ação como um valor. O valor de retorno da UDF só pode ser um único escalar. U-SQL UDF pode ser chamado em script base U-SQL como qualquer outra função c# escalar.

Recomendamos que ininicialize funções definidas pelo utilizador U-SQL como **públicas** e **estáticas.**

```
public static string MyFunction(string param1)
{
    return "my result";
}
```

Primeiro, vejamos o simples exemplo de criação de uma UDF.

Neste cenário de utilização, temos de determinar o período fiscal, incluindo o trimestre fiscal e o mês fiscal do primeiro início de sessão para o utilizador específico. O primeiro mês fiscal do ano no nosso cenário é junho.

Para calcular o período fiscal, introduzimos a seguinte função C#:

```
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

Simplesmente calcula o mês e o trimestre fiscais e devolve um valor de cadeia. Para junho, primeiro mês do primeiro trimestre fiscal, usamos o "Q1:P1". Para julho, usamos "Q1:P2", e assim por diante.

Esta é uma função c# regular que vamos usar no nosso projeto U-SQL.

Eis como a secção de código por trás parece neste cenário:

```
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

Agora vamos chamar esta função do guião base U-SQL. Para isso, temos de fornecer um nome totalmente qualificado para a função, incluindo o espaço de nome, que neste caso é NameSpace.Class.Function (parâmetro).

```
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Segue-se o roteiro base U-SQL real:

```
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

Segue-se o ficheiro de saída da execução do script:

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Este exemplo demonstra um simples uso da UDF inline em U-SQL.

### <a name="keep-state-between-udf-invocations"></a>Mantenha o estado entre as invocações da UDF
Os objetos de programabilidade U-SQL C# podem ser mais sofisticados, utilizando a interatividade através das variáveis globais por trás do código. Vamos ver o seguinte cenário de uso de negócios.

Nas grandes organizações, os utilizadores podem alternar entre variedades de aplicações internas. Estes podem incluir Microsoft Dynamics CRM, PowerBI, e assim por diante. Os clientes podem querer aplicar uma análise de telemetria de como os utilizadores mudam entre diferentes aplicações, quais são as tendências de utilização, e assim por diante. O objetivo para o negócio é otimizar o uso da aplicação. Também podem querer combinar diferentes aplicações ou rotinas específicas de inscrição.

Para atingir este objetivo, temos de determinar os IDs da sessão e o tempo de intervalo entre a última sessão que ocorreu.

Precisamos de encontrar um início de sessão prévia e, em seguida, atribuir este sign-in a todas as sessões que estão sendo geradas para a mesma aplicação. O primeiro desafio é que o script base U-SQL não nos permite aplicar cálculos sobre colunas já calculadas com função LAG. O segundo desafio é que temos de manter a sessão específica para todas as sessões dentro do mesmo período de tempo.

Para resolver este problema, usamos uma variável global `static public string globalSession;`dentro de uma secção de código: .

Esta variável global é aplicada a todo o rowset durante a execução do nosso script.

Aqui está a secção de código por trás do nosso programa U-SQL:

```
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

Este exemplo mostra `static public string globalSession;` a variável global utilizada dentro da `getStampUserSession` função e a ser reinicializada cada vez que o parâmetro session é alterado.

O roteiro base U-SQL é o seguinte:

```
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

A `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` função é chamada aqui durante o segundo cálculo do conjunto de linhas de memória. Passa na `UserSessionTimestamp` coluna e devolve `UserSessionTimestamp` o valor até que tenha mudado.

O ficheiro de saída é o seguinte:

```
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

Este exemplo demonstra um cenário de uso mais complicado em que usamos uma variável global dentro de uma secção de código atrás que é aplicada a todo o conjunto de linhas de memória.

## <a name="use-user-defined-types-udt"></a>Utilize tipos definidos pelo utilizador: UDT
Os tipos definidos pelo utilizador, ou UDT, é outra funcionalidade de programabilidade da U-SQL. U-SQL UDT age como um tipo c# definido pelo utilizador regular. C# é uma linguagem fortemente dactilografada que permite o uso de tipos definidos pelo utilizador incorporados e personalizados.

A U-SQL não pode serializar ou desserializar implicitamente UDTs arbitrários quando o UDT é passado entre vértices em rowsets. Isto significa que o utilizador tem de fornecer uma matéria explícita utilizando a interface IFormatter. Isto fornece u-SQL com os métodos de serialização e desserialização para a UDT.

> [!NOTE]
> Atualmente, os extratores e os produzdores incorporados da U-SQL não conseguem serializar ou desserializar dados uDT de ou a partir de ficheiros, mesmo com o conjunto IFormatter. Por isso, quando estiver a escrever dados da UDT para um ficheiro com a declaração OUTPUT, ou a lê-lo com um extrator, tem de os passar como uma matriz de cordas ou bytes. Em seguida, você chama explicitamente o código de serialização e dedesserialização (isto é, o método ToString() da UDT. Os extratores e os batedores definidos pelo utilizador, por outro lado, podem ler e escrever UDTs.

Se tentarmos utilizar a UDT em EXTRATOR ou OUTPUTTER (fora do SELECT anterior), como mostrado aqui:

```
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Recebemos o seguinte erro:

```
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

Para trabalhar com a UDT no outputter, ou temos de o serializar para o fio com o método ToString ou criar um outputter personalizado.

Atualmente, os UDTs não podem ser utilizados em GRUPO BY. Se a UDT for utilizada em GRUPO BY, é lançado o seguinte erro:

```
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

Para definir um UDT, temos que:

* Adicione os seguintes espaços de nome:

```
using Microsoft.Analytics.Interfaces
using System.IO;
```

* Adicione `Microsoft.Analytics.Interfaces`, que é necessário para as interfaces UDT. Além `System.IO` disso, pode ser necessário definir a interface IFormatter.

* Defina um tipo definido pelo SqlUserDefinedType.

**SqlUserDefinedType** é usado para marcar uma definição de tipo num conjunto como um tipo definido pelo utilizador (UDT) em U-SQL. As propriedades no atributo refletem as características físicas da UDT. Esta aula não pode ser herdada.

SqlUserDefinedType é um atributo necessário para a definição uDT.

O construtor da classe:  

* SqlUserDefinedTypeAttribute (tipo de queestá)

* Tipo de formatéria: Parâmetro necessário para definir um formatéria uDT `IFormatter` - especificamente, o tipo da interface deve ser passado aqui.

```
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* A UDT típica também requer a definição da interface IFormatter, como mostra o seguinte exemplo:

```
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

A `IFormatter` interface serializa e desserializa um gráfico de \<objeto com o tipo raiz de nome typeparamref="T">.

\<nome do tipo="T">O tipo de raiz para o gráfico do objeto serializar e desserializar.

* **Desserialize**: Desserialize os dados no fluxo fornecido e reconstitui o gráfico dos objetos.

* **Serialize**: Serialize um objeto, ou gráfico de objetos, com a raiz dada ao fluxo fornecido.

`MyType`instância: Instância do tipo.  
`IColumnWriter`escritor/leitor: `IColumnReader` O fluxo de coluna subjacente.  
`ISerializationContext`contexto: Enum que define um conjunto de bandeiras que especifica a fonte ou o contexto de destino para o fluxo durante a serialização.

* **Intermediário**: Especifica que o contexto de origem ou destino não é uma loja persistiu.

* **Persistência**: Especifica que o contexto de origem ou destino é uma loja persistiu.

Como um tipo C# regular, uma definição U-SQL UDT pode incluir sobreposições para operadores como +/=========================================================.=.=.=.=.=.=.=.= Também pode incluir métodos estáticos. Por exemplo, se vamos usar este UDT como parâmetro para uma função agregada U-SQL MIN, temos de definir < sobreposição do operador.

No início deste guia, demonstrámos um exemplo para a identificação do período fiscal a partir da data específica no formato. `Qn:Pn (Q1:P10)` O exemplo que se segue mostra como definir um tipo personalizado para valores do período fiscal.

Segue-se um exemplo de uma secção de código atrás com interface UDT e IFormatter personalizada:

```
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

O tipo definido inclui dois números: trimestre e mês. Os `==/!=/>/<` operadores `ToString()` e o método estático são definidos aqui.

Como mencionado anteriormente, a UDT pode ser usada em expressões SELECT, mas não pode ser usada em OUTPUTTER/EXTRATOR sem serialização personalizada. Ou tem de ser serializado `ToString()` como uma corda com ou utilizado com um OUTPUTTER/EXTRATOR personalizado.

Agora vamos discutir o uso da UDT. Numa secção de código atrás, mudamos a nossa função GetFiscalPeriod para a seguinte:

```
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

Como pode ver, devolve o valor do nosso tipo Deperíodo Fiscal.

Aqui fornecemos um exemplo de como usá-lo ainda mais no script base U-SQL. Este exemplo demonstra diferentes formas de invocação da UDT a partir do script U-SQL.

```
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

Aqui está um exemplo de uma secção completa de código atrás:

```
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
        }



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

## <a name="use-user-defined-aggregates-udagg"></a>Utilize agregados definidos pelo utilizador: UDAGG
Os agregados definidos pelo utilizador são quaisquer funções relacionadas com a agregação que não são enviadas fora da caixa com U-SQL. O exemplo pode ser um agregado para realizar cálculos de matemática personalizados, concatenações de cordas, manipulações com cordas, e assim por diante.

A definição de classe base agregada definida pelo utilizador é a seguinte:

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

**SqlUserDefinedAggregate** indica que o tipo deve ser registado como um agregado definido pelo utilizador. Esta aula não pode ser herdada.

O atributo SqlUserDefinedType é **opcional** para a definição uDAGG.


A classe base permite-lhe passar três parâmetros abstratos: dois como parâmetros de entrada e um como resultado. Os tipos de dados são variáveis e devem ser definidos durante a herança da classe.

```
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
* **A acumulação** é executada uma vez por cada valor. Fornece a principal funcionalidade para o algoritmo de agregação. Pode ser usado para agregar valores com vários tipos de dados que são definidos durante a herança da classe. Pode aceitar dois parâmetros de tipos de dados variáveis.
* **O fim** é executado uma vez por grupo de agregação no final do processamento para obter o resultado para cada grupo.

Para declarar os tipos de dados de entrada e de saída corretos, utilize a definição de classe da seguinte forma:

```
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: Primeiro parâmetro a acumular
* T2: Segundo parâmetro a acumular
* TResult: Devolução do tipo de rescisão

Por exemplo:

```
public class GuidAggregate : IAggregate<string, int, int>
```

ou

```
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>Use UDAGG em U-SQL
Para utilizar o UDAGG, primeiro defina-o em código atrás ou refira-o a partir da programabilidade existente DLL, tal como discutido anteriormente.

Em seguida, utilize a seguinte sintaxe:

```
AGG<UDAGG_functionname>(param1,param2)
```

Aqui está um exemplo de UDAGG:

```
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

E roteiro base U-SQL:

```
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
    * Extrair linha a linha
    * Usado para implementar a extração de dados a partir de ficheiros estruturados personalizados

* Os outputters definidos pelo utilizador
    * Linha de saída por linha
    * Usado para obter tipos de dados personalizados ou formatos de ficheiros personalizados

* Processadores definidos pelo utilizador
    * Pegue uma linha e produza uma linha
    * Usado para reduzir o número de colunas ou produzir novas colunas com valores derivados de um conjunto de colunas existente

* Aplicadores definidos pelo utilizador
    * Pegue uma linha e produza 0 a n linhas
    * Utilizado com EXTERIOR/CROSS APPLY

* Combinadores definidos pelo utilizador
    * Combina rowsets -- JOINs definidos pelo utilizador

* Redutores definidos pelo utilizador
    * Pegue as linhas e produza uma linha
    * Usado para reduzir o número de linhas

O UDO é tipicamente chamado explicitamente no script U-SQL como parte das seguintes declarações U-SQL:

* EXTRAIR
* SAÍDA
* PROCESSO
* COMBINE
* REDUZIR

> [!NOTE]  
> Os UDO's limitam-se a consumir a memória de 0,5Gb.  Esta limitação de memória não se aplica às execuções locais.

## <a name="use-user-defined-extractors"></a>Utilize extratores definidos pelo utilizador
A U-SQL permite-lhe importar dados externos utilizando uma declaração extrato. Uma declaração extrato pode utilizar extratores udo incorporados:  

* *Extratores.Texto()*: Fornece a extração de ficheiros de texto delimitados de diferentes codificações.

* *Extractors.Csv()*: Fornece a extração de ficheiros de valor separado simétrico (CSV) de diferentes codificações.

* *Extractors.Tsv()*: Fornece a extração de ficheiros de valor separado sapateado (TSV) de diferentes codificações.

Pode ser útil desenvolver um extrator personalizado. Isto pode ser útil durante a importação de dados se quisermos fazer qualquer uma das seguintes tarefas:

* Modificar os dados de entrada dividindo colunas e modificando valores individuais. A funcionalidade do PROCESSADOR é melhor para combinar colunas.
* Analisar dados não estruturados, tais como páginas Web e e-mails, ou dados semi-não estruturados, como xML/JSON.
* Parse dados em codificação não apoiada.

Para definir um extrator definido pelo utilizador, ou `IExtractor` UDE, precisamos criar uma interface. Todos os parâmetros de entrada para o extrator, tais como delimitadores de coluna/linha, e codificação, devem ser definidos no construtor da classe. A `IExtractor` interface deve também conter `IEnumerable<IRow>` uma definição para a sobreposição da seguinte forma:

```
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

O atributo **SqlUserDefinedDefinedExtractor** indica que o tipo deve ser registado como um extrator definido pelo utilizador. Esta aula não pode ser herdada.

SqlUserDefinedExtractor é um atributo opcional para a definição de UDE. Usou para definir a propriedade AtomicFileProcessing para o objeto UDE.

* bool AtomicFileProcessing   

* **verdade** = Indica que este extrator requer ficheiros de entrada atómica (JSON, XML, ...)
* **falso** = Indica que este extrator pode lidar com ficheiros divididos/distribuídos (CSV, SEQ, ...)

Os principais objetos de programabilidade da UDE são a **entrada** e **a saída.** O objeto de entrada é utilizado para `IUnstructuredReader`enumerar os dados de entrada como . O objeto de saída é utilizado para definir os dados de saída como resultado da atividade do extrator.

Os dados de entrada `System.IO.Stream` são `System.IO.StreamReader`acedidos através e .

Para a enumeração das colunas de entrada, dividimos primeiro o fluxo de entrada utilizando um delimitador de linha.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Em seguida, divida ainda mais a linha de entrada em partes da coluna.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

Para definir os dados `output.Set` de saída, usamos o método.

É importante entender que o extrator personalizado apenas produz colunas e valores que são definidos com a saída. Definir chamada de método.

```
output.Set<string>(count, part);
```

A saída do extrator real `yield return output.AsReadOnly();`é desencadeada pela chamada .

Segue-se o exemplo do extrator:

```
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

Neste cenário de utilização, o extrator regenera o GUID para a coluna "guid" e converte os valores da coluna "utilizador" para a caixa superior. Os extratores personalizados podem produzir resultados mais complicados analisando os dados de entrada e manipulando-os.

Segue-se o script base U-SQL que utiliza um extrator personalizado:

```
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

## <a name="use-user-defined-outputters"></a>Utilize os outputters definidos pelo utilizador
O outputter definido pelo utilizador é outro U-SQL UDO que lhe permite estender a funcionalidade U-SQL incorporada. Semelhante ao extrator, existem vários outputters incorporados.

* *Outputters.Text()*: Escreve dados para ficheiros de texto delimitados de diferentes codificações.
* *Outputters.Csv()*: Escreve dados para ficheiros de valor separado (CSV) separados pela vírda de diferentes codificações.
* *Outputters.Tsv()*: Escreve dados para ficheiros de valor separado (TSV) separados por separados de diferentes codificações.

O outputter personalizado permite-lhe escrever dados num formato personalizado definido. Isto pode ser útil para as seguintes tarefas:

* Escrever dados para ficheiros semi-estruturados ou não estruturados.
* A escrita de dados não suportacodificações.
* Modificar os dados de saída ou adicionar atributos personalizados.

Para definir o outputter definido pelo `IOutputter` utilizador, precisamos de criar a interface.

Segue-se `IOutputter` a implementação da classe base:

```
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Todos os parâmetros de entrada para o outputter, tais como delimitadores de coluna/linha, codificação, e assim por diante, devem ser definidos no construtor da classe. A `IOutputter` interface também deve `void Output` conter uma definição para sobreposição. O atributo `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` pode ser configurado opcionalmente para o processamento de ficheiros atómicos. Para mais informações, consulte os seguintes detalhes.

```
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

* `Output`é chamado para cada linha de entrada. Devolve o `IUnstructuredWriter output` set de fila.
* A classe Construtora é utilizada para passar parâmetros para o outputter definido pelo utilizador.
* `Close`é usado opcionalmente para substituir opcionalmente para libertar estado caro ou determinar quando a última linha foi escrita.

O atributo **SqlUserDefinedOutputter** indica que o tipo deve ser registado como um outputter definido pelo utilizador. Esta aula não pode ser herdada.

SqlUserDefinedOutputter é um atributo opcional para uma definição de saída definida pelo utilizador. É usado para definir a propriedade AtomicFileProcessing.

* bool AtomicFileProcessing   

* **verdade** = Indica que este outputter requer ficheiros de saída atómico (JSON, XML, ...)
* **falso** = Indica que este outputter pode lidar com ficheiros divididos/distribuídos (CSV, SEQ, ...)

Os principais objetos de programabilidade são **a linha** e **a saída.** O objeto **da linha** é usado `IRow` para enumerar os dados de saída como interface. **A saída** é utilizada para definir os dados de saída para o ficheiro alvo.

Os dados de saída `IRow` são acedidos através da interface. Os dados de saída são passados uma linha de cada vez.

Os valores individuais são enumerados chamando o método Get da interface IRow:

```
row.Get<string>("column_name")
```

Os nomes individuais `row.Schema`das colunas podem ser determinados através da chamada:

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Esta abordagem permite-lhe construir um outputter flexível para qualquer esquema de metadados.

Os dados de saída são `System.IO.StreamWriter`escritos para arquivar utilizando . O parâmetro de fluxo `output.BaseStream` está `IUnstructuredWriter output`definido como parte de .

Note que é importante descarregar o tampão de dados para o ficheiro após cada iteração de cada linha. Além disso, o `StreamWriter` objeto deve ser utilizado com o atributo Descartável ativado (predefinido) e com a palavra-chave que **utiliza:**

```
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

Caso contrário, ligue explicitamente o método Flush () após cada iteração. Mostramos isto no seguinte exemplo.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Definir cabeçalhos e rodapés para o outputter definido pelo utilizador
Para definir um cabeçalho, use um único fluxo de execução de iteração.

```
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

O código no `if (isHeaderRow)` primeiro bloco é executado apenas uma vez.

Para o rodapé, utilize a referência `System.IO.Stream` à`output.BaseStream`instância do objeto ( ). Escreva o rodapé no método Close() da `IOutputter` interface.  (Para mais informações, consulte o seguinte exemplo.)

Segue-se um exemplo de um outputter definido pelo utilizador:

```
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

E script base U-SQL:

```
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

### <a name="call-outputter-from-u-sql-base-script"></a>Saída de chamada do script base U-SQL
Para chamar um outputter personalizado a partir do script Base U-SQL, a nova instância do objeto do outputter tem de ser criada.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Para evitar criar uma instância do objeto no script base, podemos criar um invólucro de função, como mostrado no nosso exemplo anterior:

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

```
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Utilize processadores definidos pelo utilizador
O processador definido pelo utilizador, ou UDP, é um tipo de U-SQL UDO que lhe permite processar as linhas de entrada aplicando funcionalidades de programabilidade. A UDP permite combinar colunas, modificar valores e adicionar novas colunas, se necessário. Basicamente, ajuda a processar um conjunto de linhas para produzir elementos de dados necessários.

Para definir um UDP, precisamos criar uma `IProcessor` interface com o `SqlUserDefinedProcessor` atributo, que é opcional para a UDP.

Esta interface deve conter `IRow` a definição para a sobreposição do conjunto de interfaces, como mostra o seguinte exemplo:

```
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**SqlUserDefinedProcessor** indica que o tipo deve ser registado como um processador definido pelo utilizador. Esta aula não pode ser herdada.

O atributo Do Processador SqlUserDefinedé **opcional** para a definição uDP.

Os principais objetos de programabilidade são a **entrada** e **a saída.** O objeto de entrada é utilizado para enumerar colunas de entrada e saída, e para definir os dados de saída como resultado da atividade do processador.

Para a enumeração das colunas `input.Get` de entrada, utilizamos o método.

```
string column_name = input.Get<string>("column_name");
```

O parâmetro `input.Get` para o método é uma coluna `PRODUCE` que é `PROCESS` passada como parte da cláusula da declaração do script base U-SQL. Precisamos usar o tipo de dados correto aqui.

Para a saída, utilize o `output.Set` método.

É importante notar que o produtor personalizado apenas produz colunas `output.Set` e valores que são definidos com a chamada do método.

```
output.Set<string>("mycolumn", mycolumn);
```

A saída real do processador `return output.AsReadOnly();`é desencadeada pela chamada .

Segue-se um exemplo de processador:

```
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

Neste cenário de utilização, o processador está a gerar uma nova coluna chamada "full_description", combinando as colunas existentes - neste caso, "utilizador" em maiúsculas, e "des". Também regenera um GUID e devolve os valores ORIENTAis originais e novos.

Como pode ver pelo exemplo anterior, pode chamar `output.Set` métodos C# durante a chamada do método.

Segue-se um exemplo de script Base U-SQL que utiliza um processador personalizado:

```
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

## <a name="use-user-defined-appliers"></a>Utilizar aplicadores definidos pelo utilizador
Um aplicador definido por utilizador U-SQL permite-lhe invocar uma função C# personalizada para cada linha que é devolvida pela expressão exterior da tabela de uma consulta. A entrada direita é avaliada para cada linha a partir da entrada esquerda, e as linhas que são produzidas são combinadas para a saída final. A lista das colunas produzidas pelo operador APPLY é a combinação do conjunto de colunas à esquerda e à entrada direita.

O aplicador definido pelo utilizador está a ser invocado como parte da expressão USQL SELECT.

A chamada típica para o aplicador definido pelo utilizador parece ser a seguinte:

```
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Para obter mais informações sobre a utilização de aplicadores numa expressão SELECT, consulte [U-SQL SELECT A partir de CROSS APPLY e OUTER APPLY](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply).

A definição de classe base de aplicação definida pelo utilizador é a seguinte:

```
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

Para definir um aplicador definido pelo utilizador, precisamos de criar a `IApplier` interface com o atributo`SqlUserDefinedApplier`[ ], que é opcional para uma definição de aplicação definida pelo utilizador.

```
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

* A aplicação é chamada para cada linha da mesa exterior. Devolve o `IUpdatableRow` conjunto de linhas de saída.
* A classe Construtora é utilizada para passar parâmetros para o aplicador definido pelo utilizador.

**SqlUserDefinedApplier** indica que o tipo deve ser registado como um aplicador definido pelo utilizador. Esta aula não pode ser herdada.

**SqlUserDefinedApplier** é **opcional** para uma definição de aplicação definida pelo utilizador.


Os principais objetos de programabilidade são os seguintes:

```
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

As linhas de entrada `IRow` são passadas como entrada. As linhas de saída `IUpdatableRow` são geradas como interface de saída.

Nomes de colunas individuais podem ser determinados chamando o `IRow` método Schema.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Para obter os valores de `IRow`dados reais a partir `IRow` da entrada, utilizamos o método de interface Get() Get()

```
mycolumn = row.Get<int>("mycolumn")
```

Ou usamos o nome da coluna do esquema:

```
row.Get<int>(row.Schema[0].Name)
```

Os valores de `IUpdatableRow` saída devem ser definidos com saída:

```
output.Set<int>("mycolumn", mycolumn)
```

É importante entender que os aplicadores personalizados apenas aplicam colunas e valores que são definidos com `output.Set` chamada de método.

A saída real é `yield return output.AsReadOnly();`desencadeada pela chamada .

Os parâmetros de aplicação definidos pelo utilizador podem ser passados para o construtor. O aplicador pode devolver um número variável de colunas que precisam de ser definidas durante a chamada do aplicador no Script U-SQL base.

```
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Aqui está o exemplo do aplicador definido pelo utilizador:

```
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

Segue-se o script Base U-SQL para este aplicador definido pelo utilizador:

```
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

Neste cenário de utilização, o aplicador definido pelo utilizador funciona como um parser de valor delimitado em vírpara as propriedades da frota de automóveis. As linhas de ficheiro de entrada parecem as seguintes:

```
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

É um ficheiro TSV típico com um separador delimitado com uma coluna de propriedades que contém propriedades de carro, como a make and model. Essas propriedades devem ser analisadas nas colunas de mesa. O aplicador que é fornecido também permite gerar um número dinâmico de propriedades no conjunto de linhas de resultados, com base no parâmetro que passou. Você pode gerar qualquer propriedades ou um conjunto específico de propriedades apenas.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

O aplicador definido pelo utilizador pode ser chamado como uma nova instância de objeto aplicador:

```
CROSS APPLY new MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

Ou com a invocação de um método de fábrica de invólucro:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>Utilize combinadores definidos pelo utilizador
O combinador definido pelo utilizador, ou UDC, permite combinar linhas de rowsets esquerdo e direito, com base na lógica personalizada. Combinador definido pelo utilizador é utilizado com expressão COMBINE.

Um combinador está a ser invocado com a expressão COMBINE que fornece as informações necessárias sobre tanto as linhas de entrada, as colunas de agrupamento, o esquema de resultados esperados e informações adicionais.

Para chamar um combinador num script base U-SQL, usamos a seguinte sintaxe:

```
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

Para mais informações, consulte [A Expressão COMBINE (U-SQL)](/u-sql/statements-and-expressions/combine-expression).

Para definir um combinador definido pelo utilizador, precisamos de criar a `ICombiner` interface com o atributo`SqlUserDefinedCombiner`[ ], que é opcional para uma definição de Combiner definida pelo utilizador.

Definição de classe base: `ICombiner`

```
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

A implementação `ICombiner` personalizada de uma interface `IEnumerable<IRow>` deve conter a definição para um overover Combine.

```
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

O atributo **SqlUserDefinedCombiner** indica que o tipo deve ser registado como um combinador definido pelo utilizador. Esta aula não pode ser herdada.

**SqlUserDefinedCombiner** é usado para definir a propriedade do modo Combiner. É um atributo opcional para uma definição de combinador definida pelo utilizador.

Modo CombinerMode

O CombinerMode enum pode ter os seguintes valores:

* Full (0) Cada linha de saída depende potencialmente de todas as linhas de entrada da esquerda e da direita com o mesmo valor-chave.

* Esquerda (1) Cada linha de saída depende de uma única linha de entrada a partir da esquerda (e potencialmente todas as linhas da direita com o mesmo valor-chave).

* Direita (2) Cada linha de saída depende de uma única linha de entrada da direita (e potencialmente todas as linhas da esquerda com o mesmo valor-chave).

* Interior (3) Cada linha de saída depende de uma única linha de entrada da esquerda e da direita com o mesmo valor.

Exemplo:`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`[ ]


Os principais objetos de programabilidade são:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

As linhas de entrada são passadas como tipo de interface **esquerdo** e **direito.** `IRowset` Ambos os conjuntos de linhas devem ser enumerados para processamento. Só pode enumerar cada interface uma vez, por isso temos de enumerar e cache se necessário.

Para fins de cache, podemos\<\> criar um tipo de estrutura de memória tipo Lista `IRow` T como resultado de uma execução de consulta DE LINQ, especificamente Lista<>. O tipo de dados anónimos também pode ser utilizado durante a enumeração.

Consulte [a introdução a consultas LINQ (C#)](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) para obter mais informações sobre consultas LINQ e Interface T\<\> [\<\> IEnumerable](/dotnet/api/system.collections.generic.ienumerable-1) para obter mais informações sobre a interface IEnumerable T.

Para obter os valores de `IRowset`dados reais a partir `IRow` da entrada, utilizamos o método de interface Get() Get()

```
mycolumn = row.Get<int>("mycolumn")
```

Nomes de colunas individuais podem ser determinados chamando o `IRow` método Schema.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Ou usando o nome da coluna do esquema:

```
c# row.Get<int>(row.Schema[0].Name)
```

A enumeração geral com LINQ parece ser a seguinte:

```
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Depois de enumerar ambas as linhas, vamos percorrer todas as linhas. Para cada linha no lado esquerdo, vamos encontrar todas as linhas que satisfaçam a condição do nosso combinador.

Os valores de `IUpdatableRow` saída devem ser definidos com a saída.

```
output.Set<int>("mycolumn", mycolumn)
```

A saída real é desencadeada `yield return output.AsReadOnly();`ligando para .

Segue-se um exemplo combinador:

```
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

Neste cenário de utilização, estamos a construir um relatório de análise para o retalhista. O objetivo é encontrar todos os produtos que custam mais de $20.000 e que vendam através do site mais rapidamente do que através do retalhista regular dentro de um determinado prazo.

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

Um combinador definido pelo utilizador pode ser chamado como uma nova instância do objeto aplicável:

```
USING new MyNameSpace.MyCombiner();
```


Ou com a invocação de um método de fábrica de invólucro:

```
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>Utilize redutores definidos pelo utilizador

O U-SQL permite-lhe escrever redutores de linha personalizados em C# utilizando a estrutura de extebilidade do operador definida pelo utilizador e implementando uma interface IReducer.

O redutor definido pelo utilizador, ou UDR, pode ser utilizado para eliminar linhas desnecessárias durante a extração de dados (importação). Também pode ser usado para manipular e avaliar linhas e colunas. Com base na lógica de programabilidade, também pode definir quais as linhas que precisam de ser extraídas.

Para definir uma classe UDR, `IReducer` precisamos criar `SqlUserDefinedReducer` uma interface com um atributo opcional.

Esta interface de classe deve `IEnumerable` conter uma definição para a sobreposição do conjunto de linhas da interface.

```
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

O atributo **SqlUserDefinedReducer** indica que o tipo deve ser registado como um redutor definido pelo utilizador. Esta aula não pode ser herdada.
**SqlUserDefinedReducer** é um atributo opcional para uma definição de redutor definida pelo utilizador. É usado para definir propriedade isrecursiva.

* bool IsRecursive    
* **verdadeiro** = Indica se este Redutor é associativo e comutativo

Os principais objetos de programabilidade são a **entrada** e **a saída.** O objeto de entrada é utilizado para enumerar as linhas de entrada. A saída é utilizada para definir linhas de saída como resultado da redução da atividade.

Para a enumeração das linhas `Row.Get` de entrada, utilizamos o método.

```
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

O parâmetro para `Row.Get` o método é uma coluna que `PRODUCE` passou `REDUCE` como parte da classe da declaração do script base U-SQL. Precisamos usar o tipo de dados correto aqui também.

Para a saída, utilize o `output.Set` método.

É importante entender que o redutor personalizado apenas `output.Set` produz valores que são definidos com a chamada do método.

```
output.Set<string>("mycolumn", guid);
```

A saída real do redutor `yield return output.AsReadOnly();`é desencadeada por chamada .

Segue-se um exemplo redutor:

```
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

Neste cenário de utilização, o redutor está a saltar filas com um nome de utilizador vazio. Para cada linha em linha, lê cada coluna necessária e avalia o comprimento do nome do utilizador. Só produz a linha real se o comprimento do valor do nome do utilizador for superior a 0.

Segue-se o script Base U-SQL que utiliza um redutor personalizado:

```
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
