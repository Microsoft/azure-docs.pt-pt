---
title: Compreender o manuseamento do tempo no Azure Stream Analytics
description: Aprenda a escolher a melhor hora de início, lidar com eventos tardios e precoces e sobre métricas de manuseamento de tempo no Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: f9dbdb3907b376df8de988730c6c48ed01bfccd0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019946"
---
# <a name="understand-time-handling-in-azure-stream-analytics"></a>Compreender o manuseamento do tempo no Azure Stream Analytics

Neste artigo, você aprende a fazer escolhas de design para resolver problemas práticos de tratamento de tempo em trabalhos Azure Stream Analytics. As decisões de design de manuseamento de tempo estão intimamente relacionadas com os fatores de encomenda de eventos.

## <a name="background-time-concepts"></a>Conceitos de tempo de fundo

Para enquadrar melhor a discussão, vamos definir alguns conceitos de fundo:

- **Hora do evento**: A hora em que o evento original aconteceu. Por exemplo, quando um carro em movimento na autoestrada se aproxima de uma portagem.

- **Tempo de processamento**: A hora em que o evento atinge o sistema de processamento e é observado. Por exemplo, quando um sensor de portagem vê o carro e o sistema informático demora alguns momentos a processar os dados.

- **Marca de água**: Um marcador de tempo de evento que indica até que ponto os eventos foram ingressados no processador de streaming. As marcas de água permitem que o sistema indique progressos claros na ingestão dos eventos. Pela natureza dos riachos, os dados do evento nunca param, pelo que as marcas de água indicam o progresso para um determinado ponto no fluxo.

   O conceito de marca de água é importante. As marcas de água permitem que o Stream Analytics determine quando o sistema pode produzir resultados completos, corretos e repetíveis que não precisam de ser recolhidos. O processamento pode ser feito de forma previsível e repetível. Por exemplo, se for necessário fazer uma recontagem para algumas condições de manuseamento de erros, as marcas de água são pontos de partida e de finalização seguros.

Para obter recursos adicionais sobre este assunto, consulte os posts de blog de Tyler Akidau [streaming 101](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-101) e [Streaming 102](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-102).

## <a name="choose-the-best-starting-time"></a>Escolha o melhor tempo de partida

O Stream Analytics dá aos utilizadores duas opções para escolher a hora do evento: hora de chegada e hora da aplicação.

### <a name="arrival-time"></a>Hora de chegada

A hora de chegada é atribuída na fonte de entrada quando o evento chegar à fonte. Você pode aceder à hora de chegada usando a propriedade **EventEnqueuedUtcTime** para entrada de Event Hubs, a propriedade **IoTHub.EnqueuedTime** para entrada IoT Hub, e a propriedade **BlobProperties.LastModified** para entrada blob.

A hora de chegada é usada por padrão e é melhor usada para cenários de arquivamento de dados onde a lógica temporal não é necessária.

### <a name="application-time-also-named-event-time"></a>Tempo de inscrição (também nomeado Hora do Evento)

O tempo de inscrição é atribuído quando o evento é gerado, e faz parte da carga útil do evento. Para processar eventos por tempo de aplicação, utilize o **Relógio de Relógio por** cláusula na consulta SELECT. Se **o Timestamp by** estiver ausente, os eventos são processados na hora de chegada.

É importante usar um timetamp na carga útil quando a lógica temporal está envolvida para explicar os atrasos no sistema de origem ou na rede. O tempo atribuído a um evento está disponível no [SYSTEM. TIMETAMP](/stream-analytics-query/system-timestamp-stream-analytics).

## <a name="how-time-progresses-in-azure-stream-analytics"></a>Como o tempo progride no Azure Stream Analytics

Quando utiliza o tempo de aplicação, a progressão do tempo baseia-se nos eventos que chegam. É difícil para o sistema de processamento de fluxo saber se não há eventos, ou se os eventos são atrasados. Por esta razão, o Azure Stream Analytics gera marcas de água heurísticas das seguintes formas para cada partição de entrada:

* Quando há qualquer evento de entrada, a marca de água é o maior evento que stream analytics viu até agora menos o tamanho da janela de tolerância fora de ordem.

* Quando não há evento de entrada, a marca de água é a hora de chegada estimada atual, menos a janela de tolerância de chegada tardia. A hora estimada de chegada é a hora que decorreu desde a última vez que um evento de entrada foi visto mais a hora de chegada do evento de entrada.

   A hora de chegada só pode ser estimada porque o tempo real de chegada é gerado no corretor de eventos de entrada, como o Event Hubs, nem no Azure Stream Analytics VM processando os eventos.

O desenho serve duas finalidades adicionais que não a geração de marcas de água:

1. O sistema gera resultados em tempo útil com ou sem eventos de entrada.

   Tens controlo sobre a hora que queres ver os resultados da saída. No portal Azure, na página de encomendas do **Evento** do seu trabalho stream Analytics, pode configurar a definição **de eventos Fora de ordem.** Ao configurar essa definição, considere a compensação da oportunidade com tolerância de eventos fora de ordem no fluxo de eventos.

   A janela de tolerância à chegada tardia é necessária para continuar a gerar marcas de água, mesmo na ausência de eventos de entrada. Por vezes, pode haver um período em que não entram eventos, como quando um fluxo de entrada de eventos é escasso. Este problema é exacerbado pelo uso de múltiplas divisórias no corretor de eventos de entrada.

   Os sistemas de processamento de dados de streaming sem uma janela de tolerância à chegada tardia podem sofrer de saídas atrasadas quando as entradas são escassas e são utilizadas várias divisórias.

2. O comportamento do sistema tem de ser repetível. A repetibilidade é uma propriedade importante de um sistema de processamento de dados de streaming.

   A marca de água é derivada da hora de chegada e da hora de aplicação. Ambos são persistidos no corretor de eventos, e, portanto, repetíveis. Quando uma hora de chegada é estimada na ausência de eventos, a Azure Stream Analytics escreve a hora estimada de chegada para a repetibilidade durante a repetição para a recuperação de falhas.

Quando optar por utilizar a hora de **chegada** como hora do evento, aí não precisa de configurar a tolerância fora de ordem e a tolerância tardia à chegada. Uma vez que a **hora de chegada** é garantida para aumentar no corretor de eventos de entrada, a Azure Stream Analytics simplesmente ignora as configurações.

## <a name="late-arriving-events"></a>Eventos tardios de chegada

Por definição de janela de tolerância de chegada tardia, para cada evento de entrada, o Azure Stream Analytics compara a hora do **evento** com a hora de **chegada**. Se o tempo do evento estiver fora da janela de tolerância, pode configurar o sistema para deixar cair o evento ou ajustar o tempo do evento para estar dentro da tolerância.

Uma vez geradas as marcas de água, o serviço pode potencialmente receber eventos com um tempo de evento inferior ao da marca de água. Pode configurar o serviço para **deixar cair** esses eventos ou **ajustar** o tempo do evento ao valor da marca de água.

Como parte do ajuste, o **system.timestamp** do evento está definido para o novo valor, mas o campo **de tempo** do evento em si não é alterado. Este ajuste é a única situação em que o **System.Timestamp** de um evento pode ser diferente do valor no campo do tempo do evento e pode causar resultados inesperados a serem gerados.

## <a name="handle-time-variation-with-substreams"></a>Lidar com variação de tempo com substreams

O mecanismo de geração de marcas de água heurística descrito funciona bem na maioria dos casos em que o tempo é maioritariamente sincronizado entre os vários remetentes de eventos. No entanto, na vida real, especialmente em muitos cenários IoT, o sistema tem pouco controlo sobre o relógio nos remetentes do evento. Os remetentes do evento podem ser todo o tipo de dispositivos no campo, talvez em diferentes versões de hardware e software.

Em vez de usar uma marca de água que é global para todos os eventos numa partição de entrada, o Stream Analytics tem outro mecanismo chamado **substreams**. Pode utilizar substreams no seu trabalho escrevendo uma consulta de trabalho que utiliza a cláusula [**TIMETAMP BY**](/stream-analytics-query/timestamp-by-azure-stream-analytics) e a palavra-chave **OVER**. Para designar o substream, forneça um nome de coluna-chave após a palavra-chave **OVER,** como uma `deviceid` , de modo que o sistema aplique políticas de tempo por essa coluna. Cada substream recebe a sua própria marca de água independente. Este mecanismo é útil para permitir a geração de produção oportuna, quando se lida com grandes distorções de relógio ou atrasos de rede entre os remetentes de eventos.

Os substreams são uma solução única fornecida pelo Azure Stream Analytics, e não são oferecidos por outros sistemas de processamento de dados de streaming.

Quando utiliza substreams, o Stream Analytics aplica a janela de tolerância de chegada tardia a eventos de entrada. A tolerância de chegada tardia decide a quantidade máxima pela qual diferentes substreams podem ser separados uns dos outros. Por exemplo, se o Dispositivo 1 estiver no Timestamp 1 e o Dispositivo 2 estiver no Timestamp 2, a tolerância de chegada mais tardia é o Timestamp 2 menos o Timestamp 1. A definição predefinida é de 5 segundos e é provavelmente demasiado pequena para dispositivos com tempotações divergentes. Recomendamos que comece com 5 minutos e faça ajustes de acordo com o padrão de distorção do relógio do dispositivo.

## <a name="early-arriving-events"></a>Eventos de chegada precoce

Deve ter notado outro conceito chamado janela de chegada precoce que se parece com o oposto da janela de tolerância à chegada tardia. Esta janela é fixada a 5 minutos e serve um propósito diferente da janela de tolerância de chegada tardia.

Como o Azure Stream Analytics garante resultados completos, só pode especificar a hora de início de **trabalho** como a primeira hora de saída do trabalho, e não o tempo de entrada. O tempo de início do trabalho é necessário para que a janela completa seja processada, não apenas a partir do meio da janela.

Stream Analytics obtém a hora de início a partir da especificação de consulta. No entanto, como o corretor de eventos de entrada só está indexado na hora de chegada, o sistema tem de traduzir a hora de início do evento até à hora de chegada. O sistema pode começar a processar eventos a partir desse ponto no corretor de eventos de entrada. Com o limite da janela de chegada precoce, a tradução é simples: hora de início do evento menos a janela de chegada precoce de 5 minutos. Este cálculo também significa que o sistema deixa cair todos os eventos que são vistos como tendo um tempo de evento 5 minutos antes da hora de chegada. A [métrica dos eventos de entrada antecipada](stream-analytics-monitoring.md) é incrementada quando os eventos são eliminados.

Este conceito é usado para garantir que o processamento é repetível independentemente de onde você começar a produção. Sem este mecanismo, não seria possível garantir a repetibilidade, como muitos outros sistemas de streaming afirmam que sim.

## <a name="side-effects-of-event-ordering-time-tolerances"></a>Efeitos colaterais do evento ordenando tolerâncias ao tempo

Os trabalhos da Stream Analytics têm várias opções **de encomendas de Eventos.** Dois podem ser configurados no portal Azure: a definição **de eventos fora de ordem** (tolerância fora de ordem) e os **Eventos que chegam tarde** (tolerância à chegada tardia). A tolerância **à chegada precoce** é fixa e não pode ser ajustada. Estas políticas de tempo são usadas pela Stream Analytics para fornecer garantias fortes. No entanto, estas configurações têm algumas implicações por vezes inesperadas:

1. Enviando acidentalmente eventos que são muito cedo.

   Os primeiros eventos não devem ser normalmente des resultados. É possível que os primeiros eventos sejam enviados para a saída se o relógio do remetente estiver a correr muito rápido. Todos os eventos de chegada precoce são abandonados, por isso não verá nenhum deles a partir da saída.

2. Envio de eventos antigos para Os Centros de Eventos para ser processado pela Azure Stream Analytics.

   Embora os eventos antigos possam parecer inofensivos no início, devido à aplicação da tolerância à chegada tardia, os eventos antigos podem ser eliminados. Se os eventos forem demasiado antigos, o valor **System.Timestamp** é alterado durante a ingestão de eventos. Devido a este comportamento, atualmente o Azure Stream Analytics é mais adequado para cenários de processamento de eventos em tempo real, em vez de cenários históricos de processamento de eventos. Pode definir os **Eventos que chegam atrasados** ao maior valor possível (20 dias) para contornar este comportamento em alguns casos.

3. As saídas parecem estar atrasadas.

   A primeira marca de água é gerada no momento calculado: o **tempo máximo** de evento que o sistema observou até agora, menos o tamanho da janela de tolerância fora de ordem. Por predefinição, a tolerância fora de ordem é configurada a zero (00 minutos e 00 segundos). Quando se define para um valor temporal superior, sem zero, a primeira saída do trabalho de streaming é adiada por esse valor de tempo (ou superior) devido à primeira hora de marca de água que é calculada.

4. As entradas são escassas.

   Quando não há entrada numa determinada partição, o tempo de marca de água é calculado como a hora de **chegada** menos a janela de tolerância à chegada tardia. Como resultado, se os eventos de entrada forem pouco frequentes e escassos, a saída pode ser retardada por esse tempo. Os **eventos predefinidos que chegam ao** valor tardio são de 5 segundos. Você deve esperar ver algum atraso ao enviar eventos de entrada um de cada vez, por exemplo. Os atrasos podem piorar quando se **definem eventos que chegam atrasados** a um grande valor.

5. O valor **do sistema.Timestamp** é diferente do tempo no campo de tempo do **evento.**

   Como descrito anteriormente, o sistema ajusta o tempo de evento pela tolerância fora de ordem ou janelas de tolerância de chegada tardia. O valor **do system.timestamp** do evento é ajustado, mas não o campo de tempo do **evento.** Isto pode ser usado para identificar para que eventos os tempos demps ajustados. Se o sistema mudou a hora devido a uma das tolerâncias, normalmente são as mesmas.

## <a name="metrics-to-observe"></a>Métricas a observar

Pode observar uma série de efeitos de tolerância ao tempo através de [métricas](stream-analytics-monitoring.md)de trabalho stream Analytics . São relevantes as seguintes métricas:

|Metric  | Descrição  |
|---------|---------|
| **Eventos fora de ordem** | Indica o número de eventos recebidos fora de ordem, que foram retirados ou dado um tempotando ajustado. Esta métrica é diretamente impactada pela configuração dos **eventos out of order** na página **de encomendas** do Evento no trabalho no portal Azure. |
| **Eventos de entrada tardia** | Indica o número de eventos que chegam atrasados da fonte. Esta métrica inclui eventos que foram abandonados ou tiveram a sua hora marcada foi ajustado. Esta métrica é diretamente impactada pela configuração dos **Eventos que chegam tarde** na página **de encomendas** do Evento no trabalho no portal Azure. |
| **Eventos de entrada antecipada** | Indica o número de eventos que chegam mais cedo da fonte que foram largados, ou a sua hora marcada foi ajustada se forem superiores a 5 minutos antes. |
| **Atraso da marca de água** | Indica o atraso do trabalho de processamento de dados de streaming. Veja mais na secção Local.|

## <a name="watermark-delay-details"></a>Detalhes do atraso da marca de água

A métrica **de atraso da marca de água** é calculada como o tempo do relógio de parede do nó de processamento menos a maior marca de água que já viu até agora. Para mais informações, consulte o post de blog de atraso da [marca de água.](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

Pode haver várias razões para que este valor métrico seja maior do que 0 em funcionamento normal:

1. Atraso de processamento inerente ao gasoduto de streaming. Normalmente, este atraso é nominal.

2. A janela de tolerância fora de ordem introduziu atrasos, porque a marca de água é reduzida pelo tamanho da janela de tolerância.

3. A janela de chegada tardia introduziu atraso, porque a marca de água é reduzida pelo tamanho da janela de tolerância.

4. Distorcer o relógio do nó de processamento gerando a métrica.

Existem uma série de outras restrições de recursos que podem fazer com que o gasoduto de streaming abrande. A métrica do atraso da marca de água pode subir devido a:

1. Não há recursos de processamento suficientes no Stream Analytics para lidar com o volume de eventos de entrada. Para aumentar os recursos, consulte [Compreender e ajustar Unidades de Streaming.](stream-analytics-streaming-unit-consumption.md)

2. Não é suficiente produção dentro dos corretores de eventos de entrada, por isso são estrangulados. Para obter possíveis soluções, consulte [automaticamente as unidades de produção do Azure Event Hubs](../event-hubs/event-hubs-auto-inflate.md).

3. Os lavatórios de saída não são a provisionados com capacidade suficiente, por isso são estrangulados. As soluções possíveis variam muito com base no sabor do serviço de saída que está a ser utilizado.

## <a name="output-event-frequency"></a>Frequência do evento de saída

A Azure Stream Analytics usa o progresso da marca de água como o único gatilho para produzir eventos de produção. Como a marca de água é derivada de dados de entrada, é repetível durante a recuperação de falhas e também no reprocessamento iniciado pelo utilizador. Quando se utilizam [agregados com janelas,](stream-analytics-window-functions.md)o serviço produz apenas saídas na extremidade das janelas. Em alguns casos, os utilizadores podem querer ver agregados parciais gerados a partir das janelas. Os agregados parciais não são suportados atualmente no Azure Stream Analytics.

Noutras soluções de streaming, os eventos de produção poderiam ser materializados em vários pontos de gatilho, dependendo das circunstâncias externas. É possível em algumas soluções que os eventos de saída para uma determinada janela de tempo possam ser gerados várias vezes. À medida que os valores de entrada são refinados, os resultados agregados tornam-se mais precisos. Os acontecimentos podem ser especulados no início, e revistos ao longo do tempo. Por exemplo, quando um determinado dispositivo está offline da rede, um valor estimado poderia ser usado por um sistema. Mais tarde, o mesmo dispositivo chega online à rede. Em seguida, os dados reais do evento poderiam ser incluídos no fluxo de entrada. A saída resulta do processamento dessa janela de tempo que produz uma saída mais precisa.

## <a name="illustrated-example-of-watermarks"></a>Exemplo ilustrado de marcas de água

As imagens que se seguem ilustram como as marcas de água progridem em diferentes circunstâncias.

Esta tabela mostra os dados de exemplo que são cartografados abaixo. Note que a hora do evento e a hora de chegada variam, por vezes combinando e às vezes não.

| Hora do evento | Hora de chegada | DeviceId |
| --- | --- | --- |
| 12:07 | 12:07 | device1
| 12:08 | 12:08 | device2
| 12:17 | 12:11 | device1
| 12:08 | 12:13 | dispositivo3
| 12:19 | 12:16 | device1
| 12:12 | 12:17 | dispositivo3
| 12:17 | 12:18 | device2
| 12:20 | 12:19 | device2
| 12:16 | 12:21 | dispositivo3
| 12:23 | 12:22 | device2
| 12:22 | 12:24 | device2
| 12:21 | 12:27 | dispositivo3

Nesta ilustração, são utilizadas as seguintes tolerâncias:

- As janelas de chegada antecipadas são de 5 minutos
- Janela de chegada tardia é de 5 minutos
- Reencomendar a janela é de 2 minutos

1. Ilustração de marca de água progredindo através destes eventos:

   ![Ilustração de marca de água Azure Stream Analytics](media/stream-analytics-time-handling/WaterMark-graph-1.png)

   Processos notáveis ilustrados no gráfico anterior:

   1. O primeiro evento (dispositivo1) e o segundo evento (dispositivo2) têm tempos alinhados e são processados sem ajustes. A marca de água progride em cada evento.

   2. Quando o terceiro evento (dispositivo1) é processado, a hora de chegada (12:11) antecede a hora do evento (12:17). O evento chegou 6 minutos antes, pelo que o evento é abandonado devido à tolerância de chegada antecipada de 5 minutos.

      A marca de água não progride neste caso de um evento precoce.

   3. O quarto evento (dispositivo3) e o quinto evento (dispositivo1) têm tempos alinhados e são processados sem ajuste. A marca de água progride em cada evento.

   4. Quando o sexto evento (dispositivo3) é processado, a hora de chegada (12:17) e a hora do evento (12:12) estão abaixo do nível da marca de água. A hora do evento é ajustada ao nível da marca de água (12:17).

   5. Quando o décimo segundo evento (dispositivo3) é processado, a hora de chegada (12:27) é 6 minutos antes da hora do evento (12:21). A política de chegada tardia é aplicada. A hora do evento é ajustada (12:22), que está acima da marca de água (12:21) para que não seja aplicado qualquer novo ajuste.

2. Segunda ilustração da marca de água progredindo sem uma política de chegada antecipada:

   ![Azure Stream Analytics sem ilustração de marca de água de política precoce](media/stream-analytics-time-handling/watermark-graph-2.png)

   Neste exemplo, não é aplicada nenhuma política de chegada antecipada. Eventos mais distantes que chegam cedo aumentam significativamente a marca de água. Note-se que o terceiro evento (deviceId1 no momento 12:11) não é derrubado neste cenário, e a marca de água é elevada para 12:15. O quarto tempo do evento é ajustado para a frente 7 minutos (12:08 para 12:15) como resultado.

3. Na ilustração final, são utilizados substreams (OVER the DeviceId). Várias marcas de água são rastreadas, uma por riacho. Há menos eventos com os seus tempos ajustados como resultado.

   ![Azure Stream Analytics substreams ilustração de marca de água](media/stream-analytics-time-handling/watermark-graph-3.png)

## <a name="next-steps"></a>Passos seguintes

- [Considerações de ordem de eventos Azure Stream Analytics]()
- [Métricas de trabalho stream Analytics](stream-analytics-monitoring.md)