---
title: Funcionalidades de pré-visualização do Azure Stream Analytics
description: Este artigo lista as funcionalidades Azure Stream Analytics que estão atualmente em pré-visualização.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 8/07/2020
ms.openlocfilehash: 4179b06759802025f97bd32a355b788c96c9eddb
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123325"
---
# <a name="azure-stream-analytics-preview-features"></a>Funcionalidades de pré-visualização do Azure Stream Analytics

Este artigo resume todas as funcionalidades atualmente em pré-visualização para a Azure Stream Analytics. Não é recomendável utilizar funcionalidades de pré-visualização num ambiente de produção.

## <a name="public-previews"></a>Pré-visualizações públicas

As seguintes funcionalidades estão em visualização pública. Hoje em dia pode aproveitar estas funcionalidades, mas não as utilize no seu ambiente de produção.

### <a name="authenticate-to-sql-database-output-with-managed-identities"></a>Autenticar para a produção da Base de Dados SQL com identidades geridas

O Azure Stream Analytics suporta [a autenticação de identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) para lavatórios de saída da Base de Dados Azure SQL. Identidades geridas eliminam as limitações dos métodos de autenticação baseados no utilizador, como a necessidade de reauttenenciar devido a alterações de senha. 

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Pontuação de alto desempenho em tempo real com modelos ML personalizados geridos pela Azure Machine Learning

O Azure Stream Analytics suporta pontuação de alto desempenho, em tempo real, aproveitando modelos personalizados de machine learning pré-treinados geridos pela Azure Machine Learning, e hospedados no Azure Kubernetes Service (AKS) ou Azure Container Instances (ACI), utilizando um fluxo de trabalho que não exige que escreva código. [Inscreva-se](https://aka.ms/asapreview1) para pré-visualização

### <a name="c-custom-de-serializers"></a>C# des-serializers personalizados
Os desenvolvedores podem aproveitar o poder do Azure Stream Analytics para processar dados em Protobuf, XML ou em qualquer formato personalizado. Você pode implementar [des-serializers personalizados](custom-deserializer-examples.md) em C#, que podem então ser usados para des-serializar eventos recebidos pela Azure Stream Analytics.

### <a name="extensibility-with-c-custom-code"></a>Extensibilidade com código personalizado C#

Os desenvolvedores que criam módulos Stream Analytics na nuvem ou no IoT Edge podem escrever ou reutilizar funções C# personalizadas e invocá-las diretamente na consulta através [de funções definidas](stream-analytics-edge-csharp-udf-methods.md)pelo utilizador .

### <a name="debug-query-steps-in-visual-studio"></a>Passos de consulta de Debug no Estúdio Visual

Pode visualizar facilmente o conjunto de linhas intermédias num diagrama de dados ao fazer testes locais em ferramentas Azure Stream Analytics para o Visual Studio. 


### <a name="live-data-testing-in-visual-studio"></a>Teste de dados ao vivo no Estúdio Visual

As ferramentas do Estúdio Visual para O Azure Stream Analytics melhoram a funcionalidade de teste local que lhe permite testar consultas contra streams de eventos ao vivo de fontes de nuvem como o Event Hub ou o hub IoT. Saiba como [testar dados ao vivo localmente utilizando ferramentas Azure Stream Analytics para o Visual Studio](stream-analytics-live-data-local-testing.md).

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Código de estúdio visual para Azure Stream Analytics

Os trabalhos do Azure Stream Analytics podem ser da autoria do Código do Estúdio Visual. Consulte o nosso [Código VS a começar tutorial.](./quick-create-visual-studio-code.md)

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Testes locais com dados ao vivo no Código do Estúdio Visual

Pode testar as suas consultas com dados em direto na sua máquina local antes de submeter o trabalho ao Azure. Cada iteração de testes leva menos de dois a três segundos em média, resultando num processo de desenvolvimento muito eficiente.

## <a name="other-previews"></a>Outras pré-visualizações

As seguintes funcionalidades também estão disponíveis na pré-visualização a pedido.

### <a name="support-for-azure-stack"></a>Suporte para Azure Stack
Esta funcionalidade ativada no tempo de execução do Azure IoT Edge, aproveita as funcionalidades personalizadas do Azure Stack, tais como suporte nativo para entradas locais e saídas em execução em Azure Stack (por exemplo, Centros de Eventos, IoT Hub, Blob Storage). Esta nova integração permite-lhe construir arquiteturas híbridas que possam analisar os seus dados perto do local onde são gerados, reduzindo a latência e maximizando insights.
Esta funcionalidade permite-lhe construir arquiteturas híbridas que possam analisar os seus dados perto do local onde são gerados, reduzindo a latência e maximizando insights. Tem de [se inscrever](https://aka.ms/asapreview1) para esta pré-estreia.