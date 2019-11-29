---
title: Processar fluxos de dados IoT em tempo real com Azure Stream Analytics
description: Etiquetas do sensor da IoT e transmissão de dados com análises de transmissão e processamento de dados em tempo real
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: 1cc9c6dbb700664e732a67245563e9a211456767
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559779"
---
# <a name="process-real-time-iot-data-streams-with-azure-stream-analytics"></a>Processar fluxos de dados IoT em tempo real com Azure Stream Analytics

Neste artigo, você aprenderá a criar uma lógica de processamento de fluxo para coletar dados de dispositivos Internet das Coisas (IoT). Você usa um caso de uso de IoT (Internet das Coisas do mundo real) para demonstrar como criar sua solução de forma rápida e econômica.

## <a name="prerequisites"></a>Pré-requisitos

* Crie uma [assinatura gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Baixe a consulta de exemplo e os arquivos de dados do [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot).

## <a name="scenario"></a>Cenário

A Contoso, uma empresa de automatização industrial, automatizou por completo o processo de fabrico. Nesta fábrica, as máquinas têm sensores que emitem transmissões de dados em tempo real. Neste cenário, um gestor do piso de produção pretende ter conhecimentos aprofundados e em tempo real sobre os sensores de dados e tomar medidas relativamente aos mesmos. Você pode usar a linguagem de consulta do Stream Analytics (SAQL) sobre os dados do sensor para encontrar padrões interessantes do fluxo de entrada de dados.

Neste exemplo, os dados são gerados de um dispositivo de marca de sensor Texas Instruments. O payload dos dados está no formato JSON e tem o seguinte aspeto:

```json
{
    "time": "2016-01-26T20:47:53.0000000",  
    "dspl": "sensorE",  
    "temp": 123,  
    "hmdt": 34  
}  
```

Num cenário real, pode ter centenas destes sensores a gerar eventos como uma transmissão. O ideal seria ter um dispositivo de gateway a executar código para enviar estes eventos para os [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) ou [Hubs IoT do Azure](https://azure.microsoft.com/services/iot-hub/). A tarefa de Stream Analytics seria ingerir estes eventos a partir de Hubs de Eventos e executar consultas de análise em tempo real em relação aos fluxos. Em seguida, pode enviar os resultados para uma das [saídas suportadas](stream-analytics-define-outputs.md).

Para facilitar a utilização, este guia de introdução fornece um ficheiro de dados de exemplo, que foi capturado a partir de dispositivos Sensor Tag reais. Pode executar consultas nos dados de exemplo e ver os resultados. Nos tutoriais subsequentes, ficará a saber como ligar a tarefa às entradas e saídas e como as implementar no serviço do Azure.

## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics

1. Na [portal do Azure](https://portal.azure.com), selecione **+ criar um recurso** no menu de navegação à esquerda. Em seguida, selecione **Stream Analytics trabalho** do **Analytics**.
   
    ![Criar uma nova tarefa do Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

1. Introduza um nome exclusivo de tarefa e verifique se a subscrição é a correta para a tarefa. Crie um novo grupo de recursos ou selecione um existente de sua assinatura.

1. Selecione um local para seu trabalho. Use o mesmo local para o grupo de recursos e todos os recursos para aumentar a velocidade de processamento e reduzir os custos. Depois de fazer as configurações, selecione **criar**.
   
    ![Detalhes para criar uma nova tarefa do Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

## <a name="create-an-azure-stream-analytics-query"></a>Criar uma consulta do Azure Stream Analytics
A próxima etapa após a criação do trabalho é escrever uma consulta. Você pode testar consultas em dados de exemplo sem conectar uma entrada ou saída ao seu trabalho.

Baixe o [HelloWorldASA-InputStream. JSON](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json
) do github. Em seguida, navegue até seu trabalho de Azure Stream Analytics no portal do Azure.

Selecione **consulta** em **topologia do trabalho** no menu à esquerda. Em seguida, selecione **carregar entrada de exemplo**. Carregue o arquivo de `HelloWorldASA-InputStream.json` e selecione **OK**.

![Bloco de consulta do painel de Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

Observe que uma visualização dos dados é preenchida automaticamente na tabela de **visualização de entrada** .

![Visualização de dados de entrada de exemplo](./media/stream-analytics-get-started-with-iot-devices/input-preview.png)

### <a name="query-archive-your-raw-data"></a>Consulta: Arquivar os dados não processados

A forma mais simples de consulta é uma consulta pass-through que arquiva todos os dados de entrada no resultado designado. Essa consulta é a consulta padrão populada em um novo trabalho de Azure Stream Analytics.

```sql
SELECT 
    *
INTO
    Output
FROM
    InputStream
```

Selecione **testar consulta** e exibir os resultados na tabela de **resultados de teste** .

![Resultados de teste para consulta de Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Consulta: Filtrar os dados com base numa condição

Vamos tentar filtrar os resultados com base em uma condição. Gostaríamos de mostrar os resultados somente para os eventos que vêm do "sensora".

```sql
SELECT 
    time,
    dspl AS SensorName,
    temp AS Temperature,
    hmdt AS Humidity
INTO
   Output
FROM
    InputStream
WHERE dspl='sensorA'
```

Cole a consulta no editor e selecione **testar consulta** para examinar os resultados.

![Filtragem de um fluxo de dados](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Consulta: Alerta para acionar um fluxo de trabalho comercial

Vamos tornar a nossa consulta mais detalhada. Para cada tipo de sensor, queremos monitorizar a temperatura média por cada janela de 30 segundos e apresentar os resultados apenas se a temperatura média for superior a 100 graus.

```sql
SELECT 
    System.Timestamp AS OutputTime,
    dspl AS SensorName,
    Avg(temp) AS AvgTemperature
INTO
   Output
FROM
    InputStream TIMESTAMP BY time
GROUP BY TumblingWindow(second,30),dspl
HAVING Avg(temp)>100
```

![Consulta do filtro de 30 segundos](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Você deve ver os resultados que contêm apenas 245 linhas e nomes de sensores em que a média de temperatura é maior que 100. Esta consulta agrupa a transmissão de eventos por **dspl**, que é o nome do sensor, e coloca-a numa **Janela em Cascata** de 30 segundos. As consultas temporais devem indicar como você deseja o tempo de andamento. Usando a cláusula **timestamp by** , você especificou a coluna **OutputType** para associar tempos a todos os cálculos temporais. Para obter informações detalhadas, leia sobre o [Gerenciamento de tempo](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics) e as [funções de janela](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics).

### <a name="query-detect-absence-of-events"></a>Consulta: Detetar a ausência de eventos

Como podemos escrever uma consulta para encontrar uma falta de eventos de entrada? Vamos encontrar a última vez que um sensor enviou dados e, em seguida, não enviou eventos para os próximos 5 segundos.

```sql
SELECT 
    t1.time,
    t1.dspl AS SensorName
INTO
   Output
FROM
    InputStream t1 TIMESTAMP BY time
LEFT OUTER JOIN InputStream t2 TIMESTAMP BY time
ON
    t1.dspl=t2.dspl AND
    DATEDIFF(second,t1,t2) BETWEEN 1 and 5
WHERE t2.dspl IS NULL
```

![Detetar a ausência de eventos](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

Aqui, vamos utilizar uma associação **EXTERNA** para a mesma transmissão de dados (associação automática). Para uma associação **NTERNA**, só é apresentado um resultado quando é encontrada uma correspondência.  Numa associação **EXTERNA**, se um evento externo à associação não tiver qualquer correspondência, será apresentada a linha NULO para todas as colunas à direita. Esta técnica é muito útil para localizar a ausência de eventos. Para obter mais informações, consulte [Join](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics).

## <a name="conclusion"></a>Conclusão

A finalidade deste artigo é demonstrar como escrever diferentes consultas de linguagem de consulta Stream Analytics e ver os resultados no navegador. No entanto, isso é apenas para começar. O Stream Analytics suporta uma variedade de entradas e saídas e pode mesmo tirar partido de funções no Azure Machine Learning para o transformar numa ferramenta robusta para analisar fluxos de dados. Para obter mais informações sobre como escrever consultas, leia o artigo sobre [padrões de consulta comuns](stream-analytics-stream-analytics-query-patterns.md).

