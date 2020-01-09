---
title: Entenda o tratamento de tempo no Azure Stream Analytics
description: Saiba como a manipulação de tempo funciona em Azure Stream Analytics, como escolher a melhor hora de início, como lidar com eventos atrasados e antigos e métricas de tratamento de tempo.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 367b7c2e1ce1c8b3c0dbc02003218b76096b409d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354654"
---
# <a name="understand-time-handling-in-azure-stream-analytics"></a>Entenda o tratamento de tempo no Azure Stream Analytics

Neste artigo, discutiremos como você pode fazer escolhas de design para resolver problemas práticos de tratamento de tempo no serviço de Azure Stream Analytics. As decisões de design de tratamento de tempo estão fortemente relacionadas aos fatores de ordenação de eventos.

## <a name="background-time-concepts"></a>Conceitos de tempo em segundo plano

Para melhor estruturar a discussão, vamos definir alguns conceitos de plano de fundo:

- **Hora do evento**: a hora em que o evento original ocorreu. Por exemplo, quando um carro em movimento na rodovia se aproximar de um estande de chamada.

- **Tempo de processamento**: a hora em que o evento atinge o sistema de processamento e é observado. Por exemplo, quando um sensor de estande de ligação vê o carro e o sistema de computador demora alguns minutos para processar os dados.

- **Marca d' água**: um marcador de tempo de evento que indica até que ponto os eventos foram inseridos no processador de streaming. As marcas d' água permitem que o sistema indique o progresso claro ao ingerir os eventos. Pela natureza dos fluxos, os dados de evento de entrada nunca são interrompidos, portanto, as marcas d' água indicam o progresso de um determinado ponto no fluxo.

   O conceito de marca d' água é importante. As marcas d' água permitem que Stream Analytics determine quando o sistema pode produzir resultados completos, corretos e repetíveis que não precisam ser cancelados. O processamento pode ser feito de modo garantido e previsível e repetível. Por exemplo, se uma reconta precisa ser feita para algumas condições de tratamento de erros, as marcas d' água são pontos de início e término seguros.

Como recursos adicionais sobre esse assunto, consulte postagens no blog do Tyler Akidau [streaming 101](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-101) e [streaming 102](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-102).

## <a name="choosing-the-best-starting-time"></a>Escolhendo a melhor hora de início

Stream Analytics oferece aos usuários duas opções para a hora do evento de separação:

1. **Hora de chegada**  

   A hora de chegada é atribuída na fonte de entrada quando o evento atinge a origem. Você pode acessar a hora de chegada usando a propriedade **EventEnqueuedUtcTime** para entradas de hubs de eventos, a propriedade **IoTHub. EnqueuedTime** para o Hub IOT e usando a propriedade **BlobProperties. LastModified** para entrada de BLOB.

   Usar a hora de chegada é o comportamento padrão e melhor usado para cenários de arquivamento de dados, onde não há lógica temporal necessária.

2. **Tempo do aplicativo** (também chamado de hora do evento)

   A hora do aplicativo é atribuída quando o evento é gerado e faz parte da carga do evento. Para processar eventos por hora do aplicativo, use a cláusula **timestamp by** na consulta SELECT. Se a cláusula **timestamp by** estiver ausente, os eventos serão processados pela hora de chegada.

   É importante usar um carimbo de data/hora na carga quando a lógica temporal estiver envolvida. Dessa forma, os atrasos no sistema de origem ou na rede podem ser contabilizados.

## <a name="how-time-progresses-in-azure-stream-analytics"></a>Como o tempo progride no Azure Stream Analytics

Ao usar a hora do aplicativo, o tempo de progressão é baseado nos eventos de entrada. É difícil para o sistema de processamento de fluxo saber se não há eventos ou se os eventos estão atrasados. Por esse motivo, Azure Stream Analytics gera marcas-d ' água heurística das seguintes maneiras para cada partição de entrada:

1. Sempre que houver qualquer evento de entrada, a marca d' água será o maior tempo de evento que vimos até agora menos o tamanho da janela de tolerância para fora de ordem.

2. Sempre que não houver nenhum evento de entrada, a marca d' água será a hora de chegada estimada atual (o tempo decorrido em trás da VM de cenas que processa os eventos da última vez em que um evento de entrada é visto mais a hora de chegada do evento de entrada) menos a janela de tolerância de chegada tardia.

   A hora de chegada só pode ser estimada, pois a hora de chegada real é gerada no agente de evento de entrada, como os hubs de eventos, e não na Azure Stream Analytics VM que processa os eventos.

O design atende a duas finalidades adicionais, além de gerar marcas d' água:

1. O sistema gera resultados em tempo hábil com ou sem eventos de entrada.

   Você tem controle sobre o tempo que desejam ver os resultados da saída. Na portal do Azure, na página **ordenação de eventos** do seu trabalho de Stream Analytics, você pode definir a configuração **de eventos fora de ordem** . Ao definir essa configuração, considere a compensação da pontualidade com a tolerância de eventos fora de ordem no fluxo de eventos.

   A janela de tolerância de chegada tardia é importante para manter a geração de marcas d' água, mesmo na ausência de eventos de entrada. Às vezes, pode haver um período em que nenhum evento de entrada chega, como quando um fluxo de entrada de evento é esparso. Esse problema é exacerbado pelo uso de várias partições no agente de evento de entrada.

   Os sistemas de processamento de dados de streaming sem uma janela de tolerância de chegada tardia podem sofrer saídas atrasadas quando as entradas são esparsas e várias partições são usadas.

2. O comportamento do sistema deve ser repetível. A capacidade de repetição é uma propriedade importante de um sistema de processamento de dados de streaming.

   A marca d' água é derivada da hora de chegada e da hora do aplicativo. Ambos são persistidos no agente de eventos e, portanto, podem ser repetidos. No caso de a hora de chegada ter de ser estimada na ausência de eventos, o Azure Stream Analytics registra em log o tempo de chegada estimado para a repetição durante a repetição para a finalidade da recuperação de falha.

Observe que quando você opta por usar a **hora de chegada** como a hora do evento, não é necessário configurar a tolerância para fora de ordem e a tolerância de chegada tardia. Como o **tempo de chegada** é garantido para ser um aumento monotônico no agente de evento de entrada, Azure Stream Analytics simplesmente ignora as configurações.

## <a name="late-arriving-events"></a>Eventos de chegada tardia

Por definição da janela de tolerância de chegada tardia, para cada evento de entrada, Azure Stream Analytics compara a **hora do evento** com a **hora de chegada**; se a hora do evento estiver fora da janela de tolerância, você poderá configurar o sistema para descartar o evento ou ajustar a hora do evento para estar dentro da tolerância.

Considere que, após a geração das marcas d' água, o serviço pode potencialmente receber eventos com tempo de evento inferior à marca d' água. Você pode configurar o serviço para **remover** esses eventos ou **ajustar** a hora do evento para o valor da marca d' água.

Como parte do ajuste, o **System. Timestamp** do evento é definido como o novo valor, mas o próprio campo de **tempo do evento** não é alterado. Esse ajuste é a única situação em que o **System. Timestamp** de um evento pode ser diferente do valor no campo de tempo de evento e pode causar a geração de resultados inesperados.

## <a name="handling-time-variation-with-substreams"></a>Manipulando a variação de tempo com subfluxos

O mecanismo de geração de marca d' água heurística descrito aqui funciona bem na maioria dos casos em que o tempo é geralmente sincronizado entre os vários remetentes de evento. No entanto, na vida real, especialmente em muitos cenários de IoT, o sistema tem pouco controle sobre o relógio nos remetentes do evento. Os remetentes de eventos podem ser todos os tipos de dispositivos no campo, talvez em versões diferentes de hardware e software.

Em vez de usar uma marca-d ' água global para todos os eventos em uma partição de entrada, Stream Analytics tem outro mecanismo chamado subfluxos para ajudá-lo. Você pode utilizar subfluxos em seu trabalho escrevendo uma consulta de trabalho que usa a cláusula [**timestamp by**](/stream-analytics-query/timestamp-by-azure-stream-analytics) e a palavra-chave **over**. Para designar o Subfluxo, forneça um nome de coluna de chave após a palavra-chave **over** , como uma `deviceid`, para que o sistema aplique políticas de tempo por essa coluna. Cada substream obtém sua própria marca-d ' água independente. Esse mecanismo é útil para permitir a geração de saída oportuna, ao lidar com grandes distorções de relógio ou atrasos de rede entre os remetentes de eventos.

Os subfluxos são uma solução exclusiva fornecida pelo Azure Stream Analytics e não são oferecidos por outros sistemas de processamento de dados de streaming. Stream Analytics aplica a janela de tolerância de chegada tardia a eventos de entrada quando os subfluxos são usados. A configuração padrão (5 segundos) provavelmente é muito pequena para dispositivos com carimbos de data/hora divergentes. Recomendamos que você comece com 5 minutos e faça ajustes de acordo com o padrão de distorção do relógio do dispositivo.

## <a name="early-arriving-events"></a>Eventos de chegada antecipada

Você pode ter notado outro conceito chamado janela de chegada antecipada, que se parece com o oposto da janela de tolerância de chegada tardia. Essa janela é fixada em 5 minutos e atende a uma finalidade diferente da chegada tardia.

Como o Azure Stream Analytics garante que ele sempre gere resultados completos, você só pode especificar a **hora de início do trabalho** como a primeira hora de saída do trabalho, não a hora de entrada. A hora de início do trabalho é necessária para que a janela completa seja processada, não apenas do meio da janela.

Stream Analytics, em seguida, deriva a hora de início da especificação de consulta. No entanto, como o agente de evento de entrada é indexado apenas pela hora de chegada, o sistema precisa converter a hora do evento inicial em hora de chegada. O sistema pode iniciar o processamento de eventos desse ponto no agente de evento de entrada. Com o limite inicial da janela de chegada, a tradução é simples. Ele está iniciando o tempo de evento menos a janela de início de 5 minutos. Esse cálculo também significa que o sistema descarta todos os eventos que são vistos com tempo de evento 5 minutos maior do que a hora de chegada.

Esse conceito é usado para garantir que o processamento seja repetido, independentemente de onde você comece a gerar saída. Sem esse mecanismo, não seria possível garantir a possibilidade de repetição, pois muitos outros sistemas de transmissão alegam.

## <a name="side-effects-of-event-ordering-time-tolerances"></a>Efeitos colaterais de tolerâncias de tempo de ordenação de eventos

Stream Analytics trabalhos têm várias opções de **ordenação de eventos** . Dois podem ser configurados no portal do Azure: a configuração **de eventos fora de ordem** (tolerância para fora de ordem) e os **eventos que chegam** à configuração tardia (tolerância de chegada tardia). A tolerância de **chegada antecipada** é fixa e não pode ser ajustada. Essas políticas de tempo são usadas pelo Stream Analytics para fornecer fortes garantias. No entanto, essas configurações têm algumas vezes inesperadas implicações:

1. Enviando acidentalmente eventos que são muito cedo.

   Os eventos iniciais não devem ser enviados normalmente. É possível que os eventos iniciais sejam enviados para a saída se o relógio do remetente estiver sendo executado muito rápido no entanto. Todos os eventos de chegada antecipados são descartados, portanto, você não verá nenhum deles na saída.

2. Enviando eventos antigos para os hubs de eventos a serem processados pelo Azure Stream Analytics.

   Embora eventos antigos possam parecer inofensivos a princípio, devido ao aplicativo da tolerância de chegada tardia, os eventos antigos podem ser descartados. Se os eventos forem muito antigos, o valor **System. Timestamp** será alterado durante a ingestão de eventos. Devido a esse comportamento, atualmente Azure Stream Analytics é mais adequado para cenários de processamento de eventos quase em tempo real, em vez de cenários de processamento de eventos históricos. Você pode definir os **eventos que chegam** ao tempo atrasado até o maior valor possível (20 dias) para contornar esse comportamento em alguns casos.

3. As saídas parecem estar atrasadas.

   A primeira marca d' água é gerada no tempo calculado: o **tempo máximo de evento** que o sistema observou até agora, menos o tamanho da janela de tolerância de fora de ordem. Por padrão, a tolerância para fora de ordem é configurada para zero (00 minutos e 00 segundos). Quando você o define como um valor de tempo maior e diferente de zero, a primeira saída do trabalho de streaming é atrasada por esse valor de tempo (ou maior) devido ao primeiro tempo de marca d' água que é calculado.

4. As entradas são esparsas.

   Quando não há nenhuma entrada em uma determinada partição, a hora da marca d' água é calculada como a **hora de chegada** menos a janela de tolerância de chegada tardia. Como resultado, se os eventos de entrada forem infrequentes e esparsos, a saída poderá ser atrasada por esse período de tempo. Os **eventos padrão que chegam** ao valor atrasado são 5 segundos. Você deve esperar ver algum atraso ao enviar eventos de entrada um de cada vez, por exemplo. Os atrasos podem ser piores, quando você define **eventos que chegam** à janela tardia para um valor grande.

5. O valor **System. Timestamp** é diferente do horário no campo **hora do evento** .

   Conforme descrito anteriormente, o sistema ajusta a hora do evento pela tolerância de fora de ordem ou das janelas de tolerância de chegada tardia. O valor de **System. Timestamp** do evento é ajustado, mas não o campo de **hora do evento** .

## <a name="metrics-to-observe"></a>Métricas a observar

Você pode observar um número de efeitos de tolerância a tempo de ordenação de eventos por meio de [Stream Analytics métricas de trabalho](stream-analytics-monitoring.md). As métricas a seguir são relevantes:

|Métrica  | Descrição  |
|---------|---------|
| **Eventos fora de ordem** | Indica o número de eventos recebidos fora de ordem, que foram descartados ou receberam um carimbo de data/hora ajustado. Essa métrica é afetada diretamente pela configuração da configuração de **eventos fora de ordem** na página **ordenação de eventos** do trabalho no portal do Azure. |
| **Eventos de entrada tardia** | Indica o número de eventos chegando tarde da origem. Essa métrica inclui eventos que foram descartados ou tiveram seu carimbo de data/hora ajustado. Essa métrica é afetada diretamente pela configuração dos **eventos que chegam** à configuração tardia na página **ordenação de eventos** do trabalho no portal do Azure. |
| **Eventos de entrada antecipada** | Indica o número de eventos que chegam desde a origem que foram descartados, ou seu carimbo de data/hora foi ajustado se estiverem além de 5 minutos de antecedência. |
| **Atraso da marca d' água** | Indica o atraso do trabalho de processamento de dados de streaming. Veja mais informações na seção a seguir.|

## <a name="watermark-delay-details"></a>Detalhes do atraso da marca d' água

A métrica de **atraso da marca d' água** é calculada como o tempo do relógio do nó de processamento menos a maior marca d' água que ele viu até agora. Para obter mais informações, consulte a [postagem do blog atraso na marca d' água](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/).

Pode haver vários motivos pelos quais esse valor de métrica é maior que 0 em operação normal:

1. Atraso de processamento inerente do pipeline de streaming. Normalmente, esse atraso é nominal.

2. A janela de tolerância para fora de ordem apresentou atraso, pois a marca d' água é reduzida pelo tamanho da janela de tolerância.

3. A janela de chegada tardia introduziu o atraso, pois a marca d' água é reduzida pelo tamanho da janela de tolerância.

4. Distorção de relógio do nó de processamento que gera a métrica.

Há várias outras restrições de recursos que podem fazer com que o pipeline de streaming fique lento. A métrica de atraso da marca d' água pode aumentar devido a:

1. Não há recursos suficientes de processamento no Stream Analytics para lidar com o volume de eventos de entrada. Para escalar verticalmente os recursos, consulte [entender e ajustar as unidades de streaming](stream-analytics-streaming-unit-consumption.md).

2. Não há taxa de transferência suficiente nos agentes de evento de entrada, portanto elas são limitadas. Para obter soluções possíveis, consulte [escalar verticalmente automaticamente unidades de produtividade dos hubs de eventos do Azure](../event-hubs/event-hubs-auto-inflate.md).

3. Os coletores de saída não são provisionados com capacidade suficiente, portanto, eles são limitados. As soluções possíveis variam muito com base no tipo de serviço de saída que está sendo usado.

## <a name="output-event-frequency"></a>Frequência de evento de saída

Azure Stream Analytics usa o andamento da marca d' água como o único gatilho para produzir eventos de saída. Como a marca d' água é derivada dos dados de entrada, ela é repetida durante a recuperação de falha e também no reprocessamento iniciado pelo usuário.

Ao usar [agregações em janelas](stream-analytics-window-functions.md), o serviço só produz saídas no final das janelas. Em alguns casos, os usuários podem querer ver agregações parciais geradas por meio das janelas. Não há suporte para agregações parciais atualmente em Azure Stream Analytics.

Em outras soluções de streaming, os eventos de saída podem ser materializados em vários pontos de gatilho, dependendo das circunstâncias externas. É possível em algumas soluções que os eventos de saída de uma determinada janela de tempo poderiam ser gerados várias vezes. Como os valores de entrada são refinados, os resultados agregados se tornam mais precisos. Os eventos podem ser especulados a princípio e revisados ao longo do tempo. Por exemplo, quando um determinado dispositivo está offline da rede, um valor estimado pode ser usado por um sistema. Posteriormente, o mesmo dispositivo fica online para a rede. Em seguida, os dados reais do evento podem ser incluídos no fluxo de entrada. A saída resulta do processamento dessa janela de tempo produz uma saída mais precisa.

## <a name="illustrated-example-of-watermarks"></a>Exemplo ilustrado de marcas d' água

As imagens a seguir ilustram como as marcas d' água são progredidas em diferentes circunstâncias.

Esta tabela mostra os dados de exemplo que estão no gráfico abaixo. Observe que a hora do evento e a hora da chegada variam, às vezes, correspondendo e, às vezes, não.

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

Nesta ilustração, as seguintes tolerâncias são usadas:

- As janelas de chegada antecipadas são 5 minutos
- A janela de chegada tardia é de 5 minutos
- Reordenar janela é 2 minutos

1. Ilustração da marca d' água progredindo com estes eventos:

   ![Ilustração de marca d' água Azure Stream Analytics](media/stream-analytics-time-handling/WaterMark-graph-1.png)

   Processos notáveis ilustrados no gráfico anterior:

   1. O primeiro evento (Device1) e o segundo evento (dispositivo2) têm horas alinhadas e são processados sem ajustes. A marca d' água progride em cada evento.

   2. Quando o terceiro evento (Device1) é processado, a hora de chegada (12:11) precede a hora do evento (12:17). O evento chegou 6 minutos antes e, portanto, o evento é Descartado devido à tolerância de chegada antecipada de 5 minutos.

      A marca d' água não progride nesse caso de um evento antecipado.

   3. O quarto evento (dispositivo3) e o quinto evento (Device1) alinharam horas e são processados sem ajuste. A marca d' água progride em cada evento.

   4. Quando o sexto evento (dispositivo3) é processado, a hora de chegada (12:17) e a hora do evento (12:12) estão abaixo do nível da marca d' água. A hora do evento é ajustada para o nível de marca d' água (12:17).

   5. Quando o décimo-segundo evento (dispositivo3) é processado, a hora de chegada (12:27) é de 6 minutos à frente da hora do evento (12:21). A política de chegada tardia é aplicada. A hora do evento é ajustada (12:22), que está acima da marca-d ' água (12:21), portanto, nenhum ajuste adicional é aplicado.

2. Segunda ilustração da marca d' água progredindo sem uma política de chegada antecipada:

   ![Azure Stream Analytics nenhuma ilustração de marca d' água de política inicial](media/stream-analytics-time-handling/watermark-graph-2.png)

   Neste exemplo, nenhuma política de chegada antecipada é aplicada. Os eventos de exceção que chegam antecipadamente aumentam a marca d' água de forma significativa. Observe que o terceiro evento (deviceId1 no horário 12:11) não é Descartado nesse cenário e a marca d' água é gerada para 12:15. A quarta hora do evento é ajustada em frente a 7 minutos (12:08 a 12:15) como resultado.

3. Na ilustração final, os subfluxos são usados (via DeviceID). Várias marcas d' água são rastreadas, uma por fluxo. Há menos eventos com seus tempos ajustados como resultado.

   ![Ilustração da marca d' água de subfluxos Azure Stream Analytics](media/stream-analytics-time-handling/watermark-graph-3.png)

## <a name="next-steps"></a>Passos seguintes

- [Azure Stream Analytics considerações sobre ordem de evento](stream-analytics-out-of-order-and-late-events.md)
- [Métricas de trabalho Stream Analytics](stream-analytics-monitoring.md)
