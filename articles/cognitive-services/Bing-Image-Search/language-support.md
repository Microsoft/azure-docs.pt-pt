---
title: Suporte ao idioma-API de Pesquisa de Imagem do Bing
titleSuffix: Azure Cognitive Services
description: Descubra quais países/regiões e idiomas são suportados pelo API de Pesquisa de Imagem do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: ca3821b6088e45730334d1b0971e270b1d86dfce
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881912"
---
# <a name="language-and-region-support-for-the-bing-image-search-api"></a>Suporte a idiomas e regiões para o API de Pesquisa de Imagem do Bing

O API de Pesquisa de Imagem do Bing dá suporte a mais de três dezenas de países/regiões, muitos com mais de um idioma. A especificação de um país/região com uma consulta serve principalmente para refinar os resultados da pesquisa com base nos interesses desse país/região. Além disso, os resultados podem conter links para o Bing, e esses links podem localizar a experiência do usuário do Bing de acordo com o país/região ou idioma especificado.

Para especificar o país/região e idioma, defina o `mkt` parâmetro de consulta (Market) para um código da tabela Markets abaixo. O mercado especifica um país/região e um idioma. Se o usuário preferir ver exibir texto em um idioma diferente, defina `setLang` o parâmetro de consulta para o código de idioma apropriado.

Como alternativa, você pode especificar o país/região usando o `cc` parâmetro de consulta. Se você especificar um país/região, também deverá especificar um ou mais códigos de idioma usando o `Accept-Language` cabeçalho http. Os idiomas com suporte variam de acordo com o país/região; Eles são fornecidos para cada país/região na tabela de mercados.

> [!NOTE]
> A API de imagens de tendências atualmente dá suporte apenas aos seguintes mercados:
> - en-US (Inglês, Estados Unidos)
> - en-CA (Inglês, Canadá)
> - en-AU (Inglês, Austrália)
> - ZH-CN (chinês, China)

## <a name="countriesregions"></a>Países/Regiões

|País/região|Código|
|-------|----|
|Argentina|AR|
|Austrália|AU|
|Áustria|AT|
|Bélgica|BE|
|Brasil|BR|
|Canadá|AC|
|Chile|CL|
|Dinamarca|DK|
|Finlândia|FI|
|França|FR|
|Alemanha|DE|
|RAE de Hong Kong|HK|
|Índia|IN|
|Indonésia|id|
|Itália|it|
|Japão|JP|
|Coreia|KR|
|Malásia|MY|
|México|MX|
|Países Baixos|NL|
|Nova Zelândia|NZ|
|Noruega|NO|
|China|CN|
|Polónia|PL|
|Portugal|PT|
|Filipinas|PH|
|Rússia|RU|
|Arábia Saudita|SA|
|África do Sul|ZA|
|Espanha|ES|
|Suécia|SE|
|Suíça|CH|
|Taiwan|TW|
|Turquia|TR|
|Reino Unido|GB|
|Estados Unidos|EUA|


## <a name="markets"></a>Mercados

|País/região|Idioma|Código do mercado|
|-------|--------|-----------|
|Argentina|Espanhol|es-AR|
|Austrália|Português|EN-AU|
|Áustria|Alemão|Alemanha-AT|
|Bélgica|Neerlandês|NL-ser|
|Bélgica|Francês|fr-ser|
|Brasil|Português|pt-BR|
|Canadá|Português|en-CA|
|Canadá|Francês|fr-CA|
|Chile|Espanhol|es-CL|
|Dinamarca|Dinamarquês|da-DK|
|Finlândia|Finlandês|fi-FI|
|França|Francês|FR-FR|
|Alemanha|Alemão|de-DE|
|RAE de Hong Kong|Chinês tradicional|zh-HK|
|Índia|Português|EN-IN|
|Indonésia|Português|ID do en|
|Itália|Italiano|it-IT|
|Japão|Japonês|ja-JP|
|Coreia|Coreano|ko-KR|
|Malásia|Português|EN-MY|
|México|Espanhol|es-MX|
|Países Baixos|Neerlandês|NL-NL|
|Nova Zelândia|Português|EN-NZ|
|China|Chinês|zh-CN|
|Polónia|Polaco|pl-PL|
|Portugal|Português|pt-PT|
|Filipinas|Português|EN-PH|
|Rússia|Russo|ru-RU|
|Arábia Saudita|Árabe|ar SA|
|África do Sul|Português|EN-ZA|
|Espanha|Espanhol|es-ES|
|Suécia|Sueco|SV-SE|
|Suíça|Francês|FR-CH|
|Suíça|Alemão|Alemanha-CH|
|Taiwan|Chinês tradicional|zh-TW|
|Turquia|Turco|tr-TR|
|Reino Unido|Português|en-GB|
|Estados Unidos|Português|en-US|
|Estados Unidos|Espanhol|es-EUA|

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre os pontos de extremidade de Pesquisa de Notícias do Bing, consulte [referência do API v7 de notícias pesquisa de imagem](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).
