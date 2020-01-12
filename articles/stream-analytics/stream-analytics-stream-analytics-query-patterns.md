---
title: Padrões comuns de consulta no Azure Stream Analytics
description: Este artigo descreve vários padrões de consulta e designs comuns que são úteis em Azure Stream Analytics trabalhos.
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: 54d1b640a4067cf65fc28501840b4926455ec259
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903463"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Padrões comuns de consulta no Azure Stream Analytics

As consultas em Azure Stream Analytics são expressas em uma linguagem de consulta do tipo SQL. As construções de linguagem são documentadas no guia de [referência da linguagem de consulta Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference) . 

O design de consulta pode expressar a lógica de passagem simples para mover dados de um fluxo de entrada para um armazenamento de dados de saída, ou pode fazer uma correspondência de padrão avançada e análise temporal para calcular agregações em várias janelas de tempo como na [criação de uma solução de IOT usando](stream-analytics-build-an-iot-solution-using-stream-analytics.md) o guia Stream Analytics. Você pode unir dados de várias entradas para combinar eventos de streaming e pode fazer pesquisas em dados de referência estática para enriquecer os valores de evento. Você também pode gravar dados em várias saídas.

Este artigo descreve soluções para vários padrões de consulta comuns com base em cenários do mundo real.

## <a name="supported-data-formats"></a>Formatos de dados com suporte

Azure Stream Analytics dá suporte ao processamento de eventos em formatos de dados CSV, JSON e Avro.

JSON e Avro podem conter tipos complexos, como objetos aninhados (registros) ou matrizes. Para obter mais informações sobre como trabalhar com esses tipos de dados complexos, consulte o artigo [analisando dados JSON e Avro](stream-analytics-parsing-json.md) .

## <a name="simple-pass-through-query"></a>Consulta de passagem simples

Uma consulta de passagem simples pode ser usada para copiar os dados de fluxo de entrada para a saída. Por exemplo, se um fluxo de dados que contém informações de veículo em tempo real precisar ser salvo em uma análise de carta, uma simples consulta de passagem fará o trabalho.

**Entrada**:

| Criar | Tempo | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000 Z |"2000" |

**Saída**:

| Criar | Tempo | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000 Z |"2000" |

**Consulta**:

```SQL
SELECT
    *
INTO Output
FROM Input
```

Uma consulta **Select** * projeta todos os campos de um evento de entrada e os envia para a saída. Da mesma forma, **Select** também pode ser usado para projetar apenas os campos obrigatórios da entrada. Neste exemplo, se a *marca* e a *hora* do veículo forem os únicos campos obrigatórios a serem salvos, esses campos poderão ser especificados na instrução **Select** .

**Entrada**:

| Criar | Tempo | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000 Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000 Z |1500 |

**Saída**:

| Criar | Tempo |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:04.0000000 Z |

**Consulta**:

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```
## <a name="data-aggregation-over-time"></a>Agregação de dados ao longo do tempo

Para computar informações em uma janela de tempo, os dados podem ser agregados juntos. Neste exemplo, uma contagem é calculada nos últimos 10 minutos de tempo para cada marca de carro específica.

**Entrada**:

| Criar | Tempo | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000 Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000 Z |1500 |

**Saída**:

| Criar | Contagem |
| --- | --- |
| Make1 | 2 |
| Make2 | 1 |

**Consulta**:

```SQL
SELECT
    Make,
    COUNT(*) AS Count
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

Essa agregação agrupa os carros por *Make* e os conta a cada 10 segundos. A saída tem a *marca* e a *contagem* de carros que passaram pela chamada.

TumblingWindow é uma função de janela usada para agrupar eventos juntos. Uma agregação pode ser aplicada em todos os eventos agrupados. Para obter mais informações, consulte [janelas de funções](stream-analytics-window-functions.md).

Para obter mais informações sobre agregação, consulte [funções de agregação](/stream-analytics-query/aggregate-functions-azure-stream-analytics).

## <a name="data-conversion"></a>Conversão de dados

Os dados podem ser convertidos em tempo real usando o método **Cast** . Por exemplo, o peso do carro pode ser convertido do tipo **nvarchar (max)** para o tipo **bigint** e ser usado em um cálculo numérico.

**Entrada**:

| Criar | Tempo | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000 Z |"2000" |

**Saída**:

| Criar | Peso |
| --- | --- |
| Make1 |3000 |

**Consulta**:

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

Use uma instrução **Cast** para especificar seu tipo de dados. Consulte a lista de tipos de dados com suporte em [tipos de dados (Azure Stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics).

Para obter mais informações sobre [funções de conversão de dados](/stream-analytics-query/conversion-functions-azure-stream-analytics).

## <a name="string-matching-with-like-and-not-like"></a>Correspondência de cadeia de caracteres de LIKE e não LIKE

**Like** e **not like** podem ser usados para verificar se um campo corresponde a um determinado padrão. Por exemplo, um filtro pode ser criado para retornar apenas as chapas de licença que começam com a letra ' A ' e terminam com o número 9.

**Entrada**:

| Criar | License_plate | Tempo |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000 Z |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000 Z |

**Saída**:

| Criar | License_plate | Tempo |
| --- | --- | --- |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000 Z |

**Consulta**:

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

Use a instrução **like** para verificar o valor do campo de **License_plate** . Ele deve começar com a letra ' A ' e, em seguida, ter qualquer cadeia de zero ou mais caracteres, terminando com o número 9.

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>Especificar a lógica para casos/valores diferentes (instruções CASE)

As instruções **Case** podem fornecer diferentes cálculos para campos diferentes, com base em um critério específico. Por exemplo, atribua a pista ' A ' a carros de *Make1* e Lane ' B ' a qualquer outra marca.

**Entrada**:

| Criar | Tempo |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**Saída**:

| Criar |Dispatch_to_lane | Tempo |
| --- | --- | --- |
| Make1 |"A" |2015-01-01T00:00:01.0000000 Z |
| Make2 |B |2015-01-01T00:00:02.0000000 Z |

**Solução**:

```SQL
SELECT
    Make
    CASE
        WHEN Make = "Make1" THEN "A"
        ELSE "B"
    END AS Dispatch_to_lane,
    System.TimeStamp() AS Time
FROM
    Input TIMESTAMP BY Time
```

A expressão **Case** compara uma expressão a um conjunto de expressões simples para determinar seu resultado. Neste exemplo, os veículos de *Make1* são expedidos para a pista ' a ', enquanto os veículos de qualquer outro Make serão atribuídos à raia ' B '.

Para obter mais informações, consulte a [expressão CASE](/stream-analytics-query/case-azure-stream-analytics).

## <a name="send-data-to-multiple-outputs"></a>Enviar dados para várias saídas

Várias instruções **Select** podem ser usadas para gerar dados de saída para coletores de saída diferentes. Por exemplo, uma **seleção** pode gerar um alerta baseado em limite enquanto outro pode gerar eventos para o armazenamento de BLOBs.

**Entrada**:

| Criar | Tempo |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**Saídadearquivos de saída**:

| Criar | Tempo |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**AlertOutput de saída**:

| Criar | Tempo | Contagem |
| --- | --- | --- |
| Make2 |2015-01-01T00:00:10.0000000 Z |3 |

**Consulta**:

```SQL
SELECT
    *
INTO
    ArchiveOutput
FROM
    Input TIMESTAMP BY Time

SELECT
    Make,
    System.TimeStamp() AS Time,
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

A cláusula **into** informa Stream Analytics em quais das saídas gravar os dados. A primeira **seleção** define uma consulta de passagem que recebe dados da entrada e os envia para a saída chamada **saídadearquivos**. A segunda consulta faz uma agregação e filtragem simples antes de enviar os resultados para uma saída do sistema de alerta downstream chamada **AlertOutput**.

Observe que a cláusula **with** pode ser usada para definir vários blocos de subconsultas. Essa opção tem o benefício de abrir menos leitores para a fonte de entrada.

**Consulta**:

```SQL
WITH ReaderQuery AS (
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
)

SELECT * INTO ArchiveOutput FROM ReaderQuery

SELECT 
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count] 
INTO AlertOutput 
FROM ReaderQuery
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING [Count] >= 3
```

Para obter mais informações, consulte a [cláusula **with** ](/stream-analytics-query/with-azure-stream-analytics).

## <a name="count-unique-values"></a>Contar valores exclusivos

**Count** e **DISTINCT** podem ser usados para contar o número de valores de campo exclusivos que aparecem no fluxo dentro de uma janela de tempo. Uma consulta pode ser criada para calcular quantas marcas *exclusivas* de carros passaram pelo estande de chamada em uma janela de dois segundos.

**Entrada**:

| Criar | Tempo |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**Saída:**

| Count_make | Tempo |
| --- | --- |
| 2 |2015-01-01T00:00:02.000 Z |
| 1 |2015-01-01T00:00:04.000 Z |

**Consulta:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

**Contagem (diferenciação distinta)** retorna a contagem de valores distintos na coluna **Make** dentro de uma janela de tempo.
Para obter mais informações, consulte [função de agregação **Count** ](/stream-analytics-query/count-azure-stream-analytics).

## <a name="calculation-over-past-events"></a>Cálculo sobre eventos anteriores

A função **lag** pode ser usada para examinar eventos passados dentro de uma janela de tempo e compará-los com relação ao evento atual. Por exemplo, a saída do carro atual poderá ser emitida se for diferente do último carro que passou pela chamada.

**Entrada**:

| Criar | Tempo |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |

**Saída**:

| Criar | Tempo |
| --- | --- |
| Make2 |2015-01-01T00:00:02.0000000 Z |

**Consulta**:

```SQL
SELECT
    Make,
    Time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

Use o **retardo** para inspecionar o fluxo de entrada um evento, recuperando o valor *Make* e comparando-o com o valor *Make* do evento atual e a saída do evento.

Para obter mais informações, consulte [**lag**](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="retrieve-the-first-event-in-a-window"></a>Recuperar o primeiro evento em uma janela

**IsFirst** pode ser usado para recuperar o primeiro evento em uma janela de tempo. Por exemplo, a saída das informações do primeiro carro em cada intervalo de 10 minutos.

**Entrada**:

| License_plate | Criar | Tempo |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000 Z |
| RMV 8282 |Make1 |2015-07-27T00:05:01.0000000 Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000 Z |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**Saída**:

| License_plate | Criar | Tempo |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000 Z |

**Consulta**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) = 1
```

**Isprimeiro** também pode particionar os dados e calcular o primeiro evento para cada carro específico *que* for encontrado em cada intervalo de 10 minutos.

**Saída**:

| License_plate | Criar | Tempo |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000 Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**Consulta**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

Para obter mais informações, consulte [**isFirst**](/stream-analytics-query/isfirst-azure-stream-analytics).

## <a name="return-the-last-event-in-a-window"></a>Retornar o último evento em uma janela

À medida que os eventos são consumidos pelo sistema em tempo real, não há nenhuma função que possa determinar se um evento será o último a chegar para aquela janela de tempo. Para conseguir isso, o fluxo de entrada precisa ser Unido a outro em que a hora de um evento seja o tempo máximo para todos os eventos nessa janela.

**Entrada**:

| License_plate | Criar | Tempo |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000 Z |
| RMV 8282 |Make1 |2015-07-27T00:05:01.0000000 Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000 Z |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**Saída**:

| License_plate | Criar | Tempo |
| --- | --- | --- |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**Consulta**:

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
    Input.License_plate,
    Input.Make,
    Input.Time
FROM
    Input TIMESTAMP BY Time 
    INNER JOIN LastInWindow
    ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
    AND Input.Time = LastInWindow.LastEventTime
```

A primeira etapa na consulta localiza o carimbo de data/hora máximo em janelas de 10 minutos, que é o carimbo de data/hora do último evento para essa janela. A segunda etapa une os resultados da primeira consulta com o fluxo original para localizar o evento que corresponde aos últimos carimbos de data e hora em cada janela. 

**DateDiff** é uma função específica de data que compara e retorna a diferença de tempo entre dois campos DateTime, para obter mais informações, consulte [funções de data](https://docs.microsoft.com/stream-analytics-query/date-and-time-functions-azure-stream-analytics).

Para obter mais informações sobre como unir fluxos, consulte [**ingressar**](/stream-analytics-query/join-azure-stream-analytics).


## <a name="correlate-events-in-a-stream"></a>Correlacionar eventos em um fluxo

A correlação de eventos no mesmo fluxo pode ser feita examinando eventos passados usando a função **lag** . Por exemplo, uma saída pode ser gerada toda vez que dois carros consecutivos da mesma *marca* passam pela tarifa pelos últimos 90 segundos.

**Entrada**:

| Criar | License_plate | Tempo |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000 Z |
| Make1 |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Make2 |DEF-987 |2015-01-01T00:00:03.0000000 Z |
| Make1 |GHI-345 |2015-01-01T00:00:04.0000000 Z |

**Saída**:

| Criar | Tempo | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| Make1 |2015-01-01T00:00:02.0000000 Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000 Z |

**Consulta**:

```SQL
SELECT
    Make,
    Time,
    License_plate AS Current_car_license_plate,
    LAG(License_plate, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_license_plate,
    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

A função **lag** pode examinar o fluxo de entrada de um evento e recuperar o valor *Make* , comparando-o com o valor *Make* do evento atual.  Depois que a condição for atendida, os dados do evento anterior poderão ser projetados usando **lag** na instrução **Select** .

Para obter mais informações, consulte [lag](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="detect-the-duration-between-events"></a>Detectar a duração entre os eventos

A duração de um evento pode ser calculada examinando o último evento de início quando um evento de término é recebido. Essa consulta pode ser útil para determinar o tempo que um usuário gasta em uma página ou em um recurso.

**Entrada**:  

| Utilizador | Funcionalidade | Evento | Tempo |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Iniciar |2015-01-01T00:00:01.0000000 Z |
| user@location.com |RightMenu |Terminar |2015-01-01T00:00:08.0000000 Z |

**Saída**:  

| Utilizador | Funcionalidade | Duração |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Consulta**:

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

A função **Last** pode ser usada para recuperar o último evento dentro de uma condição específica. Neste exemplo, a condição é um evento do tipo iniciar, particionando a pesquisa por **partição por** usuário e recurso. Dessa forma, cada usuário e recurso é tratado de forma independente ao pesquisar o evento de início. A **duração limite** limita a pesquisa de volta ao tempo para uma hora entre os eventos de término e de início.

## <a name="detect-the-duration-of-a-condition"></a>Detectar a duração de uma condição

Para condições que abrangem vários eventos, a função **lag** pode ser usada para identificar a duração dessa condição. Por exemplo, suponha que um bug resultou em todos os carros com peso incorreto (acima de 20.000 libras) e a duração desse bug deve ser computada.

**Entrada**:

| Criar | Tempo | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |2000 |
| Make2 |2015-01-01T00:00:02.0000000 Z |25000 |
| Make1 |2015-01-01T00:00:03.0000000 Z |26000 |
| Make2 |2015-01-01T00:00:04.0000000 Z |25000 |
| Make1 |2015-01-01T00:00:05.0000000 Z |26000 |
| Make2 |2015-01-01T00:00:06.0000000 Z |25000 |
| Make1 |2015-01-01T00:00:07.0000000 Z |26000 |
| Make2 |2015-01-01T00:00:08.0000000 Z |2000 |

**Saída**:

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00:00:02.000 Z |2015-01-01T00:00:07.000 Z |

**Consulta**:

```SQL
WITH SelectPreviousEvent AS
(
SELECT
    *,
    LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previous_time,
    LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previous_weight
FROM input TIMESTAMP BY [time]
)

SELECT 
    LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previous_weight < 20000 ) [Start_fault],
    previous_time [End_fault]
FROM SelectPreviousEvent
WHERE
    [weight] < 20000
    AND previous_weight > 20000
```
A primeira instrução **Select** correlaciona a medição de peso atual com a medida anterior, projetando-a junto com a medição atual. O segundo **Select** verifica o último evento em que o *previous_weight* é menor que 20000, em que o peso atual é menor que 20000 e a *previous_weight* do evento atual era maior que 20000.

O End_fault é o evento não falho atual em que o evento anterior era falho e o Start_fault é o último evento sem falha antes disso.

## <a name="periodically-output-values"></a>Valores de saída periodicamente

No caso de eventos irregulares ou ausentes, uma saída de intervalo regular pode ser gerada a partir de uma entrada de dados mais esparsa. Por exemplo, gere um evento a cada 5 segundos que relate o ponto de dados visto mais recentemente.

**Entrada**:

| Tempo | Valor |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Saída (primeiras 10 linhas)** :

| Window_end | Last_event. Momento | Last_event. Valor |
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

**Consulta**:

```SQL
SELECT
    System.Timestamp() AS Window_end,
    TopOne() OVER (ORDER BY Time DESC) AS Last_event
FROM
    Input TIMESTAMP BY Time
GROUP BY
    HOPPINGWINDOW(second, 300, 5)
```

Essa consulta gera eventos a cada 5 segundos e gera o último evento recebido anteriormente. A duração de **HOPPINGWINDOW** determina quanto tempo a consulta procura localizar o evento mais recente.

Para obter mais informações, consulte a [janela de salto](/stream-analytics-query/hopping-window-azure-stream-analytics).

## <a name="process-events-with-independent-time-substreams"></a>Processar eventos com tempo independente (subfluxos)

Os eventos podem chegar atrasados ou fora de ordem devido a distorções de relógio entre produtores de eventos, distorções de relógio entre partições ou latência de rede.
Por exemplo, o relógio do dispositivo para a *chamada de tarifaid* 2 é de cinco segundos atrás da *chamada* 1 e o *relógio do dispositivo* para o basincronismo 3 é de dez segundos atrás da *chamada* 1. Uma computação pode ocorrer de forma independente para cada chamada, considerando apenas seus próprios dados de relógio como um carimbo de data/hora.

**Entrada**:

| LicensePlate | Criar | Tempo | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:01.0000000 Z | 1 |
| YHN 6970 |Make2 |2015-07-27T00:00:05.0000000 Z | 1 |
| QYF 9358 |Make1 |2015-07-27T00:00:01.0000000 Z | 2 |
| GXF 9462 |Make3 |2015-07-27T00:00:04.0000000 Z | 2 |
| VFE 1616 |Make2 |2015-07-27T00:00:10.0000000 Z | 1 |
| RMV 8282 |Make1 |2015-07-27T00:00:03.0000000 Z | 3 |
| MDR 6128 |Make3 |2015-07-27T00:00:11.0000000 Z | 2 |
| YZK 5704 |Make4 |2015-07-27T00:00:07.0000000 Z | 3 |

**Saída**:

| TollID | Contagem |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Consulta**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

O **carimbo de data/hora** por cláusula examina cada linha do tempo de dispositivo independentemente usando subfluxos. O evento de saída para cada *tarifaid* é gerado à medida que eles são computados, o que significa que os eventos estão em ordem em relação a cada *tarifaid* em vez de serem reordenados como se todos os dispositivos estivessem no mesmo relógio.

Para obter mais informações, consulte [carimbo de data/hora](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering)em.

## <a name="remove-duplicate-events-in-a-window"></a>Remover eventos duplicados em uma janela

Ao executar uma operação, como calcular as médias sobre eventos em uma determinada janela de tempo, os eventos duplicados devem ser filtrados. No exemplo a seguir, o segundo evento é uma duplicata do primeiro.

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

**Consulta**:

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

**Contagem (hora distinta)** retorna o número de valores distintos na coluna hora dentro de uma janela de tempo. A saída da primeira etapa pode ser usada para calcular a média por dispositivo, descartando duplicatas.

Para obter mais informações, consulte [Count (hora distinta)](/stream-analytics-query/count-azure-stream-analytics).

## <a name="session-windows"></a>Janelas de sessão

Uma janela de sessão é uma janela que continua expandindo conforme os eventos ocorrem e fecha a computação se nenhum evento for recebido após um período específico ou se a janela atingir sua duração máxima.
Essa janela é particularmente útil ao computar dados de interação do usuário. Uma janela é iniciada quando um usuário começa a interagir com o sistema e fecha quando não há mais eventos observados, o que significa que o usuário parou de interagir.
Por exemplo, um usuário está interagindo com uma página da Web em que o número de cliques é registrado, uma janela de sessão pode ser usada para descobrir por quanto tempo o usuário interagiu com o site.

**Entrada**:

| User_id | Tempo | URL |
| --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000 Z | "www.example.com/a.html" |
| 0 | 2017-01-26T00:00:20.0000000 Z | "www.example.com/b.html" |
| 1 | 2017-01-26T00:00:55.0000000 Z | "www.example.com/c.html" |
| 0 | 2017-01-26T00:01:10.0000000 Z | "www.example.com/d.html" |
| 1 | 2017-01-26T00:01:15.0000000 Z | "www.example.com/e.html" |

**Saída**:

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000 Z | 2017-01-26T00:01:10.0000000 Z | 70 |
| 1 | 2017-01-26T00:00:55.0000000 Z | 2017-01-26T00:01:15.0000000 Z | 20 |

**Consulta**:

``` SQL
SELECT
    user_id,
    MIN(time) as StartTime,
    MAX(time) as EndTime,
    DATEDIFF(second, MIN(time), MAX(time)) AS duration_in_seconds
FROM input TIMESTAMP BY time
GROUP BY
    user_id,
    SessionWindow(minute, 1, 60) OVER (PARTITION BY user_id)
```

O **Select** projeta os dados relevantes para a interação do usuário, juntamente com a duração da interação. Agrupar os dados por usuário e um **SessionWindow** que fecha se nenhuma interação ocorre dentro de 1 minuto, com um tamanho máximo de janela de 60 minutos.

Para obter mais informações sobre **SessionWindow**, consulte a [janela sessão](/stream-analytics-query/session-window-azure-stream-analytics) .

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>Extensibilidade de linguagem com função definida pelo usuário em JavaScript eC#

A linguagem de consulta Azure Stream Analytics pode ser estendida com funções personalizadas escritas em C# JavaScript ou linguagem. As funções definidas pelo usuário (UDF) são computações personalizadas/complexas que não podem ser facilmente expressas usando a linguagem **SQL** . Essas UDFs podem ser definidas uma vez e usadas várias vezes em uma consulta. Por exemplo, um UDF pode ser usado para converter um valor hexadecimal *nvarchar (max)* para um valor *bigint* .

**Entrada**:

| Device_id | HexValue |
| --- | --- |
| 1 | B4 |
| 2 | "11B" |
| 3 | "121" |

**Saída**:

| Device_id | Decimal |
| --- | --- |
| 1 | 180 |
| 2 | 283 |
| 3 | 289 |

```JavaScript
function hex2Int(hexValue){
    return parseInt(hexValue, 16);
}
```

```C#
public static class MyUdfClass {
    public static long Hex2Int(string hexValue){
        return int.Parse(hexValue, System.Globalization.NumberStyles.HexNumber);
    }
}
```

```SQL
SELECT
    Device_id,
    udf.Hex2Int(HexValue) AS Decimal
From
    Input
```

A função definida pelo usuário computará o valor *bigint* do HexValue em todos os eventos consumidos.

Para obter mais informações, consulte [JavaScript](/stream-analytics/stream-analytics-javascript-user-defined-functions) e [C#](/stream-analytics/stream-analytics-edge-csharp-udf).

## <a name="advanced-pattern-matching-with-match_recognize"></a>Correspondência de padrão avançada com MATCH_RECOGNIZE

**MATCH_RECOGNIZE** é um mecanismo de correspondência de padrão avançado que pode ser usado para corresponder uma sequência de eventos a um padrão de expressão regular bem definido.
Por exemplo, um ATM está sendo monitorado em tempo real para falhas, durante a operação do ATM, se houver duas mensagens de aviso consecutivas que o administrador precisa ser notificado.

**Entrada**:

| ATM_id | Operation_id | Return_Code | Tempo |
| --- | --- | --- | --- |
| 1 | "Entrando no PIN" | "Êxito" | 2017-01-26T00:10:00.0000000 Z |
| 2 | "Abertura do slot do Money" | "Êxito" | 2017-01-26T00:10:07.0000000 Z |
| 2 | "Slot de fechamento do Money" | "Êxito" | 2017-01-26T00:10:11.0000000 Z |
| 1 | "Inserindo a quantidade de retirada" | "Êxito" | 2017-01-26T00:10:08.0000000 Z |
| 1 | "Abertura do slot do Money" | Alerta | 2017-01-26T00:10:14.0000000 Z |
| 1 | "Imprimir saldo bancário" | Alerta | 2017-01-26T00:10:19.0000000 Z |

**Saída**:

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | "Abertura do slot do Money" | 2017-01-26T00:10:14.0000000 Z |

```SQL
SELECT *
FROM intput TIMESTAMP BY time OVER ATM_id
MATCH_RECOGNIZE (
    PARTITON BY ATM_id
    LIMIT DURATION(minute, 1)
    MEASURES
        First(Warning.ATM_id) AS ATM_id,
        First(Warning.Operation_Id) AS First_Warning_Operation_id,
        First(Warning.Time) AS Warning_Time
    AFTER MATCH SKIP TO NEXT ROW
    PATTERN (Success* Warning{2,})
    DEFINE
        Success AS Succes.Return_Code = 'Success',
        Failure AS Warning.Return_Code <> 'Success'
) AS patternMatch
```

Essa consulta corresponde a pelo menos dois eventos de falha consecutivos e gera um alarme quando as condições são atendidas.
O **padrão** define a expressão regular a ser usada na correspondência, nesse caso, qualquer número de operações bem-sucedidas seguidas por pelo menos duas falhas consecutivas.
O sucesso e a falha são definidos usando Return_Code valor e, depois que a condição é atendida, as **medidas** são projetadas com *ATM_id*, a primeira operação de aviso e a hora do primeiro aviso.

Para obter mais informações, consulte [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics).

## <a name="geofencing-and-geospatial-queries"></a>Doisolamento geográfico e consultas geoespaciais
O Azure Stream Analytics fornece funções geoespaciais internas que podem ser usadas para implementar cenários como gerenciamento de frota, compartilhamento de Rides, carros conectados e acompanhamento de ativos.
Os dados geoespaciais podem ser ingeridos em formatos geojson ou WKT como parte do fluxo de eventos ou de dados de referência.
Por exemplo, uma empresa especializada em máquinas de fabricação para imprimir passaportes, conceder suas máquinas para governos e consulates. O local dessas máquinas é muito controlado como evitar a colocação de intensamente e o possível uso para falsificação de passaportes. Cada computador é ajustado com um controlador de GPS, essas informações são retransmitidas para um trabalho Azure Stream Analytics.
O fabricante deseja controlar o local dessas máquinas e ser alertado se uma delas sair de uma área autorizada, dessa forma, ela poderá desabilitar, alertar as autoridades e recuperar o equipamento remotamente.

**Entrada**:

| Equipment_id | Equipment_current_location | Tempo |
| --- | --- | --- |
| 1 | "POINT (-122.13288797982818 47.64082002051315)" | 2017-01-26T00:10:00.0000000 Z |
| 1 | "POINT (-122.13307252987875 47.64081350934929)" | 2017-01-26T00:11:00.0000000 Z |
| 1 | "POINT (-122.13308862313283 47.6406508603241)" | 2017-01-26T00:12:00.0000000 Z |
| 1 | "POINT (-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000 Z |

**Entrada de dados de referência**:

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "POLYGON ((-122.13326028450979 47.6409833866794,-122.13261655434621 47.6409833866794,-122.13261655434621 47.64061471602751,-122.13326028450979 47.64061471602751,-122.13326028450979 47.6409833866794))" |

**Saída**:

| Equipment_id | Equipment_alert_location | Tempo |
| --- | --- | --- |
| 1 | "POINT (-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000 Z |

```SQL
SELECT
    input.Equipment_id AS Equipment_id,
    input.Equipment_current_location AS Equipment_current_location,
    input.Time AS Time
FROM input TIMESTAMP BY time
JOIN
    referenceInput 
    ON input.Equipment_id = referenceInput.Equipment_id
    WHERE 
        ST_WITHIN(input.Equipment_currenct_location, referenceInput.Equipment_lease_location) = 1
```

A consulta permite que o fabricante monitore o local das máquinas automaticamente, recebendo alertas quando um computador deixa a cerca geográfica permitida. A função geoespacial interna permite que os usuários usem dados de GPS dentro da consulta sem bibliotecas de terceiros.

Para obter mais informações, consulte os cenários de Unificação [geográfica e agregação geoespacial com Azure Stream Analytics](geospatial-scenarios.md) artigo.

## <a name="get-help"></a>Obter ajuda

Para obter assistência, tente nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
