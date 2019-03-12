---
title: Compreender a manipulação de tempo no Azure Stream Analytics
description: Saiba mais sobre o tempo de processamento no Azure Stream Analytics
author: jasonwhowell
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 2a59a81b0894cbf58c5d3ab5a5569f4749b64b00
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57543292"
---
# <a name="understand-time-handling-in-azure-stream-analytics"></a>Compreender a manipulação de tempo no Azure Stream Analytics

Neste artigo, vamos discutir como pode fazer escolhas de design para resolver o tempo prático tratando de problemas no serviço Azure Stream Analytics. Tempo de design de manipulação de decisões estão estreitamente relacionadas com fatores de ordenação de eventos.

## <a name="background-time-concepts"></a>Conceitos de tempo em segundo plano

Para melhor quadro de discussão, vamos definir alguns conceitos em segundo plano:

- **Hora do evento**: A hora quando ocorreu o evento original. Por exemplo, quando um processo de mover o carro no autoestrada aproxima pedágio.

- **Tempo de processamento**: A hora quando o evento atinja o sistema de processamento e for observado. Por exemplo, quando um sensor de portagens incidir vê o sistema de computador e o carro demora alguns minutos a processar os dados.

- **Limite de tamanho**: Um marcador de tempo do evento que indica todos os eventos até que ponto foi entrada para o processador de transmissão em fluxo. As marcas d'água deixar que o sistema indicam o progresso claro na ingestão de eventos. Pela natureza de fluxos, os dados de eventos recebidos nunca deixa de, pelo que as marcas d'água indicam o progresso até um certo ponto no fluxo.

   O conceito de marca d'água é importante. As marcas d'água permitem o Stream Analytics para determinar quando o sistema pode produzir completa e correta e resultados repetíveis que não precisam ser retraí-la. O processamento pode ser feito de forma garantida é previsível e repetível. Por exemplo, se um recontar precisa ser feito para alguma condição de tratamento de erros, as marcas d'água são pontos seguro de início e fim.

Como obter recursos adicionais sobre este assunto, consulte postagens do blog de Tyler Akidau [transmissão em fluxo 101](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-101) e [102 de transmissão em fluxo](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-102).

## <a name="choosing-the-best-starting-time"></a>Escolha a hora de início melhor

Stream Analytics fornece aos usuários duas opções para selecionar a hora do evento:

1. **Hora da chegada**  

   Hora da chegada é atribuída na origem de entrada quando o evento atinja a origem. Pode aceder a hora da chegada ao utilizar o **EventEnqueuedUtcTime** propriedade de entradas de dados de Hubs de eventos, **IoTHub.EnqueuedTime** propriedade para o IoT Hub e a utilizar o **BlobProperties.LastModified**  propriedade de entrada de Blobs.

   Usar o tempo de chegada é o comportamento predefinido e melhor utilizados para cenários, de arquivamento de dados em que não existe nenhuma lógica temporal necessário.

2. **Tempo do aplicativo** (também chamado de tempo do evento)

   Tempo do aplicativo é atribuído quando o evento é gerado, e é parte da payload do evento. Para processar eventos por hora de aplicação, utilize o **Timestamp por** cláusula na consulta select. Se o **Timestamp por** cláusula está ausente, eventos são processados pelo tempo de chegada.

   É importante usar um carimbo no payload quando lógica temporal está envolvida. Dessa forma, atrasos no sistema de origem ou na rede podem ser considerados.

## <a name="how-time-progresses-in-azure-stream-analytics"></a>Como a hora avança no Azure Stream Analytics

Ao usar o tempo do aplicativo, a progressão do tempo baseia-se sobre os eventos de entrada. É difícil para o sistema para saber se não há eventos, ou se os eventos são um atraso de processamento de fluxos. Por esse motivo, o Azure Stream Analytics gera as marcas d'água heurísticas das formas seguintes para cada partição de entrada:

1. Sempre que houver qualquer evento de entrada, a marca d'água é a hora do evento maior que vimos até aqui menos o tamanho da janela de tolerância de fora de ordem.

2. Sempre que não haja nenhum evento de entrada, a marca d'água é a hora de chegada estimado atual (a decorrido hora no segundo plano VM processar os eventos da última hora de um evento de entrada é visto além de que a hora da chegada do evento de entrada) menos a janela de tolerância de chegada tardia.

   O tempo de chegada só pode ser estimado porque o tempo de chegada real é gerado no Mediador de eventos de entrada, como Hubs de eventos e não a VM do Azure Stream Analytics os eventos de processamento.

O design tem duas finalidades de adicionais, além de gerar as marcas d'água:

1. O sistema gera os resultados de forma oportuna com ou sem eventos de entrada.

   Tem controle sobre como pontuais pretendem ver os resultados de saída. No portal do Azure, sobre o **ordenação de eventos** página da sua tarefa do Stream Analytics, pode configurar o **eventos desordenados** definição. Ao configurar essa definição, considere o compromisso de pontualidade com tolerância a de eventos de fora de ordem no fluxo de eventos.

   A janela de tolerância de chegada tardia é importante manter a gerar as marcas d'água, até mesmo na ausência de eventos de entrada. Às vezes, pode haver um período em que não existem eventos de entrada são fornecidos em, como quando um fluxo de entrada de eventos é disperso. Esse problema é exacerbado pelo uso de várias partições no Mediador de eventos de entrada.

   Sistemas de processamento de dados sem uma janela de tolerância de chegada final de transmissão em fluxo possam ficar comprometidos com saídas atrasadas quando entradas estão dispersas e várias partições são utilizadas.

2. O comportamento do sistema deve ser passível de repetição. Capacidade de repetição é uma propriedade importante de um sistema de processamento de dados de transmissão em fluxo.

   A marca d'água é derivada de tempo de chegada e de tempo do aplicativo. Ambos são persistidas no Mediador de eventos e, portanto, repetíveis. No caso do tempo de chegada tem de ser estimado na ausência de eventos, o tempo de chegada estimado para a capacidade de repetição durante a reprodução com o objetivo de recuperação de falhas de diários de Azure Stream Analytics.

Tenha em atenção que, quando opta por utilizar **hora da chegada** como a hora do evento, não é necessário para configurar a tolerância de fora de ordem e a tolerância de chegada tardia. Uma vez que **hora da chegada** é garantido que ser forma monótona aumentando do Mediador de eventos de entrada, o Azure Stream Analytics simplesmente disregards as configurações.

## <a name="late-arriving-events"></a>Eventos que são recebidos atrasados

Por definição da janela de tolerância a chegada tardia, para cada evento de entrada, o Azure Stream Analytics compara o **hora do evento** com o **hora da chegada**; se a hora do evento está fora da janela de tolerância, Pode configurar o sistema para remover o evento ou ajustar o tempo do evento entre a tolerância.

Considere que depois das marcas d'água são geradas, o serviço pode potencialmente receber eventos com o tempo do evento é menor do que a marca d'água. Pode configurar o serviço a qualquer um **drop** desses eventos, ou **ajustar** tempo do evento para o valor de marca d'água.

Como do ajuste, o evento parte **Timestamp** está definido para o novo valor, mas o **hora do evento** campo em si não é alterado. Esta alteração é a única situação em que um evento **Timestamp** pode ser diferente do valor no campo de tempo do evento e poderá provocar resultados inesperados ser gerado.

## <a name="handling-time-variation-with-substreams"></a>Manipulação de variação de tempo com subfluxos

O mecanismo de geração de marca d'água heurística descrito aqui funciona bem na maioria dos casos em que a hora principalmente está sincronizada entre os vários remetentes de eventos. No entanto, na vida real, especialmente em muitos cenários de IoT, o sistema tem pouca controle sobre o relógio nos remetentes de eventos. Os remetentes de eventos podem ser todos os tipos de dispositivos no terreno, talvez em versões diferentes de hardware e software.

Em vez de usar uma marca d'água global para todos os eventos numa partição de entrada, o Stream Analytics tem outro mecanismo chamado subfluxos para ajudá-lo. Pode utilizar subfluxos na sua tarefa, escrevendo uma consulta de trabalho que utiliza a [ **TIMESTAMP BY** ](/stream-analytics-query/timestamp-by-azure-stream-analytics) cláusula e a palavra-chave **sobre**. Para designar o para subfluxos, forneça um nome de coluna chave depois do **ao longo** palavra-chave, como um `deviceid`, para que o sistema aplica políticas de tempo por essa coluna. Para cada subfluxos obtém seu próprio limite de tamanho independente. Esse mecanismo é útil para permitir a geração de saída tempo hábil, ao lidar com grandes desvios de relógio ou atrasos entre remetentes de eventos de rede.

Subfluxos são uma solução única fornecida pelo Azure Stream Analytics e não são oferecidos por outros sistemas de processamento de dados de transmissão em fluxo. Stream Analytics aplica-se a janela de tolerância de chegada tardia para eventos de entrada quando subfluxos são utilizados. A predefinição (5 segundos) provavelmente é demasiado pequena para dispositivos com carimbos de data / divergentes. Recomendamos que comece com 5 minutos e fazer ajustes de acordo com seu padrão de distorção de relógio de dispositivo.

## <a name="early-arriving-events"></a>Primeiros eventos que são recebidos

Talvez tenha notado outro conceito chamado janela de chegada inicial, parecido com o oposto da janela de tolerância de chegada tardia. Essa janela é fixa em 5 minutos e serve a uma finalidade diferente de chegada tardia um.

Porque o Azure Stream Analytics garante sempre gera resultados completos, só pode especificar **hora de início da tarefa** como o primeiro resultado tempo da tarefa, não a hora de entrada. A hora de início de tarefa é necessária para que a janela completa é processada, não apenas do meio da janela.

Stream Analytics deriva, em seguida, a hora de início de especificação de consulta. No entanto, porque o Mediador de eventos de entrada apenas é indexada por um tempo de chegada, o sistema tem de converter a hora do evento inicial para o tempo de chegada. O sistema pode começar processamento de eventos a partir desse ponto em que o Mediador de eventos de entrada. Com o limite de janela que são recebidos iniciais, a tradução é simples. Ele está começando a hora de eventos menos a janela de evento que aconteça antecipada de 5 minutos. Esse cálculo também significa que o sistema ignora todos os eventos que são vistos ter a hora do evento posterior à hora da chegada de 5 minutos.

Esse conceito é utilizado para garantir que o processamento é passível de repetição, independentemente de onde começa a saída do. Sem um mecanismo, não seria possível garantir a capacidade de repetição, como vários outros sistemas de transmissão em fluxo de afirmações que fazem.

## <a name="side-effects-of-event-ordering-time-tolerances"></a>Efeitos colaterais das tolerâncias de tempo de ordenação de eventos

Tarefas do Stream Analytics possuem vários **ordenação de eventos** opções. Dois pode ser configurada no portal do Azure: os **eventos fora de ordem** definição (fora de ordem tolerância) e o **eventos que se atrasam** definição (tardia tolerância de chegada). O **chegada antecipada** tolerância é fixo e não pode ser ajustada. Estas políticas de tempo são utilizadas pelo Stream Analytics para providenciar garantias fortes. No entanto, estas definições têm algumas implicações, às vezes, inesperadas:

1. Acidentalmente envio de eventos que são demasiado antigo.

   Primeiros eventos não devem ser produzidos normalmente. É possível que os primeiros eventos sejam enviados para a saída se o relógio do remetente está a executar demasiado rápido entanto. Todos os eventos que são recebidos iniciais são ignorados, portanto, não verá qualquer um da partir da saída.

2. Enviar eventos antigos para os Hubs de eventos a serem processados pelo Azure Stream Analytics.

   Embora podem parecer eventos antigos inofensivos em primeiro lugar, devido a aplicação de tolerância de chegada tardia, os eventos antigos podem ser removidos. Se os eventos são demasiado antigos, o **Timestamp** valor é alterado durante a ingestão de eventos. Devido a este comportamento, atualmente Azure Stream Analytics é mais adequado para cenários, em vez de cenários de processamento de eventos históricos de processamento de eventos em quase tempo real. Pode definir o **eventos que se atrasam** altura de maior valor possível (20 dias) para contornar este comportamento em alguns casos.

3. Saídas parecem ser atrasada.

   A primeira marca d'água é gerada no momento calculado: os **hora máxima do evento** o sistema tenha observado até agora, menos o tamanho da janela de tolerância de fora de ordem. Por predefinição, a tolerância de fora de ordem está configurada para zero (00 minutos e 00 segundos). Quando defini-la para um valor de tempo superior, diferente de zero, o resultado de primeiro da tarefa de transmissão em fluxo é atrasado por esse valor de tempo (ou superior) devido a primeira vez de marca d'água é calculada.

4. Entradas estão dispersas.

   Quando não existe nenhuma entrada numa determinada partição, o tempo limite de tamanho é calculado como o **hora da chegada** menos a janela de tolerância de chegada tardia. Como resultado, se os eventos de entrada são pouco frequentes e dispersas, o resultado pode ser atrasado por essa quantidade de tempo. A predefinição **eventos que se atrasam** valor é de 5 segundos. Deve esperar para ver algum atraso ao enviar eventos de entrada um por vez, por exemplo. Podem obter piores, os atrasos quando definir **eventos que se atrasam** janela para um grande valor.

5. **Timestamp** valor é diferente a partir do momento no **hora do evento** campo.

   Conforme descrito anteriormente, o sistema ajusta a hora do evento pela tolerância de fora de ordem ou o windows de tolerância de chegada tardia. O **Timestamp** valor do evento é ajustada, mas não a **hora do evento** campo.

## <a name="metrics-to-observe"></a>Métricas para observar

Pode observar os efeitos de tolerância de tempo por meio de ordenação de eventos diversas [métricas de tarefa do Stream Analytics](stream-analytics-monitoring.md). As métricas seguintes são relevantes:

|Métrica  | Descrição  |
|---------|---------|
| **Eventos de fora de ordem** | Indica o número de eventos recebidos fora de ordem, que foram removidos ou tendo em conta um timestamp ajustado. Esta métrica é diretamente afetada pela configuração do **eventos fora de ordem** definir o **ordenação de eventos** página na tarefa no portal do Azure. |
| **Eventos de entrada atrasados** | Indica o número de eventos que chegam mais tarde a partir da origem. Esta métrica inclui eventos que foram ignorados ou tinham seus timestamp foi ajustado. Esta métrica é diretamente afetada pela configuração dos **eventos que se atrasam** definição **ordenação de eventos** página na tarefa no portal do Azure. |
| **Eventos de entrada antigos** | Indica o número de eventos que chegam no início da origem de que tem um foram removido ou seus timestamp foi ajustado que estejam para além de 5 minutos desde o início. |
| **Atraso de marca d'água** | Indica o atraso da tarefa de processamento de dados de transmissão em fluxo. Ver mais informações na secção seguinte.|

## <a name="watermark-delay-details"></a>Detalhes de atraso de marca d'água

O **atraso de marca d'água** métrica é computada como o tempo de relógio do nó de processamento menos o limite de tamanho maior, ele é visto como até agora. Para obter mais informações, consulte a [mensagem de blogue de atraso de marca d'água](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/).

Pode haver vários motivos, que este valor de métrica é maior que 0 sob operação normal:

1. Atraso de processamento inerente de pipeline de transmissão em fluxo. Normalmente, este atraso é nominal.

2. A janela de tolerância de fora de ordem introduziu o atraso, porque o limite de tamanho é reduzido pelo tamanho da janela de tolerância.

3. A janela de chegada tardia introduziu o atraso, porque o limite de tamanho é reduzido pelo tamanho da janela de tolerância.

4. Relógio distorção do nó de processamento de gerar a métrica.

Existem várias outras restrições de recurso que pode fazer com que o pipeline de transmissão em fluxo abrandamento. A métrica de atraso de marca d'água possam superar devido a:

1. Sem processamento recursos suficientes no Stream Analytics para processar o volume de eventos de entrada. Para aumentar verticalmente recursos, consulte [compreender e ajustar as unidades transmissão em fluxo](stream-analytics-streaming-unit-consumption.md).

2. Insuficiente débito dentro de mediadores de eventos de entrada, para que eles são limitados. Para encontrar possíveis soluções, veja [Dimensionar automaticamente unidades de débito dos Hubs de eventos do Azure](../event-hubs/event-hubs-auto-inflate.md).

3. Sinks de saída não são aprovisionadas com capacidade suficiente para que eles são limitados. Possíveis soluções variam bastante consoante o tipo de serviço de saída que está a ser utilizado.

## <a name="output-event-frequency"></a>Frequência de eventos de saída

O Azure Stream Analytics utiliza o progresso de marca d'água como o acionador só para produzir eventos de saída. Como a marca d'água é derivada de dados de entrada, é passível de repetição durante a recuperação de falhas e também reprocessamento iniciada pelo utilizador.

Ao usar [agregados em janelas](stream-analytics-window-functions.md), o serviço produz apenas saídas no final do windows. Em alguns casos, os usuários podem querer ver agregações parciais geradas a partir do windows. Agregações parciais não são atualmente suportadas no Azure Stream Analytics.

Em outras soluções de transmissão em fluxo, os eventos de saída podem ser materializados em vários pontos de Acionador, dependendo das circunstâncias externos. É possível em algumas soluções que os eventos de saída para uma janela de tempo especificado foi possível gerar várias vezes. Como os valores de entrada estão refinados, os resultados de agregação tornam-se mais precisos. Eventos poderiam ser speculated na primeira e revisado ao longo do tempo. Por exemplo, quando um determinado dispositivo está offline a partir da rede, um valor previsto poderia ser usado por um sistema. Mais tarde em diante, o mesmo dispositivo fica online para a rede. Em seguida, os dados do evento real poderiam estar incluídos no fluxo de entrada. Os resultados da saída da janela de tempo de processamento produz um resultado mais preciso.

## <a name="illustrated-example-of-watermarks"></a>Exemplo ilustrado de marcas d'água

As imagens seguintes mostram como marcas d'água progride em diferentes circunstâncias.

Esta tabela mostra os dados de exemplo que são apresentados abaixo. Tenha em atenção que a hora do evento e o tempo de chegada variam, às vezes, a correspondência e, às vezes, não.

| Hora do evento | Hora da chegada | DeviceId |
| --- | --- | --- |
| 12:07 | 12:07 | device1
| 12:08 | 12:08 | device2
| 12:17 | 12:11 | device1
| 12:08 | 12:13 | device3
| 12:19 | 12:16 | device1
| 12:12 | 12:17 | device3
| 12:17 | 12:18 | device2
| 12:20 | 12:19 | device2
| 12:16 | 12:21 | device3
| 12:23 | 12:22 | device2
| 12:22 | 12:24 | device2
| 12:21 | 12:27 | device3

Nesta ilustração, são utilizadas rigorosas serão as tolerâncias seguintes:

- Windows de chegada antecipado é de 5 minutos
- Janela que são recebida tardia é de 5 minutos
- Janela de reordenação é 2 minutos

1. Ilustração da marca d'água a ser processada por meio desses eventos:

   ![Ilustração de marca d'água do Stream Analytics do Azure](media/stream-analytics-time-handling/WaterMark-graph-1.png)

   Processos notáveis ilustrados no gráfico anterior:

   1. O primeiro evento (device1) e o segundo evento (device2) tempos alinhado e são processados sem ajustes. O andamento da marca d'água em cada evento.

   2. Quando o evento de terceiro (device1) é processado, o tempo de chegada (12:11) precede a hora do evento (12:17). O evento chegado 6 minutos desde o início, para que o evento é removido devido à tolerância de chegada antecipada de 5 minutos.

      A marca d'água não progride no caso de um evento inicial.

   3. O evento quarto (device3) e o quinto eventos (device1) tempos alinhado e são processados sem ajuste. O andamento da marca d'água em cada evento.

   4. Quando o sexto evento (device3) é processado, o tempo de chegada (12:17) e a hora do evento (12:12) está abaixo do nível de marca d'água. A hora do evento é ajustada para o nível de marca de água (12:17).

   5. Quando o evento nono (device3) é processado, o tempo de chegada (12:27) é 6 minutos antes do tempo do evento (12:21). A política de chegada tardia é aplicada. A hora do evento é ajustada (12:22), que se encontra acima do limite de tamanho (12:21) assim, nenhuma ajuste adicional é aplicada.

2. Segundo ilustração da marca d'água a progredir sem uma política de chegada iniciais:

   ![O Azure Stream Analytics não antecipada ilustração de limite de tamanho de política](media/stream-analytics-time-handling/watermark-graph-2.png)

   Neste exemplo, não é aplicada nenhuma política de chegada antecipada. Eventos de exceção que chegam numa fase inicial aumentar significativamente a marca d'água. Observe o terceiro evento (deviceId1 na hora 12:11) não é enviada para este cenário e a marca d'água é gerada para 12:15. A hora do evento quarta é ajustados reencaminhar 7 minutos (12:08 a 12:15) como resultado.

3. Na ilustração final, subfluxos são utilizados (sobre o ID do dispositivo). As várias marcas d'água são registadas, um por fluxo. Existem menos eventos com os tempos de ajustado assim.

   ![O Azure Stream Analytics substreams ilustração de marca d'água](media/stream-analytics-time-handling/watermark-graph-3.png)

## <a name="next-steps"></a>Passos Seguintes

- [Considerações de ordem de eventos do Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md)
- [Métricas de tarefa do Stream Analytics](stream-analytics-monitoring.md)
