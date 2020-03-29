---
title: Inputs de resolução de problemas para Azure Stream Analytics
description: Este artigo descreve técnicas para resolver problemas as suas ligações de entrada em empregos azure Stream Analytics.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: dac3037f82c38980c9ac16685aa7fddac68a2e7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720304"
---
# <a name="troubleshoot-input-connections"></a>Resolver problemas em ligações de entrada

Esta página descreve problemas comuns com ligações de entrada e como resolver problemas.

## <a name="input-events-not-received-by-job"></a>Eventos de entrada não recebidos por trabalho 
1.  Teste a sua conectividade. Verifique a conectividade com as inputs e saídas utilizando o botão **de ligação** de teste para cada entrada e saída.

2.  Examine os seus dados de entrada.

    1. Para verificar se os dados de entrada estão a fluir para o Event Hub, utilize o [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) para se ligar ao Azure Event Hub (se for utilizada a entrada do Event Hub).
        
    1. Utilize o botão Dados da [**Amostra**](stream-analytics-sample-data-input.md) para cada entrada. Descarregue os dados da amostra de entrada.
        
    1. Inspecione os dados da amostra para compreender a forma dos dados- isto é, o esquema e os tipos de [dados](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics).

3.  Certifique-se de que selecionou um intervalo de tempo na pré-visualização da entrada. Escolha o intervalo de **tempo e**introduza uma duração da amostra antes de testar a sua consulta.

## <a name="malformed-input-events-causes-deserialization-errors"></a>Eventos de entrada mal formados provocam erros de desserialização 
Os problemas de desserialização são causados quando o fluxo de entrada do seu trabalho stream Analytics contém mensagens mal formadas. Por exemplo, uma mensagem mal formada pode ser causada por um parêntese em falta ou um suporte num objeto JSON, ou por um formato de carimbo de tempo incorreto no campo de tempo. 
 
Quando um trabalho de Stream Analytics recebe uma mensagem mal formada de uma entrada, deixa cair a mensagem e avisa-o com um aviso. Um símbolo de aviso é mostrado no azulejo de **entrada** do seu trabalho stream analytics. Este sinal de aviso existe enquanto o trabalho estiver em estado de execução:

![Azure Stream Analytics inputs azulejos](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Ative os registos de diagnóstico para ver os detalhes do aviso. Para eventos de entrada mal formados, os registos de execução contêm uma entrada com a mensagem que parece: 
```
Could not deserialize the input event(s) from resource <blob URI> as json.
```

### <a name="what-caused-the-deserialization-error"></a>O que causou o erro de desserialização
Pode tomar os seguintes passos para analisar os eventos de entrada em detalhe para obter uma compreensão clara do que causou o erro de desserialização. Em seguida, pode corrigir a fonte do evento para gerar eventos no formato certo para evitar que volte a atingir este problema.

1. Navegue para o azulejo de entrada e clique nos símbolos de aviso para ver a lista de problemas.

2. Os detalhes da entrada mostram uma lista de avisos com detalhes sobre cada edição. A mensagem de aviso de exemplo abaixo inclui os números de partição, offset e sequência onde há dados JSON mal formados. 

   ![Mensagem de aviso stream Analytics com offset](media/stream-analytics-malformed-events/warning-message-with-offset.png)
   
3. Para encontrar os dados da JSON com o formato incorreto, execute o código CheckMalformedEvents.cs disponível no [repositório de amostras GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Este código lê o ID da partição, offset, e imprime os dados que estão localizados nessa compensação. 

4. Depois de ler os dados, pode analisar e corrigir o formato de serialização.

5. Também pode [ler eventos a partir de um Hub IoT com o Service Bus Explorer.](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b)

## <a name="job-exceeds-maximum-event-hub-receivers"></a>Trabalho excede recetores máximos do Hub de Eventos
A melhor prática para usar os Hubs de Eventos é usar vários grupos de consumidores para garantir a escalabilidade do trabalho. O número de leitores no trabalho do Stream Analytics para uma entrada específica afeta o número de leitores num único grupo de consumidores. O número preciso de recetores baseia-se em detalhes de implementação interna para a lógica de topologia de escala e não é exposto externamente. O número de leitores pode mudar quando um trabalho é iniciado ou durante as atualizações de trabalho.

O erro mostrado quando o número de recetores excede o máximo é `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Quando o número de leitores muda durante uma atualização de trabalho, os avisos transitórios são escritos para registos de auditoria. Os trabalhos da Stream Analytics recuperam-se automaticamente destas questões transitórias.

### <a name="add-a-consumer-group-in-event-hubs"></a>Adicione um grupo de consumidores em Centros de Eventos
Para adicionar um novo grupo de consumidores no seu caso De Event Hubs, siga estes passos:

1. Inicie sessão no Portal do Azure.

2. Localize os seus Centros de Eventos.

3. Selecione Centros de **Eventos** sob a rubrica **Entidades.**

4. Selecione o Centro de Eventos pelo nome.

5. Na página De Exemplo de Hubs de **Eventos,** sob a rubrica **Entidades,** selecione **Grupos de Consumidores**. Está listado um grupo de consumidores com nome **$Default.**

6. Selecione **+ Grupo de Consumidores** para adicionar um novo grupo de consumidores. 

   ![Adicione um grupo de consumidores em Centros de Eventos](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Quando criou a entrada no trabalho stream analytics para apontar para o Event Hub, especificou o grupo de consumidores lá. $Default é utilizado quando nenhum é especificado. Assim que criar um novo grupo de consumidores, edite a entrada do Event Hub no trabalho stream analytics e especifique o nome do novo grupo de consumidores.


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
