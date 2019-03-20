---
title: Padrões de consulta comuns no Azure Stream Analytics
description: Este artigo descreve um número de padrões de consulta comuns e estruturas que são úteis para tarefas do Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: 9c9a5f219af0d474e1608f98595abe027b894117
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58001739"
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Consultar os exemplos de padrões de utilização comuns do Stream Analytics

## <a name="introduction"></a>Introdução
Consultas no Azure Stream Analytics são expressos numa linguagem de consulta do tipo SQL. As construções de linguagem estão documentadas no [referência de linguagem de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) guia. 

O design de consulta pode expressar a lógica de pass-through simple para mover dados de eventos de um fluxo de entrada para outro armazenamento de dados de saída. Ou ele pode fazer a análise correspondente e temporais avançado padrão para calcular os agregados ao longo de várias janelas de tempo, tal como no exemplo a TollApp. Pode associar dados de várias entradas para combinar eventos de transmissões e fazer pesquisas em dados de referência estáticos para enriquecer os valores dos eventos. Também pode escrever dados para várias saídas.

Este artigo descreve as soluções para vários padrões de consulta comuns, com base em cenários reais. É um trabalho em andamento e continua a ser atualizados com novos padrões de forma contínua.

## <a name="work-with-complex-data-types-in-json-and-avro"></a>Trabalhar com Tipos de Dados complexos em JSON e AVRO 
O Azure Stream Analytics suporta o processamento de eventos em formatos de dados do CSV, JSON e Avro.
JSON e Avro podem conter tipos complexos, como objetos aninhados (registos) ou matrizes. Para trabalhar com esses tipos de dados complexas, consulte a [dados JSON de análise e AVRO](stream-analytics-parsing-json.md) artigo.


## <a name="query-example-convert-data-types"></a>Exemplo de consulta: Converter tipos de dados
**Descrição**: Defina os tipos de propriedades no fluxo de entrada.
Por exemplo, o peso de carro estará disponível no fluxo de entrada como cadeias de caracteres e tem de ser convertida **INT** para efetuar **soma** -o.

**Entrada**:

| Criar | Hora | Peso |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000Z |"2000" |

**Saída**:

| Criar | Peso |
| --- | --- |
| Honda |3000 |

**Solução**:

```SQL
    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
```

**EXPLICAÇÃO**: Utilize um **CAST** instrução no **peso** campo para especificar o respetivo tipo de dados. Ver a lista de tipos de dados suportados no [tipos de dados (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835065.aspx).

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>Exemplo de consulta: Utilize Like/Not gostam de padrão de correspondência
**Descrição**: Verifique que um valor de campo no evento corresponde a um determinado padrão.
Por exemplo, verifique que o resultado retorna pratos de licença que começam por A e terminar com 9.

**Entrada**:

| Criar | LicensePlate | Hora |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Saída**:

| Criar | LicensePlate | Hora |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Solução**:

```SQL
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'
```

**EXPLICAÇÃO**: Utilize o **, como** instrução para verificar a **LicensePlate** campo valor. Ele deve começar com um A, em seguida, tiver qualquer cadeia de caracteres de zero ou mais e, em seguida, terminar com um 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Exemplo de consulta: Especifique a lógica para diferentes casos/valores (declarações CASE)
**Descrição**: Fornecem uma computação diferente de um campo, com base num determinado critério.
Por exemplo, fornece uma descrição de cadeia de caracteres para tornam o número de carros do mesmo passado, com um caso especial para 1.

**Entrada**:

| Criar | Hora |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Saída**:

| CarsPassed | Hora |
| --- | --- |
| 1 Honda |2015-01-01T00:00:10.0000000Z |
| 2 Toyotas |2015-01-01T00:00:10.0000000Z |

**Solução**:

```SQL
    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp AS Time
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
```

**EXPLICAÇÃO**: O **caso** expressão compara uma expressão a um conjunto de simples expressões para determinar o resultado. Neste exemplo, veículo torna-se com uma contagem de devolveu uma descrição de cadeia de caracteres diferentes que torna o veículo com uma contagem diferente de 1 de 1. 

## <a name="query-example-send-data-to-multiple-outputs"></a>Exemplo de consulta: Enviar dados para várias saídas
**Descrição**: Envie dados para vários destinos de saída de uma única tarefa.
Por exemplo, analise dados de um alerta baseadas em limiares e arquivar todos os eventos para o armazenamento de Blobs.

**Entrada**:

| Criar | Hora |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output1**:

| Criar | Hora |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output2**:

| Criar | Hora | Contagem |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000Z |3 |

**Solução**:

```SQL
    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp AS Time,
        COUNT(*) AS [Count]
    INTO
        AlertOutput
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
    HAVING
        [Count] >= 3
```

**EXPLICAÇÃO**: O **INTO** cláusula informa ao Stream Analytics que as saídas para escrever os dados a partir esta instrução.
A primeira consulta é um pass-through dos dados recebidos para uma saída que com o nome **ArchiveOutput**.
A segunda consulta faz algumas agregação simples e filtragem e envia os resultados para um sistema de alerta downstream.

Tenha em atenção que também é possível reutilizar os resultados das expressões de tabela comuns (as CTEs) (como **WITH** instruções) em várias instruções de saída. Esta opção tem o benefício adicional de abrir os leitores menos para a origem de entrada.
Por exemplo: 

```SQL
    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'
```

## <a name="query-example-count-unique-values"></a>Exemplo de consulta: Contagem de valores exclusivos
**Descrição**: Conte o número de valores de campos exclusivos que aparecem no fluxo durante um período de tempo.
Por exemplo, o número exclusivo faz de carros transmitidos através do pedágio numa janela de 2 segundos?

**Entrada**:

| Criar | Hora |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Saída:**

| CountMake | Hora |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**Solução:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP AS TIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```


**Explicação:**
**CONTAGEM (não distintos)** devolve o número de valores distintos no **tornar** coluna durante um período de tempo.

## <a name="query-example-determine-if-a-value-has-changed"></a>Exemplo de consulta: Determinar se um valor foi alterado
**Descrição**: Ver um valor anterior para determinar se é diferente do valor atual.
Por exemplo, é o carro anterior na estrada incidir a mesma marca como o carro atual?

**Entrada**:

| Criar | Hora |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Saída**:

| Criar | Hora |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Solução**:

```SQL
    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

**EXPLICAÇÃO**: Uso **ATRASO** para dar uma espiada no fluxo de entrada um evento volta e obter o **tornar** valor. Em seguida, compare-o para o **tornar** valor no evento atual e o evento de saída, se forem diferentes.

## <a name="query-example-find-the-first-event-in-a-window"></a>Exemplo de consulta: Encontrar o primeiro evento numa janela
**Descrição**: Encontre o primeiro carro em cada intervalo de 10 minutos.

**Entrada**:

| LicensePlate | Criar | Hora |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Saída**:

| LicensePlate | Criar | Hora |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |

**Solução**:

```SQL
    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1
```

Agora vamos alterar o problema e localize o carro primeiro de uma marca específica em cada intervalo de 10 minutos.

| LicensePlate | Criar | Hora |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Solução**:

```SQL
    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

## <a name="query-example-find-the-last-event-in-a-window"></a>Exemplo de consulta: Encontrar o último evento numa janela
**Descrição**: Encontre o carro último em cada intervalo de 10 minutos.

**Entrada**:

| LicensePlate | Criar | Hora |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Saída**:

| LicensePlate | Criar | Hora |
| --- | --- | --- |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Solução**:

```SQL
    WITH LastInWindow AS
    (
        SELECT 
            MAX(Time) AS LastEventTime
        FROM 
            Input TIMESTAMP BY Time
        GROUP BY 
            TumblingWindow(minute, 10)
    )
    SELECT 
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime
```

**EXPLICAÇÃO**: Existem dois passos da consulta. Primeiro localiza o carimbo de hora mais recente do windows 10 minutos. A segunda etapa associa os resultados da consulta primeiro com o fluxo original para localizar os eventos que correspondem os última carimbos de hora em cada janela. 

## <a name="query-example-detect-the-absence-of-events"></a>Exemplo de consulta: Detetar a ausência de eventos
**Descrição**: Certifique-se de que um fluxo não tem nenhum valor que corresponde a um determinado critério.
Por exemplo, 2 carros consecutivos desde a mesma introduzidas ao longo do processo incidir nos últimos 90 segundos?

**Entrada**:

| Criar | LicensePlate | Hora |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Saída**:

| Criar | Hora | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**Solução**:

```SQL
    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

**EXPLICAÇÃO**: Uso **ATRASO** para dar uma espiada no fluxo de entrada um evento volta e obter o **tornar** valor. Compare-o para o **tornar** valor no evento atual e, em seguida, o evento de saída se eles forem iguais. Também pode utilizar **ATRASO** para obter dados sobre o carro anterior.

## <a name="query-example-detect-the-duration-between-events"></a>Exemplo de consulta: Detetar a duração entre os eventos
**Descrição**: Encontre a duração de um evento específico. Por exemplo, dada uma clickstream da web, determine o tempo gasto num recurso.

**Entrada**:  

| Utilizador | Funcionalidade | Evento | Hora |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Iniciar |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |Terminar |2015-01-01T00:00:08.0000000Z |

**Saída**:  

| Utilizador | Funcionalidade | Duração |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Solução**:

```SQL
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
```

**EXPLICAÇÃO**: Utilize o **último** função para recuperar a última **tempo** valor quando o tipo de evento foi **iniciar**. O **último** função utiliza **PARTITION BY [utilizador]** para indicar que o resultado é calculado por utilizador exclusivo. A consulta tem um limite de máximo de 1 hora para a diferença de tempo entre **começar** e **parar** eventos, mas pode ser configurado conforme necessário **(limite DURATION(hour, 1)**.

## <a name="query-example-detect-the-duration-of-a-condition"></a>Exemplo de consulta: Detetar a duração de uma condição
**Descrição**: Descobrir o tempo que ocorreu uma condição.
Por exemplo, suponha que um bug resultou em todos os carros ter um peso incorreto (acima de 20 000 libras) e a duração desse bug deve ser calculada.

**Entrada**:

| Criar | Hora | Peso |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |2000 |
| Toyota |2015-01-01T00:00:02.0000000Z |25000 |
| Honda |2015-01-01T00:00:03.0000000Z |26000 |
| Toyota |2015-01-01T00:00:04.0000000Z |25000 |
| Honda |2015-01-01T00:00:05.0000000Z |26000 |
| Toyota |2015-01-01T00:00:06.0000000Z |25000 |
| Honda |2015-01-01T00:00:07.0000000Z |26000 |
| Toyota |2015-01-01T00:00:08.0000000Z |2000 |

**Saída**:

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**Solução**:

```SQL
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
```

**EXPLICAÇÃO**: Uso **ATRASO** para ver o fluxo de entrada de 24 horas e procurar instâncias onde **StartFault** e **StopFault** são abrangido pelo peso < 20000.

## <a name="query-example-fill-missing-values"></a>Exemplo de consulta: Preencher valores em falta
**Descrição**: Para a transmissão de eventos que tenham valores em falta, produza um fluxo de eventos com intervalos regulares.
Por exemplo, gere um evento em 5 segundos, que informa o ponto de dados mais recentemente visto.

**Entrada**:

| t | valor |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Saída (primeiras 10 linhas)**:

| windowend | lastevent.t | lastevent.value |
| --- | --- | --- |
| 2014-01-01T14:01:00.000Z |2014-01-01T14:01:00.000Z |1 |
| 2014-01-01T14:01:05.000Z |2014-01-01T14:01:05.000Z |2 |
| 2014-01-01T14:01:10.000Z |2014-01-01T14:01:10.000Z |3 |
| 2014-01-01T14:01:15.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:20.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:25.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:30.000Z |2014-01-01T14:01:30.000Z |5 |
| 2014-01-01T14:01:35.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:40.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:45.000Z |2014-01-01T14:01:35.000Z |6 |

**Solução**:

```SQL
    SELECT
        System.Timestamp AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)
```

**EXPLICAÇÃO**: Esta consulta gera eventos a cada cinco segundos e devolve o último evento de que recebeu anteriormente. O [janela Hopping](https://msdn.microsoft.com/library/dn835041.aspx "Hopping janela – Azure Stream Analytics") duração determina até que ponto back-a consulta de procura para localizar o evento mais recente (300 segundos neste exemplo).


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>Exemplo de consulta: Correlacione os dois tipos de evento dentro do mesmo fluxo
**Descrição**: Por vezes, alertas tem de ser gerados com base em vários tipos de eventos que ocorreram num determinado intervalo de tempo.
Por exemplo, num cenário de IoT para ovens iniciais, deve ser gerado um alerta quando a temperatura ventoinha é inferior a 40 e a capacidade máxima durante os últimos 3 minutos é inferior a 10.

**Entrada**:

| hora | deviceId | sensorName | valor |
| --- | --- | --- | --- |
| "2018-01-01T16:01:00" | "Oven1" | "temp" |120 |
| "2018-01-01T16:01:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:02:00" | "Oven1" | "temp" |100 |
| "2018-01-01T16:02:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:03:00" | "Oven1" | "temp" |70 |
| "2018-01-01T16:03:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:04:00" | "Oven1" | "temp" |50 |
| "2018-01-01T16:04:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:05:00" | "Oven1" | "temp" |30 |
| "2018-01-01T16:05:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:06:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:06:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:07:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:07:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:08:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:08:00" | "Oven1" | "power" |8 |

**Saída**:

| eventTime | deviceId | temp | alertMessage | maxPowerDuringLast3mins |
| --- | --- | --- | --- | --- | 
| "2018-01-01T16:05:00" | "Oven1" |30 | "Curto-circuito aquecer elementos" |15 |
| "2018-01-01T16:06:00" | "Oven1" |20 | "Curto-circuito aquecer elementos" |15 |
| "2018-01-01T16:07:00" | "Oven1" |20 | "Curto-circuito aquecer elementos" |15 |

**Solução**:

```SQL
WITH max_power_during_last_3_mins AS (
    SELECT 
        System.TimeStamp AS windowTime,
        deviceId,
        max(value) as maxPower
    FROM
        input TIMESTAMP BY t
    WHERE 
        sensorName = 'power' 
    GROUP BY 
        deviceId, 
        SlidingWindow(minute, 3) 
)

SELECT 
    t1.t AS eventTime,
    t1.deviceId, 
    t1.value AS temp,
    'Short circuit heating elements' as alertMessage,
    t2.maxPower AS maxPowerDuringLast3mins
    
INTO resultsr

FROM input t1 TIMESTAMP BY t
JOIN max_power_during_last_3_mins t2
    ON t1.deviceId = t2.deviceId 
    AND t1.t = t2.windowTime
    AND DATEDIFF(minute,t1,t2) between 0 and 3
    
WHERE
    t1.sensorName = 'temp'
    AND t1.value <= 40
    AND t2.maxPower > 10
```

**EXPLICAÇÃO**: A primeira consulta `max_power_during_last_3_mins`, utiliza o [deslizantes janela](https://msdn.microsoft.com/azure/stream-analytics/reference/sliding-window-azure-stream-analytics) para determinar o valor máximo do sensor de alimentação para todos os dispositivos, durante os últimos 3 minutos. A segunda consulta é associada à primeira consulta para encontrar o valor de energia na janela do mais recente relevantes para o evento atual. E, em seguida, desde que as condições são cumpridas, é gerado um alerta para o dispositivo.

## <a name="query-example-process-events-independent-of-device-clock-skew-substreams"></a>Exemplo de consulta: Processar eventos independentes de dispositivo inclinar do relógio (subfluxos)
**Descrição**: Eventos podem se atrasam ou fora de ordem devido a desvios de relógio entre produtores de eventos, o relógio inclina entre partições ou latência de rede. No exemplo a seguir, o relógio do dispositivo para TollID 2 é dez segundos atrás TollID 1 e o relógio do dispositivo para TollID 3 é cinco segundos por trás TollID 1. 


**Entrada**:

| LicensePlate | Criar | Hora | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:01.0000000Z | 1 |
| YHN 6970 |Toyota |2015-07-27T00:00:05.0000000Z | 1 |
| QYF 9358 |Honda |2015-07-27T00:00:01.0000000Z | 2 |
| GXF 9462 |BMW |2015-07-27T00:00:04.0000000Z | 2 |
| VFE 1616 |Toyota |2015-07-27T00:00:10.0000000Z | 1 |
| RMV 8282 |Honda |2015-07-27T00:00:03.0000000Z | 3 |
| MDR 6128 |BMW |2015-07-27T00:00:11.0000000Z | 2 |
| YZK 5704 |Ford |2015-07-27T00:00:07.0000000Z | 3 |

**Saída**:

| TollID | Contagem |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Solução**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

**EXPLICAÇÃO**: O [TIMESTAMP BY OVER](https://msdn.microsoft.com/azure/stream-analytics/reference/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering) cláusula examina cada linha cronológica de dispositivo separadamente com subfluxos. Os eventos de saída para cada TollID são gerados como eles são computados, que significa que os eventos estão na ordem em relação a cada TollID em vez de que está a ser reordenadas como se todos os dispositivos foram no relógio do mesmo.

## <a name="query-example-remove-duplicate-events-in-a-window"></a>Exemplo de consulta: Remover duplicados eventos numa janela
**Descrição**: Ao realizar uma operação como calcular médias sobre eventos numa janela de tempo especificado, os eventos duplicados devem ser filtrados.

**Entrada**:  

| DeviceId | Hora | Atributo | Value |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatura |50 |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatura |50 |
| 2 |2018-07-27T00:00:01.0000000Z |Temperatura |40 |
| 1 |2018-07-27T00:00:05.0000000Z |Temperatura |60 |
| 2 |2018-07-27T00:00:05.0000000Z |Temperatura |50 |
| 1 |2018-07-27T00:00:10.0000000Z |Temperatura |100 |

**Saída**:  

| AverageValue | DeviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**Solução**:

```SQL
With Temp AS (
    SELECT
        COUNT(DISTINCT Time) AS CountTime,
        Value,
        DeviceId
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Value,
        DeviceId,
        SYSTEM.TIMESTAMP
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

**EXPLICAÇÃO**: [CONTAGEM (distinta hora)](https://docs.microsoft.com/stream-analytics-query/count-azure-stream-analytics) devolve o número de valores distintos da coluna de tempo durante um período de tempo. Em seguida, pode utilizar a saída deste passo para calcular a média por dispositivo ao rejeitar duplicatas.

## <a name="get-help"></a>Obter ajuda
Para obter assistência, tente nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos Seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

