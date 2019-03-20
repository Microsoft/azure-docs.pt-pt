---
title: Funcionalidades de pré-visualização do Azure Stream Analytics
description: Este artigo lista as funcionalidades do Azure Stream Analytics que estão atualmente em pré-visualização.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/05/2019
ms.openlocfilehash: e4037e79812f0cf548ddfcc277c1b66332e0f1f7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57902880"
---
# <a name="azure-stream-analytics-preview-features"></a>Funcionalidades de pré-visualização do Azure Stream Analytics

Este artigo resume todas as funcionalidades atualmente em pré-visualização do Azure Stream Analytics. Não é recomendado a utilizar funcionalidades de pré-visualização num ambiente de produção.

## <a name="public-previews"></a>Pré-visualizações públicas

As seguintes funcionalidades estão em pré-visualização pública. Pode aproveitar esses recursos hoje, mas não usá-los no seu ambiente de produção.

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

## <a name="private-previews"></a>Pré-visualizações privadas

As seguintes funcionalidades estão em pré-visualização privada.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge"></a>C# desserializador personalizado para o Azure Stream Analytics do IoT Edge

Os desenvolvedores agora podem implementar desserializadores personalizadas em c# para anular a serialização de eventos recebidos pelo Azure Stream Analytics. Exemplos de formatos que podem ser desserializados incluem Parquet, Protobuf, XML ou qualquer formato binário.

### <a name="managed-identities-for-azure-resource-authentication-to-azure-data-lake-storage"></a>Identidades geridas para a autenticação de recursos do Azure para armazenamento do Azure Data Lake

Agora, pode operacionalizar os pipelines em tempo real com identidades geridas para autenticação de recursos do Azure com base ao escrever no Gen1 de armazenamento do Azure Data Lake, permitindo-lhe criar tarefas programaticamente. Para obter mais informações, visite [identidades geridas por um uso dos recursos do Azure para autenticar o Azure Stream Analytics Jobs a saída de geração 1 do armazenamento do Azure Data Lake](stream-analytics-managed-identities-adls.md).

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code para o Azure Stream Analytics

Tarefas do Azure Stream Analytics podem ser criadas no Visual Studio Code. Para ter acesso a ferramentas de recursos de pré-visualização privada, entre em contato com a *ASAToolsfeedback\@microsoft.com*.

## <a name="next-steps"></a>Passos Seguintes

* [Oito novos recursos do Azure Stream Analytics](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/)

* [Quatro novos recursos agora disponíveis no Azure Stream Analytics](https://azure.microsoft.com/blog/4-new-features-now-available-in-azure-stream-analytics/)
