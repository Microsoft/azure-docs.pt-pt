---
title: Configuração do serviço - QnA Maker
description: Entenda como e onde configurar recursos.
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 3be32d1778604121c2acac88415cbfbc4bdbca3d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80804265"
---
# <a name="service-configuration"></a>Configuração do serviço

O QnA Maker utiliza vários recursos Azure (serviços) incluindo Pesquisa Cognitiva, Serviço de Aplicações, Plano de Serviço de Aplicações e Insights de Aplicações.

Todas as personalizações para estas configurações suportadas pelo QnA Maker estão listadas abaixo.

## <a name="app-service"></a>Serviço de Aplicações

O QnA Maker utiliza o Serviço de Aplicações para fornecer o tempo de execução de consulta utilizado pela [generateAnswer API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer).


Estas definições estão disponíveis no portal Azure, para o Serviço de Aplicações. As definições estão disponíveis selecionando **Definições**, em **seguida, Configuração**.

Pode definir uma definição individual através da lista de Definições de Aplicação ou modificar várias definições selecionando **advanced edit**.

|Recurso|Definição|
|--|--|
|AzureSearchAdminKey|Cognitive Search - usado para armazenamento de pares QnA e Ranker #1|
|Nome AzureSearch|Cognitive Search - usado para armazenamento de pares QnA e Ranker #1|
|Resposta por defeito|Texto de resposta quando não é encontrado nenhum jogo|
|UserAppInsightsAppId|Registo de chat e telemetria|
|UserAppInsightsKey|Registo de chat e telemetria|
|UserAppInsightsName|Registo de chat e telemetria|

Saiba [como adicionar o seu serviço](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) de Pesquisa Cognitiva ao seu serviço.

É necessário **reiniciar** o serviço a partir da página **de visão geral** do portal Azure, uma vez que esteja a fazer alterações.

## <a name="qna-maker-service"></a>Serviço de Fabricante QnA

O serviço QnA Maker fornece configuração para que os seguintes utilizadores colaborem num único serviço QnA Maker e em todas as suas bases de conhecimento.

Aprenda [a adicionar colaboradores](./how-to/collaborate-knowledge-base.md) ao seu serviço.

## <a name="application-insights"></a>Application Insights

Os Insights de Aplicação não têm definições de configuração específicas para o Fabricante QnA.

## <a name="app-service-plan"></a>Plano do Serviço de Aplicações

O Plano de Serviço de Aplicações não tem definições de configuração específicas para o Fabricante QnA.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [formatos](reference-document-format-guidelines.md) para documentos e URLs que pretende importar para uma base de conhecimento.