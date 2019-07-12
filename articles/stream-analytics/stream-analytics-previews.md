---
title: Funcionalidades de pré-visualização do Azure Stream Analytics
description: Este artigo lista as funcionalidades do Azure Stream Analytics que estão atualmente em pré-visualização.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 587304968cdf3a3763e47b9f8b614fe67aebf534
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798040"
---
# <a name="azure-stream-analytics-preview-features"></a>Funcionalidades de pré-visualização do Azure Stream Analytics

Este artigo resume todas as funcionalidades atualmente em pré-visualização do Azure Stream Analytics. Não é recomendado a utilizar funcionalidades de pré-visualização num ambiente de produção.

## <a name="public-previews"></a>Pré-visualizações públicas

As seguintes funcionalidades estão em pré-visualização pública. Pode aproveitar esses recursos hoje, mas não usá-los no seu ambiente de produção.

### <a name="one-click-integration-with-event-hubs"></a>Integração de um clique com o Hubs de eventos 
Com esta integração, agora poderá visualizar os dados recebidos e começar a escrever uma consulta do Stream Analytics com um clique a partir do portal do Hub de eventos. Assim que a consulta estiver pronta, poderá productize-lo em apenas alguns cliques e começar a obter informações em tempo real. Isso reduzirá significativamente o tempo e o custo de desenvolvimento de soluções de análise em tempo real. A documentação está disponível [aqui](https://docs.microsoft.com/azure/event-hubs/process-data-azure-stream-analytics).

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code para o Azure Stream Analytics

Tarefas do Azure Stream Analytics podem ser criadas no Visual Studio Code. Consulte nossos [VS Code tutorial de introdução](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).

### <a name="anomaly-detection"></a>Deteção de Anomalias

O Azure Stream Analytics introduz novos modelos de aprendizagem automática com suporte para *pico* e *quedas* deteção, além de deteção, bidirecional, lento positivos e lenta tendências negativas. Para obter mais informações, visite [deteção de anomalias no Azure Stream Analytics](stream-analytics-machine-learning-anomaly-detection.md).

### <a name="integration-with-azure-machine-learning"></a>Integração com o Azure Machine Learning

Pode dimensionar tarefas do Stream Analytics com as funções de Machine Learning (ML). Para saber mais sobre como pode usar as funções de ML na sua tarefa do Stream Analytics, visite [dimensionar a sua tarefa do Stream Analytics com as funções do Azure Machine Learning](stream-analytics-scale-with-machine-learning-functions.md). Confira um cenário do mundo real com [executar análise de sentimentos com o Azure Stream Analytics e o Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

### <a name="javascript-user-defined-aggregate"></a>Agregado definido pelo utilizador do JavaScript

O Azure Stream Analytics suporta definidas pelo utilizador agregações existentes (UDA) escritos em JavaScript, que permitem que implemente a lógica de negócio complexa de monitorização de estado. Saiba como utilizar os UDAs do [agregações definidas pelo utilizador do JavaScript do Azure Stream Analytics](stream-analytics-javascript-user-defined-aggregates.md) documentação. 

### <a name="live-data-testing-in-visual-studio"></a>Live o teste de dados no Visual Studio

O Visual Studio tools para o Azure Stream Analytics aprimore a funcionalidade de teste local que permite que teste consultas em fluxos de eventos em direto de origens na cloud, como o Hub de eventos ou IoT hub. Saiba como [testar dados dinâmicos localmente com ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-live-data-local-testing.md).

### <a name="net-user-defined-functions-on-iot-edge"></a>Funções definidas pelo utilizador do .NET no IoT Edge

Com o .NET padrão definido pelo utilizador funções, pode executar código padrão do .NET como parte do seu pipeline de transmissão em fluxo. Pode criar classes simples c# ou importar projeto completo e bibliotecas. Criação e a experiência de depuração completa é suportada no Visual Studio. Para obter mais informações, visite [desenvolver .NET Standard funções definidas pelo utilizador para tarefas do Edge do Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Outras visualizações

Os seguintes recursos também estão disponíveis em pré-visualização no pedido.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge-and-cloud"></a>C#desserializador personalizado para o Azure Stream Analytics no IoT Edge e na Cloud

Os desenvolvedores podem implementar desserializadores personalizados no C# para anular a serialização de eventos recebidos pelo Azure Stream Analytics. Exemplos de formatos que podem ser desserializados incluem Parquet, Protobuf, XML ou qualquer formato binário. Inscreva-se para esta pré-visualização [aqui](https://aka.ms/asapreview1).

### <a name="support-for-azure-stack"></a>Suporte para o Azure Stack
Esta funcionalidade ativada, o tempo de execução do Azure IoT Edge, tira partido de recursos personalizados do Azure Stack, como o suporte nativo de entradas de dados locais e produz em execução no Azure Stack (por exemplo, os Hubs de eventos, o IoT Hub, armazenamento de BLOBs). Esta nova integração permite-lhe criar arquiteturas híbridas que podem analisar seus dados perto de onde é gerado, reduzindo a latência e maximizar informações.
Inscreva-se para esta pré-visualização [aqui](https://aka.ms/asapreview1).

