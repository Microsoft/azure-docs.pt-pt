---
title: Configuração de serviço - QnA Maker
description: Entenda como e onde configurar recursos.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/9/2020
ms.openlocfilehash: d2095919d6f6be482390250fd8d0ee20e9015237
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444865"
---
# <a name="service-configuration"></a>Configuração do serviço

Cada versão do QnA Maker utiliza um conjunto diferente de recursos Azure (serviços). Este artigo descreve as personalizações suportadas para estes serviços. 

## <a name="app-service"></a>Serviço de Aplicações

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

O QnA Maker utiliza o Serviço de Aplicações para fornecer o tempo de funcionação da consulta utilizado pela [API de 200 000](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker4.0/runtime/generateanswer).

Estas configurações estão disponíveis no portal Azure, para o Serviço de Aplicações. As definições estão disponíveis selecionando **Definições** e, em seguida, **Configuração**.

Pode definir uma definição individual através da lista de Definições de Aplicação ou modificar várias definições selecionando **edição avançada**.

|Recurso|Definição|
|--|--|
|AzureSearchAdminKey|Cognitive Search - usado para armazenamento de pares QnA e ranker #1|
|AzureSearchName|Cognitive Search - usado para armazenamento de pares QnA e ranker #1|
|PadrãoAnswer|Texto de resposta quando não se encontra qualquer correspondência|
|UserAppInsightsAppId|Log de chat e telemetria|
|UserAppInsightsKey|Log de chat e telemetria|
|Nome do UserAppInsights|Log de chat e telemetria|

É necessário **reiniciar** o serviço a partir da página **geral** do portal Azure, uma vez que esteja feito de alterações.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

As personalizações do Serviço de Aplicações não se aplicam à gerida (pré-visualização) do QnA Maker.

---

## <a name="qna-maker-service"></a>Serviço de Fabricante QnA

O serviço QnA Maker fornece configuração para os seguintes utilizadores colaborarem num único serviço QnA Maker e em todas as suas bases de conhecimento.

Saiba [como adicionar colaboradores](./how-to/collaborate-knowledge-base.md) ao seu serviço.

## <a name="change-azure-cognitive-search"></a>Alterar a pesquisa cognitiva do Azure

Saiba [como alterar o serviço de Pesquisa Cognitiva](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) ligado ao seu serviço QnA Maker.

## <a name="change-default-answer"></a>Alterar resposta predefinida

Saiba [como alterar o texto das suas respostas predefinindo](How-To/change-default-answer.md). 

## <a name="telemetry"></a>Telemetria

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

O Application Insights é utilizado para monitorizar a telemetria com o QnA Maker GA. Não existem configurações específicas do QnA Maker.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

Saiba [como adicionar telemetria ao seu serviço gerido (Preview) do QnA Maker](How-To/get-analytics-knowledge-base.md). 

---

## <a name="app-service-plan"></a>Plano do Serviço de Aplicações

# <a name="qnamaker-ga-stable-release"></a>[QnAMaker GA (lançamento estável)](#tab/v1)

O Plano de Serviço de Aplicações não tem configurações específicas do QnA Maker.

# <a name="qnamaker-managed-preview-release"></a>[QnAMaker gerido (pré-visualização)](#tab/v2)

O Plano de Serviço de Aplicações não é utilizado com o QnA Maker gerido (pré-visualização).

---

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre formatos para [documentos](reference-document-format-guidelines.md) e URLs que pretende importar para uma base de conhecimento.
