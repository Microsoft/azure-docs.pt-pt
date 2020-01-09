---
title: Padrões comuns de consulta no Azure Stream Analytics
description: Este artigo descreve vários padrões de consulta e designs comuns que são úteis em trabalhos de Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/16/2019
ms.openlocfilehash: 61f9e128fa9299a743012e18882fe32591fdd3f0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75369954"
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Exemplos de consulta para padrões comuns de uso de Stream Analytics

As consultas em Azure Stream Analytics são expressas em uma linguagem de consulta do tipo SQL. As construções de linguagem são documentadas no guia de [referência da linguagem de consulta Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference) . 

O design de consulta pode expressar a lógica de passagem simples para mover dados de um fluxo de entrada para um armazenamento de dados de saída, ou pode fazer uma correspondência de padrão avançada e análise temporal para calcular agregações em várias janelas de tempo como na [criação de uma solução de IOT usando](stream-analytics-build-an-iot-solution-using-stream-analytics.md) o guia Stream Analytics. Você pode unir dados de várias entradas para combinar eventos de streaming e pode fazer pesquisas em dados de referência estática para enriquecer os valores de evento. Você também pode gravar dados em várias saídas.

Este artigo descreve soluções para vários padrões de consulta comuns com base em cenários do mundo real.

## <a name="work-with-complex-data-types-in-json-and-avro"></a>Trabalhar com Tipos de Dados complexos em JSON e AVRO

Azure Stream Analytics dá suporte ao processamento de eventos em formatos de dados CSV, JSON e Avro.

JSON e Avro podem conter tipos complexos, como objetos aninhados (registros) ou matrizes. Para obter mais informações sobre como trabalhar com esses tipos de dados complexos, consulte o artigo [analisando dados JSON e Avro](stream-analytics-parsing-json.md) .

## <a name="query-example-convert-data-types"></a>Exemplo de consulta: converter tipos de dados

**Descrição**: defina os tipos de propriedades no fluxo de entrada. Por exemplo, o peso do carro está chegando no fluxo de entrada como cadeias de caracteres e precisa ser convertido em **int** para executar **sum**.

**Entrada**:

| Criar | Tempo | Peso |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000 Z |"2000" |

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

**Explicação**: Use uma instrução **Cast** no campo **Weight** para especificar seu tipo de dados. Consulte a lista de tipos de dados com suporte em [tipos de dados (Azure Stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics).

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>Exemplo de consulta: usar LIKE/não gostar de fazer correspondência de padrões

**Descrição**: Verifique se um valor de campo no evento corresponde a um determinado padrão.
Por exemplo, verifique se o resultado retorna placas de licença que começam com um e terminam com 9.

**Entrada**:

| Criar | LicensePlate | Tempo |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000 Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000 Z |

**Saída**:

| Criar | LicensePlate | Tempo |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000 Z |

**Solução**:

```SQL
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'
```

**Explicação**: Use a instrução **like** para verificar o valor do campo **placadecarro** . Ele deve começar com a letra A e, em seguida, ter qualquer cadeia de caracteres de zero ou mais e terminar com o número 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Exemplo de consulta: especificar a lógica para casos/valores diferentes (instruções CASE)

**Descrição**: forneça um cálculo diferente para um campo, com base em um critério específico. Por exemplo, forneça uma descrição de cadeia de caracteres para quantos carros da mesma marca passaram, com um caso especial para 1.

**Entrada**:

| Criar | Tempo |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |
| Toyota |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:03.0000000 Z |

**Saída**:

| CarsPassed | Tempo |
| --- | --- |
| 1 Honda |2015-01-01T00:00:10.0000000 Z |
| 2 Toyotas |2015-01-01T00:00:10.0000000 Z |

**Solução**:

```SQL
    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp() AS AsaTime
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
```

**Explicação**: a expressão **Case** compara uma expressão a um conjunto de expressões simples para determinar o resultado. Neste exemplo, o veículo faz com uma contagem de 1 retornou uma descrição de cadeia de caracteres diferente da que o veículo faz com uma contagem diferente de 1.

## <a name="query-example-send-data-to-multiple-outputs"></a>Exemplo de consulta: enviar dados para várias saídas

**Descrição**: enviar dados para vários destinos de saída de um único trabalho. Por exemplo, analise dados para um alerta baseado em limite e arquive todos os eventos no armazenamento de BLOBs.

**Entrada**:

| Criar | Tempo |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |
| Honda |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:01.0000000 Z |
| Toyota |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:03.0000000 Z |

**Saída1**:

| Criar | Tempo |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |
| Honda |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:01.0000000 Z |
| Toyota |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:03.0000000 Z |

**Output2**:

| Criar | Tempo | Contagem |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000 Z |3 |

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
        System.TimeStamp() AS AsaTime,
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

**Explicação**: a cláusula **into** informa Stream Analytics a quais das saídas gravarão os dados dessa instrução. A primeira consulta é uma passagem dos dados recebidos para uma saída chamada **saídadearquivos**. A segunda consulta faz uma agregação e filtragem simples e envia os resultados para um sistema de alerta downstream, **AlertOutput**.

Observe que você também pode reutilizar os resultados das CTEs (expressões de tabela comuns) (como instruções **with** ) em várias instruções de saída. Essa opção tem o benefício adicional de abrir menos leitores para a fonte de entrada.

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

## <a name="query-example-count-unique-values"></a>Exemplo de consulta: contar valores exclusivos

**Descrição**: Conte o número de valores de campo exclusivos que aparecem no fluxo dentro de uma janela de tempo. Por exemplo, quantas criações exclusivas de carros passaram pelo estande de chamada em uma janela de dois segundos?

**Entrada**:

| Criar | Tempo |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |
| Honda |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:01.0000000 Z |
| Toyota |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:03.0000000 Z |

**Saída:**

| CountMake | Tempo |
| --- | --- |
| 2 |2015-01-01T00:00:02.000 Z |
| 1 |2015-01-01T00:00:04.000 Z |

**Solução:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP() AS AsaTIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```


**Explicação:** contagem de
 **(diferenciação distinta)** retorna o número de valores distintos na coluna **Make** dentro de uma janela de tempo.

## <a name="query-example-determine-if-a-value-has-changed"></a>Exemplo de consulta: determinar se um valor foi alterado

**Descrição**: examine um valor anterior para determinar se ele é diferente do valor atual. Por exemplo, o carro anterior na estrada de chamada é o mesmo que o carro atual?

**Entrada**:

| Criar | Tempo |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |
| Toyota |2015-01-01T00:00:02.0000000 Z |

**Saída**:

| Criar | Tempo |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000 Z |

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

**Explicação**: Use o **retardo** para inspecionar o fluxo de entrada um evento e obter o valor **Make** . Em seguida, compare-o com o valor **Make** no evento atual e gere o evento se eles forem diferentes.

## <a name="query-example-find-the-first-event-in-a-window"></a>Exemplo de consulta: localizar o primeiro evento em uma janela

**Descrição**: Localize o primeiro carro em cada intervalo de 10 minutos.

**Entrada**:

| LicensePlate | Criar | Tempo |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000 Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000 Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000 Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000 Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000 Z |

**Saída**:

| LicensePlate | Criar | Tempo |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000 Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000 Z |

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

Agora, vamos alterar o problema e encontrar o primeiro carro de uma marca específica em cada intervalo de 10 minutos.

| LicensePlate | Criar | Tempo |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000 Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000 Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000 Z |

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

## <a name="query-example-find-the-last-event-in-a-window"></a>Exemplo de consulta: localizar o último evento em uma janela

**Descrição**: Localize o último carro em cada intervalo de 10 minutos.

**Entrada**:

| LicensePlate | Criar | Tempo |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000 Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000 Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000 Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000 Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000 Z |

**Saída**:

| LicensePlate | Criar | Tempo |
| --- | --- | --- |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000 Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000 Z |

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

**Explicação**: há duas etapas na consulta. A primeira encontra o carimbo de data/hora mais recente em janelas de 10 minutos. A segunda etapa une os resultados da primeira consulta com o fluxo original para localizar os eventos que correspondem aos últimos carimbos de data e hora em cada janela. 

## <a name="query-example-locate-correlated-events-in-a-stream"></a>Exemplo de consulta: localizar eventos correlacionados em um fluxo

**Descrição**: localizar eventos correlacionados em um fluxo. Por exemplo, ter dois carros consecutivos da mesma marca foram inseridos na estrada nos últimos 90 segundos?

**Entrada**:

| Criar | LicensePlate | Tempo |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000 Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000 Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000 Z |

**Saída**:

| Criar | Tempo | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000 Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000 Z |

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

**Explicação**: Use o **retardo** para inspecionar o fluxo de entrada um evento e obter o valor **Make** . Compare-o com o valor **Make** no evento atual e, em seguida, gere o evento se eles forem iguais. Você também pode usar o **retardo** para obter dados sobre o carro anterior.

## <a name="query-example-detect-the-duration-between-events"></a>Exemplo de consulta: detectar a duração entre os eventos

**Descrição**: Localize a duração de um determinado evento. Por exemplo, considerando um cliques da Web, determine o tempo gasto em um recurso.

**Entrada**:  

| Utilizador | Funcionalidade | Evento | Tempo |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Iniciar |2015-01-01T00:00:01.0000000 Z |
| user@location.com |RightMenu |Terminar |2015-01-01T00:00:08.0000000 Z |

**Saída**:  

| Utilizador | Funcionalidade | Duração |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Solução**:

```SQL
    SELECT
        [user],
    feature,
    DATEDIFF(
        second,
        LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'),
        Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
```

**Explicação**: Use a função **Last** para recuperar o último valor de **hora** quando o tipo de evento foi **iniciado**. A função **Last** usa **Partition by [User]** para indicar que o resultado é calculado por usuário exclusivo. A consulta tem um limite máximo de 1 hora para a diferença de tempo entre os eventos de **início** e de **parada** , mas é configurável conforme necessário **(limite de duração (hora, 1)** .

## <a name="query-example-detect-the-duration-of-a-condition"></a>Exemplo de consulta: detectar a duração de uma condição
**Descrição**: Descubra por quanto tempo uma condição ocorreu.
Por exemplo, suponha que um bug resultou em todos os carros com peso incorreto (acima de 20.000 libras) e a duração desse bug deve ser computada.

**Entrada**:

| Criar | Tempo | Peso |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |2000 |
| Toyota |2015-01-01T00:00:02.0000000 Z |25000 |
| Honda |2015-01-01T00:00:03.0000000 Z |26000 |
| Toyota |2015-01-01T00:00:04.0000000 Z |25000 |
| Honda |2015-01-01T00:00:05.0000000 Z |26000 |
| Toyota |2015-01-01T00:00:06.0000000 Z |25000 |
| Honda |2015-01-01T00:00:07.0000000 Z |26000 |
| Toyota |2015-01-01T00:00:08.0000000 Z |2000 |

**Saída**:

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000 Z |2015-01-01T00:00:07.000 Z |

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

**Explicação**: use **lag** para exibir o fluxo de entrada por 24 horas e procure instâncias em que **StartFault** e **StopFault** são distribuídas pelo peso < 20000.

## <a name="query-example-fill-missing-values"></a>Exemplo de consulta: preencher valores ausentes

**Descrição**: para o fluxo de eventos que têm valores ausentes, produz um fluxo de eventos com intervalos regulares. Por exemplo, gere um evento a cada 5 segundos que relate o ponto de dados visto mais recentemente.

**Entrada**:

| t | valor |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Saída (primeiras 10 linhas)** :

| windowEnd | lastevent. t | lastevent.value |
| --- | --- | --- |
| 2014-01-01T14:01:00.000 Z |2014-01-01T14:01:00.000 Z |1 |
| 2014-01-01T14:01:05.000 Z |2014-01-01T14:01:05.000 Z |2 |
| 2014-01-01T14:01:10.000 Z |2014-01-01T14:01:10.000 Z |3 |
| 2014-01-01T14:01:15.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:20.000 É Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:25.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:30.000 Z |2014-01-01T14:01:30.000 Z |5 |
| 2014-01-01T14:01:35.000 Z |2014-01-01T14:01:35.000 Z |6 |
| 2014-01-01T14:01:40.000 Z |2014-01-01T14:01:35.000 Z |6 |
| 2014-01-01T14:01:45.000 Z |2014-01-01T14:01:35.000 Z |6 |

**Solução**:

```SQL
    SELECT
        System.Timestamp() AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)
```

**Explicação**: essa consulta gera eventos a cada 5 segundos e gera o último evento recebido anteriormente. A duração da [janela de salto](/stream-analytics-query/hopping-window-azure-stream-analytics) determina quanto tempo a consulta procura localizar o evento mais recente (300 segundos neste exemplo).


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>Exemplo de consulta: correlacionar dois tipos de evento dentro do mesmo fluxo

**Descrição**: às vezes, os alertas precisam ser gerados com base em vários tipos de evento que ocorreram em um determinado intervalo de tempo. Por exemplo, em um cenário de IoT para fornos residenciais, um alerta deve ser gerado quando a temperatura do ventilador for menor que 40 e a potência máxima durante os últimos 3 minutos for menor que 10.

**Entrada**:

| hora | deviceId | sensorName | valor |
| --- | --- | --- | --- |
| "2018-01-01T16:01:00" | "Oven1" | Temp |120 |
| "2018-01-01T16:01:00" | "Oven1" | alimentar |15 |
| "2018-01-01T16:02:00" | "Oven1" | Temp |100 |
| "2018-01-01T16:02:00" | "Oven1" | alimentar |15 |
| "2018-01-01T16:03:00" | "Oven1" | Temp |70 |
| "2018-01-01T16:03:00" | "Oven1" | alimentar |15 |
| "2018-01-01T16:04:00" | "Oven1" | Temp |50 |
| "2018-01-01T16:04:00" | "Oven1" | alimentar |15 |
| "2018-01-01T16:05:00" | "Oven1" | Temp |30 |
| "2018-01-01T16:05:00" | "Oven1" | alimentar |8 |
| "2018-01-01T16:06:00" | "Oven1" | Temp |20 |
| "2018-01-01T16:06:00" | "Oven1" | alimentar |8 |
| "2018-01-01T16:07:00" | "Oven1" | Temp |20 |
| "2018-01-01T16:07:00" | "Oven1" | alimentar |8 |
| "2018-01-01T16:08:00" | "Oven1" | Temp |20 |
| "2018-01-01T16:08:00" | "Oven1" | alimentar |8 |

**Saída**:

| eventTime | deviceId | temp | alertMessage | maxPowerDuringLast3mins |
| --- | --- | --- | --- | --- | 
| "2018-01-01T16:05:00" | "Oven1" |30 | "Elementos de aquecimento de circuito pequeno" |15 |
| "2018-01-01T16:06:00" | "Oven1" |20 | "Elementos de aquecimento de circuito pequeno" |15 |
| "2018-01-01T16:07:00" | "Oven1" |20 | "Elementos de aquecimento de circuito pequeno" |15 |

**Solução**:

```SQL
WITH max_power_during_last_3_mins AS (
    SELECT 
        System.TimeStamp() AS windowTime,
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

**Explicação**: a primeira `max_power_during_last_3_mins`de consulta, usa a [janela deslizante](/stream-analytics-query/sliding-window-azure-stream-analytics) para localizar o valor máximo do sensor de energia para cada dispositivo, durante os últimos 3 minutos. A segunda consulta é unida à primeira consulta para localizar o valor de energia na janela mais recente relevante para o evento atual. E, em seguida, desde que as condições sejam atendidas, um alerta é gerado para o dispositivo.

## <a name="query-example-process-events-independent-of-device-clock-skew-substreams"></a>Exemplo de consulta: processar eventos independentemente da distorção do relógio do dispositivo (subfluxos)

**Descrição**: os eventos podem chegar atrasados ou fora de ordem devido a distorções de relógio entre produtores de evento, distorções de relógio entre partições ou latência de rede. No exemplo a seguir, o relógio do dispositivo para a chamada de Tarifaid 2 é de cinco segundos atrás da chamada 1 e o relógio do dispositivo para o Chamadaid 3 é de dez segundos atrás da chamada 1. 

**Entrada**:

| LicensePlate | Criar | Tempo | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:01.0000000 Z | 1 |
| YHN 6970 |Toyota |2015-07-27T00:00:05.0000000 Z | 1 |
| QYF 9358 |Honda |2015-07-27T00:00:01.0000000 Z | 2 |
| GXF 9462 |BMW |2015-07-27T00:00:04.0000000 Z | 2 |
| VFE 1616 |Toyota |2015-07-27T00:00:10.0000000 Z | 1 |
| RMV 8282 |Honda |2015-07-27T00:00:03.0000000 Z | 3 |
| MDR 6128 |BMW |2015-07-27T00:00:11.0000000 Z | 2 |
| YZK 5704 |Ford |2015-07-27T00:00:07.0000000 Z | 3 |

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

**Explicação**: a cláusula [timestamp by over](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering) examina cada linha do tempo de dispositivo separadamente usando subfluxos. Os eventos de saída para cada Tarifaid são gerados à medida que são computados, o que significa que os eventos estão em ordem em relação a cada Tarifaid em vez de serem reordenados como se todos os dispositivos estivessem no mesmo relógio.

## <a name="query-example-remove-duplicate-events-in-a-window"></a>Exemplo de consulta: remover eventos duplicados em uma janela

**Descrição**: ao executar uma operação, como calcular médias sobre eventos em uma determinada janela de tempo, os eventos duplicados devem ser filtrados. No exemplo a seguir, o segundo evento é uma duplicata do primeiro.

**Entrada**:  

| DeviceId | Tempo | Atributo | Valor |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000 Z |Temperatura |50 |
| 1 |2018-07-27T00:00:01.0000000 Z |Temperatura |50 |
| 2 |2018-07-27T00:00:01.0000000 Z |Temperatura |40 |
| 1 |2018-07-27T00:00:05.0000000 Z |Temperatura |60 |
| 2 |2018-07-27T00:00:05.0000000 Z |Temperatura |50 |
| 1 |2018-07-27T00:00:10.0000000 Z |Temperatura |100 |

**Saída**:  

| Médiavalue | DeviceId |
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
        SYSTEM.TIMESTAMP()
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

**Explicação**: [Count (hora distinta)](/stream-analytics-query/count-azure-stream-analytics) retorna o número de valores distintos na coluna time dentro de uma janela de tempo. Você pode usar a saída desta etapa para calcular a média por dispositivo descartando duplicatas.

## <a name="geofencing-and-geospatial-queries"></a>Doisolamento geográfico e consultas geoespaciais
O Azure Stream Analytics fornece funções geoespaciais internas que podem ser usadas para implementar cenários como gerenciamento de frota, compartilhamento de Rides, carros conectados e acompanhamento de ativos. Os dados geoespaciais podem ser ingeridos em formatos geojson ou WKT como parte do fluxo de eventos ou de dados de referência. Para obter mais informações, consulte os cenários de Unificação [geográfica e agregação geoespacial com Azure Stream Analytics](geospatial-scenarios.md) artigo.

## <a name="language-extensibility-through-javascript-and-c"></a>Extensibilidade de linguagem por meio de JavaScript eC#
O Azure Stream Ananlytics Query langugae pode ser estendido com funções personalizadas escritas em C# JavaScript ou linguagens. Para obter mais informações, consulte os artigos foolowing:
* [Azure Stream Analytics funções definidas pelo usuário do JavaScript](stream-analytics-javascript-user-defined-functions.md)
* [Azure Stream Analytics agregações definidas pelo usuário do JavaScript](stream-analytics-javascript-user-defined-aggregates.md)
* [Desenvolver .NET Standard funções definidas pelo usuário para trabalhos do Azure Stream Analytics Edge](stream-analytics-edge-csharp-udf-methods.md)

## <a name="get-help"></a>Obter ajuda

Para obter assistência, tente nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

