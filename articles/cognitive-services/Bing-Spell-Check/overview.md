---
title: O que é a API de Verificação Ortográfica do Bing?
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre a API de Verificação ortográfica de Bing, que utiliza machine learning e tradução automática estatística para verificação de feitiços contextuais.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: c0453fa99376cb6a5dba1e427cdc0deccb3e03de
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94367051"
---
# <a name="what-is-the-bing-spell-check-api"></a>O que é a API de Verificação Ortográfica do Bing?

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

A API de Verificação ortográfica de Bing permite-lhe realizar gramática contextual e verificação ortográfica no texto. Embora a maioria dos verificadores de feitiços dependa de conjuntos de regras baseados no dicionário, o verificador ortográfico Bing aproveita a aprendizagem automática e a tradução de máquinas estatísticas para fornecer correções precisas e contextuais. 

## <a name="features"></a>Funcionalidades

| Funcionalidade | Descrição |
|---------|---------|
|Vários modos de verificação ortográfica     | Vários modos de verificação ortográfica permitem-lhe obter correções focadas na gramática e/ou ortografia. |
|Gíria e reconhecimento de linguagem informal     | Reconhecer expressões comuns e termos informais usados em texto.         |
|Diferenciar entre palavras semelhantes     | Encontre o uso correto entre palavras que soam semelhantes mas diferem no significado (por exemplo, "ver" e "mar")        |
|Marca, título e suporte popular de uso     | Reconhecer novas marcas, títulos e outras expressões populares à medida que emergem |

## <a name="workflow"></a>Fluxo de trabalho

A API de Verificação ortográfica de Bing é fácil de ligar a partir de qualquer linguagem de programação que possa fazer pedidos HTTP e analisar respostas JSON. O serviço é acessível utilizando a API REST ou os Bing Spell Check SDKs. 

1. Crie uma [conta de API dos Serviços Cognitivos](../cognitive-services-apis-create-account.md) com acesso às APIs de Pesquisa do Bing. Se não tiver uma subscrição do Azure, pode criar uma conta gratuita. 
2. Envie um pedido para a API de Pesquisa na Web do Bing.
3. Parse a resposta JSON

## <a name="next-steps"></a>Passos seguintes

Primeiro, experimente a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/spell-check/) de pesquisa de Bing Spell Check API para ver como pode verificar rapidamente uma variedade de textos.

Quando estiver pronto para chamar a API, crie uma [conta dos serviços cognitivos da API](../../cognitive-services/cognitive-services-apis-create-account.md). Se não tiver uma subscrição do Azure, pode [criar uma conta](https://azure.microsoft.com/free/cognitive-services/) gratuita.

Também pode visitar a página do hub da [Bing Search API](../bing-web-search/overview.md) para explorar as outras APIs disponíveis.