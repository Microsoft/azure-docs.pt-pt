---
title: Negociação de alta frequência usando Azure Stream Analytics
description: Como fazer a preparação e classificação de modelos de regressão linear num trabalho do Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2021
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: f632c916c3de61b94532e96be23da511ad5863ea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593038"
---
# <a name="high-frequency-trading-simulation-with-stream-analytics"></a>Simulação de negociação de elevada frequência com o Stream Analytics
A combinação da linguagem SQL e das funções definidas pelo utilizador (UDFs) e dos agregados definidos pelo utilizador (UDAs) de JavaScript no Azure Stream Analytics permite aos utilizadores realizar análises avançadas. As análises avançadas podem incluir preparação e classificação de machine learning online, bem como simulação de processos sem estado. Este artigo descreve como realizar uma regressão linear num trabalho do Azure Stream Analytics que faz preparação e classificação contínua num cenário comercial de elevada frequência.

## <a name="high-frequency-trading"></a>Negociação de elevada frequência
O fluxo lógico da negociação de elevada frequência tem que ver com:
1. Obter cotações em tempo real de uma negociação de valores mobiliários.
2. Criar um modelo preditivo em torno das cotações, para que possamos antecipar o movimento dos preços.
3. Emitir ordens de compra ou venda para ganhar dinheiro com a predição bem-sucedida dos movimentos dos preços. 

Como resultado, precisamos de:
* Um feed de cotação em tempo real.
* Um modelo preditivo passível de funcionar nas cotações em tempo real.
* Uma simulação negocial que demonstre os ganhos ou perdas do algoritmo de negociação.

### <a name="real-time-quote-feed"></a>Um feed de cotação em tempo real
O IEX oferece [cotações de compra e venda em tempo real](https://iextrading.com/developer/docs/#websockets) mediante a utilização do socket.io. Pode ser escrito um programa de consola simples para receber as cotações em tempo real e enviar para os Hubs de Eventos do Azure como origem de dados. O código seguinte é uma estrutura do programa. O código omite o processamento de erros, por uma questão de brevidade. Também tem de incluir os pacotes NuGet SocketIoClientDotNet e WindowsAzure.ServiceBus no seu projeto.

```csharp
using Quobject.SocketIoClientDotNet.Client;
using Microsoft.ServiceBus.Messaging;
var symbols = "msft,fb,amzn,goog";
var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
var socket = IO.Socket("https://ws-api.iextrading.com/1.0/tops");
socket.On(Socket.EVENT_MESSAGE, (message) =>
{
    eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes((string)message)));
});
socket.On(Socket.EVENT_CONNECT, () =>
{
    socket.Emit("subscribe", symbols);
});
```

Seguem-se alguns eventos de exemplo gerados:

```json
{"symbol":"MSFT","marketPercent":0.03246,"bidSize":100,"bidPrice":74.8,"askSize":300,"askPrice":74.83,volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,lastUpdated":1506953357170,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"GOOG","marketPercent":0.04825,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,lastUpdated":1506953357633,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"MSFT","marketPercent":0.03244,"bidSize":100,"bidPrice":74.8,"askSize":100,"askPrice":74.83,volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,lastUpdated":1506953359118,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"FB","marketPercent":0.01211,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.67,volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,lastUpdated":1506953359641,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"GOOG","marketPercent":0.04795,"bidSize":100,"bidPrice":959.19,"askSize":0,"askPrice":0,volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,lastUpdated":1506953360949,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"FB","marketPercent":0.0121,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.7,volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,lastUpdated":1506953362205,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"GOOG","marketPercent":0.04795,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,lastUpdated":1506953362629,"sector":"softwareservices","securityType":"commonstock"}
```

>[!NOTE]
>O carimbo de data/hora do evento é **lastUpdated**, no formato de hora “epoch”.

### <a name="predictive-model-for-high-frequency-trading"></a>Modelo preditivo para negociação de elevada frequência
Para fins de demonstração, usamos um modelo linear descrito por Darryl Shen no [seu trabalho.](https://docplayer.net/23038840-Order-imbalance-based-strategy-in-high-frequency-trading.html)

O Volume Order Imbalance (VOI) é uma função do preço e volume de compra/venda atual e do preço e volume de compra/venda desde o último tick. O documento identifica a correlação entre o VOI e o movimento de preços futuro. Cria um modelo linear entre os cinco últimos valores de VOI e a alteração do preço nos dez ticks seguintes. Para preparar o modelo, são utilizados os dados do dia anterior com regressão linear. 

O modelo preparado é, então, utilizado para fazer predições das alterações dos preços em cotações no dia de negociação atual em tempo real. Quando é prevista uma alteração dos preços suficientemente grande, é executada uma negociação. Consoante a definição do limiar, podem ser esperadas milhares de negociações para uma única ação durante um dia de negociação.

![Definição de desequilíbrio da ordem de volume](./media/stream-analytics-high-frequency-trading/volume-order-imbalance-formula.png)

Agora, vamos expressar as operações de preparação e predição num trabalho do Azure Stream Analytics.

Em primeiro lugar, as entradas são limpas. A hora “epoch” é convertida em data/hora através de **DATEADD**. **TRY_CAST** é utilizado para limitar tipos de dados sem falhar a consulta. É sempre boa prática converter os campos de entrada nos tipos de dados esperados, para que não haja comportamentos inesperados na manipulação ou comparação dos campos.

```SQL
WITH
typeconvertedquotes AS (
    /* convert all input fields to proper types */
    SELECT
        System.Timestamp AS lastUpdated,
        symbol,
        DATEADD(millisecond, CAST(lastSaleTime as bigint), '1970-01-01T00:00:00Z') AS lastSaleTime,
        TRY_CAST(bidSize as bigint) AS bidSize,
        TRY_CAST(bidPrice as float) AS bidPrice,
        TRY_CAST(askSize as bigint) AS askSize,
        TRY_CAST(askPrice as float) AS askPrice,
        TRY_CAST(volume as bigint) AS volume,
        TRY_CAST(lastSaleSize as bigint) AS lastSaleSize,
        TRY_CAST(lastSalePrice as float) AS lastSalePrice
    FROM quotes TIMESTAMP BY DATEADD(millisecond, CAST(lastUpdated as bigint), '1970-01-01T00:00:00Z')
),
timefilteredquotes AS (
    /* filter between 7am and 1pm PST, 14:00 to 20:00 UTC */
    /* clean up invalid data points */
    SELECT * FROM typeconvertedquotes
    WHERE DATEPART(hour, lastUpdated) >= 14 AND DATEPART(hour, lastUpdated) < 20 AND bidSize > 0 AND askSize > 0 AND bidPrice > 0 AND askPrice > 0
),
```

Depois, utilizamos a função **LAG** para obter os valores do último tick. É escolhida aleatoriamente uma hora do valor **LIMIT DURATION**. Tendo em conta a frequência da cotação, é seguro presumir que vai encontrar o tick anterior se procurar há uma hora.  

```SQL
shiftedquotes AS (
    /* get previous bid/ask price and size in order to calculate VOI */
    SELECT
        symbol,
        (bidPrice + askPrice)/2 AS midPrice,
        bidPrice,
        bidSize,
        askPrice,
        askSize,
        LAG(bidPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidPricePrev,
        LAG(bidSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidSizePrev,
        LAG(askPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askPricePrev,
        LAG(askSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askSizePrev
    FROM timefilteredquotes
),
```

Em seguida, calculamos o valor de VOI. Se o tick anterior não existir, filtramos os valores nulos, pelo sim, pelo não.

```SQL
currentPriceAndVOI AS (
    /* calculate VOI */
    SELECT
        symbol,
        midPrice,
        (CASE WHEN (bidPrice < bidPricePrev) THEN 0
            ELSE (CASE WHEN (bidPrice = bidPricePrev) THEN (bidSize - bidSizePrev) ELSE bidSize END)
         END) -
        (CASE WHEN (askPrice < askPricePrev) THEN askSize
            ELSE (CASE WHEN (askPrice = askPricePrev) THEN (askSize - askSizePrev) ELSE 0 END)
         END) AS VOI
    FROM shiftedquotes
    WHERE
        bidPrice IS NOT NULL AND
        bidSize IS NOT NULL AND
        askPrice IS NOT NULL AND
        askSize IS NOT NULL AND
        bidPricePrev IS NOT NULL AND
        bidSizePrev IS NOT NULL AND
        askPricePrev IS NOT NULL AND
        askSizePrev IS NOT NULL
),
```

Agora, utilizamos **LAG** novamente para criar uma sequência com dois valores de VOI seguidos, seguida de dez valores de preço médio consecutivos.

```SQL
shiftedPriceAndShiftedVOI AS (
    /* get 10 future prices and 2 previous VOIs */
    SELECT
        symbol,
        midPrice AS midPrice10,
        LAG(midPrice, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice9,
        LAG(midPrice, 2) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice8,
        LAG(midPrice, 3) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice7,
        LAG(midPrice, 4) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice6,
        LAG(midPrice, 5) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice5,
        LAG(midPrice, 6) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice4,
        LAG(midPrice, 7) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice3,
        LAG(midPrice, 8) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice2,
        LAG(midPrice, 9) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice1,
        LAG(midPrice, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice,
        LAG(VOI, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI1,
        LAG(VOI, 11) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
    FROM currentPriceAndVOI
),
```

De seguida, reformatamos os dados em entradas para um modelo linear de duas variáveis. Mais uma vez, filtramos os eventos em que não temos todos os dados.

```SQL
modelInput AS (
    /* create feature vector, x being VOI, y being delta price */
    SELECT
        symbol,
        (midPrice1 + midPrice2 + midPrice3 + midPrice4 + midPrice5 + midPrice6 + midPrice7 + midPrice8 + midPrice9 + midPrice10)/10.0 - midPrice AS y,
        VOI1 AS x1,
        VOI2 AS x2
    FROM shiftedPriceAndShiftedVOI
    WHERE
        midPrice1 IS NOT NULL AND
        midPrice2 IS NOT NULL AND
        midPrice3 IS NOT NULL AND
        midPrice4 IS NOT NULL AND
        midPrice5 IS NOT NULL AND
        midPrice6 IS NOT NULL AND
        midPrice7 IS NOT NULL AND
        midPrice8 IS NOT NULL AND
        midPrice9 IS NOT NULL AND
        midPrice10 IS NOT NULL AND
        midPrice IS NOT NULL AND
        VOI1 IS NOT NULL AND
        VOI2 IS NOT NULL
),
```

Uma vez que o Azure Stream Analytics não tem uma função de regressão linear incorporada, utilizamos as agregações **SUM** e **AVG** para calcular os coeficientes do modelo linear.

![Fórmula matemática linear de regressão](./media/stream-analytics-high-frequency-trading/linear-regression-formula.png)

```SQL
modelagg AS (
    /* get aggregates for linear regression calculation,
     http://faculty.cas.usf.edu/mbrannick/regression/Reg2IV.html */
    SELECT
        symbol,
        SUM(x1 * x1) AS x1x1,
        SUM(x2 * x2) AS x2x2,
        SUM(x1 * y) AS x1y,
        SUM(x2 * y) AS x2y,
        SUM(x1 * x2) AS x1x2,
        AVG(y) AS avgy,
        AVG(x1) AS avgx1,
        AVG(x2) AS avgx2
    FROM modelInput
    GROUP BY symbol, TumblingWindow(hour, 24, -4)
),
modelparambs AS (
    /* calculate b1 and b2 for the linear model */
    SELECT
        symbol,
        (x2x2 * x1y - x1x2 * x2y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b1,
        (x1x1 * x2y - x1x2 * x1y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b2,
        avgy,
        avgx1,
        avgx2
    FROM modelagg
),
model AS (
    /* calculate a for the linear model */
    SELECT
        symbol,
        avgy - b1 * avgx1 - b2 * avgx2 AS a,
        b1,
        b2
    FROM modelparambs
),
```

Para utilizar o modelo do dia anterior para a classificação do evento atual, vamos associar as cotações ao mesmo. Contudo, em vez de **JOIN**, utilizamos **UNION**, para unir os eventos do modelo aos eventos de cotação. Em seguida, utilizamos **LAG** para emparelhar os eventos com o modelo do dia anterior, para obtermos exatamente uma correspondência. Devido ao fim de semana, temos de procurar três dias no passado. Se tivéssemos utilizado um **JOIN** simples, obteríamos três modelos para cada evento de cotação.

```SQL
shiftedVOI AS (
    /* get two consecutive VOIs */
    SELECT
        symbol,
        midPrice,
        VOI AS VOI1,        
        LAG(VOI, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
    FROM currentPriceAndVOI
),
VOIAndModel AS (
    /* combine VOIs and models */
    SELECT
        'voi' AS type,
        symbol,
        midPrice,
        VOI1,
        VOI2,
        0.0 AS a,
        0.0 AS b1,
        0.0 AS b2
    FROM shiftedVOI
    UNION
    SELECT
        'model' AS type,
        symbol,
        0.0 AS midPrice,
        0 AS VOI1,
        0 AS VOI2,
        a,
        b1,
        b2
    FROM model
),
VOIANDModelJoined AS (
    /* match VOIs with the latest model within 3 days (72 hours, to take the weekend into account) */
    SELECT
        symbol,
        midPrice,
        VOI1 as x1,
        VOI2 as x2,
        LAG(a, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS a,
        LAG(b1, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b1,
        LAG(b2, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b2
    FROM VOIAndModel
    WHERE type = 'voi'
),
```

Agora, podemos fazer predições e gerar sinais de compra/venda com base no modelo, com um valor de limiar de 0,02. O valor de negociação 10 significa comprar. O valor de negociação -10 significa vender.

``` SQL
prediction AS (
    /* make prediction if there is a model */
    SELECT
        symbol,
        midPrice,
        a + b1 * x1 + b2 * x2 AS efpc
    FROM VOIANDModelJoined
    WHERE
        a IS NOT NULL AND
        b1 IS NOT NULL AND
        b2 IS NOT NULL AND
        x1 IS NOT NULL AND
        x2 IS NOT NULL
),
tradeSignal AS (
    /* generate buy/sell signals */
    SELECT
        DateAdd(hour, -7, System.Timestamp) AS time,
        symbol,     
        midPrice,
        efpc,
        CASE WHEN (efpc > 0.02) THEN 10 ELSE (CASE WHEN (efpc < -0.02) THEN -10 ELSE 0 END) END AS trade,
        DATETIMEFROMPARTS(DATEPART(year, System.Timestamp), DATEPART(month, System.Timestamp), DATEPART(day, System.Timestamp), 0, 0, 0, 0) as date
    FROM prediction
),
```

### <a name="trading-simulation"></a>Simulação de negociação
Quando tivermos os sinais de negociação, é útil testar a eficiência da estratégia de negociação, sem fazer uma negociação verdadeira. 

Para fazer este teste, utilizamos um UDA, com uma janela de salto, que salta de um em um minuto. O agrupamento adicional pela data e a cláusula “having” permitem à janela apenas contas para eventos que pertençam ao mesmo dia. Para uma janela de salto que remonte há dois dias, a data **GROUP BY** separa o agrupamento em dia anterior e dia de hoje. A cláusula **HAVING** filtra as janelas que terminam no dia de hoje, mas agrupando o dia anterior.

```SQL
simulation AS
(
    /* perform trade simulation for the past 7 hours to cover an entire trading day, and generate output every minute */
    SELECT
        DateAdd(hour, -7, System.Timestamp) AS time,
        symbol,
        date,
        uda.TradeSimulation(tradeSignal) AS s
    FROM tradeSignal
    GROUP BY HoppingWindow(minute, 420, 1), symbol, date
    Having DateDiff(day, date, time) < 1 AND DATEPART(hour, time) < 13
)
```

O UDA de JavaScript inicializa todos os acumuladores na função `init`, calcula a transição do estado com cada evento adicionado à janela e devolve os resultados da simulação no final da janela. O processo de negociação geral é:

- Comprar ações quando é recebido um sinal de compra e não são detidas ações.
- Vender ações quando é recebido um sinal de venda e são detidas ações.
- Posição curta, se não forem detidas ações. 

Se a posição for curta e for recebido um sinal de compra, comprar para cobrir. Temos ou menos 10 ações de uma ação nesta simulação. O custo de transação são sempre 8 $.

```javascript
function main() {
    var TRADE_COST = 8.0;
    var SHARES = 10;
    this.init = function () {
        this.own = false;
        this.pos = 0;
        this.pnl = 0.0;
        this.tradeCosts = 0.0;
        this.buyPrice = 0.0;
        this.sellPrice = 0.0;
        this.buySize = 0;
        this.sellSize = 0;
        this.buyTotal = 0.0;
        this.sellTotal = 0.0;
    }
    this.accumulate = function (tradeSignal, timestamp) {
        if(!this.own && tradeSignal.trade == 10) {
          // Buy to open
          this.own = true;
          this.pos = 1;
          this.buyPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.buySize += SHARES;
          this.buyTotal += SHARES * tradeSignal.midprice;
        } else if(!this.own && tradeSignal.trade == -10) {
          // Sell to open
          this.own = true;
          this.pos = -1
          this.sellPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.sellSize += SHARES;
          this.sellTotal += SHARES * tradeSignal.midprice;
        } else if(this.own && this.pos == 1 && tradeSignal.trade == -10) {
          // Sell to close
          this.own = false;
          this.pos = 0;
          this.sellPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
          this.sellSize += SHARES;
          this.sellTotal += SHARES * tradeSignal.midprice;
          // Sell to open
          this.own = true;
          this.pos = -1;
          this.sellPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.sellSize += SHARES;        
          this.sellTotal += SHARES * tradeSignal.midprice;
        } else if(this.own && this.pos == -1 && tradeSignal.trade == 10) {
          // Buy to close
          this.own = false;
          this.pos = 0;
          this.buyPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
          this.buySize += SHARES;
          this.buyTotal += SHARES * tradeSignal.midprice;
          // Buy to open
          this.own = true;
          this.pos = 1;
          this.buyPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.buySize += SHARES;         
          this.buyTotal += SHARES * tradeSignal.midprice;
        }
    }
    this.computeResult = function () {
        var result = {
            "pnl": this.pnl,
            "buySize": this.buySize,
            "sellSize": this.sellSize,
            "buyTotal": this.buyTotal,
            "sellTotal": this.sellTotal,
            "tradeCost": this.tradeCost
            };
        return result;
    }
}
```

Por fim, produzimos a saída no dashboard do Power BI, para visualização.

```SQL
SELECT * INTO tradeSignalDashboard FROM tradeSignal /* output tradeSignal to PBI */
SELECT
    symbol,
    time,
    date,
    TRY_CAST(s.pnl as float) AS pnl,
    TRY_CAST(s.buySize as bigint) AS buySize,
    TRY_CAST(s.sellSize as bigint) AS sellSize,
    TRY_CAST(s.buyTotal as float) AS buyTotal,
    TRY_CAST(s.sellTotal as float) AS sellTotal
    INTO pnlDashboard
FROM simulation /* output trade simulation to PBI */
```

![Trades Power BI gráfico visual](./media/stream-analytics-high-frequency-trading/trades-power-bi-chart.png)

![PNL Power BI gráfico visual](./media/stream-analytics-high-frequency-trading/pnl-power-bi-chart.png)


## <a name="summary"></a>Resumo
Podemos implementar um modelo de negociação de elevada frequência realístico com uma consulta moderadamente complexa no Azure Stream Analytics. Temos de simplificar o modelo de cinco variáveis de entrada para duas, devido à falta da função de regressão linear incorporada. Contudo, para determinados utilizadores, pode ser possível implementar também algoritmos com dimensões maiores e mais sofisticados como UDA de JavaScript. 

É de salientar que a maior parte da consulta, que não o UDA de JavaScript, pode ser testada e depurada no Visual Studio com as [ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-tools-for-visual-studio-install.md). Após a consulta inicial ter sido escrita, o autor demorou menos de meia hora a testá-la e a depurá-la no Visual Studio. 

Atualmente, o UDA não pode ser depurado no Visual Studio. Estamos a trabalhar no sentido de o permitir, bem como de permitir a capacidade de percorrer o código JavaScript. Além disso, tenha em atenção que os nomes dos campos que chegam ao UDA estão em minúsculas. Este não foi um comportamento óbvio durante o teste da consulta. No entanto, com o nível de compatibilidade 1.1 do Azure Stream Analytics, preservamos a utilização de maiúsculas/minúsculas nos nomes dos campos, para que o comportamento seja mais natural.

Espero que este artigo sirva de inspiração a todos os utilizadores do Azure Stream Analytics, que podem utilizar o nosso serviço para realizar análises avançadas em tempo real e continuamente. Não hesite em dar-nos o seu feedback, para que seja mais fácil implementar consultas para cenários de análises avançadas.
