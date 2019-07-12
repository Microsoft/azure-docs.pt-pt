---
title: Fluxos de dados em tempo real da IoT com o Azure Stream Analytics
description: Etiquetas do sensor da IoT e transmissão de dados com análises de transmissão e processamento de dados em tempo real
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: f6a1d5e5a15a2af7db5b6256a6a0c5f19f0e7cf5
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620977"
---
# <a name="get-started-with-azure-stream-analytics-to-process-data-from-iot-devices"></a>Introdução ao Azure Stream Analytics para o processamento de dados a partir de dispositivos da IoT

Neste tutorial, saiba como criar a lógica de processamento de fluxo para recolher dados de dispositivos Internet das coisas (IoT). Vamos analisar um caso real de utilização da Internet das Coisas (IoT) para mostrar como deve construir a solução rapidamente e de forma económica.

## <a name="prerequisites"></a>Pré-requisitos

* [Subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/)
* A consulta de exemplos e os ficheiros de dados estão disponíveis para transferência em [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)

## <a name="scenario"></a>Cenário

A Contoso, uma empresa de automatização industrial, automatizou por completo o processo de fabrico. Nesta fábrica, as máquinas têm sensores que emitem transmissões de dados em tempo real. Neste cenário, um gestor do piso de produção pretende ter conhecimentos aprofundados e em tempo real sobre os sensores de dados e tomar medidas relativamente aos mesmos. Vamos utilizar a Linguagem SAQL (Stream Analytics Query Language) para os dados do sensor para descobrir padrões interessantes na transmissão de entrada de dados.

Aqui, os dados estão a ser gerados a partir de um dispositivo Sensor Tag da Texas Instruments. O payload dos dados está no formato JSON e tem o seguinte aspeto:

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
1. No [Portal do Azure](https://portal.azure.com), clique no sinal de adição e, em seguida, escreva **STREAM ANALYTICS** na janela de texto à direita. Em seguida, selecione **tarefa do Stream Analytics** na lista de resultados.
   
    ![Criar uma nova tarefa do Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)
2. Introduza um nome exclusivo de tarefa e verifique se a subscrição é a correta para a tarefa. Crie um novo grupo de recursos ou selecione um existente na sua subscrição.
3. Em seguida, selecione uma localização para a sua tarefa. Para acelerar o processamento e a redução de custos na transferência de dados, recomenda-se a seleção da mesma localização do grupo de recursos e da conta de armazenamento pretendida.
   
    ![Detalhes para criar uma nova tarefa do Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)
   
   > [!NOTE]
   > Deve criar esta conta de armazenamento apenas uma vez por região. Este armazenamento será partilhado com todas as tarefas do Stream Analytics que são criadas nessa região.
   > 
   > 
4. Selecione a caixa para colocar a sua tarefa no seu dashboard e, em seguida, clique em **CRIAR**.
   
    ![Tarefa de análise de Stream criação em curso](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03a.png)
5. Deverá ver uma "Implementação iniciada..." apresentada na parte superior direita da janela do browser. Em pouco tempo, será alterada para uma janela completa conforme mostrado abaixo.
   
    ![Implementação do Stream analytics foi concluída com êxito](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03b.png)

## <a name="create-an-azure-stream-analytics-query"></a>Criar uma consulta do Azure Stream Analytics
Depois de a tarefa ser criada, deve abri-la e criar uma consulta. Pode aceder facilmente à sua tarefa ao clicar no mosaico relacionado com a mesma.

![Mosaico de tarefa do Stream Analytics no portal do Azure](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

No painel **Topologia da Tarefa**, clique na caixa **CONSULTA** para aceder ao Editor de Consultas. O separador **CONSULTA** permite-lhe introduzir uma consulta T-SQL que faz a transformação através dos dados de eventos recebidos.

![Mosaico de consulta do Stream Analytics dashboard](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

### <a name="query-archive-your-raw-data"></a>Consulta: Arquivar os dados não processados
A forma mais simples de consulta é uma consulta pass-through que arquiva todos os dados de entrada no resultado designado. Transfira o ficheiro de dados de exemplo do [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot) para uma localização no computador. 

1. Cole a consulta a partir do ficheiro PassThrough.txt. 
   
    ![Cole a consulta no editor de consultas do Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)
2. Clique nos três pontos junto à sua entrada e selecione a caixa **Carregar dados de exemplo do ficheiro**.
   
    ![Escolha os dados de exemplo de carregamento do ficheiro](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06a.png)
3. Por conseguinte, um painel abre-se à direita. No mesmo, selecione o ficheiro de dados HelloWorldASA-InputStream.json a partir da sua localização transferida e clique em **OK** na parte inferior do painel.
   
    ![Carregar ficheiro de dados de exemplo de json](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06b.png)
4. Em seguida, clique no ícone de roda dentada **estar** na área superior esquerda da janela e processe a consulta de teste relativamente ao conjunto de dados de exemplo. Será aberta uma janela de resultados por baixo da sua consulta depois de o processamento estar concluído.
   
    ![Resultados do teste para a consulta do Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Consulta: Filtrar os dados com base numa condição
Vamos tentar filtrar os resultados com base numa condição. Gostaríamos de mostrar os resultados apenas dos eventos provenientes do "sensorA". A consulta está localizada no ficheiro Filtering.txt.

![Filtragem de um fluxo de dados](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

Tenha em atenção que a consulta sensível às maiúsculas e minúsculas compara um valor da cadeia. Clique no ícone de roda dentada **Testar** novamente para executar a consulta. A consulta deverá devolver 389 linhas de 1860 eventos.

![Segundos resultados de saída do teste de consulta](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Consulta: Alerta para acionar um fluxo de trabalho comercial
Vamos tornar a nossa consulta mais detalhada. Para cada tipo de sensor, queremos monitorizar a temperatura média por cada janela de 30 segundos e apresentar os resultados apenas se a temperatura média for superior a 100 graus. Vamos escrever a seguinte consulta e, em seguida, clique em **Testar** para ver os resultados. A consulta está localizada no ficheiro ThresholdAlerting.txt.

![Consulta do filtro de 30 segundos](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Agora, deve ver resultados que contêm apenas 245 linhas e nomes de sensores em que a temperatura média é superior a 100. Esta consulta agrupa a transmissão de eventos por **dspl**, que é o nome do sensor, e coloca-a numa **Janela em Cascata** de 30 segundos. As consultas temporais têm de indicar como queremos que o tempo progrida. Com a cláusula **TIMESTAMP BY**, especificamos a coluna **OUTPUTTIME** para associar as horas a todos os cálculos temporais. Para obter informações detalhadas, leia os artigos MSDN sobre as funções [Time Management (Gestão do Tempo)](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics) e [Windowing (Múltiplas Janelas)](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics).

### <a name="query-detect-absence-of-events"></a>Consulta: Detetar a ausência de eventos
Como podemos escrever uma consulta para encontrar uma falta de eventos de entrada? Vamos descobrir quando foi a última vez que um sensor enviou dados e não enviou eventos durante os cinco minuto seguintes. A consulta está localizada no ficheiro AbsenceOfEvent.txt.

![Detetar a ausência de eventos](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

Aqui, vamos utilizar uma associação **EXTERNA** para a mesma transmissão de dados (associação automática). Para uma associação **NTERNA**, só é apresentado um resultado quando é encontrada uma correspondência.  Numa associação **EXTERNA**, se um evento externo à associação não tiver qualquer correspondência, será apresentada a linha NULO para todas as colunas à direita. Esta técnica é muito útil para localizar a ausência de eventos. Para obter mais informações sobre [JOIN (ASSOCIAÇÃO)](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics), veja a nossa documentação MSDN.

## <a name="conclusion"></a>Conclusão
O objetivo deste tutorial é demonstrar como escrever diferentes consultas de linguagem SAQL (Stream Analytics Query Language) e ver os resultados no browser. No entanto, isto é apenas o início. O Stream Analytics tem muitas outras funcionalidades. O Stream Analytics suporta uma variedade de entradas e saídas e pode mesmo tirar partido de funções no Azure Machine Learning para o transformar numa ferramenta robusta para analisar fluxos de dados. Pode começar a explorar mais sobre o Stream Analytics através do nosso [mapa de aprendizagem](https://docs.microsoft.com/azure/stream-analytics/). Para obter mais informações sobre como escrever consultas, leia o artigo sobre [padrões de consulta comuns](stream-analytics-stream-analytics-query-patterns.md).

