---
title: Compreender o tratamento de tempo no Azure Stream Analytics
description: Saiba como escolher a melhor hora de início, lidar com eventos tardios e precoces, e sobre métricas de tratamento de tempo no Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 0830a8b552283b5b39fa78c505ed177d1959989f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83640041"
---
# <a name="understand-time-handling-in-azure-stream-analytics"></a>Compreender o tratamento de tempo no Azure Stream Analytics

Neste artigo, aprende-se a fazer escolhas de design para resolver problemas práticos de tratamento de tempo em empregos da Azure Stream Analytics. As decisões de design de tratamento de tempo estão intimamente relacionadas com os fatores de ordenação de eventos.

## <a name="background-time-concepts"></a>Conceitos de tempo de fundo

Para melhor enquadrar a discussão, vamos definir alguns conceitos de fundo:

- **Hora**do evento : A hora em que o evento original aconteceu. Por exemplo, quando um carro em movimento na autoestrada se aproxima de uma portagem.

- **Tempo**de processamento : O momento em que o evento chega ao sistema de processamento e é observado. Por exemplo, quando um sensor de portagem vê o carro e o sistema informático leva alguns momentos para processar os dados.

- **Marca de água**: Um marcador de tempo de evento que indica até que ponto foram incorridos eventos para o processador de streaming. As marcas de água permitem que o sistema indique progressos claros na ingerir os eventos. Pela natureza dos fluxos, os dados do evento de chegada nunca param, pelo que as marcas de água indicam o progresso para um certo ponto no fluxo.

   O conceito de marca de água é importante. As marcas de água permitem ao Stream Analytics determinar quando o sistema pode produzir resultados completos, corretos e repetíveis que não precisam de ser recolhidos. O processamento pode ser feito de uma forma previsível e repetível. Por exemplo, se for necessário fazer uma recontagem para alguma condição de manipulação de erros, as marcas de água são pontos de arranque e de terminação seguros.

Para obter recursos adicionais sobre este assunto, consulte as publicações de blogue de Tyler Akidau [Streaming 101](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-101) e [Streaming 102](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-102).

## <a name="choose-the-best-starting-time"></a>Escolha o melhor tempo de partida

O Stream Analytics dá aos utilizadores duas opções para escolher o tempo do evento: hora de chegada e hora da aplicação.

### <a name="arrival-time"></a>Hora de chegada

A hora de chegada é atribuída na fonte de entrada quando o evento chegar à fonte. Você pode aceder à hora de chegada usando a propriedade **EventEnqueuedUtcTime** para entrada de Hubs de Eventos, a propriedade **IoTHub.EnqueuedTime** para entrada IoT Hub, e a propriedade **BlobProperties.LastModified** para entrada de blob.

O tempo de chegada é usado por padrão e é melhor utilizado para cenários de arquivamento de dados onde a lógica temporal não é necessária.

### <a name="application-time-also-named-event-time"></a>Tempo de candidatura (também nomeado Hora do Evento)

O tempo de candidatura é atribuído quando o evento é gerado, e faz parte da carga útil do evento. Para processar os eventos por tempo de aplicação, utilize a **marca** por cláusula na consulta SELECT. Se o **timestamp por** estiver ausente, os eventos são processados pela hora de chegada.

É importante usar uma marca de tempo na carga útil quando a lógica temporal está envolvida para responder a atrasos no sistema de origem ou na rede. O tempo atribuído a um evento está disponível no [SYSTEM. CARIMBO TEMPORAL.](https://docs.microsoft.com/stream-analytics-query/system-timestamp-stream-analytics)

## <a name="how-time-progresses-in-azure-stream-analytics"></a>Como o tempo progride no Azure Stream Analytics

Quando utiliza o tempo de aplicação, a progressão do tempo baseia-se nos eventos que chegam. É difícil para o sistema de processamento de fluxo saber se não há eventos, ou se os eventos estão atrasados. Por esta razão, o Azure Stream Analytics gera marcas de água heurísticas nas seguintes formas para cada partição de entrada:

* Quando há algum evento de entrada, a marca de água é o maior evento que o Stream Analytics viu até agora menos o tamanho da janela de tolerância fora de ordem.

* Quando não há evento de entrada, a marca de água é a hora de chegada estimada atual menos a janela de tolerância à chegada tardia. A hora estimada de chegada é o tempo que decorreu desde a última vez que um evento de entrada foi visto mais a hora de chegada do evento de entrada.

   A hora de chegada só pode ser estimada porque a hora real de chegada é gerada no corretor de eventos de entrada, como Os Hubs de Eventos, nem no Azure Stream Analytics VM processando os eventos.

O desenho serve dois propósitos adicionais que não a geração de marcas de água:

1. O sistema gera resultados em tempo oportuno com ou sem eventos de entrada.

   Tem controlo sobre o tempo que pretende ver os resultados da saída. No portal Azure, na página de encomendas do **Evento** do seu trabalho stream analytics, pode configurar a definição **de eventos Fora de ordem.** Ao configurar essa definição, considere a troca da oportunidade com tolerância a eventos fora de ordem no fluxo de eventos.

   A janela de tolerância à chegada tardia é necessária para continuar a gerar marcas de água, mesmo na ausência de eventos de entrada. Às vezes, pode haver um período em que não entram eventos, como quando um fluxo de entrada de eventoé escasso. Este problema é exacerbado pelo uso de múltiplas divisórias no corretor de eventos de entrada.

   O streaming de sistemas de processamento de dados sem uma janela de tolerância de chegada tardia pode sofrer de saídas atrasadas quando as entradas são escassas e várias divisórias são usadas.

2. O comportamento do sistema tem de ser repetível. A repetição é uma propriedade importante de um sistema de processamento de dados de streaming.

   A marca de água é derivada da hora de chegada e hora de aplicação. Ambos são persistidos no corretor do evento, e assim repetíveis. Quando se estima uma hora de chegada na ausência de eventos, o Azure Stream Analytics estima o tempo estimado de chegada para a repetição durante a repetição para recuperação de falhas.

Quando escolhe usar a hora de **chegada** como hora do evento, não precisa de configurar a tolerância fora de ordem e a tolerância à chegada tardia. Uma vez que a hora de **chegada** é garantida para aumentar no corretor de eventos de entrada, o Azure Stream Analytics simplesmente ignora as configurações.

## <a name="late-arriving-events"></a>Eventos de chegada tardia

Por definição de janela de tolerância à chegada tardia, para cada evento que está a chegar, o Azure Stream Analytics compara o tempo do **evento** com a hora de **chegada**. Se o tempo do evento estiver fora da janela de tolerância, pode configurar o sistema para abandonar o evento ou ajustar o tempo do evento para estar dentro da tolerância.

Uma vez geradas marcas de água, o serviço pode potencialmente receber eventos com um tempo de evento inferior ao da marca de água. Pode configurar o serviço para **deixar cair** esses eventos ou **ajustar** o tempo do evento ao valor da marca de água.

Como parte do ajuste, o **System.Timestamp** do evento está definido para o novo valor, mas o campo de tempo do **evento** em si não é alterado. Este ajuste é a única situação em que o **System.Timestamp** de um evento pode ser diferente do valor no campo de tempo do evento e pode causar resultados inesperados a serem gerados.

## <a name="handle-time-variation-with-substreams"></a>Manuseie a variação do tempo com substreams

O mecanismo heurístico de geração de marcas de água descrito funciona bem na maioria dos casos em que o tempo é sincronizado entre os vários remetentes de eventos. No entanto, na vida real, especialmente em muitos cenários ioT, o sistema tem pouco controlo sobre o relógio nos remetentes do evento. Os remetentes do evento podem ser todo o tipo de dispositivos no campo, talvez em diferentes versões de hardware e software.

Em vez de usar uma marca de água global para todos os eventos numa partição de entrada, o Stream Analytics tem outro mecanismo chamado **substreams**. Pode utilizar substreams no seu trabalho escrevendo uma consulta de trabalho que utiliza a cláusula [**TIMESTAMP BY**](/stream-analytics-query/timestamp-by-azure-stream-analytics) e a palavra-chave **OVER**. Para designar o subfluxo, forneça um nome de coluna-chave após a palavra-chave **OVER,** como a , de modo a que o sistema aplique políticas de `deviceid` tempo por essa coluna. Cada subfluxo obtém a sua própria marca de água independente. Este mecanismo é útil para permitir a produção de produção atempada, quando se trata de grandes desvios de relógio ou atrasos de rede entre os remetentes do evento.

Os substreams são uma solução única fornecida pelo Azure Stream Analytics, e não são oferecidas por outros sistemas de processamento de dados de streaming.

Quando utiliza substreams, o Stream Analytics aplica a janela de tolerância à chegada tardia aos eventos que chegam. A tolerância à chegada tardia decide a quantidade máxima pela qual diferentes subfluxos podem ser separados uns dos outros. Por exemplo, se o Dispositivo 1 estiver no Timestamp 1, e o Dispositivo 2 estiver no Timestamp 2, a tolerância de chegada mais tardia é o Timestamp 2 menos Timestamp 1. A definição predefinida é de 5 segundos e é provavelmente demasiado pequena para dispositivos com selos de tempo divergentes. Recomendamos que comece com 5 minutos e efaça ajustes de acordo com o padrão de rotação do relógio do dispositivo.

## <a name="early-arriving-events"></a>Eventos de chegada precoce

Você deve ter notado outro conceito chamado janela de chegada precoce que se parece com o oposto da janela de tolerância à chegada tardia. Esta janela é fixada a 5 minutos e serve um propósito diferente da janela de tolerância à chegada tardia.

Como o Azure Stream Analytics garante resultados completos, só pode especificar o horário de início de **trabalho** como a primeira hora de saída do trabalho, e não o tempo de entrada. A hora de início de trabalho é necessária para que a janela completa seja processada, não apenas a partir do meio da janela.

Stream Analytics obtém o tempo de início a partir da especificação de consulta. No entanto, como o corretor de eventos de entrada é indexado apenas pela hora de chegada, o sistema tem que traduzir a hora inicial do evento para a hora de chegada. O sistema pode começar a processar eventos a partir desse ponto no corretor de eventos de entrada. Com o limite da janela de chegada antecipada, a tradução é simples: hora de início do evento menos a janela de chegada antecipada de 5 minutos. Este cálculo também significa que o sistema deixa cair todos os eventos que são vistos como tendo um tempo de evento 5 minutos mais cedo do que a hora de chegada. A métrica dos [eventos](stream-analytics-monitoring.md) de entrada inicial é incrementada quando os eventos são eliminados.

Este conceito é utilizado para garantir que o processamento é repetível independentemente de onde comece a produção. Sem este mecanismo, não seria possível garantir a repetível, como muitos outros sistemas de streaming afirmam que sim.

## <a name="side-effects-of-event-ordering-time-tolerances"></a>Efeitos colaterais da ordem do evento tolerâncias de tempo

Os trabalhos da Stream Analytics têm várias opções de **encomenda de eventos.** Dois podem ser configurados no portal Azure: os **eventos fora de ordem** (tolerância fora de ordem), e os **Eventos que chegam atrasados** (tolerância à chegada tardia). A tolerância à **chegada precoce** é fixada e não pode ser ajustada. Estas políticas de tempo são usadas pela Stream Analytics para fornecer fortes garantias. No entanto, estas configurações têm algumas implicações por vezes inesperadas:

1. Enviando acidentalmente eventos que são muito cedo.

   Os primeiros eventos não devem ser normalmente resumidos. É possível que os primeiros eventos sejam enviados para a saída se o relógio do remetente estiver a correr muito rápido. Todos os eventos de chegada antecipados são abandonados, então você não vai ver nenhum deles da saída.

2. Envio de eventos antigos para Centros de Eventos para ser processado pela Azure Stream Analytics.

   Embora os eventos antigos possam parecer inofensivos no início, devido à aplicação da tolerância à chegada tardia, os eventos antigos podem ser abandonados. Se os eventos forem muito antigos, o valor do carimbo de **tempo do Sistema** é alterado durante a ingestão de eventos. Devido a este comportamento, atualmente o Azure Stream Analytics é mais adequado para cenários de processamento de eventos quase em tempo real, em vez de cenários históricos de processamento de eventos. Pode definir os **Eventos que chegam atrasados** ao maior valor possível (20 dias) para contornar este comportamento em alguns casos.

3. As saídas parecem estar atrasadas.

   A primeira marca de água é gerada no momento calculado: o **tempo máximo** de evento observado até agora, menos o tamanho da janela de tolerância fora de ordem. Por predefinição, a tolerância fora de ordem está configurada a zero (00 minutos e 00 segundos). Quando se fixa num valor temporal mais elevado e não nulo, a primeira saída do trabalho de streaming é adiada por esse valor de tempo (ou superior) devido ao primeiro tempo de marca de água calculado.

4. As inputs são escassas.

   Quando não há entrada numa dada divisória, o tempo da marca de água é calculado como a hora de **chegada** menos a janela de tolerância à chegada tardia. Como resultado, se os eventos de entrada forem pouco frequentes e escassos, a saída pode ser adiada por esse período de tempo. Os **eventos** padrão que chegam ao valor tardio é de 5 segundos. Você deve esperar ver algum atraso ao enviar eventos de entrada um de cada vez, por exemplo. Os atrasos podem piorar, quando se define **eventos que chegam tarde** a um grande valor.

5. **Sistema.O** valor do carimbo de tempo é diferente do tempo no campo de tempo do **evento.**

   Como descrito anteriormente, o sistema ajusta o tempo do evento pela tolerância fora de ordem ou janelas de tolerância à chegada tardia. O valor do **tempo do** evento é ajustado, mas não o campo de tempo do **evento.** Isto pode ser usado para identificar para que eventos os selos temporais ajustados. Se o sistema alterou a marca de tempo devido a uma das tolerâncias, normalmente são as mesmas.

## <a name="metrics-to-observe"></a>Métricas a observar

Pode observar uma série de efeitos de tolerância de tempo através das [métricas](stream-analytics-monitoring.md)de trabalho do Stream Analytics . As seguintes métricas são relevantes:

|Metric  | Descrição  |
|---------|---------|
| **Eventos fora de ordem** | Indica o número de eventos recebidos fora de ordem, que foram retirados ou dado um carimbo de tempo ajustado. Esta métrica é impactada diretamente pela configuração da definição **de eventos Fora de ordem** na página de encomenda do **Evento** no trabalho no portal Azure. |
| **Eventos de entrada tardia** | Indica o número de eventos que chegam atrasados da fonte. Esta métrica inclui eventos que foram retirados ou que tiveram o seu carimbo temporal ajustado. Esta métrica é diretamente impactada pela configuração dos **Eventos que chegam atrasados** na página **de encomendas** do Evento no trabalho no portal Azure. |
| **Eventos de entrada precoce** | Indica o número de eventos que chegam mais cedo da fonte que foram retirados, ou a sua marca de tempo foi ajustada se forem mais de 5 minutos antes. |
| **Atraso na marca de água** | Indica o atraso do trabalho de processamento de dados de streaming. Veja mais na secção seguinte.|

## <a name="watermark-delay-details"></a>Detalhes do atraso da marca de água

A métrica de atraso da **marca de água** é calculada como o tempo do relógio de parede do nó de processamento menos a maior marca de água que já viu até agora. Para mais informações, consulte o post de blog de atraso da [marca de água.](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

Pode haver várias razões para este valor métrico ser maior do que 0 em funcionamento normal:

1. Atraso de processamento inerente ao gasoduto de streaming. Normalmente este atraso é nominal.

2. A janela de tolerância fora de ordem introduziu o atraso, porque a marca de água é reduzida pelo tamanho da janela de tolerância.

3. A janela de chegada tardia introduziu o atraso, porque a marca de água é reduzida pelo tamanho da janela de tolerância.

4. O desvio do relógio do nó de processamento gera a métrica.

Há uma série de outros constrangimentos de recursos que podem fazer com que o gasoduto de streaming abrande. A métrica de atraso da marca de água pode subir devido a:

1. Não são suficientes recursos de processamento no Stream Analytics para lidar com o volume de eventos de entrada. Para aumentar os recursos, consulte [Compreender e ajustar unidades](stream-analytics-streaming-unit-consumption.md)de streaming.

2. Não há entrada suficiente dentro dos corretores de eventos de entrada, por isso são estrangulados. Para obter possíveis soluções, consulte automaticamente a escala das unidades de produção do [Azure Event Hubs](../event-hubs/event-hubs-auto-inflate.md).

3. Os lavatórios de produção não são aprovisionados com capacidade suficiente, pelo que são estrangulados. As soluções possíveis variam muito com base no sabor do serviço de saída que está a ser utilizado.

## <a name="output-event-frequency"></a>Frequência de evento de saída

O Azure Stream Analytics usa o progresso da marca de água como o único gatilho para produzir eventos de saída. Uma vez que a marca de água é derivada de dados de entrada, é repetível durante a recuperação da falha e também no reprocessamento iniciado pelo utilizador. Ao utilizar [agregados com janelas,](stream-analytics-window-functions.md)o serviço produz apenas saídas na extremidade das janelas. Em alguns casos, os utilizadores podem querer ver agregados parciais gerados a partir das janelas. Os agregados parciais não são suportados atualmente no Azure Stream Analytics.

Noutras soluções de streaming, os eventos de saída poderiam ser materializados em vários pontos de gatilho, dependendo das circunstâncias externas. É possível em algumas soluções que os eventos de saída para uma determinada janela de tempo possam ser gerados várias vezes. À medida que os valores de entrada são refinados, os resultados agregados tornam-se mais precisos. Os acontecimentos podem ser especulados no início, e revistos ao longo do tempo. Por exemplo, quando um determinado dispositivo está offline da rede, um valor estimado pode ser usado por um sistema. Mais tarde, o mesmo dispositivo entra em linha para a rede. Em seguida, os dados reais do evento podem ser incluídos no fluxo de entrada. A saída resulta do processamento dessa janela de tempo produz uma saída mais precisa.

## <a name="illustrated-example-of-watermarks"></a>Exemplo ilustrado de marcas de água

As imagens que se seguem ilustram como as marcas de água progridem em circunstâncias diferentes.

Esta tabela mostra os dados de exemplo que são gráficos abaixo. Note que a hora do evento e a hora de chegada variam, às vezes combinando e às vezes não.

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

- Janelas de chegada antecipada são 5 minutos
- Janela de chegada tardia é de 5 minutos
- A janela de reencomenda ção é de 2 minutos

1. Ilustração da marca de água progredindo através destes eventos:

   ![Ilustração de marca de água Azure Stream Analytics](media/stream-analytics-time-handling/WaterMark-graph-1.png)

   Processos notáveis ilustrados no gráfico anterior:

   1. O primeiro evento (dispositivo1) e o segundo evento (dispositivo2) têm tempos alinhados e são processados sem ajustes. A marca de água progride em cada evento.

   2. Quando o terceiro evento (dispositivo1) é processado, a hora de chegada (12:11) precede a hora do evento (12:17). O evento chegou 6 minutos antes, pelo que o evento é abandonado devido à tolerância de chegada antecipada de 5 minutos.

      A marca de água não progride neste caso de um evento precoce.

   3. O quarto evento (dispositivo3) e o quinto evento (dispositivo1) têm tempos alinhados e são processados sem ajustes. A marca de água progride em cada evento.

   4. Quando o sexto evento (dispositivo3) é processado, a hora de chegada (12:17) e a hora do evento (12:12) estão abaixo do nível da marca de água. O tempo do evento é ajustado ao nível da marca de água (12:17).

   5. Quando o décimo segundo evento (dispositivo3) é processado, a hora de chegada (12:27) é 6 minutos antes da hora do evento (12:21). A política de chegada tardia é aplicada. O tempo do evento é ajustado (12:22), que está acima da marca de água (12:21) para que não seja aplicado mais ajustes.

2. Segunda ilustração da marca de água progredindo sem uma política de chegada antecipada:

   ![Azure Stream Analytics sem ilustração de marca de água de política precoce](media/stream-analytics-time-handling/watermark-graph-2.png)

   Neste exemplo, não é aplicada nenhuma política de chegada antecipada. Eventos mais distantes que chegam cedo aumentam significativamente a marca de água. Note que o terceiro evento (dispositivoId1 no momento 12:11) não é largado neste cenário, e a marca de água é elevada para 12:15. O quarto evento é ajustado para a frente 7 minutos (12:08 a 12:15) como resultado.

3. Na ilustração final, são utilizados substreams (OVER the DeviceId). Várias marcas de água são rastreadas, uma por riacho. Há menos eventos com os seus tempos ajustados como resultado.

   ![Azure Stream Analytics substreams ilustração de marca de água](media/stream-analytics-time-handling/watermark-graph-3.png)

## <a name="next-steps"></a>Passos seguintes

- [Considerações de ordem de eventos Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md)
- [Métricas de trabalho de Stream Analytics](stream-analytics-monitoring.md)
