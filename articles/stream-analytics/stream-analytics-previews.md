---
title: Funcionalidades de pré-visualização do Azure Stream Analytics
description: Este artigo lista as funcionalidades Azure Stream Analytics que estão atualmente em pré-visualização
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 55745c022038fa85f5b114f2bc347ed7292665eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589655"
---
# <a name="azure-stream-analytics-preview-features"></a>Funcionalidades de pré-visualização do Azure Stream Analytics

Este artigo resume todas as funcionalidades atualmente em pré-visualização para a Azure Stream Analytics. Não é recomendável utilizar funcionalidades de pré-visualização num ambiente de produção.

## <a name="authenticate-to-sql-database-output-with-managed-identities-preview"></a>Autenticar para a base de dados SQL com identidades geridas (pré-visualização)

O Azure Stream Analytics suporta [a autenticação de identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) para lavatórios de saída da Base de Dados Azure SQL. Identidades geridas eliminam as limitações dos métodos de autenticação baseados no utilizador, como a necessidade de reauttenenciar devido a alterações de senha. 

## <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Pontuação de alto desempenho em tempo real com modelos ML personalizados geridos pela Azure Machine Learning

O Azure Stream Analytics suporta pontuação de alto desempenho, em tempo real, aproveitando modelos personalizados de machine learning pré-treinados geridos pela Azure Machine Learning, e hospedados no Azure Kubernetes Service (AKS) ou Azure Container Instances (ACI), utilizando um fluxo de trabalho que não exige que escreva código. [Inscreva-se](https://aka.ms/asapreview1) para pré-visualização

## <a name="c-custom-de-serializers"></a>C# des-serializers personalizados
Os desenvolvedores podem aproveitar o poder do Azure Stream Analytics para processar dados em Protobuf, XML ou em qualquer formato personalizado. Você pode implementar [des-serializers personalizados](custom-deserializer-examples.md) em C#, que podem então ser usados para des-serializar eventos recebidos pela Azure Stream Analytics.

## <a name="extensibility-with-c-custom-code"></a>Extensibilidade com código personalizado C#

Os desenvolvedores que criam módulos Stream Analytics na nuvem ou no IoT Edge podem escrever ou reutilizar funções C# personalizadas e invocá-las diretamente na consulta através [de funções definidas](stream-analytics-edge-csharp-udf-methods.md)pelo utilizador .

## <a name="debug-query-steps-in-visual-studio"></a>Passos de consulta de Debug no Estúdio Visual

Pode visualizar facilmente o conjunto de linhas intermédias num diagrama de dados ao fazer testes locais em ferramentas Azure Stream Analytics para o Visual Studio. 


## <a name="live-data-testing-in-visual-studio"></a>Teste de dados ao vivo no Estúdio Visual

As ferramentas do Estúdio Visual para O Azure Stream Analytics melhoram a funcionalidade de teste local que lhe permite testar consultas contra streams de eventos ao vivo de fontes de nuvem como o Event Hub ou o hub IoT. Saiba como [testar dados ao vivo localmente utilizando ferramentas Azure Stream Analytics para o Visual Studio](stream-analytics-live-data-local-testing.md).

## <a name="visual-studio-code-for-azure-stream-analytics"></a>Código de estúdio visual para Azure Stream Analytics

Os trabalhos do Azure Stream Analytics podem ser da autoria do Código do Estúdio Visual. Consulte o nosso [Código VS a começar tutorial.](./quick-create-visual-studio-code.md)

## <a name="local-testing-with-live-data-in-visual-studio-code"></a>Testes locais com dados ao vivo no Código do Estúdio Visual

Pode testar as suas consultas com dados em direto na sua máquina local antes de submeter o trabalho ao Azure. Cada iteração de testes leva menos de dois a três segundos em média, resultando num processo de desenvolvimento muito eficiente.

