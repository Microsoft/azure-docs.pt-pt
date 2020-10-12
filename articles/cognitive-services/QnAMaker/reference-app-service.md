---
title: Configuração de serviço - QnA Maker
description: Entenda como e onde configurar recursos.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 1d54b912d2177a3ccd0cf34d57fc0358af653199
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776719"
---
# <a name="service-configuration"></a>Configuração do serviço

O QnA Maker utiliza vários recursos Azure (serviços) incluindo Pesquisa Cognitiva, Serviço de Aplicações, Plano de Serviço de Aplicações e Insights de Aplicações.

Todas as personalizações a estas definições suportadas pelo QnA Maker estão listadas abaixo.

## <a name="app-service"></a>Serviço de Aplicações

O QnA Maker utiliza o Serviço de Aplicações para fornecer o tempo de funcionação da consulta utilizado pela [API de 200 000](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer).


Estas configurações estão disponíveis no portal Azure, para o Serviço de Aplicações. As definições estão disponíveis selecionando **Definições**e, em seguida, **Configuração**.

Pode definir uma definição individual através da lista de Definições de Aplicação ou modificar várias definições selecionando **edição avançada**.

|Recurso|Definição|
|--|--|
|AzureSearchAdminKey|Cognitive Search - usado para armazenamento de pares QnA e ranker #1|
|AzureSearchName|Cognitive Search - usado para armazenamento de pares QnA e ranker #1|
|PadrãoAnswer|Texto de resposta quando não se encontra qualquer correspondência|
|UserAppInsightsAppId|Log de chat e telemetria|
|UserAppInsightsKey|Log de chat e telemetria|
|Nome do UserAppInsights|Log de chat e telemetria|

Saiba [como adicionar alterar o seu serviço de Pesquisa Cognitiva](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) ao seu serviço.

É necessário **reiniciar** o serviço a partir da página **geral** do portal Azure, uma vez que esteja feito de alterações.

## <a name="qna-maker-service"></a>Serviço de Fabricante QnA

O serviço QnA Maker fornece configuração para os seguintes utilizadores colaborarem num único serviço QnA Maker e em todas as suas bases de conhecimento.

Saiba [como adicionar colaboradores](./how-to/collaborate-knowledge-base.md) ao seu serviço.

## <a name="application-insights"></a>Application Insights

O Application Insights não tem configurações específicas do QnA Maker.

## <a name="app-service-plan"></a>Plano do Serviço de Aplicações

O Plano de Serviço de Aplicações não tem configurações específicas do QnA Maker.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre formatos para [documentos](reference-document-format-guidelines.md) e URLs que pretende importar para uma base de conhecimento.