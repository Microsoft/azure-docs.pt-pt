---
title: Inputs de resolução de problemas para Azure Stream Analytics
description: Este artigo descreve técnicas para resolver problemas as suas ligações de entrada em empregos azure Stream Analytics.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: 3d88123b3dd79e5707c5c19cbbae13c30cbdeb84
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409406"
---
# <a name="troubleshoot-input-connections"></a>Resolver problemas em ligações de entrada

Este artigo descreve questões comuns com as ligações de entrada do Azure Stream Analytics, como resolver problemas de entrada e como corrigir as questões. Muitos passos de resolução de problemas requerem que os registos de diagnóstico sejam ativados para o seu trabalho de Stream Analytics. Se não tiver registos de diagnóstico ativados, consulte [o Troubleshoot Azure Stream Analytics utilizando registos](stream-analytics-job-diagnostic-logs.md)de diagnóstico .

## <a name="input-events-not-received-by-job"></a>Eventos de entrada não recebidos por trabalho 

1.  Teste a sua entrada e conectividade de saída. Verifique a conectividade com as inputs e saídas utilizando o botão **de ligação** de teste para cada entrada e saída.

2.  Examine os seus dados de entrada.

    1. Utilize o botão Dados da [**Amostra**](stream-analytics-sample-data-input.md) para cada entrada. Descarregue os dados da amostra de entrada.
        
    1. Inspecione os dados da amostra para compreender os tipos de esquemas e [dados](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics).
    
    1. Verifique [as métricas do Event Hub](../event-hubs/event-hubs-metrics-azure-monitor.md) para garantir que os eventos estão a ser enviados. As métricas da mensagem devem ser superiores a zero se os Centros de Eventos estiverem a receber mensagens.

3.  Certifique-se de que selecionou um intervalo de tempo na pré-visualização da entrada. Escolha o intervalo de **tempo e**introduza uma duração da amostra antes de testar a sua consulta.

## <a name="malformed-input-events-causes-deserialization-errors"></a>Eventos de entrada mal formados provocam erros de desserialização 

Os problemas de desserialização são causados quando o fluxo de entrada do seu trabalho stream Analytics contém mensagens mal formadas. Por exemplo, uma mensagem mal formada pode ser causada por um parêntese em falta, ou cinta, num objeto JSON ou num formato de carimbo de tempo incorreto no campo de tempo. 
 
Quando um trabalho de Stream Analytics recebe uma mensagem mal formada de uma entrada, deixa cair a mensagem e avisa-o com um aviso. Um símbolo de aviso é mostrado no azulejo de **entrada** do seu trabalho stream analytics. O seguinte símbolo de advertência existe enquanto o trabalho estiver em estado de funcionamento:

![Azure Stream Analytics inputs azulejos](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Ative os registos de diagnóstico para visualizar os detalhes do erro e da mensagem (carga útil) que causou o erro. Existem múltiplas razões pelas quais podem ocorrer erros de desserialização. Para obter mais informações sobre erros específicos de desserialização, consulte erros de [dados de entrada](data-errors.md#input-data-errors). Se os registos de diagnóstico não estiverem ativados, uma breve notificação estará disponível no portal Azure.

![Informação detalhes notificação de aviso](media/stream-analytics-malformed-events/warning-message-with-offset.png)

Nos casos em que a carga útil da mensagem seja superior a 32 KB ou esteja em formato binário, execute o código CheckMalformedEvents.cs disponível no [repositório de amostras GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Este código lê o ID da partição, offset, e imprime os dados que estão localizados nessa compensação. 

## <a name="job-exceeds-maximum-event-hub-receivers"></a>Trabalho excede recetores máximos do Event Hub

A melhor prática para usar os Hubs de Eventos é utilizar vários grupos de consumidores para escalabilidade de emprego. O número de leitores no trabalho do Stream Analytics para uma entrada específica afeta o número de leitores num único grupo de consumidores. O número preciso de recetores baseia-se em detalhes de implementação interna para a lógica de topologia de escala e não é exposto externamente. O número de leitores pode mudar quando um trabalho é iniciado ou durante as atualizações de trabalho.

O erro mostrado quando o número de recetores excede o máximo é 

`The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Quando o número de leitores muda durante uma atualização de trabalho, os avisos transitórios são escritos para registos de auditoria. Os trabalhos da Stream Analytics recuperam-se automaticamente destas questões transitórias.

### <a name="add-a-consumer-group-in-event-hubs"></a>Adicione um grupo de consumidores em Centros de Eventos

Para adicionar um novo grupo de consumidores no seu caso De Event Hubs, siga estes passos:

1. Inicie sessão no Portal do Azure.

2. Localize o seu Centro de Eventos.

3. Selecione Centros de **Eventos** sob a rubrica **Entidades.**

4. Selecione o Centro de Eventos pelo nome.

5. Na página De Exemplo de Hubs de **Eventos,** sob a rubrica **Entidades,** selecione **Grupos de Consumidores**. Está listado um grupo de consumidores com nome **$Default.**

6. Selecione **+ Grupo de Consumidores** para adicionar um novo grupo de consumidores. 

   ![Adicione um grupo de consumidores em Centros de Eventos](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Quando criou a entrada no trabalho stream analytics para apontar para o Event Hub, especificou o grupo de consumidores lá. **$Default** é utilizado quando nenhum é especificado. Assim que criar um novo grupo de consumidores, edite a entrada do Event Hub no trabalho stream analytics e especifique o nome do novo grupo de consumidores.

## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Leitores por partição excedem limite de Centros de Eventos

Se a sua sintaxe de consulta de streaming refere o mesmo recurso de entrada Event Hub várias vezes, o motor de trabalho pode usar vários leitores por consulta desse mesmo grupo de consumidores. Quando há demasiadas referências ao mesmo grupo de consumidores, o trabalho pode ultrapassar o limite de cinco e lançar um erro. Nestas circunstâncias, pode dividir-se ainda mais utilizando várias inputs em vários grupos de consumidores utilizando a solução descrita na secção seguinte. 

Os cenários em que o número de leitores por partição excede o limite de Cinco Centros de Eventos incluem o seguinte:

* Múltiplas declarações SELECT: Se utilizar várias declarações SELECT que se referem à entrada **do mesmo** hub de eventos, cada declaração SELECT faz com que um novo recetor seja criado.

* Quando se usa uma UNIÃO, é possível ter várias inputs que se referem ao **mesmo** centro de eventos e grupo de consumidores.

* SELF JOIN: Quando utilizar uma operação SELF JOIN, é possível referir-se ao **mesmo** centro de eventos várias vezes.

As seguintes boas práticas podem ajudar a mitigar cenários em que o número de leitores por partição excede o limite de Cinco Centros de Eventos.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Divida a sua consulta em vários passos usando uma cláusula WITH

A cláusula COM especifica um conjunto de resultados nomeado temporário que pode ser referenciado por uma cláusula FROM na consulta. Define a cláusula COM no âmbito de execução de uma única declaração SELECT.

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

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Certifique-se de que os inputs se ligam a diferentes grupos de consumidores

Para consultas em que três ou mais entradas estão ligadas ao mesmo grupo de consumidores Event Hubs, crie grupos de consumidores separados. Isto requer a criação de inputs adicionais stream analytics.

## <a name="get-help"></a>Obter ajuda

Para mais assistência, experimente o nosso [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
