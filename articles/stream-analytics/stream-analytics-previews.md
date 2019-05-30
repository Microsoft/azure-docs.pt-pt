---
title: Funcionalidades de pré-visualização do Azure Stream Analytics
description: Este artigo lista as funcionalidades do Azure Stream Analytics que estão atualmente em pré-visualização.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2019
ms.openlocfilehash: f3838bf6b9f7daa24c0cdb5b0c5a08d41d164530
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/13/2019
ms.locfileid: "65561145"
---
# <a name="azure-stream-analytics-preview-features"></a>Funcionalidades de pré-visualização do Azure Stream Analytics

Este artigo resume todas as funcionalidades atualmente em pré-visualização do Azure Stream Analytics. Não é recomendado a utilizar funcionalidades de pré-visualização num ambiente de produção.

## <a name="public-previews"></a>Pré-visualizações públicas

As seguintes funcionalidades estão em pré-visualização pública. Pode aproveitar esses recursos hoje, mas não usá-los no seu ambiente de produção.

### <a name="visual-studio-code-for-azure-stream-analytics-released-may-2019"></a>Visual Studio Code para análise do Azure Stream (lançado em Maio de 2019)

Tarefas do Azure Stream Analytics podem ser criadas no Visual Studio Code. Consulte nossos [VS Code tutorial de introdução](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).

### <a name="anomaly-detection"></a>Deteção de Anomalias

O Azure Stream Analytics introduz novos modelos de aprendizagem automática com suporte para *pico* e *quedas* deteção, além de deteção, bidirecional, lento positivos e lenta tendências negativas. Para obter mais informações, visite [deteção de anomalias no Azure Stream Analytics](stream-analytics-machine-learning-anomaly-detection.md).

### <a name="sql-database-reference-data"></a>Dados de referência de base de dados SQL

O Azure Stream Analytics oferece suporte a SQL Database do Azure como uma origem de entrada para dados de referência. Pode usar a base de dados SQL como dados de referência para a sua tarefa do Stream Analytics no portal do Azure e no Visual Studio com as ferramentas do Stream Analytics. Para obter mais informações, visite, [dados de referência de utilização de um banco de dados SQL para uma tarefa do Azure Stream Analytics](sql-reference-data.md).

### <a name="integration-with-azure-machine-learning"></a>Integração com o Azure Machine Learning

Pode dimensionar tarefas do Stream Analytics com as funções de Machine Learning (ML). Para saber mais sobre como pode usar as funções de ML na sua tarefa do Stream Analytics, visite [dimensionar a sua tarefa do Stream Analytics com as funções do Azure Machine Learning](stream-analytics-scale-with-machine-learning-functions.md). Confira um cenário do mundo real com [executar análise de sentimentos com o Azure Stream Analytics e o Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

### <a name="javascript-user-defined-aggregate"></a>Agregado definido pelo utilizador do JavaScript

O Azure Stream Analytics suporta definidas pelo utilizador agregações existentes (UDA) escritos em JavaScript, que permitem que implemente a lógica de negócio complexa de monitorização de estado. Saiba como utilizar os UDAs do [agregações definidas pelo utilizador do JavaScript do Azure Stream Analytics](stream-analytics-javascript-user-defined-aggregates.md) documentação. 

### <a name="live-data-testing-in-visual-studio"></a>Live o teste de dados no Visual Studio

O Visual Studio tools para o Azure Stream Analytics aprimore a funcionalidade de teste local que permite que teste consultas em fluxos de eventos em direto de origens na cloud, como o Hub de eventos ou IoT hub. Saiba como [testar dados dinâmicos localmente com ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-live-data-local-testing.md).

### <a name="net-user-defined-functions-on-iot-edge"></a>Funções definidas pelo utilizador do .NET no IoT Edge

Com o .NET padrão definido pelo utilizador funções, pode executar código padrão do .NET como parte do seu pipeline de transmissão em fluxo. Pode criar classes simples c# ou importar projeto completo e bibliotecas. Criação e a experiência de depuração completa é suportada no Visual Studio. Para obter mais informações, visite [desenvolver .NET Standard funções definidas pelo utilizador para tarefas do Edge do Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Outras visualizações

Os seguintes recursos também estão disponíveis em pré-visualização.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge-and-cloud-announced-may-2019"></a>C#desserializador personalizado para o Azure Stream Analytics no IoT Edge e Cloud (Announced Maio de 2019)

Os desenvolvedores podem implementar desserializadores personalizados no C# para anular a serialização de eventos recebidos pelo Azure Stream Analytics. Exemplos de formatos que podem ser desserializados incluem Parquet, Protobuf, XML ou qualquer formato binário. Inscreva-se para esta pré-visualização [aqui](https://aka.ms/asapreview1).

### <a name="parquet-output-announced-may-2019"></a>Saída de parquet (Maio de 2019 anunciada)
Parquet é um formato colunar ativar eficiente processamento de macrodados. Enviando dados em formato Parquet num data lake, pode tirar partido do Azure Stream Analytics para power dimensionamento de grande escala ETL de transmissão em fluxo e executar processamento em lotes, preparar os algoritmos de machine learning ou executar consultas interativas nos seus dados históricos. Inscreva-se para esta pré-visualização [aqui](https://aka.ms/asapreview1).

### <a name="one-click-integration-with-event-hubs-announced-may-2019"></a>Integração de um clique com o Hubs de eventos (Announced Maio de 2019) 
Com esta integração, agora poderá visualizar os dados recebidos e começar a escrever uma consulta do Stream Analytics com um clique a partir do portal do Hub de eventos. Assim que a consulta estiver pronta, poderá productize-lo em apenas alguns cliques e começar a obter informações em tempo real. Isso reduzirá significativamente o tempo e o custo de desenvolvimento de soluções de análise em tempo real. Inscreva-se para esta pré-visualização [aqui](https://aka.ms/asapreview1).

### <a name="support-for-azure-stack-announced-may-2019"></a>Suporte para o Azure Stack (Maio de 2019 anunciado)
Esta funcionalidade ativada, o tempo de execução do Azure IoT Edge, tira partido de recursos personalizados do Azure Stack, como o suporte nativo de entradas de dados locais e produz em execução no Azure Stack (por exemplo, os Hubs de eventos, o IoT Hub, armazenamento de BLOBs). Esta nova integração permite-lhe criar arquiteturas híbridas que podem analisar seus dados perto de onde é gerado, reduzindo a latência e maximizar informações.
Inscreva-se para esta pré-visualização [aqui](https://aka.ms/asapreview1).

