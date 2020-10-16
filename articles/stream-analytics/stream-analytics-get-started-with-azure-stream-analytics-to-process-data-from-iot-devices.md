---
title: Processar fluxos de dados IoT em tempo real com Azure Stream Analytics
description: Etiquetas do sensor da IoT e transmissão de dados com análises de transmissão e processamento de dados em tempo real
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/26/2019
ms.openlocfilehash: a40f92e88d2d8e5ca253446b9c67ad30df538a5b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86043432"
---
# <a name="process-real-time-iot-data-streams-with-azure-stream-analytics"></a>Processar fluxos de dados IoT em tempo real com Azure Stream Analytics

Neste artigo, aprende-se a criar lógica de processamento de streaming para recolher dados a partir de dispositivos da Internet das Coisas (IoT). Você usa um caso de Internet of Things (IoT) no mundo real para demonstrar como construir a sua solução de forma rápida e económica.

## <a name="prerequisites"></a>Pré-requisitos

* Crie uma [subscrição gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Descarregue a consulta de amostras e ficheiros de dados do [GitHub.](https://aka.ms/azure-stream-analytics-get-started-iot)

## <a name="scenario"></a>Cenário

A Contoso, uma empresa de automatização industrial, automatizou por completo o processo de fabrico. Nesta fábrica, as máquinas têm sensores que emitem transmissões de dados em tempo real. Neste cenário, um gestor do piso de produção pretende ter conhecimentos aprofundados e em tempo real sobre os sensores de dados e tomar medidas relativamente aos mesmos. Pode utilizar o Stream Analytics Query Language (SAQL) sobre os dados do sensor para encontrar padrões interessantes a partir do fluxo de dados que chegam.

Neste exemplo, os dados são gerados a partir de um dispositivo de marcação de sensores Texas Instruments. O payload dos dados está no formato JSON e tem o seguinte aspeto:

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

1. No [portal Azure](https://portal.azure.com), selecione **+ Crie um recurso** a partir do menu de navegação à esquerda. Em seguida, selecione **stream analytics job** from **Analytics**.
   
    ![Criar uma nova tarefa do Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

1. Introduza um nome exclusivo de tarefa e verifique se a subscrição é a correta para a tarefa. Crie um novo grupo de recursos ou selecione um existente a partir da sua subscrição.

1. Selecione um local para o seu trabalho. Utilize a mesma localização para o seu grupo de recursos e todos os recursos para aumentar a velocidade de processamento e reduzir os custos. Depois de escoar as configurações, **selecione Criar**.
   
    ![Detalhes para criar uma nova tarefa do Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

## <a name="create-an-azure-stream-analytics-query"></a>Criar uma consulta do Azure Stream Analytics
O próximo passo após a criação do seu trabalho é escrever uma consulta. Pode testar consultas contra dados de amostra sem ligar uma entrada ou saída ao seu trabalho.

Descarregue o [HelloWorldASA-InputStream.jsa](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json
) partir do GitHub. Em seguida, navegue para o seu trabalho Azure Stream Analytics no portal Azure.

Selecione **Consulta** em **Topologia de Trabalho** a partir do menu esquerdo. Em seguida, **selecione Carregar a entrada da amostra**. Faça o upload do `HelloWorldASA-InputStream.json` ficheiro e selecione **Ok**.

![Azulejo de consulta do painel de instrumentos stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

Note que uma pré-visualização dos dados é automaticamente povoada na tabela **de pré-visualização de Entrada.**

![Pré-visualização dos dados de entrada da amostra](./media/stream-analytics-get-started-with-iot-devices/input-preview.png)

### <a name="query-archive-your-raw-data"></a>Consulta: Arquivar os dados não processados

A forma mais simples de consulta é uma consulta pass-through que arquiva todos os dados de entrada no resultado designado. Esta consulta é a consulta predefinida povoada num novo trabalho Azure Stream Analytics.

```sql
SELECT 
    *
INTO
    Output
FROM
    InputStream
```

Selecione **a consulta de teste** e veja os resultados na tabela de resultados do **Teste.**

![Resultados dos testes para consulta stream analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Consulta: Filtrar os dados com base numa condição

Vamos tentar filtrar os resultados com base numa condição. Gostaríamos de mostrar resultados apenas para os eventos que vêm de "sensorA".

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

Cole a consulta no editor e selecione **a consulta de teste** para rever os resultados.

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

Deve ver resultados que contêm apenas 245 linhas e nomes de sensores onde o temperado médio é superior a 100. Esta consulta agrupa a transmissão de eventos por **dspl**, que é o nome do sensor, e coloca-a numa **Janela em Cascata** de 30 segundos. As consultas temporais devem indicar como quer tempo para progredir. Ao utilizar a cláusula **TIMETAMP BY,** especificou a coluna **OUTPUTTIME** para associar os tempos a todos os cálculos temporais. Para obter informações detalhadas, leia sobre funções [de Gestão do Tempo](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics) e [Descamagem](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics).

### <a name="query-detect-absence-of-events"></a>Consulta: Detetar a ausência de eventos

Como podemos escrever uma consulta para encontrar uma falta de eventos de entrada? Vamos descobrir a última vez que um sensor enviou dados e depois não enviou eventos durante os próximos 5 segundos.

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

Aqui, vamos utilizar uma associação **EXTERNA** para a mesma transmissão de dados (associação automática). Para uma associação **NTERNA**, só é apresentado um resultado quando é encontrada uma correspondência.  Numa associação **EXTERNA**, se um evento externo à associação não tiver qualquer correspondência, será apresentada a linha NULO para todas as colunas à direita. Esta técnica é muito útil para localizar a ausência de eventos. Para mais informações, consulte [JOIN.](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics)

## <a name="conclusion"></a>Conclusão

O objetivo deste artigo é demonstrar como escrever diferentes consultas de Stream Analytics Questionry Language e ver resultados no navegador. No entanto, isto é só para começares. O Stream Analytics suporta uma variedade de entradas e saídas e pode mesmo tirar partido de funções no Azure Machine Learning para o transformar numa ferramenta robusta para analisar fluxos de dados. Para obter mais informações sobre como escrever consultas, leia o artigo sobre [padrões de consulta comuns](stream-analytics-stream-analytics-query-patterns.md).

