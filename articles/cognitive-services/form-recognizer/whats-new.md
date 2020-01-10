---
title: Novidades no Reconhecedor de Formato?
titleSuffix: Azure Cognitive Services
description: Entenda as alterações mais recentes na API do reconhecedor de formulário.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: pafarley
ms.openlocfilehash: cb5639dcf0e13ea03d34604816b3939085674c2e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456368"
---
# <a name="whats-new-in-form-recognizer"></a>Novidades no Reconhecedor de Formato?

Este artigo destaca as principais alterações que acompanham as novas versões da API do reconhecedor de formulário.

> [!NOTE]
> O guia de início rápido e os guias neste documento definem sempre a versão mais recente da API, a menos que eles especifiquem o contrário.

## <a name="form-recognizer-20-preview"></a>Reconhecedor de formulário 2,0 (visualização)

> [!IMPORTANT]
> O reconhecedor de formulário 2,0 está disponível atualmente para assinaturas nas regiões `West US 2` e `West Europe`. Se sua assinatura não estiver nessa região, use a API 1,0. Os guias de início rápido para treinamento e uso de um modelo personalizado estão disponíveis para v 1.0 e v 2.0.

### <a name="new-features"></a>Novas funcionalidades

* **Modelo personalizado**
  * **Treinar com rótulos** Agora você pode treinar um modelo personalizado com dados rotulados manualmente. Isso resulta em modelos de melhor desempenho e pode produzir modelos que funcionam com formulários complexos ou formulários que contêm valores sem chaves.
  * **API assíncrona** Você pode usar chamadas de API assíncronas para treinar e analisar grandes conjuntos de dados e arquivos.
  * **Suporte a arquivo TIFF** Agora você pode treinar e extrair dados de documentos TIFF.
  * **Aprimoramentos de precisão de extração**

* **Modelo de recebimento predefinido**
  * **Valores de gorjeta** Agora você pode extrair valores de gorjeta e outros valores manuscritos.
  * **Extração de item de linha** Você pode extrair valores de item de linha de recebimentos.
  * **Valores de confiança** Você pode exibir a confiança do modelo para cada valor extraído.
  * **Aprimoramentos de precisão de extração**

* **Extração de layout** Agora você pode usar a API de layout para extrair dados de texto e dados de tabela de seus formulários.

### <a name="custom-model-api-changes"></a>Alterações da API do modelo personalizado

Todas as APIs para treinamento e uso de modelos personalizados foram renomeadas e alguns métodos síncronos agora são assíncronos. Estas são as principais alterações:

* O processo de treinamento de um modelo agora é assíncrono. Você inicia o treinamento por meio da chamada à API do **/Custom/Models** . Essa chamada retorna uma ID de operação, que pode ser passada em **Custom/Models/{ModelId}** para retornar os resultados de treinamento.
* A extração de chave/valor agora é iniciada pela chamada à API **/Custom/Models/{modelID}/Analyze** . Essa chamada retorna uma ID de operação, que pode ser passada em **Custom/Models/{ModelId}/analyzeResults/{resultid}** para retornar os resultados de extração.
* As IDs de operação para a operação de treinamento agora estão localizadas no cabeçalho **local** de respostas http, não no cabeçalho **Operation-Location** .

### <a name="receipt-api-changes"></a>Alterações da API de recebimento

As APIs para ler os recibos de vendas foram renomeadas.

* A extração de dados de recebimento agora é iniciada pela chamada à API **/prebuilt/Receipt/Analyze** . Essa chamada retorna uma ID de operação, que pode ser passada em **/prebuilt/Receipt/analyzeResults/{resultID}** para retornar os resultados de extração.

### <a name="output-format-changes"></a>Alterações de formato de saída

As respostas JSON para todas as chamadas de API têm novos formatos. Algumas chaves e valores foram adicionados, removidos ou renomeados. Consulte os guias de início rápido para obter exemplos dos formatos JSON atuais.

## <a name="next-steps"></a>Passos seguintes

Conclua um guia de [início rápido](quickstarts/curl-train-extract.md) para começar a usar as [APIs do reconhecedor de formulário](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm).