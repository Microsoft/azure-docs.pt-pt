---
title: Padrões de consulta comuns no Azure Stream Analytics
description: Este artigo descreve vários padrões e desenhos comuns de consulta que são úteis em trabalhos de Azure Stream Analytics.
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: aa8bd6e89dd47c4e972a860691d1bc3779ba5bc7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75982319"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Padrões de consulta comuns no Azure Stream Analytics

As consultas no Azure Stream Analytics são expressas numa linguagem de consulta semelhante a SQL. As construções linguísticas estão documentadas no guia de referência da linguagem de [consulta Stream Analytics.](/stream-analytics-query/stream-analytics-query-language-reference) 

O design de consulta pode expressar uma lógica simples de passagem para mover dados de eventos de um fluxo de entrada para uma loja de dados de saída, ou pode fazer uma combinação de padrões ricos e análise temporal para calcular agregados ao longo de várias janelas de tempo como na [solução Build uma solução IoT usando o](stream-analytics-build-an-iot-solution-using-stream-analytics.md) guia Stream Analytics. Pode juntar dados de várias inputs para combinar eventos de streaming, e pode fazer análises contra dados de referência estáticos para enriquecer os valores do evento. Também pode escrever dados para várias saídas.

Este artigo descreve soluções para vários padrões de consulta comuns baseados em cenários do mundo real.

## <a name="supported-data-formats"></a>Formatos de Dados Suportados

O Azure Stream Analytics suporta eventos de processamento em formatos de dados CSV, JSON e Avro.

Tanto a JSON como a Avro podem conter tipos complexos, tais como objetos aninhados (registos) ou matrizes. Para obter mais informações sobre o trabalho com estes tipos complexos de dados, consulte o artigo de [dados da Parsing JSON e da AVRO.](stream-analytics-parsing-json.md)

## <a name="simple-pass-through-query"></a>Consulta de passagem simples

Uma consulta simples de passagem pode ser usada para copiar os dados de fluxo de entrada para a saída. Por exemplo, se um fluxo de dados que contenham informações sobre veículos em tempo real tiver de ser guardado numa base de dados SQL para análise de cartas, uma simples consulta de passagem fará o trabalho.

**Entrada:**

| Fabrico | Hora | Peso |
| --- | --- | --- |
| Make1 |2015-01-01t00:00:00:00000000Z |"1000" |
| Make1 |2015-01-01t00:00:00:00.0000000Z |"2000" |

**Saída:**

| Fabrico | Hora | Peso |
| --- | --- | --- |
| Make1 |2015-01-01t00:00:00:00000000Z |"1000" |
| Make1 |2015-01-01t00:00:00:00.0000000Z |"2000" |

**Consulta:**

```SQL
SELECT
    *
INTO Output
FROM Input
```

Uma consulta **SELECT** * projeta todos os campos de um evento de entrada e envia-os para a saída. Da mesma forma, o **SELECT** também pode ser utilizado apenas para projetar os campos necessários a partir da entrada. Neste exemplo, se o veículo *Make* and *Time* for o único campo necessário a ser guardado, esses campos podem ser especificados na declaração **SELECT.**

**Entrada:**

| Fabrico | Hora | Peso |
| --- | --- | --- |
| Make1 |2015-01-01t00:00:00:00000000Z |1000 |
| Make1 |2015-01-01t00:00:00:00.0000000Z |2000 |
| Make2 |2015-01-01t00:00:00:00.0000000Z |1500 |

**Saída:**

| Fabrico | Hora |
| --- | --- |
| Make1 |2015-01-01t00:00:00:00000000Z |
| Make1 |2015-01-01t00:00:00:00.0000000Z |
| Make2 |2015-01-01t00:00:00:00.0000000Z |

**Consulta:**

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```
## <a name="data-aggregation-over-time"></a>Agregação de dados ao longo do tempo

Para calcular informação ao longo de uma janela de tempo, os dados podem ser agregados em conjunto. Neste exemplo, uma contagem é calculada ao longo dos últimos 10 minutos de tempo para cada carro específico.

**Entrada:**

| Fabrico | Hora | Peso |
| --- | --- | --- |
| Make1 |2015-01-01t00:00:00:00000000Z |1000 |
| Make1 |2015-01-01t00:00:00:00.0000000Z |2000 |
| Make2 |2015-01-01t00:00:00:00.0000000Z |1500 |

**Saída:**

| Fabrico | Contagem |
| --- | --- |
| Make1 | 2 |
| Make2 | 1 |

**Consulta:**

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

Esta agregação agrupa os carros pela *Make* e conta-os a cada 10 segundos. A saída tem a *Marca* e *contagem* de carros que passaram pela portagem.

TumblingWindow é uma função de janela usada para agrupar eventos em conjunto. Uma agregação pode ser aplicada em todos os eventos agrupados. Para mais informações, consulte [as funções de janela](stream-analytics-window-functions.md).

Para obter mais informações sobre a agregação, consulte [funções agregadas](/stream-analytics-query/aggregate-functions-azure-stream-analytics).

## <a name="data-conversion"></a>Conversão de dados

Os dados podem ser fundidos em tempo real utilizando o método **CAST.** Por exemplo, o peso do carro pode ser convertido de **nvarchar tipo nvarchar (máx)** para tipo **bigint** e ser usado num cálculo numérico.

**Entrada:**

| Fabrico | Hora | Peso |
| --- | --- | --- |
| Make1 |2015-01-01t00:00:00:00000000Z |"1000" |
| Make1 |2015-01-01t00:00:00:00.0000000Z |"2000" |

**Saída:**

| Fabrico | Peso |
| --- | --- |
| Make1 |3.000 |

**Consulta:**

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

Utilize uma declaração **DOCAST** para especificar o seu tipo de dados. Consulte a lista de tipos de dados suportados em tipos de [dados (Azure Stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics).

Para obter mais informações sobre [as funções](/stream-analytics-query/conversion-functions-azure-stream-analytics)de conversão de dados.

## <a name="string-matching-with-like-and-not-like"></a>Corda combinando com LIKE e NÃO LIKE

**Like** and **NOT LIKE** pode ser usado para verificar se um campo corresponde a um determinado padrão. Por exemplo, um filtro pode ser criado para devolver apenas as placas que começam com a letra 'A' e terminam com o número 9.

**Entrada:**

| Fabrico | License_plate | Hora |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01t00:00:00:00000000Z |
| Make2 |AAA-999 |2015-01-01t00:00:00:00.0000000Z |
| Make3 |ABC-369 |2015-01-01t00:00:00:00.0000000Z |

**Saída:**

| Fabrico | License_plate | Hora |
| --- | --- | --- |
| Make2 |AAA-999 |2015-01-01t00:00:00:00.0000000Z |
| Make3 |ABC-369 |2015-01-01t00:00:00:00.0000000Z |

**Consulta:**

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

Utilize a declaração **LIKE** para verificar o valor **de campo License_plate.** Deve começar com a letra 'A', depois ter qualquer cadeia de caracteres zero ou mais, terminando com o número 9.

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>Especificar lógica para diferentes casos/valores (declarações de CASO)

**As** declarações de CASO podem fornecer diferentes computações para diferentes campos, com base em critérios específicos. Por exemplo, atribuir a faixa de rodagem 'A' aos carros da *Make1* e à faixa 'B' a qualquer outra marca.

**Entrada:**

| Fabrico | Hora |
| --- | --- |
| Make1 |2015-01-01t00:00:00:00000000Z |
| Make2 |2015-01-01t00:00:00:00.0000000Z |
| Make2 |2015-01-01t00:00:00:00.0000000Z |

**Saída:**

| Fabrico |Dispatch_to_lane | Hora |
| --- | --- | --- |
| Make1 |"A" |2015-01-01t00:00:00:00000000Z |
| Make2 |"B" |2015-01-01t00:00:00:00.0000000Z |

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

A expressão **CASE** compara uma expressão a um conjunto de expressões simples para determinar o seu resultado. Neste exemplo, os veículos da *Make1* são enviados para a faixa de rodagem 'A', enquanto os veículos de qualquer outra via serão atribuídos à faixa B.

Para mais informações, consulte a expressão do [caso.](/stream-analytics-query/case-azure-stream-analytics)

## <a name="send-data-to-multiple-outputs"></a>Enviar dados para várias saídas

Várias declarações **SELECT** podem ser usadas para obter dados para diferentes sumidouros de saída. Por exemplo, um **SELECT** pode obter um alerta baseado no limiar, enquanto outro pode output eventos para armazenamento blob.

**Entrada:**

| Fabrico | Hora |
| --- | --- |
| Make1 |2015-01-01t00:00:00:00000000Z |
| Make1 |2015-01-01t00:00:00:00.0000000Z |
| Make2 |2015-01-01t00:00:00:00000000Z |
| Make2 |2015-01-01t00:00:00:00.0000000Z |
| Make2 |2015-01-01t00:00:00:00.0000000Z |

**Saída de arquivoSaídas:**

| Fabrico | Hora |
| --- | --- |
| Make1 |2015-01-01t00:00:00:00000000Z |
| Make1 |2015-01-01t00:00:00:00.0000000Z |
| Make2 |2015-01-01t00:00:00:00000000Z |
| Make2 |2015-01-01t00:00:00:00.0000000Z |
| Make2 |2015-01-01t00:00:00:00.0000000Z |

**Alerta de saídaSaída:**

| Fabrico | Hora | Contagem |
| --- | --- | --- |
| Make2 |2015-01-01t00:00:10.0000000Z |3 |

**Consulta:**

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

A cláusula **INTO** diz ao Stream Analytics qual das saídas para escrever os dados. O primeiro **SELECT** define uma consulta pass-through que recebe dados da entrada e envia-os para a saída denominada **ArchiveOutput**. A segunda consulta faz uma simples agregação e filtragem antes de enviar os resultados para uma saída do sistema de alerta a jusante chamada **AlertOutput**.

Note que a cláusula **COM** pode ser usada para definir vários blocos de sub-consulta. Esta opção tem o benefício de abrir menos leitores à fonte de entrada.

**Consulta:**

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

Para mais informações, consulte a cláusula [ **COM** ](/stream-analytics-query/with-azure-stream-analytics).

## <a name="count-unique-values"></a>Contar valores únicos

**COUNT** e **DISTINCT** podem ser usados para contar o número de valores de campo únicos que aparecem no fluxo dentro de uma janela de tempo. Uma consulta pode ser criada para calcular quantas *fezes* únicas de carros passaram pela portagem numa janela de 2 segundos.

**Entrada:**

| Fabrico | Hora |
| --- | --- |
| Make1 |2015-01-01t00:00:00:00000000Z |
| Make1 |2015-01-01t00:00:00:00.0000000Z |
| Make2 |2015-01-01t00:00:00:00000000Z |
| Make2 |2015-01-01t00:00:00:00.0000000Z |
| Make2 |2015-01-01t00:00:00:00.0000000Z |

**Saída:**

| Count_make | Hora |
| --- | --- |
| 2 |2015-01-01t00:00:00:02.000Z |
| 1 |2015-01-01t00:00:00:04.000Z |

**Consulta:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

**COUNT(DISTINTA Fazer)** devolve a contagem de valores distintos na coluna **Fazer** dentro de uma janela de tempo.
Para mais informações, consulte a [função agregado **COUNT** ](/stream-analytics-query/count-azure-stream-analytics).

## <a name="calculation-over-past-events"></a>Cálculo sobre eventos passados

A função **LAG** pode ser usada para olhar para eventos passados dentro de uma janela de tempo e compará-los com o evento atual. Por exemplo, a produção atual do carro pode ser saída se for diferente do último carro que passou pela portagem.

**Entrada:**

| Fabrico | Hora |
| --- | --- |
| Make1 |2015-01-01t00:00:00:00000000Z |
| Make2 |2015-01-01t00:00:00:00.0000000Z |

**Saída:**

| Fabrico | Hora |
| --- | --- |
| Make2 |2015-01-01t00:00:00:00.0000000Z |

**Consulta:**

```SQL
SELECT
    Make,
    Time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

Utilize o **LAG** para espreitar o fluxo de entrada de um evento para trás, recuperando o valor *Fazer* e comparando-o com o *valor de fazer* do evento atual e de saída do evento.

Para mais informações, consulte o [**LAG**](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="retrieve-the-first-event-in-a-window"></a>Recupere o primeiro evento numa janela

**IsFirst** pode ser usado para recuperar o primeiro evento em uma janela do tempo. Por exemplo, a partir da informação do primeiro carro a cada intervalo de 10 minutos.

**Entrada:**

| License_plate | Fabrico | Hora |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27t00:00:00.0000000Z |
| YZK 5704 |Make3 |2015-07-27t00:02:17.0000000Z |
| RMV 8282 |Make1 |2015-07-27t00:05:01.0000000Z |
| YHN 6970 |Make2 |2015-07-27t00:06:00.0000000Z |
| VFE 1616 |Make2 |2015-07-27t00:09:31.0000000Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000Z |

**Saída:**

| License_plate | Fabrico | Hora |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27t00:00:00.0000000Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000Z |

**Consulta:**

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

**IsFirst** também pode dividir os dados e calcular o primeiro evento para cada carro específico *Fazer* encontrado em cada intervalo de 10 minutos.

**Saída:**

| License_plate | Fabrico | Hora |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27t00:00:00.0000000Z |
| YZK 5704 |Make3 |2015-07-27t00:02:17.0000000Z |
| YHN 6970 |Make2 |2015-07-27t00:06:00.0000000Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000Z |

**Consulta:**

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

Para mais informações, consulte o [**IsFirst**](/stream-analytics-query/isfirst-azure-stream-analytics).

## <a name="return-the-last-event-in-a-window"></a>Devolva o último evento numa janela

Como os eventos são consumidos pelo sistema em tempo real, não há nenhuma função que possa determinar se um evento será o último a chegar para essa janela de tempo. Para isso, o fluxo de entrada precisa de ser acompanhado por outro em que o tempo de um evento é o tempo máximo para todos os eventos naquela janela.

**Entrada:**

| License_plate | Fabrico | Hora |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27t00:00:00.0000000Z |
| YZK 5704 |Make3 |2015-07-27t00:02:17.0000000Z |
| RMV 8282 |Make1 |2015-07-27t00:05:01.0000000Z |
| YHN 6970 |Make2 |2015-07-27t00:06:00.0000000Z |
| VFE 1616 |Make2 |2015-07-27t00:09:31.0000000Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000Z |

**Saída:**

| License_plate | Fabrico | Hora |
| --- | --- | --- |
| VFE 1616 |Make2 |2015-07-27t00:09:31.0000000Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000Z |

**Consulta:**

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

O primeiro passo na consulta encontra o carimbo máximo de tempo em janelas de 10 minutos, que é o carimbo temporal do último evento para aquela janela. O segundo passo junta os resultados da primeira consulta com o fluxo original para encontrar o evento que corresponda aos selos da última vez em cada janela. 

**DATEDIFF** é uma função específica da data que compara e devolve a diferença de tempo entre dois campos DateTime, para mais informações, consulte as [funções](https://docs.microsoft.com/stream-analytics-query/date-and-time-functions-azure-stream-analytics)da data .

Para mais informações sobre a adesão de streams, consulte a [**JOIN**](/stream-analytics-query/join-azure-stream-analytics).


## <a name="correlate-events-in-a-stream"></a>Correlacionar eventos em um riacho

Correlacionar eventos no mesmo fluxo pode ser feito olhando para eventos passados usando a função **LAG.** Por exemplo, uma saída pode ser gerada cada vez que dois carros consecutivos do mesmo *Faça* passar pela portagem nos últimos 90 segundos.

**Entrada:**

| Fabrico | License_plate | Hora |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01t00:00:00:00000000Z |
| Make1 |AAA-999 |2015-01-01t00:00:00:00.0000000Z |
| Make2 |DEF-987 |2015-01-01t00:00:00:00.0000000Z |
| Make1 |GHI-345 |2015-01-01t00:00:00:00.0000000Z |

**Saída:**

| Fabrico | Hora | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| Make1 |2015-01-01t00:00:00:00.0000000Z |AAA-999 |ABC-123 |2015-01-01t00:00:00:00000000Z |

**Consulta:**

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

A função **LAG** pode olhar para o fluxo de entrada de um evento para trás e recuperar o valor *Fazer,* comparando-o com o *valor de fazer* do evento atual.  Uma vez satisfeita a condição, os dados do evento anterior podem ser projetados utilizando o **LAG** na declaração **SELECT.**

Para mais informações, consulte o [LAG](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="detect-the-duration-between-events"></a>Detetar a duração entre eventos

A duração de um evento pode ser calculada olhando para o último evento Start uma vez que um evento End é recebido. Esta consulta pode ser útil para determinar o tempo que um utilizador gasta numa página ou numa funcionalidade.

**Entrada:**  

| Utilizador | Funcionalidade | Evento | Hora |
| --- | --- | --- | --- |
| user@location.com |Menu Direito |Iniciar |2015-01-01t00:00:00:00000000Z |
| user@location.com |Menu Direito |Fim |2015-01-01t00:00:00:00.0000000Z |

**Saída:**  

| Utilizador | Funcionalidade | Duração |
| --- | --- | --- |
| user@location.com |Menu Direito |7 |

**Consulta:**

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

A **última** função pode ser utilizada para recuperar o último evento numa condição específica. Neste exemplo, a condição é um evento de tipo Iniciar, dividindo a pesquisa **por PARTIÇÃO por** utilizador e funcionalidade. Desta forma, cada utilizador e funcionalidade é tratado de forma independente quando se procura o evento Iniciar. **A DURAÇÃO DO LIMITE** limita a procura no tempo até 1 hora entre os eventos De Fim e Início.

## <a name="detect-the-duration-of-a-condition"></a>Detetar a duração de uma condição

Para condições que se estendem por vários eventos, a função **LAG** pode ser usada para identificar a duração dessa condição. Por exemplo, suponha que um inseto resultou em todos os carros com um peso incorreto (acima de 20.000 libras), e a duração desse bug deve ser calculada.

**Entrada:**

| Fabrico | Hora | Peso |
| --- | --- | --- |
| Make1 |2015-01-01t00:00:00:00000000Z |2000 |
| Make2 |2015-01-01t00:00:00:00.0000000Z |25 000 |
| Make1 |2015-01-01t00:00:00:00.0000000Z |26000 |
| Make2 |2015-01-01t00:00:00:00.0000000Z |25 000 |
| Make1 |2015-01-01t00:00:00:00.0000000Z |26000 |
| Make2 |2015-01-01t00:00:06.0000000Z |25 000 |
| Make1 |2015-01-01t00:00:00:07.0000000Z |26000 |
| Make2 |2015-01-01t00:00:00:00.0000000Z |2000 |

**Saída:**

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01t00:00:00:02.000Z |2015-01-01t00:00:07.000Z |

**Consulta:**

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
A primeira declaração **SELECT** correlaciona a medição de peso atual com a medição anterior, projetando-a juntamente com a medição atual. O segundo **SELECT** olha para o último evento onde o *previous_weight* é inferior a 20000, onde o peso atual é menor que 20000 e o *previous_weight* do evento atual foi maior que 20000.

O End_fault é o evento atual não defeituoso onde o evento anterior foi defeituoso, e o Start_fault é o último evento não defeituoso antes disso.

## <a name="periodically-output-values"></a>Valores de saída periodicamente

Em caso de eventos irregulares ou em falta, uma saída regular de intervalo pode ser gerada a partir de uma entrada de dados mais escassa. Por exemplo, gere um evento a cada 5 segundos que reporta o ponto de dados mais recentemente visto.

**Entrada:**

| Hora | Valor |
| --- | --- |
| "2014-01-01t06:01:00" |1 |
| "2014-01-01t06:01:05" |2 |
| "2014-01-01t06:01:10" |3 |
| "2014-01-01t06:01:15" |4 |
| "2014-01-01t06:01:30" |5 |
| "2014-01-01t06:01:35" |6 |

**Saída (primeiras 10 linhas)**:

| Window_end | Last_event. Tempo | Last_event. Valor |
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

**Consulta:**

```SQL
SELECT
    System.Timestamp() AS Window_end,
    TopOne() OVER (ORDER BY Time DESC) AS Last_event
FROM
    Input TIMESTAMP BY Time
GROUP BY
    HOPPINGWINDOW(second, 300, 5)
```

Esta consulta gera eventos a cada 5 segundos e produz o último evento que foi recebido anteriormente. A duração **do HOPPINGWINDOW** determina até onde a consulta procura encontrar o mais recente evento.

Para mais informações, consulte a [janela Hopping](/stream-analytics-query/hopping-window-azure-stream-analytics).

## <a name="process-events-with-independent-time-substreams"></a>Processar eventos com tempo independente (Substreams)

Os eventos podem chegar atrasados ou fora de ordem devido a distorções entre os produtores de eventos, os desvios do relógio entre divisórias ou latência da rede.
Por exemplo, o relógio do dispositivo para *o TollID* 2 está cinco segundos atrás do *TollID* 1, e o relógio do dispositivo para *o TollID* 3 está dez segundos atrás do *TollID* 1. Um cálculo pode acontecer de forma independente para cada portagem, considerando apenas os seus próprios dados do relógio como uma marca de tempo.

**Entrada:**

| Placa de matrícula | Fabrico | Hora | Portagem |
| --- | --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27t00:00:01.0000000Z | 1 |
| YHN 6970 |Make2 |2015-07-27t00:00:00.0000000Z | 1 |
| QYF 9358 |Make1 |2015-07-27t00:00:01.0000000Z | 2 |
| GXF 9462 |Make3 |2015-07-27t00:00:04.0000000Z | 2 |
| VFE 1616 |Make2 |2015-07-27t00:00:10.0000000Z | 1 |
| RMV 8282 |Make1 |2015-07-27t00:00:03.0000000Z | 3 |
| MDR 6128 |Make3 |2015-07-27t00:00:11.0000000Z | 2 |
| YZK 5704 |Make4 |2015-07-27t00:00:07.0000000Z | 3 |

**Saída:**

| Portagem | Contagem |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Consulta:**

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

A cláusula **TIMESTAMP OVER BY** olha para cada linha temporal do dispositivo de forma independente utilizando substreams. O evento de saída para cada *Portagem* é gerado à medida que são calculados, o que significa que os eventos estão em ordem em relação a cada *Portagem em* vez de serem reordenados como se todos os dispositivos estivessem no mesmo relógio.

Para mais informações, consulte a [TIMESTAMP BY OVER](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering).

## <a name="remove-duplicate-events-in-a-window"></a>Remova eventos duplicados numa janela

Ao realizar uma operação como calcular as médias sobre os eventos numa determinada janela de tempo, os eventos duplicados devem ser filtrados. No exemplo seguinte, o segundo evento é uma duplicação do primeiro.

**Entrada:**  

| DeviceId | Hora | Atributo | Valor |
| --- | --- | --- | --- |
| 1 |2018-07-27t00:00:00.0000000Z |Temperatura |50 |
| 1 |2018-07-27t00:00:00.0000000Z |Temperatura |50 |
| 2 |2018-07-27t00:00:00.0000000Z |Temperatura |40 |
| 1 |2018-07-27t00:00:00.0000000Z |Temperatura |60 |
| 2 |2018-07-27t00:00:00.0000000Z |Temperatura |50 |
| 1 |2018-07-27t00:00:10.0000000Z |Temperatura |100 |

**Saída:**  

| Valor Médio | DeviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**Consulta:**

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

**COUNT (TEMPO DISTINTO)** devolve o número de valores distintos na coluna Time dentro de um intervalo de tempo. A saída do primeiro passo pode então ser utilizada para calcular a média por dispositivo, descartando duplicados.

Para mais informações, consulte o [COUNT (TEMPO DISTINTO)](/stream-analytics-query/count-azure-stream-analytics).

## <a name="session-windows"></a>Janelas de sessão

Uma Janela de Sessão é uma janela que continua a expandir-se à medida que os eventos ocorrem e fecha para computação se nenhum evento for recebido após um período de tempo específico ou se a janela atingir a sua duração máxima.
Esta janela é particularmente útil na computação de dados de interação do utilizador. Uma janela começa quando um utilizador começa a interagir com o sistema e fecha quando não se observam mais eventos, o que significa que o utilizador deixou de interagir.
Por exemplo, um utilizador está interagindo com uma página web onde o número de cliques é registado, uma Janela de Sessão pode ser usada para descobrir quanto tempo o utilizador interagiu com o site.

**Entrada:**

| User_id | Hora | do IdP |
| --- | --- | --- |
| 0 | 2017-01-26t00:00:00.0000000Z | "www.example.com/a.html" |
| 0 | 2017-01-26t00:00:20.0000000Z | "www.example.com/b.html" |
| 1 | 2017-01-26t00:00:55.0000000Z | "www.example.com/c.html" |
| 0 | 2017-01-26t00:01:10.0000000Z | "www.example.com/d.html" |
| 1 | 2017-01-26t00:01:15.0000000Z | "www.example.com/e.html" |

**Saída:**

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26t00:00:00.0000000Z | 2017-01-26t00:01:10.0000000Z | 70 |
| 1 | 2017-01-26t00:00:55.0000000Z | 2017-01-26t00:01:15.0000000Z | 20 |

**Consulta:**

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

O **SELECT projeta** os dados relevantes para a interação do utilizador, juntamente com a duração da interação. Agrupar os dados pelo utilizador e por uma Janela de **Sessão** que fecha se não houver interação no prazo de 1 minuto, com um tamanho máximo de janela de 60 minutos.

Para mais informações sobre **o SessionWindow,** consulte a [Janela de Sessão](/stream-analytics-query/session-window-azure-stream-analytics) .

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>Extensibility de idioma com função definida pelo utilizador no JavaScript e C #

O idioma de consulta Azure Stream Analytics pode ser estendido com funções personalizadas escritas em linguagem JavaScript ou C#. Funções Definidas pelo Utilizador (UDF) são computações personalizadas/complexas que não podem ser facilmente expressas utilizando o idioma **SQL.** Estes UDFs podem ser definidos uma vez e usados várias vezes dentro de uma consulta. Por exemplo, uma UDF pode ser usada para converter um valor hexadecimal *nvarchar (max)* para um valor *mais grande.*

**Entrada:**

| Device_id | HexValue |
| --- | --- |
| 1 | "B4" |
| 2 | "11B" |
| 3 | "121" |

**Saída:**

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

A Função Definida do Utilizador calculará o valor *mais grandioso* do HexValue em todos os eventos consumidos.

Para mais informações, consulte [javaScript](/azure/stream-analytics/stream-analytics-javascript-user-defined-functions) e [C#](/azure/stream-analytics/stream-analytics-edge-csharp-udf).

## <a name="advanced-pattern-matching-with-match_recognize"></a>Padrão avançado combinando com MATCH_RECOGNIZE

**MATCH_RECOGNIZE** é um mecanismo avançado de correspondência de padrões que pode ser usado para combinar uma sequência de eventos com um padrão de expressão regular bem definido.
Por exemplo, um multibanco está a ser monitorizado em tempo real para falhas, durante o funcionamento do multibanco se houver duas mensagens de aviso consecutivas, o administrador precisa de ser notificado.

**Entrada:**

| ATM_id | Operation_id | Return_Code | Hora |
| --- | --- | --- | --- |
| 1 | "Entrando no Pin" | "Sucesso" | 2017-01-26t00:10:00.0000000Z |
| 2 | "Abertura de dinheiro" | "Sucesso" | 2017-01-26t00:10:07.0000000Z |
| 2 | "Slot de dinheiro de encerramento" | "Sucesso" | 2017-01-26t00:10:11.0000000Z |
| 1 | "Entrar na quantidade de levantamento" | "Sucesso" | 2017-01-26t00:10:08.0000000Z |
| 1 | "Abertura de dinheiro" | "Aviso" | 2017-01-26t00:10:14.0000000Z |
| 1 | "Saldo Bancário de Impressão" | "Aviso" | 2017-01-26t00:10:19.0000000Z |

**Saída:**

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | "Abertura de dinheiro" | 2017-01-26t00:10:14.0000000Z |

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

Esta consulta corresponde a pelo menos dois eventos de falha consecutivos e gera um alarme quando as condições são satisfeitas.
**O PADRÃO** define a expressão regular a utilizar na correspondência, neste caso, qualquer número de operações bem sucedidas seguidas de pelo menos duas falhas consecutivas.
O sucesso e a falha são definidos com base Return_Code valor e uma vez cumprida a condição, as MEDIDAS são **projetadas** com *ATM_id*, a primeira operação de alerta e primeiro tempo de alerta.

Para mais informações, consulte [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics).

## <a name="geofencing-and-geospatial-queries"></a>Questões geoespaciais e geoespaciais
O Azure Stream Analytics fornece funções geoespaciais incorporadas que podem ser usadas para implementar cenários como gestão de frotas, partilha de boleias, carros conectados e rastreio de ativos.
Os dados geoespaciais podem ser ingeridos em formatos GeoJSON ou WKT como parte do fluxo de eventos ou dados de referência.
Por exemplo, uma empresa especializada em máquinas de fabrico para impressão de passaportes, aluga as suas máquinas a governos e consulados. A localização dessas máquinas é fortemente controlada para evitar a deslocação indevido e possível utilização para a contrafação de passaportes. Cada máquina está equipada com um localizador GPS, essa informação é retransmitida para um trabalho de Azure Stream Analytics.
O fabrico pretende acompanhar a localização dessas máquinas e ser alertado se uma delas deixar uma área autorizada, desta forma podem desativar, alertar as autoridades e recuperar o equipamento.

**Entrada:**

| Equipment_id | Equipment_current_location | Hora |
| --- | --- | --- |
| 1 | "PONTO (-122.132887982818 47.64082002051315)" | 2017-01-26t00:10:00.0000000Z |
| 1 | "PONTO (-122.13307252987875 47.64081350934929)" | 2017-01-26t00:11:00.0000000Z |
| 1 | "PONTO (-122.13308862313283 47.6406508603241)" | 2017-01-26t00:12:00.0000000Z |
| 1 | "PONTO (-122.13341048821462 47.64043760861279)" | 2017-01-26t00:13:00.0000000Z |

**Entrada de dados de referência:**

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "POLÍgono (-122.13326028450979 47.6409833866794,-122.13261655434621 47.640983866794-122.13261655444462 1 47.64061471602751,-122.13326028450979 47.64061471602751-122.13326028450979 47.6409833866794)" |

**Saída:**

| Equipment_id | Equipment_alert_location | Hora |
| --- | --- | --- |
| 1 | "PONTO (-122.13341048821462 47.64043760861279)" | 2017-01-26t00:13:00.0000000Z |

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

A consulta permite ao fabricante monitorizar automaticamente a localização das máquinas, recebendo alertas quando uma máquina deixa a geoveda permitida. A função geoespacial incorporada permite que os utilizadores utilizem dados de GPS dentro da consulta sem bibliotecas de terceiros.

Para mais informações, consulte os cenários de [agregação geoespacial e geoespacial com](geospatial-scenarios.md) o artigo da Azure Stream Analytics.

## <a name="get-help"></a>Obter ajuda

Para mais assistência, experimente o nosso [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
