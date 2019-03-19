---
title: Guia de programação para U-SQL para o Azure Data Lake
description: Saiba mais sobre o conjunto de serviços no Azure Data Lake Analytics que permitem-lhe criar uma plataforma de macrodados com base na cloud.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.topic: conceptual
ms.date: 06/30/2017
ms.openlocfilehash: f0f5a4ee5206201cca20e705011126e6cf472a1a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57835457"
---
# <a name="u-sql-programmability-guide"></a>Guia de programação para U-SQL

U-SQL é uma linguagem de consulta que foi concebida para o tipo de dados grande de cargas de trabalho. Uma das funcionalidades únicas de U-SQL é a combinação da linguagem declarativa semelhante a SQL com a extensibilidade e programabilidade fornecido pelo c#. Neste guia, vamos concentrar-se a extensibilidade e programabilidade da linguagem U-SQL que está ativada por c#.

## <a name="requirements"></a>Requisitos

Transfira e instale [do Azure Data Lake Tools para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

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

Este script define dois conjuntos de linhas: `@a` e `@results`. Conjunto de linhas `@results` é definido de `@a`.

## <a name="c-types-and-expressions-in-u-sql-script"></a>Tipos de c# e expressões no script de U-SQL

Uma expressão de U-SQL é uma expressão de c#, combinada com operações de lógicas de U-SQL, tais `AND`, `OR`, e `NOT`. Expressões de U-SQL pode ser utilizadas com SELECT, EXTRAÇÃO, onde, tendo, agrupar por e declarar. Por exemplo, o script seguinte analisa uma cadeia de caracteres como um valor de DateTime.

```
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

O fragmento seguinte analisa uma cadeia de caracteres como valor de DateTime numa instrução DECLARE.

```
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>Utilizar c# expressões para conversões de tipo de dados

O exemplo seguinte demonstra como pode fazer uma conversão de dados datetime usando expressões do c#. Neste cenário específico, os dados de datetime da cadeia de caracteres são convertidos para datetime padrão com a notação de tempo de 00:00:00 meia-noite.

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

### <a name="use-c-expressions-for-todays-date"></a>Utilize expressões do c# para a data de hoje

Para obter a data de hoje, podemos usar a seguinte expressão do c#: `DateTime.Now.ToString("M/d/yyyy")`

Eis um exemplo de como usar essa expressão num script:

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
## <a name="using-net-assemblies"></a>Usando .NET assemblies

Modelo de extensibilidade U-SQL depende muito a capacidade de adicionar código personalizado a partir de assemblies do .NET. 

### <a name="register-a-net-assembly"></a>Registrar um assembly do .NET

Utilize o `CREATE ASSEMBLY` instrução para colocar um assembly do .NET num banco de dados U-SQL. Em seguida, scripts U-SQL podem utilizar esses assemblies ao utilizar o `REFERENCE ASSEMBLY` instrução. 

O código a seguir mostra como registrar um assembly:

```
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

O código seguinte mostra como fazer referência a um assembly:

```
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Consulte a [assembly do registo-instruções](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/) que aborda esse tópico em mais detalhes.


### <a name="use-assembly-versioning"></a>Utilizar o controlo de versões do assembly
Atualmente, o U-SQL utiliza o .NET Framework versão 4.5. Por isso, certifique-se de que os seus próprios assemblies são compatíveis com essa versão do runtime.

Conforme mencionado anteriormente, o código de execuções de U-SQL num formato de 64 bits (x64). Por isso, certifique-se de que o seu código é compilado para executar em x64. Caso contrário, obterá o erro de formato incorreto mostrado anteriormente.

Carregado cada DLL do assembly e o arquivo de recurso, como um tempo de execução diferente, um assembly nativo ou um ficheiro de configuração, pode ter um máximo de 400 MB. O tamanho total dos recursos implementados, por meio do recurso de implementar ou através de referências a assemblies e respetivos ficheiros adicionais, não pode exceder os 3 GB.

Por fim, tenha em atenção que cada base de dados U-SQL só pode conter uma versão de qualquer determinado assembly. Por exemplo, se precisar de tanto a versão 7, como a versão 8 da biblioteca NewtonSoft Json.NET, terá de registá-los no duas bases de dados diferentes. Além disso, cada script só pode fazer referência a uma versão de um determinado assembly DLL. Nesse sentido, o U-SQL segue a c# assembly de gerenciamento e controle de versão semântica.

## <a name="use-user-defined-functions-udf"></a>Utilize as funções definidas pelo utilizador: UDF
Funções definidas pelo utilizador do U-SQL ou UDF, está programando as rotinas que aceitam parâmetros, executam uma ação (por exemplo, um cálculo complexo) e retornam o resultado da ação como um valor. O valor devolvido do UDF só pode ser um valor de escalar único. UDF do U-SQL pode ser chamado no script de base de U-SQL como qualquer outra linguagem c# função escalar.

Recomendamos que inicializar funções definidas pelo utilizador do U-SQL como **pública** e **estático**.

```
public static string MyFunction(string param1)
{
    return "my result";
}
```

Primeiro vamos examinar o exemplo simples de criação de uma UDF.

Neste cenário de casos de utilização, é necessário determinar o período fiscal, incluindo o trimestre fiscal e mês fiscal do primeiro início de sessão para o usuário específico. O primeiro mês fiscal do ano em nosso cenário é Junho.

Para calcular o período fiscal, apresentamos a seguinte função do c#:

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

Ele simplesmente calcula mês fiscal e trimestre e devolve um valor de cadeia de caracteres. Junho, o primeiro mês do trimestre fiscal primeiro, vamos utilizar "Q1:P1". Para Julho, podemos utilizar "Q1:P2" e assim por diante.

Esta é uma regular função c# que vamos usar em nosso projeto U-SQL.

Eis o aspeto a seção de código-behind neste cenário:

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

Agora, vamos chamar essa função de base scripts U-SQL. Para fazer isso, temos de fornecer um nome totalmente qualificado para a função, incluindo o espaço de nomes, que neste caso é NameSpace.Class.Function(parameter).

```
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Segue-se o script de base do U-SQL real:

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

Este exemplo demonstra um uso simple da inline UDF em U-SQL.

### <a name="keep-state-between-udf-invocations"></a>Manter o estado entre invocações de UDF
Objetos de programação para U-SQL c# podem ser mais sofisticados, utilizando a interatividade as variáveis globais de código-behind. Vamos examinar o seguinte cenário de casos de utilização de negócios.

Em grandes organizações, os usuários podem alternar entre uma variedade de aplicativos internos. Pode incluir o Microsoft Dynamics CRM, Power BI e assim por diante. Os clientes podem querer aplicar uma análise de telemetria de como os utilizadores alternarem entre diferentes aplicativos, quais são as tendências de utilização, e assim por diante. O objetivo para as empresas é para otimizar a utilização da aplicação. Eles também podem querer combinar diferentes aplicativos ou as rotinas de início de sessão específicas.

Para atingir esse objetivo, temos de determinar os IDs de sessão e lag tempo entre a última sessão que ocorreram.

Precisamos encontrar um anterior início de sessão e, em seguida, atribuir este início de sessão para todas as sessões que estão a ser geradas para a mesma aplicação. O primeiro desafio é que o script de base de U-SQL não permite-nos aplicar cálculos em colunas calculadas já com função de ATRASO. O segundo desafio é que temos que manter a sessão específica para todas as sessões dentro do mesmo período de tempo.

Para resolver este problema, usamos uma variável global dentro de uma seção de código-behind: `static public string globalSession;`.

Essa variável global é aplicada ao conjunto de linhas completo durante a execução do nosso script.

Aqui é a seção de código-behind do nosso programa de U-SQL:

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

Este exemplo mostra a variável global `static public string globalSession;` usados dentro da `getStampUserSession` função e obter reinicializada sempre que o parâmetro de sessão é alterado.

O script de base do U-SQL é o seguinte:

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

Função `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` é chamado aqui durante o segundo conjunto de linhas cálculo da memória. Ele passa a `UserSessionTimestamp` coluna e devolve o valor até `UserSessionTimestamp` foi alterado.

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

Este exemplo demonstra um cenário de casos de utilização mais complicado na qual usamos uma variável global dentro de uma seção de código-behind que é aplicada ao conjunto de linhas de memória completo.

## <a name="use-user-defined-types-udt"></a>Utilize os tipos definidos pelo utilizador: UDT
Tipos definidos pelo utilizador, ou UDT, é outro recurso de programação do U-SQL. U-SQL UDT funciona como um c# definidas pelo utilizador tipo regular. C# é uma linguagem com rigidez de tipos que permite o uso de tipos definidos pelo utilizador incorporados e personalizados.

U-SQL implicitamente não é possível serializar ou anular a serialização UDTs arbitrários, quando o UDT é passada entre vértices em conjuntos de linhas. Isso significa que o utilizador tem de fornecer um formatador explícita utilizando a interface do IFormatter. Isto fornece U-SQL com o serializar e anular a serialização de métodos para o UDT.

> [!NOTE]
> Extratores incorporadas e operadores de U-SQL atualmente não é possível serializar ou anular a serialização de dados UDT para ou a partir de ficheiros, mesmo com o conjunto de IFormatter. Quando está gravando dados UDT num arquivo com a instrução de saída ou lê-lo com um extrator, temos passá-lo como uma cadeia ou matriz de bytes. Em seguida, chamar a serialização e desserialização código (ou seja, método de ToString () a UDT) explicitamente. Definido pelo utilizador extratores e operadores, por outro lado, podem ler e escrever os UDTs.

Se vamos tentar usar UDT no EXTRATOR ou OUTPUTTER (de SELEÇÃO anterior), conforme mostrado aqui:

```
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Estamos a receber o erro seguinte:

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

Para trabalhar com UDT em outputter, temos tanto para serializá-lo com o método ToString () de cadeias de caracteres ou criar um outputter personalizado.

Os UDTs atualmente não não possível utilizar GROUP BY. Se UDT é utilizado em GROUP BY, é emitido o erro seguinte:

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

Para definir um UDT, temos de:

* Adicione os espaços de nomes seguintes:

```
using Microsoft.Analytics.Interfaces
using System.IO;
```

* Adicionar `Microsoft.Analytics.Interfaces`, que é necessário para as interfaces UDT. Além disso, `System.IO` podem ser necessários para definir a interface do IFormatter.

* Defina um tipo definido pelo utilizado com o atributo SqlUserDefinedType.

**SqlUserDefinedType** é utilizada para marcar uma definição de tipo num assembly como um tipo definido pelo utilizador (UDT) em U-SQL. As propriedades no atributo refletem as características físicas do UDT. Esta classe não pode ser herdada.

SqlUserDefinedType é um atributo necessário para a definição de UDT.

O construtor da classe:  

* SqlUserDefinedTypeAttribute (formatador de tipo)

* Formatador de tipo: Parâmetro para definir um formatador UDT – mais especificamente, o tipo de necessário a `IFormatter` interface têm de ser transmitida aqui.

```
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* UDT típico também requer a definição da IFormatter interface, conforme mostrado no exemplo a seguir:

```
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

O `IFormatter` interface serializa e anula a serialização um gráfico de objeto com o tipo de raiz de \<typeparamref nome = "T" >.

\<nome de typeparam = "T" > o tipo de raiz para o gráfico do objeto para serializar e anular a serialização.

* **Deserialize**: Anula a serialização de dados no fluxo fornecido e reconstitutes o gráfico de objetos.

* **Serializar**: Serializa um objeto ou o gráfico de objetos com o raiz indicado no fluxo fornecido.

`MyType` Instância: Instância do tipo.  
`IColumnWriter` escritor / `IColumnReader` leitor: O fluxo subjacente de coluna.  
`ISerializationContext` Contexto: Enumeração que define um conjunto de sinalizadores que especifica o contexto de origem ou de destino para o fluxo durante a serialização.

* **Intermediários**: Especifica que o contexto de origem ou de destino não é um armazenamento persistente.

* **Persistência**: Especifica que o contexto de origem ou de destino é um armazenamento persistente.

Como um regular c# tipo, uma definição de U-SQL UDT pode incluir substituições para os operadores, como c++ /CLI = = /! =. Ele também pode incluir métodos estáticos. Por exemplo, se, vamos utilizar esta UDT como um parâmetro para uma função de agregação MIN de U-SQL, precisamos definir < a substituição de operador.

Anteriormente neste guia, demonstramos um exemplo para a identificação de período fiscal a contar da data específica no formato `Qn:Pn (Q1:P10)`. O exemplo seguinte mostra como definir um tipo personalizado para os valores de período fiscais.

Segue-se um exemplo de uma seção de código-behind com interface UDT e IFormatter personalizada:

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

O tipo definido inclui dois números: trimestre e mês. Operadores `==/!=/>/<` e o método estático `ToString()` são definidos aqui.

Como mencionado anteriormente, UDT pode ser utilizado em expressões de SELECT, mas não é possível utilizar OUTPUTTER/EXTRATOR sem serialização personalizada. Ele tem de ser serializado como uma cadeia de caracteres com `ToString()` utilizadas com um OUTPUTTER/EXTRATOR.

Agora vamos falar sobre a utilização de UDT. Numa seção de código-behind, Alterámos nossa função GetFiscalPeriod ao seguinte:

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

Como pode ver, ele retorna o valor do nosso tipo FiscalPeriod.

Aqui fornecemos um exemplo de como usá-lo ainda mais no script de base de U-SQL. Este exemplo demonstra as diferentes formas de invocação de UDT do script U-SQL.

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

Eis um exemplo de uma seção de código completo-behind:

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

## <a name="use-user-defined-aggregates-udagg"></a>Utilize agregações definidas pelo utilizador: UDAGG
Agregações definidas pelo utilizador são quaisquer funções relacionadas a agregação, que são fornecidas não out-of-the-box com U-SQL. O exemplo pode ser uma agregação para realizar cálculos personalizados de matemática, concatenações de cadeia de caracteres, manipulações de cadeias de caracteres e assim por diante.

Segue-se a definição de classe base agregado definido pelo utilizador:

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

É o atributo SqlUserDefinedType **opcional** para definição de UDAGG.


A classe base, pode passar três parâmetros abstratos: dois como parâmetros de entrada e outro como o resultado. Os tipos de dados são variáveis e devem ser definidos durante a herança de classe.

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

* **Init** invoca uma vez para cada grupo durante o cálculo. Ele fornece uma rotina de inicialização para cada grupo de agregação.  
* **Acumular** é executado uma vez para cada valor. Fornece a funcionalidade principal para o algoritmo de agregação. Ele pode ser usado para valores de agregação com vários tipos de dados que estão definidos durante a herança de classe. Ele pode aceitar dois parâmetros de tipos de dados da variável.
* **Terminar** é executado uma vez por grupo de agregação no final do processamento para produzir o resultado para cada grupo.

Para declarar a entrada correta e tipos de dados de saída, utilize a definição de classe da seguinte forma:

```
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: Primeiro parâmetro para acumular
* T2: Segundo parâmetro para acumular
* TResult: Tipo de retorno de encerramento

Por exemplo:

```
public class GuidAggregate : IAggregate<string, int, int>
```

ou

```
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>Utilizar UDAGG em U-SQL
Para utilizar UDAGG, defini-lo no code-behind ou referenciá-lo a partir da DLL de programação existente, como discutido anteriormente.

Em seguida, utilize a seguinte sintaxe:

```
AGG<UDAGG_functionname>(param1,param2)
```

Eis um exemplo de UDAGG:

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

E o script de U-SQL base:

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

Neste cenário de casos de utilização, concatenamos os GUIDs de classe para os utilizadores específicos.

## <a name="use-user-defined-objects-udo"></a>Utilize objetos definidos pelo utilizador: UDO
U-SQL permite-lhe definir os objetos de programação personalizada, que são chamados de objetos definidos pelo utilizador ou UDO.

Segue-se uma lista de UDO em U-SQL:

* Extractors definidas pelo utilizador
    * Extrair linha por linha
    * Usado para implementar a extração de dados de ficheiros estruturados personalizados

* Operadores definidos pelo utilizador
    * Linha por linha de saída
    * Utilizado para tipos de dados personalizados de saída ou arquivo personalizado formatos

* Processadores definidos pelo utilizador
    * Necessitam de uma linha e produzem uma linha
    * Usado para reduzir o número de colunas ou produzir novas colunas com valores que são derivados de um conjunto de coluna existente

* Appliers definidas pelo utilizador
    * Necessitam de uma linha e produzem 0 para n linhas
    * Utilizado com entre/OUTER APPLY

* Combiners definidas pelo utilizador
    * Combina os conjuntos de linhas – as associações definidas pelo utilizador

* Redutores definidas pelo utilizador
    * Necessitam de n linhas e produzem uma linha
    * Usado para reduzir o número de linhas

UDO é normalmente chamada explicitamente no script de U-SQL como parte das declarações de U-SQL a seguir:

* EXTRAIR
* SAÍDA
* PROCESSO
* COMBINAR
* REDUZIR

> [!NOTE]  
> UDO está limitados a consumir 0,5 Gb de memória.  Esta limitação de memória não é aplicável a execuções locais.

## <a name="use-user-defined-extractors"></a>Utilizar extractors definidas pelo utilizador
U-SQL permite-lhe importar dados externos através de uma instrução de EXTRAÇÃO. Uma instrução de EXTRAÇÃO pode utilizar extratores UDO incorporadas:  

* *Extractors.Text()*: Fornece a extração dos ficheiros de texto delimitado de codificações diferentes.

* *Extractors.Csv()*: Fornece a extração de valores separados por vírgulas ficheiros (CSV) de codificações diferentes.

* *Extractors.Tsv()*: Fornece a extração do valor separado por tabulações (TSV) ficheiros. o das codificações diferentes.

Pode ser útil desenvolver um extrator. Isto pode ser útil durante a importação de dados para efetuar qualquer uma das seguintes tarefas:

* Modificar dados de entrada dividir colunas e modificar valores individuais. A funcionalidade do processador é melhor para combinar as colunas.
* Analisar dados não estruturados, como páginas da Web e os e-mails ou dados semiestruturados e não estruturados, como XML/JSON.
* Analisar dados na codificação não suportado.

Para definir um extrator definidas pelo utilizador ou UIR, temos de criar um `IExtractor` interface. Todos os parâmetros para extrator, tais como delimitadores de linha/coluna de entrada e de codificação, têm de ser definidos no construtor da classe. O `IExtractor` interface também deve conter uma definição para o `IEnumerable<IRow>` substituir da seguinte forma:

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

O **SqlUserDefinedExtractor** atributo indica que o tipo deve ser registado como um extrator definidas pelo utilizador. Esta classe não pode ser herdada.

SqlUserDefinedExtractor é um atributo opcional para a definição de UIR. Ele é usado para definir a propriedade AtomicFileProcessing para o objeto de UIR.

* bool     AtomicFileProcessing   

* **Verdadeiro** = indica que este extrator requer ficheiros de entrada atômicos (JSON, XML,...)
* **FALSO** = indica que este extrator pode lidar com arquivos de dividir / distribuídos (CSV, SEQ,...)

Os objetos de programação UIR principais são **entrada** e **saída**. O objeto de entrada é utilizado para enumerar os dados de entrada como `IUnstructuredReader`. O objeto de saída é usado para definir os dados de saída como resultado da atividade de extrator.

Os dados de entrada são acedidos através de `System.IO.Stream` e `System.IO.StreamReader`.

Para a enumeração de colunas de entrada, dividimos primeiro o fluxo de entrada utilizando um delimitador de linha.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Em seguida, divida ainda mais linhas de entrada em partes de coluna.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

Para definir os dados de saída, usamos o `output.Set` método.

É importante compreender que o extrator devolve apenas colunas e valores que estão definidos com a saída. Definir a chamada de método.

```
output.Set<string>(count, part);
```

A saída de extrator real é acionada ao chamar `yield return output.AsReadOnly();`.

Segue-se o exemplo de extrator:

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

Neste cenário de casos de utilização, o extrator regenera o GUID para a coluna "guid" e converte os valores de coluna de "usuário" em maiúsculas. Extratores personalizados podem produzir resultados mais complicados ao analisar dados de entrada e manipulá-lo.

Segue-se base script de U-SQL que usa um extrator:

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

## <a name="use-user-defined-outputters"></a>Utilize operadores definidos pelo utilizador
Definido pelo utilizador outputter é outro UDO de U-SQL permite-lhe expandir a funcionalidade de U-SQL incorporada. Assim como o extrator, existem vários operadores incorporadas.

* *Outputters.Text()*: Escreve dados para ficheiros de texto delimitado de codificações diferentes.
* *Outputters.Csv()*: Escreve dados para ficheiros de valores separados por vírgulas (CSV) de codificações diferentes.
* *Outputters.Tsv()*: Escreve dados para ficheiros de valor separado por tabulações (TSV) de codificações diferentes.

Outputter personalizado permite-lhe escrever dados num formato personalizado definido. Isso pode ser útil para as seguintes tarefas:

* Escrever dados estruturados ou semiestruturados de ficheiros.
* Escrita de dados não suportado codificações.
* Modificar os dados de saída ou adicionar atributos personalizados.

Para definir outputter definidas pelo utilizador, temos de criar o `IOutputter` interface.

Segue-se a base de `IOutputter` implementação da classe:

```
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Todos os parâmetros de entrada para o outputter, como delimitadores de linha/coluna, codificação e assim por diante, tem de ser definido no construtor da classe. O `IOutputter` interface também deve conter uma definição para `void Output` substituir. O atributo `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` , opcionalmente, pode ser definido para o processamento de arquivos atômicas. Para obter mais informações, consulte os seguintes detalhes.

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

* `Output` é chamado para cada linha de entrada. Devolve o `IUnstructuredWriter output` conjunto de linhas.
* A classe de construtor é usada para passar parâmetros para o outputter definidas pelo utilizador.
* `Close` é utilizada para, opcionalmente, substituir a versão Estado Caro ou a determinar quando a última linha foi escrita.

**SqlUserDefinedOutputter** atributo indica que o tipo deve ser registado como um outputter definidas pelo utilizador. Esta classe não pode ser herdada.

SqlUserDefinedOutputter é um atributo opcional para obter uma definição de outputter definidas pelo utilizador. Ele é usado para definir a propriedade AtomicFileProcessing.

* bool     AtomicFileProcessing   

* **Verdadeiro** = indica que este outputter requer ficheiros de saída atômica (JSON, XML,...)
* **FALSO** = indica que este outputter pode lidar com arquivos de dividir / distribuídos (CSV, SEQ,...)

Os objetos de programação principais são **linha** e **saída**. O **linha** objeto é usado para enumerar os dados de saída como `IRow` interface. **Saída** é usado para definir os dados de saída para o ficheiro de destino.

Os dados de saída são acedidos através do `IRow` interface. Dados de saída são passados uma linha por vez.

Os valores individuais são enumerados chamando o método Get de rozhraní IRow:

```
row.Get<string>("column_name")
```

Nomes de coluna individual podem ser determinados chamando `row.Schema`:

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Esta abordagem permite-lhe criar um outputter flexível para qualquer esquema de metadados.

Os dados de saída são escritos no ficheiro ao utilizar `System.IO.StreamWriter`. O parâmetro de fluxo é definido como `output.BaseStream` como parte da `IUnstructuredWriter output`.

Tenha em atenção que é importante liberar o buffer de dados para o ficheiro após cada iteração de linha. Além disso, o `StreamWriter` objeto tem de ser utilizado com o atributo descartável ativada (predefinição) e com o **usando** palavra-chave:

```
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

Caso contrário, chame explicitamente o método de Flush () após cada iteração. Vamos mostrar isso no exemplo a seguir.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Conjunto de cabeçalhos e rodapés outputter definidas pelo utilizador
Para definir um cabeçalho, utilize o fluxo de execução única iteração.

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

O código na primeira `if (isHeaderRow)` bloco é executado apenas uma vez.

Para o rodapé, utilize a referência à instância do `System.IO.Stream` objeto (`output.BaseStream`). Escrever o rodapé no método Close () da `IOutputter` interface.  (Para obter mais informações, consulte o exemplo a seguir).

Segue-se um exemplo de um outputter definidas pelo utilizador:

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

E o script de base de U-SQL:

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

### <a name="call-outputter-from-u-sql-base-script"></a>Outputter de chamada de script de base de U-SQL
Para chamar um outputter personalizado do script U-SQL base, a nova instância do objeto outputter deve ser criado.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Para evitar a criação de uma instância do objeto no base script, podemos criar um wrapper de função, conforme mostrado no nosso exemplo anterior:

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

Neste caso, a chamada original é semelhante ao seguinte:

```
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Utilize processadores definidos pelo utilizador
Processador definido pelo utilizador ou UDP, é um tipo de UDO de U-SQL que permite-lhe processar as linhas de entrada através da aplicação de recursos de programação. UDP permite-lhe combinar colunas, modificar os valores e adicionar novas colunas, se necessário. Basicamente, ele ajuda a processar um conjunto de linhas para produzir os elementos de dados necessários.

Para definir um UDP, temos de criar uma `IProcessor` interface com o `SqlUserDefinedProcessor` atributo, que é opcional para UDP.

Essa interface deve conter a definição para o `IRow` conjunto de linhas de interface substituir, conforme mostrado no exemplo a seguir:

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

**SqlUserDefinedProcessor** indica que o tipo deve ser registado como um processador definido pelo utilizador. Esta classe não pode ser herdada.

É o atributo SqlUserDefinedProcessor **opcional** para definição de UDP.

Os objetos de programação principais são **entrada** e **saída**. O objeto de entrada é utilizado para enumerar as colunas de entrada e saída e para definir os dados de saída como resultado da atividade de processador.

Enumeração de colunas de entrada, vamos utilizar o `input.Get` método.

```
string column_name = input.Get<string>("column_name");
```

O parâmetro `input.Get` método é uma coluna que é transmitida como parte dos `PRODUCE` cláusula do `PROCESS` declaração do Estado do script de base do U-SQL. É necessário usar o tipo de dados correto aqui.

Para a saída, utilize o `output.Set` método.

É importante observar que produtor personalizado produz apenas colunas e os valores que são definidas com o `output.Set` chamada de método.

```
output.Set<string>("mycolumn", mycolumn);
```

A saída real do processador é acionada ao chamar `return output.AsReadOnly();`.

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

Neste cenário de casos de utilização, o processador está a gerar uma nova coluna chamada "full_description" ao combinar as colunas existentes – nesse caso, "utilizador" em maiúscula e "des". Ele também gera novamente um GUID e devolve os valores GUID novos e originais.

Como pode ver no exemplo anterior, pode chamar métodos c# durante `output.Set` chamada de método.

Segue-se um exemplo de script de U-SQL base que utiliza um processador personalizado:

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

## <a name="use-user-defined-appliers"></a>Utilizar appliers definidas pelo utilizador
Um applier definidas pelo utilizador do U-SQL permite-lhe invocar uma função do c# personalizada para cada linha, que é devolvida pela expressão de tabela externa de uma consulta. Entrada da direita é avaliada para cada linha da entrada da esquerda, e as linhas que são produzidas são combinadas para o resultado final. A lista de colunas que são produzidos pelo operador APPLY são a combinação do conjunto de colunas no lado esquerdo e a entrada da direita.

Está sendo solicitada applier definidas pelo utilizador como parte da expressão SELECIONE u-SQL.

A chamada típica para o applier definidas pelo utilizador é semelhante ao seguinte:

```
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Para obter mais informações sobre como utilizar appliers numa expressão SELECT, consulte [U-SQL SELECIONE selecionando de CROSS APPLY e OUTER APPLY](https://msdn.microsoft.com/library/azure/mt621307.aspx).

A definição de classe base definida pelo utilizador applier é o seguinte:

```
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

Para definir um applier definidas pelo utilizador, temos de criar o `IApplier` interface com o [`SqlUserDefinedApplier`] atributo, que é opcional para uma definição de applier definidas pelo utilizador.

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

* Aplicar é chamado para cada linha da tabela externa. Devolve o `IUpdatableRow` conjunto de linhas de saída.
* A classe de construtor é usada para passar parâmetros para o applier definidas pelo utilizador.

**SqlUserDefinedApplier** indica que o tipo deve ser registado como um applier definidas pelo utilizador. Esta classe não pode ser herdada.

**SqlUserDefinedApplier** é **opcional** para obter uma definição applier definidas pelo utilizador.


Os objetos de programação principais são os seguintes:

```
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Conjuntos de linhas de entrada são transmitidos como `IRow` entrada. As linhas de saída são geradas como `IUpdatableRow` interface de saída.

Nomes de coluna individual podem ser determinados ao chamar o `IRow` método de esquema.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Para obter os valores de dados real a partir de entrada `IRow`, usamos o método GET () de `IRow` interface.

```
mycolumn = row.Get<int>("mycolumn")
```

Em alternativa, utilizamos o nome de coluna do esquema:

```
row.Get<int>(row.Schema[0].Name)
```

Os valores de saída devem ser definidos com `IUpdatableRow` saída:

```
output.Set<int>("mycolumn", mycolumn)
```

É importante compreender que appliers personalizados saída apenas colunas e os valores que são definidas com `output.Set` chamada de método.

O resultado real é acionado ao chamar `yield return output.AsReadOnly();`.

Os parâmetros applier definidas pelo utilizador podem ser passados para o construtor. Applier pode devolver um número variável de colunas que têm de ser definidos durante a chamada applier no Script de U-SQL base.

```
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Eis o exemplo applier definidas pelo utilizador:

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

Segue-se o script de U-SQL base para este applier definidas pelo utilizador:

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

Neste cenário de casos de utilização, definido pelo utilizador applier atua como um analisador de valor delimitado por vírgulas para as propriedades de frotas de carros. As linhas do ficheiro de entrada tem o seguinte aspeto:

```
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

É delimitado por tabulação TSV ficheiros típico com uma coluna de propriedades que contém as propriedades de carro, como a marca e modelo. Essas propriedades deverão ser analisadas para as colunas da tabela. O applier fornecida também permite-lhe gerar dinâmica diversas propriedades no conjunto de linhas do resultado, com base no parâmetro que é transmitido. Pode gerar todas as propriedades ou um conjunto específico de apenas propriedades.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

O applier definidas pelo utilizador pode ser chamado como uma nova instância do objeto applier:

```
CROSS APPLY new MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

Ou com a invocação de um método de fábrica do wrapper:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>Utilizar combiners definidas pelo utilizador
Combinação definidas pelo utilizador ou UDC, permite-lhe combinar as linhas da esquerda e direita conjuntos de linhas, com base na lógica personalizada. Combinação definidas pelo utilizador é utilizada com a combinação de expressão.

Uma combinação está sendo solicitada com a expressão de combinação que fornece as informações necessárias sobre os conjuntos de linhas de entrada, as colunas de agrupamento, o resultado esperado tanto de esquema informações adicionais.

Para chamar uma combinação num script de U-SQL base, usamos a seguinte sintaxe:

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

Para obter mais informações, consulte [expressão COMBINAR (U-SQL)](https://msdn.microsoft.com/library/azure/mt621339.aspx).

Para definir uma combinação de definidas pelo utilizador, temos de criar o `ICombiner` interface com o [`SqlUserDefinedCombiner`] atributo, que é opcional para uma definição de combinação definidas pelo utilizador.

Base `ICombiner` definição de classe:

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

A implementação personalizada de um `ICombiner` interface deve conter a definição para um `IEnumerable<IRow>` combinar a substituição.

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

O **SqlUserDefinedCombiner** atributo indica que o tipo deve ser registado como uma combinação de definidas pelo utilizador. Esta classe não pode ser herdada.

**SqlUserDefinedCombiner** é usada para definir a propriedade do modo de combinação. É um atributo opcional para uma definição de combinação definidas pelo utilizador.

Modo de CombinerMode

CombinerMode enum pode realizar os seguintes valores:

* Completo (0) todas as linhas de saída depende potencialmente todas as linhas entradas da esquerda e direita com o mesmo valor de chave.

* À esquerda (1) todas as linhas de saída depende de uma única linha de entrada da esquerda (e potencialmente todas as linhas da direita com o mesmo valor de chave).

* Direito (2) todas as linhas de saída depende de uma única linha de entrada do lado direito (e potencialmente todas as linhas a partir da esquerda com o mesmo valor de chave).

* Interna (3) todas as linhas de saída depende de uma única linha de entrada da esquerda e direita com o mesmo valor.

Exemplo: [`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


Os objetos de programação principais são:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Conjuntos de linhas de entrada são transmitidos como **esquerda** e **certo** `IRowset` tipo de interface. Ambos os conjuntos de linhas tem de ser enumerados para processamento. Só é possível enumerar cada interface de uma vez, portanto, precisamos de enumerar e colocar em cache, se necessário.

Para fins de colocação em cache, podemos criar uma lista\<T\> tipo de estrutura de memória como resultado de um LINQ execução da consulta, especificamente lista <`IRow`>. O tipo de dados anônimos pode ser utilizado durante a enumeração também.

Ver [introdução às consultas de LINQ (c#)](https://msdn.microsoft.com/library/bb397906.aspx) para obter mais informações sobre consultas do LINQ, e [IEnumerable\<T\> Interface](https://msdn.microsoft.com/library/9eekhta0(v=vs.110).aspx) para obter mais informações sobre o IEnumerable\<T\> interface.

Para obter os valores de dados real a partir de entrada `IRowset`, usamos o método GET () de `IRow` interface.

```
mycolumn = row.Get<int>("mycolumn")
```

Nomes de coluna individual podem ser determinados ao chamar o `IRow` método de esquema.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Ou ao utilizar o nome de coluna do esquema:

```
c# row.Get<int>(row.Schema[0].Name)
```

A enumeração geral com o LINQ é semelhante ao seguinte:

```
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Depois de enumerar os dois conjuntos de linhas, que vamos fazer um loop por todas as linhas. Para cada linha no conjunto de linhas à esquerda, vamos para localizar todas as linhas que satisfaçam a condição de nossa combinação.

Os valores de saída devem ser definidos com `IUpdatableRow` saída.

```
output.Set<int>("mycolumn", mycolumn)
```

O resultado real é acionado por chamar `yield return output.AsReadOnly();`.

Segue-se um exemplo de combinação:

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

Neste cenário de casos de utilização, que estamos criando um relatório de análise para o varejista. O objetivo é encontrar todos os produtos que mais de US $20.000 de custos e que vender através do site mais rápido do que por meio do varejista regular durante um intervalo de tempo.

Aqui está o script de U-SQL base. É possível comparar a lógica entre uma associação normal e uma combinação:

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

Uma combinação de definidas pelo utilizador pode ser chamada como uma nova instância do objeto applier:

```
USING new MyNameSpace.MyCombiner();
```


Ou com a invocação de um método de fábrica do wrapper:

```
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>Utilizar redutores definidas pelo utilizador

U-SQL permite-lhe escrever redutores de conjunto de linhas personalizada em c# usando a estrutura de extensibilidade do operador definido pelo utilizador e a implementação de uma interface de IReducer.

Definido pelo utilizador reducer ou UDR, pode ser utilizado para eliminar linhas desnecessárias durante a extração de dados (importação). Ele também pode ser usado para manipular e avaliar linhas e colunas. Com base na lógica de programação, ele também pode definir quais linhas precisam ser extraídos.

Para definir uma classe UDR, temos de criar uma `IReducer` interface com opcional `SqlUserDefinedReducer` atributo.

Essa interface da classe deve conter uma definição para o `IEnumerable` substituir o conjunto de linhas de interface.

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

O **SqlUserDefinedReducer** atributo indica que o tipo deve ser registado como um reducer definidas pelo utilizador. Esta classe não pode ser herdada.
**SqlUserDefinedReducer** é um atributo opcional para obter uma definição de reducer definidas pelo utilizador. Ele é usado para definir a propriedade IsRecursive.

* bool     IsRecursive    
* **Verdadeiro** = indica se esta Reducer é associativo e comutativa

Os objetos de programação principais são **entrada** e **saída**. O objeto de entrada é utilizado para enumerar as linhas de entrada. Saída é utilizada para definir as linhas de saída como resultado de reduzir a atividade.

Enumeração de linhas de entrada, vamos utilizar o `Row.Get` método.

```
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

O parâmetro para o `Row.Get` método é uma coluna que é transmitida como parte do `PRODUCE` classe do `REDUCE` declaração do Estado do script de base do U-SQL. É necessário usar o tipo de dados correto aqui também.

Para a saída, utilize o `output.Set` método.

É importante compreender que reducer personalizado apenas produz os valores que são definidas com o `output.Set` chamada de método.

```
output.Set<string>("mycolumn", guid);
```

A saída de reducer real é acionada ao chamar `yield return output.AsReadOnly();`.

Segue-se um exemplo de reducer:

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

Neste cenário de casos de utilização, o reducer está a ignorar linhas com um nome de utilizador vazia. Para cada linha no conjunto de linhas, lê de cada coluna necessária, em seguida, avalia o comprimento do nome do utilizador. Ele produz a linha apenas se o comprimento do valor de nome de utilizador é superior a 0.

Segue-se base script de U-SQL que usa um reducer personalizado:

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
