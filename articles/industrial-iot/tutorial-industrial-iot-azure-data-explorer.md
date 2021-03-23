---
title: Puxe os dados de IoT Industrial Azure para o ADX
description: Neste tutorial, aprende-se a puxar dados IIoT para o ADX.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 4c344dc09ad6c8aa4b2aa431952fc271d946b60d
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787774"
---
# <a name="tutorial-pull-azure-industrial-iot-data-into-adx"></a>Tutorial: Puxe os dados do IoT Industrial Azure para o ADX

A Plataforma Azure Industrial IoT (IIoT) combina módulos de borda e microserviços em nuvem com uma série de serviços Azure PaaS para fornecer capacidades para a descoberta de ativos industriais e para recolher dados destes ativos usando o OPC UA. [O Azure Data Explorer (ADX)](https://docs.microsoft.com/azure/data-explorer) é um destino natural para dados IIoT com funcionalidades de análise de dados que permitem executar consultas flexíveis sobre os dados ingeridos dos servidores OPC UA ligados ao Hub IoT através do Editor OPC. Embora um cluster ADX possa ingerir dados diretamente do IoT Hub, a plataforma IIoT faz o processamento adicional dos dados para torná-lo mais útil antes de colocá-lo no Centro de Eventos fornecido quando uma implementação completa dos microserviços é usada (consulte a arquitetura da plataforma IIoT).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma tabela em ADX
> * Ligue o Centro de Eventos ao Cluster ADX
> * Analisar os dados em ADX

## <a name="how-to-make-the-data-available-in-the-adx-cluster-to-query-it-effectively"></a>Como disponibilizar os dados no cluster ADX para o consultar eficazmente 

Se olharmos para o formato de mensagem a partir do Event Hub (tal como definido pela classe Microsoft.Azure.IIoT.OpcUa.Subscriber.Models.MonitoredItemMessageModel), podemos ver uma dica para a estrutura que precisamos para o esquema de tabela ADX.

![Estrutura](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-1.png)

Abaixo estão os passos que precisamos para disponibilizar os dados no cluster ADX e para consultar os dados de forma eficaz.  
1. Crie um cluster ADX. Se ainda não tem um cluster ADX a provisionado com a plataforma IIoT, ou se pretende utilizar um cluster diferente, siga os passos [aqui.](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal#create-a-cluster) 
2. Permitir a ingestão de streaming no cluster ADX, conforme explicado [aqui.](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming#enable-streaming-ingestion-on-your-cluster) 
3. Crie uma base de dados ADX seguindo os passos [aqui.](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal#create-a-database)

Para o passo seguinte, usaremos a [interface web ADX](https://docs.microsoft.com/azure/data-explorer/web-query-data) para executar as consultas necessárias. Certifique-se de adicionar o seu cluster à interface web, conforme explicado no link.  
 
4. Crie uma tabela em ADX para colocar os dados ingeridos.  Embora a classe MonitoredItemMessageModel possa ser usada para definir o esquema da tabela ADX, recomenda-se ingerir os dados primeiro numa tabela de encenação com uma coluna do tipo [Dynamic.](https://docs.microsoft.com/azure/data-explorer/kusto/query/scalar-data-types/dynamic) Isto dá-nos mais flexibilidade no tratamento dos dados e no processamento em outras tabelas (potencialmente combinando-os com outras fontes de dados) que servem as necessidades de múltiplos casos de uso. A seguinte consulta ADX cria a tabela de encenação 'iiot_stage' com uma coluna 'carga útil',

```
.create table ['iiot_stage']  (['payload']:dynamic)
```

Também precisamos adicionar um mapeamento de ingestão json para instruir o cluster para colocar toda a mensagem Json do centro de eventos na mesa de preparação,

```
.create table ['iiot_stage'] ingestion json mapping 'iiot_stage_mapping' '[{"column":"payload","path":"$","datatype":"dynamic"}]'
```

5. A nossa tabela está agora pronta para receber dados do Centro de Eventos. 
6. Use as instruções [aqui](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#connect-to-the-event-hub) para ligar o Centro de Eventos ao cluster ADX e começar a ingerir os dados na nossa tabela de preparação. Só precisamos de criar a ligação, uma vez que já temos um Centro de Eventos a provisionado pela plataforma IIoT.  
7. Assim que a ligação for verificada, os dados começarão a fluir para a nossa tabela e após um curto atraso podemos começar a examinar os dados na nossa tabela. Utilize a seguinte consulta na interface web ADX para ver uma amostra de dados de 10 linhas. Podemos ver aqui como os dados na carga útil se assemelham à classe MonitorItemMEssageModel mencionada anteriormente.

![Consulta](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-2.png)

8. Vamos agora analisar alguns dados analisando diretamente os dados dinâmicos na coluna 'carga útil'. Neste exemplo, calcularemos a média da telemetria identificada pelo "DisplayName": "PositiveTrendData", ao longo de janelas de tempo de 10 minutos, em todos os registos ingeridos desde um ponto de tempo específico (definido pela variável min_t) deixe min_t = data (2020-10-23); iiot_stage | onde o tempo de tempo (carga útil). > min_t | de tempo onde o tostring (carga útil. DisplayName)== 'PositiveTrendData' | resumir event_avg = avg (todouble(carga útil). Valor)) por bin (tempo de pagamento. Timetamp), 10 m)
 
Uma vez que a nossa coluna 'carga útil' contém um tipo de dados dinâmico, precisamos de realizar a conversão de dados no tempo de consulta para que os nossos cálculos sejam realizados sobre os tipos de dados corretos.

![Carga útil Horatampia](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-3.png)

Como mencionamos anteriormente, ingerir os dados da UA da OPC numa tabela de encenação com uma coluna "Dinâmica" dá-nos flexibilidade. No entanto, ter de executar conversões de tipo de dados no tempo de consulta pode resultar em atrasos na execução das consultas, particularmente se o volume de dados é grande e se existem muitas consultas simultâneas. Nesta fase, podemos criar outra tabela com os tipos de dados já determinados, de modo a evitar as conversões de tipo de dados de consulta.
 
9. Criar uma nova tabela para os dados analisados que consiste numa seleção limitada a partir do conteúdo da dinâmica 'carga útil' na tabela de preparação. Note que criamos uma coluna de valor para cada um dos tipos de dados esperados na nossa telemetria.

```
.create table ['iiot_parsed']  
    (['Tag_Timestamp']: datetime ,  
    ['Tag_PublisherId']:string ,  
    ['Tag']:string ,
    ['Tag_Datatype']:string ,  
    ['Tag_NodeId']:string,  
    ['Tag_value_long']:long ,  
    ['Tag_value_double']:double,  
    ['Tag_value_boolean']:bool)
```

10. Crie uma função (ao nível da base de dados) para projetar os dados necessários a partir da tabela de preparação. Aqui selecionamos os itens 'Timestamp', 'PublisherId', 'DisplayName', 'Datatype' e 'NodeId' da coluna 'payload' e projetamos estes como 'Tag_Timestamp', 'Tag_PublisherId', 'Tag', 'Tag_Datatype', 'Tag_NodeId'. O item 'Valor' é projetado como três partes diferentes com base no 'DataType'.

```
.create-or-alter function fn_InflightParseIIoTEvent()
{
iiot_stage
| extend Tag_Timestamp =  todatetime(payload.Timestamp)
| extend Tag_PublisherId = tostring(payload.PublisherId)
| extend Tag = tostring(payload.DisplayName)
| extend Tag_Datatype = tostring(payload.DataType)
| extend Tag_NodeId = tostring(payload.NodeId)
| extend Tag_value_long = case(Tag_Datatype == "Int64", tolong(payload.Value), long(null))
| extend Tag_value_double = case(Tag_Datatype == "Double", todouble(payload.Value), double(null))
| extend Tag_value_boolean = case(Tag_Datatype == "Boolean", tobool(payload.Value), bool(null))
| project Tag_Timestamp, Tag_PublisherId, Tag, Tag_Datatype, Tag_NodeId, Tag_value_long, Tag_value_double, Tag_value_boolean
}
```

Para obter mais informações sobre tipos de dados de mapeamento em ADX, consulte [aqui,](https://docs.microsoft.com/azure/data-explorer/kusto/query/scalar-data-types/dynamic)e para funções em ADX pode começar [aqui](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/stored-functions).
 
11. Aplique a função do passo anterior à tabela parsed utilizando uma política de atualização. A [política de](https://docs.microsoft.com/azure/data-explorer/kusto/management/updatepolicy) atualização instrui o ADX a anexar automaticamente dados a uma tabela-alvo sempre que novos dados são inseridos na tabela de origem, com base numa consulta de transformação que corre nos dados inseridos na tabela de origem. Podemos usar a seguinte consulta para atribuir a tabela parsed como destino e a mesa de palco como a fonte para a política de atualização definida pela função que criamos no passo anterior.

```
.alter table iiot_parsed policy update
@'[{"IsEnabled": true, "Source": "iiot_stage", "Query": "fn_InflightParseIIoTEvent()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
```

Assim que a consulta acima for executada, os dados começarão a fluir e a povoar a tabela de destino 'iiot_parsed'. Podemos olhar para os dados em "iiot_parsed da seguinte forma".

![Tabela de Parsed](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-4.png)

12. Vejamos agora como podemos repetir as análises que fizemos num passo anterior; calcular a média da telemetria identificada por "DisplayName": "PositiveTrendData", ao longo de janelas de tempo de 10 minutos, em todos os registos ingeridos desde um ponto de tempo específico (definido pela variável min_t). Como temos agora os valores da tag 'PositveTrendData' armazenados numa coluna de duplo tipo de dados, esperamos uma melhoria no desempenho da consulta.

![Repita a análise](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-5.png)

13. Comparemos finalmente o desempenho da consulta em ambos os casos. Podemos encontrar o tempo necessário para executar a consulta utilizando as 'Estatísticas' no UI ADX (que pode ser localizado acima dos resultados da consulta).  

![Desempenho de consulta 1](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-6.png)

![Desempenho de consulta 2](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-7.png)

Podemos ver que a consulta que usa a tabela de parsed é aproximadamente duas vezes mais rápida que a da mesa de preparação. Neste exemplo, temos um pequeno conjunto de dados e não existem consultas simultâneas, pelo que o efeito no tempo de execução da consulta não é grande, no entanto, para uma carga de trabalho realista, haveria um grande impacto no desempenho. É por isso que é importante considerar a separação dos diferentes tipos de dados em diferentes colunas.

> [!NOTE] 
> A Política de Atualização funciona apenas com os dados que são ingeridos na tabela de encenação após a criação da apólice e não se aplica a quaisquer dados pré-existentes. Isto tem de ser tomado em consideração quando, por exemplo, temos de alterar a política de atualização. Todos os detalhes podem ser encontrados na documentação do ADX.

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu a alterar os valores padrão da configuração, pode 

> [!div class="nextstepaction"]
> [Configure componentes de IoT industrial](tutorial-configure-industrial-iot-components.md)

> [!div class="nextstepaction"]
> [Visualizar e analisar os dados utilizando insights da Série De Tempo](tutorial-visualize-data-time-series-insights.md)