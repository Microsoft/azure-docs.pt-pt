---
title: Guia de programabilidade U-SQL para O Lago de Dados Azure
description: Conheça a visão geral do U-SQL e a programabilidade do UDF no Azure Data Lake Analytics para permitir a criação de um bom script USQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: d263f2616607a96a8aa14f1ad1c06330b1b44644
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510867"
---
# <a name="u-sql-programmability-guide-overview"></a>Visão geral do guia de programabilidade U-SQL

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

## <a name="next-steps"></a>Passos seguintes
* [Guia de programabilidade U-SQL - UDT e UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)
* [Guia de programabilidade U-SQL - UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)