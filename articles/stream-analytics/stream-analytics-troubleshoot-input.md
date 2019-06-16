---
title: Entradas de resolução de problemas para o Azure Stream Analytics
description: Este artigo descreve técnicas para resolver problemas de suas ligações de entrada em tarefas do Azure Stream Analytics.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: b5ed614fdd378b36d8f95fc90ce7ff98d63ef31a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60761921"
---
# <a name="troubleshoot-input-connections"></a>Resolver problemas de ligações de entrada

Esta página descreve problemas comuns com ligações de entrada e como resolvê-los.

## <a name="input-events-not-received-by-job"></a>Eventos de entrada não recebidos por tarefa 
1.  Teste a conectividade. Verifique a conectividade com entradas e saídas ao utilizar o **Testar ligação** botão para cada entrada e saída.

2.  Examine os dados de entrada.

    Para verificar que os dados de entrada é fluir para o Hub de eventos, utilize [Explorador do Service Bus](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) para ligar ao Hub de eventos do Azure (se for utilizada a entrada do Hub de eventos).
        
    Utilize o [ **dados de exemplo** ](stream-analytics-sample-data-input.md) botão para cada entrada e transferir os dados de exemplo de entrada.
        
    Inspecionar os dados de exemplo para compreender a forma dos dados: o esquema e o [tipos de dados](https://msdn.microsoft.com/library/azure/dn835065.aspx).

## <a name="malformed-input-events-causes-deserialization-errors"></a>Erros de desserialização de causas de eventos de entrada com formato incorreto 
Desserialização problemas causados quando o fluxo de entrada da sua tarefa do Stream Analytics contém mensagens com formato incorreto. Por exemplo, pode dever-se uma mensagem incorretamente formada por um parêntesis de em falta ou uma chave num objeto JSON ou tem um formato de timestamp incorreto no campo de tempo. 
 
Quando uma tarefa do Stream Analytics recebe uma mensagem incorretamente formada de entrada, ele ignora a mensagem e notifica-o com um aviso. Um símbolo de aviso é apresentado no **entradas** mosaico da sua tarefa do Stream Analytics. Este início de sessão de aviso existe, desde que a tarefa está no estado de execução:

![Mosaico de entradas de Stream Analytics do Azure](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Ative os registos de diagnóstico para ver os detalhes do aviso. Para eventos de entrada com formato incorreto, os registos de execução contém uma entrada com a mensagem que é semelhante a: 
```
Could not deserialize the input event(s) from resource <blob URI> as json.
```

### <a name="what-caused-the-deserialization-error"></a>O que causou o erro de desserialização
Pode realizar os passos seguintes para analisar os eventos de entrada em detalhes para obter uma compreensão clara sobre o que causou o erro de desserialização. Em seguida, pode corrigir a origem do evento para gerar eventos no formato correto para o impedir de acessar novamente este problema.

1. Navegue para o mosaico de entrada e clique nos símbolos de aviso para ver a lista de problemas.

2. O mosaico de detalhes de entrada apresenta uma lista de avisos com detalhes sobre cada problema. A mensagem de aviso de exemplo abaixo inclui a partição, o deslocamento e a números de sequência em que há dados JSON com formato incorreto. 

   ![Mensagem de aviso do Stream Analytics com o deslocamento](media/stream-analytics-malformed-events/warning-message-with-offset.png)
   
3. Para encontrar os dados JSON com formato incorreto, execute o código de CheckMalformedEvents.cs disponível na [repositório de exemplos do GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Este código leituras o ID de partição, deslocamento e imprime os dados que estão localizados no deslocamento. 

4. Depois de ler os dados, pode analisar e corrigir o formato de serialização.

5. Também pode [ler eventos a partir de um IoT Hub com o Explorador do Service Bus](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b).

## <a name="job-exceeds-maximum-event-hub-receivers"></a>Tarefa excede o máximo Recetores do Hub de eventos
Uma melhor prática para utilizar os Hubs de eventos é usar vários grupos de consumidores para assegurar a escalabilidade de tarefa. O número de leitores da tarefa do Stream Analytics para uma introdução específica afeta o número de leitores num grupo de consumidor. O número exato de recetores baseia-se nos detalhes da implementação interna para a lógica de topologia de escalamento horizontal e não está exposto externamente. O número de leitores pode alterar quando é iniciada uma tarefa ou durante atualizações de tarefa.

O erro mostrado quando o número de destinatários excede o máximo é: `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Quando o número de leitores são alterados durante uma atualização da tarefa, transitórios avisos são escritos para registos de auditoria. Tarefas do Stream Analytics recuperar automaticamente a partir desses problemas transitórios.

### <a name="add-a-consumer-group-in-event-hubs"></a>Adicionar um grupo de consumidores em Hubs de eventos
Para adicionar um novo grupo de consumidores na sua instância de Hubs de eventos, siga estes passos:

1. Inicie sessão no Portal do Azure.

2. Localize os Hubs de eventos.

3. Selecione **os Hubs de eventos** sob a **entidades** cabeçalho.

4. Selecione o Hub de eventos por nome.

5. Sobre o **instância de Hubs de eventos** página, no **entidades** título, selecione **grupos de consumidores**. Um grupo de consumidores com o nome **$Default** está listado.

6. Selecione **+ grupo de consumidores** para adicionar um novo grupo de consumidor. 

   ![Adicionar um grupo de consumidores em Hubs de eventos](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Quando criou a entrada da tarefa do Stream Analytics para apontar para o Hub de eventos, que especificou existir o grupo de consumidores. $Default é utilizado quando não for especificado nenhum. Depois de criar um novo grupo de consumidores, edite a entrada do Hub de eventos da tarefa do Stream Analytics e especifique o nome do novo grupo de consumidor.


## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Os leitores por partição excede o limite de Hubs de eventos

Se a sintaxe de consulta de transmissão em fluxo referencia o mesmo recurso de Hub de eventos de entrada várias vezes, o mecanismo de trabalho pode utilizar vários leitores por consulta desse mesmo grupo de consumidor. Quando existem demasiadas referências para o mesmo grupo de consumidores, a tarefa pode exceder o limite de cinco e lançada um erro. Nessas circunstâncias, pode dividir ainda mais usando várias entradas em vários grupos de consumidores com a solução descrita na secção seguinte. 

Cenários em que o número de leitores por partição excede o limite de Hubs de eventos de cinco incluem o seguinte:

* Múltiplas instruções SELECIONADAS: Se utilizar várias instruções SELECT que fazem referência a **mesmo** hub de eventos de entrada, cada instrução SELECIONADA faz com que um novo recetor a ser criada.
* UNIÃO: Quando utiliza uma União, é possível ter várias entradas que consulte o **mesmo** grupo de hub e o consumidor de eventos.
* ASSOCIAÇÃO AUTOMÁTICA: Quando utiliza uma operação de associação de SELF, é possível fazer referência a **mesmo** hub de eventos várias vezes.

As seguintes práticas recomendadas podem ajudar a atenuar os cenários em que o número de leitores por partição excede o limite de Hubs de eventos de cinco.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Dividir a consulta em vários passos ao utilizar uma cláusula WITH

A cláusula WITH Especifica um conjunto de resultados de com nome temporário que pode ser referenciado por uma cláusula FROM na consulta. Definir a cláusula WITH no âmbito da execução de uma única instrução SELECT.

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

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Certifique-se de que as entradas de ligam para grupos de consumidores diferentes

Para consultas em que os três ou mais entradas estão ligadas ao mesmo grupo de consumidores de Hubs de eventos, crie grupos de consumidores separado. Isto requer a criação de entradas adicionais do Stream Analytics.

## <a name="get-help"></a>Obter ajuda

Para obter assistência, tente nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos Seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
