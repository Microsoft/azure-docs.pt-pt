---
title: Resolução de problemas entradas para Azure Stream Analytics
description: Este artigo descreve técnicas para resolver problemas nas suas ligações de entrada em trabalhos Azure Stream Analytics.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 05/01/2020
ms.custom: seodec18
ms.openlocfilehash: f4f79a28dbe8a49e608ca6fae1781a1e19646619
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87448881"
---
# <a name="troubleshoot-input-connections"></a>Resolver problemas em ligações de entrada

Este artigo descreve problemas comuns com as ligações de entrada do Azure Stream Analytics, como resolver problemas de entrada e como corrigir os problemas. Muitas etapas de resolução de problemas requerem registos de recursos para o seu trabalho stream Analytics. Se não tiver registos de recursos ativados, consulte [o Troubleshoot Azure Stream Analytics utilizando registos de recursos](stream-analytics-job-diagnostic-logs.md).

## <a name="input-events-not-received-by-job"></a>Eventos de entrada não recebidos por trabalho 

1.  Teste a sua conectividade de entrada e saída. Verifique a conectividade com as entradas e saídas utilizando o botão **de Ligação de Teste** para cada entrada e saída.

2.  Examine os seus dados de entrada.

    1. Utilize o botão [**'Dados de Amostra'**](stream-analytics-sample-data-input.md) para cada entrada. Descarregue os dados da amostra de entrada.
        
    1. Inspecione os dados da amostra para compreender os tipos de esquemas e [dados](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics).
    
    1. Consulte as [métricas do Event Hub](../event-hubs/event-hubs-metrics-azure-monitor.md) para garantir que os eventos estão a ser enviados. As métricas de mensagens devem ser superiores a zero se os Centros de Eventos estiverem a receber mensagens.

3.  Certifique-se de que selecionou um intervalo de tempo na pré-visualização da entrada. **Escolha Selecionar o intervalo de tempo**e, em seguida, introduzir uma duração da amostra antes de testar a sua consulta.

## <a name="malformed-input-events-causes-deserialization-errors"></a>Eventos de entrada mal formados provocam erros de desserialização 

Os problemas de deserialização são causados quando o fluxo de entrada do seu trabalho stream Analytics contém mensagens mal formadas. Por exemplo, uma mensagem mal formada pode ser causada por um parênteses desaparecido, ou cinta, num objeto JSON ou num formato de relógio incorreto no campo de tempo. 
 
Quando um trabalho stream Analytics recebe uma mensagem mal formada a partir de uma entrada, deixa cair a mensagem e notifica-o com um aviso. Um símbolo de aviso é mostrado no azulejo **de entradas** do seu trabalho stream Analytics. O seguinte símbolo de aviso existe enquanto o trabalho estiver em funcionamento:

![Azulejo de entrada Azure Stream Analytics](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Ativar os registos de recursos para visualizar os detalhes do erro e a mensagem (carga útil) que causou o erro. Existem várias razões pelas quais podem ocorrer erros de deserialização. Para obter mais informações sobre erros específicos de deserialização, consulte [erros de dados de entrada](data-errors.md#input-data-errors). Se os registos de recursos não estiverem ativados, uma breve notificação estará disponível no portal Azure.

![Informação de aviso de detalhes de entrada](media/stream-analytics-malformed-events/warning-message-with-offset.png)

Nos casos em que a carga útil da mensagem seja superior a 32 KB ou se esteja em formato binário, execute o código de CheckMalformedEvents.cs disponível no [repositório de amostras do GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Este código lê o ID da partição, compensa e imprime os dados que estão localizados nessa offset. 

## <a name="job-exceeds-maximum-event-hub-receivers"></a>Job excede os recetores máximos do Event Hub

Uma das melhores práticas para a utilização de Centros de Eventos é utilizar vários grupos de consumidores para a escalabilidade do trabalho. O número de leitores no trabalho stream Analytics para uma entrada específica afeta o número de leitores num único grupo de consumidores. O número preciso de recetores baseia-se em detalhes de implementação interna para a lógica de topologia de escala e não é exposto externamente. O número de leitores pode mudar quando um trabalho é iniciado ou durante as atualizações de emprego.

As seguintes mensagens de erro são mostradas quando o número de recetores excede o máximo. A mensagem de erro inclui uma lista de ligações existentes feitas ao Event Hub sob um grupo de consumidores. A etiqueta `AzureStreamAnalytics` indica que as ligações são do Serviço de Streaming Azure.

```
The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.

The following information may be helpful in identifying the connected receivers: Exceeded the maximum number of allowed receivers per partition in a consumer group which is 5. List of connected receivers – 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1.
```

> [!NOTE]
> Quando o número de leitores muda durante uma atualização de trabalho, os avisos transitórios são escritos para registos de auditoria. Os trabalhos stream Analytics recuperam automaticamente destes problemas transitórios.

### <a name="add-a-consumer-group-in-event-hubs"></a>Adicione um grupo de consumidores em Event Hubs

Para adicionar um novo grupo de consumidores na sua instância Event Hubs, siga estes passos:

1. Inicie sessão no Portal do Azure.

2. Localize o seu Centro de Eventos.

3. Selecione **Centros de Eventos** sob o título **de Entidades.**

4. Selecione o Centro de Eventos pelo nome.

5. Na página **De Exemplo de Centros de Eventos,** sob a rubrica **Entidades,** selecione **Grupos de Consumidores**. Está listado um grupo de consumidores com **nome $Default.**

6. Selecione **+ Grupo de Consumidores** para adicionar um novo grupo de consumidores. 

   ![Adicione um grupo de consumidores em Event Hubs](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Quando criou a entrada no trabalho stream Analytics para apontar para o Centro de Eventos, especificou o grupo de consumidores lá. **$Default** é utilizado quando nenhum é especificado. Assim que criar um novo grupo de consumidores, edite a entrada do Event Hub no trabalho stream Analytics e especifique o nome do novo grupo de consumidores.

## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Leitores por partição excedem limite de Centros de Eventos

Se a sua consulta de streaming referenciar o mesmo recurso de entrada Event Hub várias vezes, o motor de trabalho pode utilizar vários leitores por consulta a partir desse mesmo grupo de consumidores. Quando há demasiadas referências ao mesmo grupo de consumidores, o trabalho pode exceder o limite de cinco e errar um erro. Nestas circunstâncias, pode dividir-se ainda mais utilizando múltiplas entradas em vários grupos de consumidores utilizando a solução descrita na secção seguinte. 

Os cenários em que o número de leitores por partição excede o limite de cinco Centros de Eventos incluem:

* Várias declarações SELECT: Se utilizar várias declarações SELECT que se referem à **mesma** entrada do centro de eventos, cada declaração SELECT provoca a criação de um novo recetor.

* Quando se utiliza uma UNIÃO, é possível ter múltiplas entradas que se referem ao **mesmo** centro de eventos e ao grupo de consumidores.

* SELF JOIN: Quando utiliza uma operação SELF JOIN, é possível consultar o **mesmo** centro de eventos várias vezes.

As seguintes boas práticas podem ajudar a mitigar cenários em que o número de leitores por partição excede o limite de cinco Centros de Eventos.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Divida a sua consulta em vários passos usando uma cláusula COM

A cláusula WITH especifica um conjunto de resultados nomeado temporário que pode ser referenciado por uma cláusula FROM na consulta. Define a cláusula COM no âmbito de execução de uma única declaração SELECT.

Por exemplo, em vez desta consulta:

```SQL
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Utilize esta consulta:

```SQL
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Certifique-se de que as entradas se ligam a diferentes grupos de consumidores

Para consultas em que três ou mais entradas estão ligadas ao mesmo grupo de consumidores Event Hubs, crie grupos de consumidores separados. Isto requer a criação de entradas adicionais stream analytics.

### <a name="create-separate-inputs-with-different-consumer-groups"></a>Criar entradas separadas com diferentes grupos de consumidores

Pode criar entradas separadas com diferentes grupos de consumidores para o mesmo Event Hub. A seguinte consulta da UE é um exemplo em que *a InputOne* e *a InputTwo* referem-se à mesma fonte do Event Hub. Qualquer consulta pode ter entradas separadas com diferentes grupos de consumidores. A consulta da UE é apenas um exemplo.

```sql
WITH 
DataOne AS 
(
SELECT * FROM InputOne 
),

DataTwo AS 
(
SELECT * FROM InputTwo 
),

SELECT foo FROM DataOne
UNION 
SELECT foo FROM DataTwo

```

## <a name="readers-per-partition-exceeds-iot-hub-limit"></a>Leitores por partição excedem limite do IoT Hub

Os trabalhos de Stream Analytics usam o [ponto de final compatível com o Centro](../iot-hub/iot-hub-devguide-messages-read-builtin.md) de Eventos do IoT Hub para conectar e ler eventos a partir do IoT Hub. Se a sua leitura por partição exceder os limites do IoT Hub, pode utilizar as [soluções para](#readers-per-partition-exceeds-event-hubs-limit) o Event Hub resolvê-lo. Pode criar um grupo de consumidores para o ponto final incorporado através da sessão de ponto final do portal IoT Hub ou através do [IoT Hub SDK](https://docs.microsoft.com/rest/api/iothub/IotHubResource/CreateEventHubConsumerGroup).

## <a name="get-help"></a>Obter ajuda

Para obter mais assistência, experimente o nosso [Microsoft Q&Uma página de perguntas para a Azure Stream Analytics](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
