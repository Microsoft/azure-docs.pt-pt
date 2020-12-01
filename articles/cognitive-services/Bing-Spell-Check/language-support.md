---
title: Suporte linguístico - Bing Spell Check API
titleSuffix: Azure Cognitive Services
description: Uma lista de línguas naturais apoiada pela API de Verificação ortográfica de Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: a97bd6bfc1cb7a0760894faaf3fd0617e6165f58
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351202"
---
# <a name="language-and-region-support-for-bing-spell-check-api"></a>Suporte linguístico e regional para Bing Spell Check API

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Estes idiomas são suportados pela API de Verificação ortográfica de Bing (apenas no `spell` modo).

Por favor, note que para trabalhar com qualquer outra língua que `en-US` não, o `mkt` deve ser definido e deve ser definido como foi descrito na referência Spell Check `Accept-Language` `setLang` [API v7](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)

| Linguagem    | Código do idioma |
|:----------- |:-------------:|
| Árabe      | `ar`          |
| Chinês (República Popular da China)     | `zh-CN`          |
| Chinês (HONG KONG SAR)    | `zh-HK`          |
| Chinês (Taiwan)     | `zh-TW`          |
| Dinamarquês      | `da`          |
| Neerlandês (Bélgica)       | `nl-BE`          |
| Neerlandês (Países Baixos)      | `nl-NL`          |
| Inglês (Austrália)    | `en-AU`          |
| Inglês (Canadá)     | `en-CA`          |
| Inglês (Índia)    | `en-IN`          |
| Inglês (Indonésia)     | `en-ID`          |
| Inglês (Malásia)     | `en-MY`          |
| Inglês (Nova Zelândia)    | `en-NZ`          |
| Inglês (Filipinas)     | `en-PH`          |
| Inglês (África do Sul)    | `en-ZA`          |
| Inglês (Reino Unido)    | `en-GB`          |
| Inglês (Estados Unidos)    | `en-US`          |
| Finlandês     | `fi`          |
| Francês (Bélgica)     | `fr-BE`          |
| Francês (Canadá)     | `fr-CA`          |
| Francês (França)     | `fr-FR`          |
| Francês (Suíça)      | `fr-CH`          |
| Alemão (Áustria)      | `de-AT`          |
| Alemão (Alemanha)      | `de-DE`          |
| Alemão (Suíça)      | `de-CH`          |
| Italiano     | `it`          |
| Japonês    | `ja`          |
| Coreano      | `ko`          |
| Norueguês   | `no`          |
| Polaco      | `pl`          |
| Português (Brasil)   | `pt-BR`|
| Português (Portugal) | `pt-PT`|
| Russo     | `ru`          |
| Espanhol (Argentina)    | `es-AR`          |
| Espanhol (Chile)     | `es-CL`          |
| Espanhol (México)    | `es-MX`          |
| Espanhol (Espanha)    | `es-ES`          |
| Espanhol (Estados Unidos)    | `es-US`          |
| Sueco     | `sv`          |
| Turco     | `tr`          |

## <a name="see-also"></a>Ver também

- [Página de Documentação dos Serviços Cognitivos](../index.yml)
- [Página de Produto dos Serviços Cognitivos](https://azure.microsoft.com/services/cognitive-services/)