---
title: Funcionalidades de pré-visualização do Azure Stream Analytics
description: Este artigo lista as funcionalidades do Azure Stream Analytics que estão atualmente em pré-visualização.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/10/2019
ms.openlocfilehash: c84b814ddc06c583fc2f07288c7aa5cd65cc70a0
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2019
ms.locfileid: "54232129"
---
# <a name="azure-stream-analytics-preview-features"></a>Funcionalidades de pré-visualização do Azure Stream Analytics

Este artigo resume todas as funcionalidades atualmente em pré-visualização do Azure Stream Analytics. Não é recomendado a utilizar funcionalidades de pré-visualização num ambiente de produção.

## <a name="public-previews"></a>Pré-visualizações públicas

As seguintes funcionalidades estão em pré-visualização pública. Pode aproveitar esses recursos hoje, mas não usá-los no seu ambiente de produção.

### <a name="integration-with-azure-machine-learning"></a>Integração com o Azure Machine Learning

Pode dimensionar tarefas do Stream Analytics com as funções de Machine Learning (ML). Para saber mais sobre como pode usar as funções de ML na sua tarefa do Stream Analytics, visite [dimensionar a sua tarefa do Stream Analytics com as funções do Azure Machine Learning](stream-analytics-scale-with-machine-learning-functions.md). Confira um cenário do mundo real com [executar análise de sentimentos com o Azure Stream Analytics e o Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

### <a name="blob-output-partitioning-by-custom-time"></a>Saída do blob a criação de partições por tempo personalizado

O Azure Stream Analytics podem enviar resultados para o armazenamento de Blobs com base em atributos de tempo personalizado. Para obter mais informações, visite [saída de armazenamento de BLOBs de padrões de caminho personalizada DateTime para o Azure Stream Analytics](stream-analytics-custom-path-patterns-blob-storage-output.md).

### <a name="javascript-user-defined-aggregate"></a>Agregado definido pelo utilizador do JavaScript

O Azure Stream Analytics suporta definidas pelo utilizador agregações existentes (UDA) escritos em JavaScript, que permitem que implemente a lógica de negócio complexa de monitorização de estado. Saiba como utilizar os UDAs do [agregações definidas pelo utilizador do JavaScript do Azure Stream Analytics](stream-analytics-javascript-user-defined-aggregates.md) documentação. 

### <a name="live-data-testing-in-visual-studio"></a>Live o teste de dados no Visual Studio

O Visual Studio tools para o Azure Stream Analytics aprimore a funcionalidade de teste local que permite que teste consultas em fluxos de eventos em direto de origens na cloud, como o Hub de eventos ou IoT hub. Saiba como [testar dados dinâmicos localmente com ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-live-data-local-testing.md).

### <a name="net-user-defined-functions-on-iot-edge"></a>Funções definidas pelo utilizador do .NET no IoT Edge

Com o .NET padrão definido pelo utilizador funções, pode executar código padrão do .NET como parte do seu pipeline de transmissão em fluxo. Pode criar classes simples c# ou importar projeto completo e bibliotecas. Criação e a experiência de depuração completa é suportada no Visual Studio. Para obter mais informações, visite [desenvolver .NET Standard funções definidas pelo utilizador para tarefas do Edge do Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md).

## <a name="private-previews"></a>Pré-visualizações privadas

As seguintes funcionalidades estão em pré-visualização privada. Para aceder a estas pré-visualizações, visite a pré-visualização privada do Azure Stream Analytics [Inscreva-se](https://aka.ms/ASApreview1) página.

### <a name="anomaly-detection"></a>Deteção de Anomalias

O Azure Stream Analytics introduz novos modelos de aprendizagem automática com suporte para *pico* e *quedas* deteção, além de deteção, bidirecional, lento positivos e lenta tendências negativas.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge"></a>C# desserializador personalizado para o Azure Stream Analytics do IoT Edge

Os desenvolvedores agora podem implementar desserializadores personalizadas em c# para anular a serialização de eventos recebidos pelo Azure Stream Analytics. Exemplos de formatos que podem ser desserializados incluem Parquet, Protobuf, XML ou qualquer formato binário.

### <a name="blob-output-partitioning-by-custom-attribute"></a>Saída de blob, criação de partições pelo atributo personalizado

Agora, é possível particionar a saída do Azure Stream Analytics para o armazenamento de Blobs com base em qualquer coluna na sua consulta.

### <a name="managed-identities-for-azure-resources-authentication-to-azure-data-lake-storage"></a>Identidades geridas para a autenticação de recursos do Azure para armazenamento do Azure Data Lake

Agora, pode operacionalizar os pipelines em tempo real com identidades geridas para autenticação de recursos do Azure com base ao escrever no Gen1 de armazenamento do Azure Data Lake, permitindo-lhe criar tarefas programaticamente. Para obter mais informações, visite [identidades geridas por um uso dos recursos do Azure para autenticar o Azure Stream Analytics Jobs a saída de geração 1 do armazenamento do Azure Data Lake](stream-analytics-managed-identities-adls.md).

## <a name="next-steps"></a>Passos Seguintes

* [Oito novos recursos do Azure Stream Analytics](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/)

* [4 novas funcionalidades agora disponíveis no Azure Stream Analytics](https://azure.microsoft.com/blog/4-new-features-now-available-in-azure-stream-analytics/)
