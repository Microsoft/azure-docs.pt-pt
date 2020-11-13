---
title: Suporte linguístico - Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Descubra quais países/regiões e línguas são apoiados pela API de Pesquisa de Imagem de Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 3057a118117f4f293ec5f90405a4bbd82d7a353d
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592078"
---
# <a name="language-and-region-support-for-the-bing-image-search-api"></a>Suporte linguístico e regional para a API de Pesquisa de Imagem de Bing

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

A API de Pesquisa de Imagem Bing suporta mais de três dezenas de países/regiões, muitos com mais de uma língua. Especificar um país/região com uma consulta serve principalmente para refinar os resultados da pesquisa com base em interesses nesse país/região. Além disso, os resultados podem conter ligações com Bing, e estas ligações podem localizar a experiência do utilizador Bing de acordo com o país/regiões ou idiomas especificados.

Para especificar o país/região e a língua, desacordo o `mkt` parâmetro de consulta (mercado) para um código a partir da tabela **Mercados** abaixo. O mercado especifica tanto um país/região como uma língua. Se o utilizador preferir ver o texto do visor num idioma diferente, desate `setLang` o parâmetro de consulta para o código de idioma apropriado.

Em alternativa, pode especificar o país/região utilizando o `cc` parâmetro de consulta. Se especificar um país/região, também deve especificar um ou mais códigos linguísticos utilizando o `Accept-Language` cabeçalho HTTP. As línguas apoiadas variam por país/região; são dados para cada país/região na tabela Mercados.

> [!NOTE]
> A API de Imagens de Tendência suporta apenas os seguintes mercados:
> - en-US (Inglês, Estados Unidos)
> - en-CA (Inglês, Canadá)
> - en-AU (Inglês, Austrália)
> - zh-CN (Chinês, China)

## <a name="countriesregions"></a>Países/Regiões

|Pais/região|Código|
|-------|----|
|Argentina|AR|
|Austrália|AU|
|Áustria|AT|
|Bélgica|BE|
|Brasil|BR|
|Canadá|CA|
|Chile|CL|
|Dinamarca|DK|
|Finlândia|FI|
|França|FR|
|Alemanha|DE|
|R.A.E. de Hong Kong|HK|
|Índia|IN|
|Indonésia|ID|
|Itália|TI|
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
|Estados Unidos da América|EUA|


## <a name="markets"></a>Mercados

|Pais/região|Linguagem|Código de Mercado|
|-------|--------|-----------|
|Argentina|Espanhol|es-AR|
|Austrália|Inglês|en-AU|
|Áustria|Alemão|de-AT|
|Bélgica|Neerlandês|nl-BE|
|Bélgica|Francês|fr-BE|
|Brasil|Português|pt-BR|
|Canadá|Inglês|en-CA|
|Canadá|Francês|fr-CA|
|Chile|Espanhol|es-CL|
|Dinamarca|Dinamarquês|da-DK|
|Finlândia|Finlandês|fi-FI|
|França|Francês|fr-FR|
|Alemanha|Alemão|de-DE|
|R.A.E. de Hong Kong|Chinês Tradicional|zh-HK|
|Índia|Inglês|en-IN|
|Indonésia|Inglês|en-ID|
|Itália|Italiano|it-IT|
|Japão|Japonês|ja-JP|
|Coreia|Coreano|ko-KR|
|Malásia|Inglês|en-MY|
|México|Espanhol|es-MX|
|Países Baixos|Neerlandês|nl-NL|
|Nova Zelândia|Inglês|en-NZ|
|China|Chinês|zh-CN|
|Polónia|Polaco|pl-PL|
|Portugal|Português|pt-PT|
|Filipinas|Inglês|en-PH|
|Rússia|Russo|ru-RU|
|Arábia Saudita|Árabe|ar-SA|
|África do Sul|Inglês|en-ZA|
|Espanha|Espanhol|es-ES|
|Suécia|Sueco|sv-SE|
|Suíça|Francês|fr-CH|
|Suíça|Alemão|de-CH|
|Taiwan|Chinês Tradicional|zh-TW|
|Turquia|Turco|tr-TR|
|Reino Unido|Inglês|en-GB|
|Estados Unidos da América|Inglês|en-PT|
|Estados Unidos da América|Espanhol|es-EUA|

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os pontos finais da Bing News Search, consulte [a referência API de pesquisa de imagem de notícias v7](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).