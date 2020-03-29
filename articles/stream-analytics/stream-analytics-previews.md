---
title: Funcionalidades de pré-visualização do Azure Stream Analytics
description: Este artigo lista as funcionalidades do Azure Stream Analytics que estão atualmente em pré-visualização.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 3e99263f6bf472c256e1747b8567249bbd62a445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969625"
---
# <a name="azure-stream-analytics-preview-features"></a>Funcionalidades de pré-visualização do Azure Stream Analytics

Este artigo resume todas as funcionalidades atualmente em pré-visualização para o Azure Stream Analytics. Não é recomendado utilizar funcionalidades de pré-visualização num ambiente de produção.

## <a name="public-previews"></a>Pré-visualizações públicas

As seguintes características estão em pré-visualização pública. Você pode aproveitar estas características hoje, mas não usá-las no seu ambiente de produção.

### <a name="online-scaling"></a>Escala ção online

Com a escala ção online, não é obrigado a parar o seu trabalho se precisar de alterar a atribuição de SU. Pode aumentar ou diminuir a capacidade da SU de um trabalho de corrida sem ter que detê-lo. Isto baseia-se na promessa do cliente de oleodutos críticos de missão de longa duração que o Stream Analytics oferece hoje. Para mais informações, consulte [Configure Azure Stream Analytics Streaming Units](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus).

### <a name="c-custom-de-serializers"></a>C# desserializers
Os desenvolvedores podem aproveitar a potência do Azure Stream Analytics para processar dados em Protobuf, XML ou qualquer formato personalizado. Pode implementar [desserializers personalizados](custom-deserializer-examples.md) em C#, que podem ser usados para desserializar eventos recebidos pelo Azure Stream Analytics.

### <a name="extensibility-with-c-custom-code"></a>Extensibility com código personalizado C#

Os desenvolvedores que criam módulos Stream Analytics na nuvem ou no IoT Edge podem escrever ou reutilizar funções personalizadas de C# e invocá-las diretamente na consulta através de [funções definidas pelo utilizador](stream-analytics-edge-csharp-udf-methods.md).


### <a name="debug-query-steps-in-visual-studio"></a>Debug consulta passos no Estúdio Visual

Pode facilmente pré-visualizar o conjunto de linha intermédia num diagrama de dados ao fazer testes locais em ferramentas Azure Stream Analytics para O Estúdio Visual. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Testes locais com dados ao vivo no Código do Estúdio Visual

Pode testar as suas consultas contra dados ao vivo na sua máquina local antes de submeter o trabalho ao Azure. Cada iteração de teste leva menos de dois a três segundos, em média, resultando num processo de desenvolvimento muito eficiente.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Código de estúdio visual para Azure Stream Analytics

Os trabalhos da Azure Stream Analytics podem ser da autoria do Código do Estúdio Visual. Veja o nosso [Código VS a começar tutorial.](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code)


### <a name="integration-with-azure-machine-learning"></a>Integração com aprendizagem automática Azure

Pode escalar os trabalhos de Stream Analytics com funções de Machine Learning (ML). Para saber mais sobre como pode utilizar as funções ML no seu trabalho de Stream Analytics, visite [Scale your Stream Analytics job with Azure Machine Learning functions](stream-analytics-scale-with-machine-learning-functions.md). Confira um cenário real com a análise de [sentimentos performativos utilizando o Azure Stream Analytics e o Azure Machine Learning.](stream-analytics-machine-learning-integration-tutorial.md)


### <a name="live-data-testing-in-visual-studio"></a>Teste de dados ao vivo no Estúdio Visual

As ferramentas do Estúdio Visual para o Azure Stream Analytics melhoram a funcionalidade de teste local que lhe permite testar consultas contra fluxos de eventos ao vivo de fontes em nuvem como O Hub de Eventos ou hub IoT. Saiba como [testar dados ao vivo localmente utilizando ferramentas Azure Stream Analytics para Estúdio Visual](stream-analytics-live-data-local-testing.md).


### <a name="net-user-defined-functions-on-iot-edge"></a>.NET funções definidas pelo utilizador no IoT Edge

Com funções padrão de utilização .NET, pode executar o código .NET Standard como parte do seu pipeline de streaming. Você pode criar classes C# simples ou importar projeto completo e bibliotecas. A experiência completa de autoria e depuração é apoiada no Visual Studio. Para mais informações, visite [Desenvolver funções definidas pelo utilizador .NET Standard para trabalhos de Edge Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Outras pré-visualizações

As seguintes funcionalidades também estão disponíveis em pré-visualização a pedido.

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Pontuação de alto desempenho em tempo real com modelos ML personalizados geridos por Azure Machine Learning

O Azure Stream Analytics suporta pontuações de alto desempenho, em tempo real, aproveitando modelos personalizados de Machine Learning pré-treinados geridos pela Azure Machine Learning, e hospedados no Serviço Azure Kubernetes (AKS) ou Em Caixas de Contentores Azure (ACI), utilizando um fluxo de trabalho que não requer que escreva código. [Inscreva-se](https://aka.ms/asapreview1) para pré-visualização

### <a name="support-for-azure-stack"></a>Suporte para Stack Azure
Esta funcionalidade ativada no tempo de funcionamento do Azure IoT Edge, aproveita as funcionalidades personalizadas do Azure Stack, tais como suporte nativo para inputs locais e saídas em funcionamento em Azure Stack (por exemplo, Hubs de Eventos, Hub IoT, Armazenamento Blob). Esta nova integração permite-lhe construir arquiteturas híbridas que possam analisar os seus dados perto de onde são gerados, reduzindo a latência e maximizando insights.
Esta funcionalidade permite-lhe construir arquiteturas híbridas que possam analisar os seus dados perto de onde são gerados, reduzindo a latência e maximizando insights. Deve [inscrever-se](https://aka.ms/asapreview1) para esta pré-estreia.
