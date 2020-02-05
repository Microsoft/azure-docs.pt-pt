---
title: Funcionalidades de pré-visualização do Azure Stream Analytics
description: Este artigo lista as funcionalidades do Azure Stream Analytics que estão atualmente em pré-visualização.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 2/1/2020
ms.openlocfilehash: aaff56ba1de69485d1c3b93bc7ed95ce1a3cbd88
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983554"
---
# <a name="azure-stream-analytics-preview-features"></a>Funcionalidades de pré-visualização do Azure Stream Analytics

Este artigo resume todas as funcionalidades atualmente em pré-visualização do Azure Stream Analytics. Não é recomendado a utilizar funcionalidades de pré-visualização num ambiente de produção.

## <a name="public-previews"></a>Pré-visualizações públicas

As seguintes funcionalidades estão em pré-visualização pública. Pode aproveitar esses recursos hoje, mas não usá-los no seu ambiente de produção.

### <a name="online-scaling"></a>Dimensionamento online

Com o dimensionamento online, não é necessário interromper seu trabalho se você precisar alterar a alocação de SU. Você pode aumentar ou diminuir a capacidade de SU de um trabalho em execução sem precisar interrompê-lo. Isso se baseia na promessa do cliente de pipelines de missão crítica de longa execução que Stream Analytics oferece hoje. Para obter mais informações, consulte [configurar Azure Stream Analytics unidades de streaming](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus).

### <a name="c-custom-de-serializers"></a>C#desserializadores personalizados
Os desenvolvedores podem aproveitar o poder do Azure Stream Analytics para processar dados em Protobuf, XML ou qualquer formato personalizado. Você pode implementar [desserializadores personalizados](custom-deserializer-examples.md) no C#, que podem ser usados para desserializar eventos recebidos por Azure Stream Analytics.

### <a name="extensibility-with-c-custom-code"></a>Extensibilidade com C# código personalizado

Os desenvolvedores que criam Stream Analytics módulos na nuvem ou em IoT Edge podem gravar ou reutilizar funções personalizadas C# e chamá-las diretamente na consulta por meio de [funções definidas pelo usuário](stream-analytics-edge-csharp-udf-methods.md).

### <a name="managed-identity-authentication-with-power-bi"></a>Autenticação de identidade gerenciada com Power BI

O Azure Stream Analytics oferece suporte completo à autenticação baseada em identidade gerenciada com Power BI para uma experiência de painel dinâmico.

### <a name="anomaly-detection"></a>Deteção de Anomalias

Azure Stream Analytics os modelos de aprendizado de máquina têm suporte para detecção de *pico* e *DIPs* , além de detecção bidirecional, positiva lenta e baixa de tendências negativas. Para obter mais informações, visite [detecção de anomalias em Azure Stream Analytics](stream-analytics-machine-learning-anomaly-detection.md).

### <a name="debug-query-steps-in-visual-studio"></a>Depurar etapas de consulta no Visual Studio

Você pode visualizar facilmente o conjunto de linhas intermediários em um diagrama de dados ao fazer testes locais no Azure Stream Analytics Tools para Visual Studio. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Teste local com dados dinâmicos no Visual Studio Code

Você pode testar suas consultas em dados dinâmicos em seu computador local antes de enviar o trabalho para o Azure. Cada iteração de teste leva menos de dois a três segundos em média, resultando em um processo de desenvolvimento muito eficiente.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code para Azure Stream Analytics

Azure Stream Analytics trabalhos podem ser criados no Visual Studio Code. Veja nosso [tutorial de introdução ao vs Code](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).


### <a name="integration-with-azure-machine-learning"></a>Integração com o Azure Machine Learning

Pode dimensionar tarefas do Stream Analytics com as funções de Machine Learning (ML). Para saber mais sobre como pode usar as funções de ML na sua tarefa do Stream Analytics, visite [dimensionar a sua tarefa do Stream Analytics com as funções do Azure Machine Learning](stream-analytics-scale-with-machine-learning-functions.md). Confira um cenário do mundo real com [executar análise de sentimentos com o Azure Stream Analytics e o Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).


### <a name="live-data-testing-in-visual-studio"></a>Live o teste de dados no Visual Studio

O Visual Studio tools para o Azure Stream Analytics aprimore a funcionalidade de teste local que permite que teste consultas em fluxos de eventos em direto de origens na cloud, como o Hub de eventos ou IoT hub. Saiba como [testar dados dinâmicos localmente com ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-live-data-local-testing.md).


### <a name="net-user-defined-functions-on-iot-edge"></a>Funções definidas pelo utilizador do .NET no IoT Edge

Com o .NET padrão definido pelo utilizador funções, pode executar código padrão do .NET como parte do seu pipeline de transmissão em fluxo. Pode criar classes simples c# ou importar projeto completo e bibliotecas. Criação e a experiência de depuração completa é suportada no Visual Studio. Para obter mais informações, visite [desenvolver .NET Standard funções definidas pelo utilizador para tarefas do Edge do Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Outras visualizações

Os recursos a seguir também estão disponíveis em visualização na solicitação.

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Pontuação de alto desempenho em tempo real com modelos de ML personalizados gerenciados por Azure Machine Learning

O Azure Stream Analytics dá suporte a pontuação em tempo real de alto desempenho, aproveitando modelos de Machine Learning pré-treinados personalizados gerenciados por Azure Machine Learning e hospedados no AKS (serviço kubernetes do Azure) ou ACI (instâncias de contêiner do Azure), usando um fluxo de trabalho Isso não exige que você escreva código. [Inscrever-se](https://aka.ms/asapreview1) para visualização

### <a name="support-for-azure-stack"></a>Suporte para Azure Stack
Esse recurso habilitado no tempo de execução de Azure IoT Edge, aproveita os recursos de Azure Stack personalizados, como suporte nativo para entradas locais e saídas em execução em Azure Stack (por exemplo, hubs de eventos, Hub IoT, armazenamento de BLOBs). Essa nova integração permite que você crie arquiteturas híbridas que podem analisar seus dados perto de onde são gerados, reduzindo a latência e maximizando as informações.
Esse recurso permite que você crie arquiteturas híbridas que podem analisar seus dados perto de onde são gerados, reduzindo a latência e maximizando as informações. Você deve [se inscrever](https://aka.ms/asapreview1) para esta versão prévia.
