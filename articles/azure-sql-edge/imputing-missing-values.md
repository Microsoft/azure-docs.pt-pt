---
title: Lacunas de tempo de preenchimento e impução de valores em falta - Azure SQL Edge
description: Saiba mais sobre preencher lacunas de tempo e imputing valores em falta no Azure SQL Edge
keywords: SQL Edge, timeeries
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: c444732a497d595235ac337d7f5c71fb84f17cca
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185594"
---
# <a name="filling-time-gaps-and-imputing-missing-values"></a>Preencher lacunas de tempo e impurção de valores em falta 

Quando se lida com os dados das séries de tempo, é muitas vezes possível que os dados da série de tempos têm valores em falta para os atributos. Também é possível que, devido à natureza dos dados, ou devido a interrupções na recolha de dados, existam lacunas de tempo no conjunto de *dados.*

Por exemplo, ao recolher estatísticas de utilização de energia para um dispositivo inteligente, sempre que o dispositivo não estiver operacional, haverá lacunas nas estatísticas de utilização. Da mesma forma, num cenário de recolha de dados de telemetria de máquina, é possível que os diferentes sensores estejam configurados para emitir dados em diferentes frequências, resultando em valores em falta para os sensores. Por exemplo, se existirem dois sensores, tensão e pressão, configurados a frequência de 100 Hz e 10-Hz, respectivamente, o sensor de tensão emitirá dados a cada centésimo de segundo, enquanto o sensor de pressão emitirá apenas dados a cada um décimo de segundo.

A tabela seguinte descreve um conjunto de dados de telemetria da máquina, que foi recolhido num intervalo de um segundo. 

```
timestamp               VoltageReading  PressureReading
----------------------- --------------- ----------------
2020-09-07 06:14:41.000 164.990400      97.223600
2020-09-07 06:14:42.000 162.241300      93.992800
2020-09-07 06:14:43.000 163.271200      NULL
2020-09-07 06:14:44.000 161.368100      93.403700
2020-09-07 06:14:45.000 NULL            NULL
2020-09-07 06:14:46.000 NULL            98.364800
2020-09-07 06:14:49.000 NULL            94.098300
2020-09-07 06:14:51.000 157.695700      103.359100
2020-09-07 06:14:52.000 157.019200      NULL
2020-09-07 06:14:54.000 NULL            95.352000
2020-09-07 06:14:56.000 159.183500      100.748200

```

Existem duas características importantes do conjunto de dados anteriores. 

- O conjunto de dados não contém quaisquer pontos de dados relacionados com vários postes de tempo `2020-09-07 06:14:47.000` `2020-09-07 06:14:48.000` , , e `2020-09-07 06:14:50.000` `2020-09-07 06:14:53.000` `2020-09-07 06:14:55.000` . Estes picos de tempo são lacunas no conjunto de *dados.*  
- Faltam valores, representados como `null` , para as leituras de Tensão e pressão. 

## <a name="gap-filling"></a>Preenchimento de lacunas 

O preenchimento de lacunas é uma técnica que ajuda a criar um conjunto contíguo de relógios para facilitar a análise dos dados das séries de tempo. No Azure SQL Edge, a forma mais fácil de preencher lacunas no conjunto de dados da série de tempo é definir uma tabela temporária com a distribuição de tempo desejada e, em seguida, fazer uma `LEFT OUTER JOIN` ou uma `RIGHT OUTER JOIN` operação na tabela de conjuntos de dados. 

Tomando os `MachineTelemetry` dados acima representados como exemplo, a seguinte consulta pode ser usada para gerar um conjunto contíguo e ordenado de timetamps para análise. 

> [!NOTE]
> A consulta abaixo gera as linhas em falta, com os valores e valores do timetamp `null` para os atributos. 

```sql
Create Table #SeriesGenerate(dt datetime Primary key Clustered)
GO

Declare @startdate datetime = '2020-09-07 06:14:41.000', @endtime datetime = '2020-09-07 06:14:56.000'
While (@startdate <= @endtime)
BEGIN
Insert into #SeriesGenerate values (@startdate)
set @startdate = DATEADD(SECOND, 1, @startdate)
END

Select a.dt as timestamp, b.VoltageReading, b.PressureReading 
From 
#SeriesGenerate a LEFT OUTER JOIN MachineTelemetry b 
    on a.dt = b.[timestamp]
```
A consulta acima produz a seguinte saída contendo todos os sinais temporais *de um segundo* na gama especificada.

Aqui está o conjunto de resultados

```

timestamp               VoltageReading    PressureReading
----------------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400        97.223600
2020-09-07 06:14:42.000 162.241300        93.992800
2020-09-07 06:14:43.000 163.271200        NULL
2020-09-07 06:14:44.000 161.368100        93.403700
2020-09-07 06:14:45.000 NULL              NULL
2020-09-07 06:14:46.000 NULL              98.364800
2020-09-07 06:14:47.000 NULL              NULL
2020-09-07 06:14:48.000 NULL              NULL
2020-09-07 06:14:49.000 NULL              94.098300
2020-09-07 06:14:50.000 NULL              NULL
2020-09-07 06:14:51.000 157.695700        103.359100
2020-09-07 06:14:52.000 157.019200        NULL
2020-09-07 06:14:53.000 NULL              NULL
2020-09-07 06:14:54.000 NULL              95.352000
2020-09-07 06:14:55.000 NULL              NULL
2020-09-07 06:14:56.000 159.183500        100.748200
```

## <a name="imputing-missing-values"></a>Introduzir valores em falta

A consulta anterior gerou os tempos em falta para análise de dados, no entanto não substituiu nenhum dos valores em falta (representados como nulos) `voltage` e `pressure` leituras. No Azure SQL Edge, foi adicionada uma nova sintaxe ao T-SQL `LAST_VALUE()` e `FIRST_VALUE()` às funções, que fornecem mecanismos para imputar valores em falta, com base nos valores anteriores ou seguintes no conjunto de dados. 

A nova sintaxe adiciona `IGNORE NULLS` e `RESPECT NULLS` cláusula às `LAST_VALUE()` `FIRST_VALUE()` funções e funções. Uma consulta seguinte sobre o `MachineTelemetry` conjunto de dados calcula os valores em falta utilizando a função last_value, onde os valores em falta são substituídos pelo último valor observado no conjunto de dados.

```sql
Select 
    timestamp,
    VoltageReading As OriginalVoltageValues,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue, 
    PressureReading As OriginalPressureValues,
    LAST_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue
From 
MachineTelemetry 
order by timestamp 
```
Aqui está o conjunto de resultados

```

timestamp               OrigVoltageVals  ImputedVoltage OrigPressureVals  ImputedPressure
----------------------- ---------------- -------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400       164.990400     97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300     93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200     NULL              93.992800
2020-09-07 06:14:44.000 161.368100       161.368100     93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100     NULL              93.403700
2020-09-07 06:14:46.000 NULL             161.368100     98.364800         98.364800
2020-09-07 06:14:49.000 NULL             161.368100     94.098300         94.098300
2020-09-07 06:14:51.000 157.695700       157.695700     103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200     NULL              103.359100
2020-09-07 06:14:54.000 NULL             157.019200     95.352000         95.352000
2020-09-07 06:14:56.000 159.183500       159.183500     100.748200        100.748200

```

A seguinte consulta imputa os valores em falta utilizando a `LAST_VALUE()` função e a `FIRST_VALUE` função. Pois, a coluna de saída `ImputedVoltage` os valores em falta são substituídos pelo último valor observado, enquanto para a coluna de saída `ImputedPressure` os valores em falta são substituídos pelo valor observado seguinte no conjunto de dados. 

```sql
Select 
    dt as timestamp, 
    VoltageReading As OrigVoltageVals,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY dt) As ImputedVoltage, 
    PressureReading As OrigPressureVals,
    First_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY dt ROWS 
                    BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) As ImputedPressure
From 
(Select a.dt, b.VoltageReading,b.PressureReading  from 
    #SeriesGenerate a 
        LEFT OUTER JOIN 
    MachineTelemetry b 
        on a.dt = b.[timestamp]) A
order by timestamp
```
Aqui está o conjunto de resultados

```

timestamp               OrigVoltageVals  ImputedVoltage  OrigPressureVals  ImputedPressure
----------------------- ---------------- --------------- ----------------- ---------------
2020-09-07 06:14:41.000 164.990400       164.990400      97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300      93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200      NULL              93.403700
2020-09-07 06:14:44.000 161.368100       161.368100      93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100      NULL              98.364800
2020-09-07 06:14:46.000 NULL             161.368100      98.364800         98.364800
2020-09-07 06:14:47.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:48.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:49.000 NULL             161.368100      94.098300         94.098300
2020-09-07 06:14:50.000 NULL             161.368100      NULL              103.359100
2020-09-07 06:14:51.000 157.695700       157.695700      103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200      NULL              95.352000
2020-09-07 06:14:53.000 NULL             157.019200      NULL              95.352000
2020-09-07 06:14:54.000 NULL             157.019200      95.352000         95.352000
2020-09-07 06:14:55.000 NULL             157.019200      NULL              100.748200
2020-09-07 06:14:56.000 159.183500       159.183500      100.748200        100.748200
```

> [!NOTE]
> A consulta acima utiliza a `FIRST_VALUE()` função para substituir os valores em falta pelo valor observado seguinte. O mesmo resultado pode ser alcançado utilizando a `LAST_VALUE()` função com uma `ORDER BY <ordering_column> DESC` cláusula.

## <a name="next-steps"></a>Passos seguintes 

- [FIRST_VALUE (Transact-SQL)](/sql/t-sql/functions/first-value-transact-sql?toc=%2fazure%2fazure-sql-edge%2ftoc.json)
- [LAST_VALUE (Transact-SQL)](/sql/t-sql/functions/last-value-transact-sql?toc=%2fazure%2fazure-sql-edge%2ftoc.json)
- [DATE_BUCKET (Transact-SQL)](date-bucket-tsql.md)
- [Funções Agregadas (Transact-SQL)](/sql/t-sql/functions/aggregate-functions-transact-sql)