---
title: Suporte linguístico - Bing Visual Search API
titleSuffix: Azure Cognitive Services
description: Uma lista de línguas naturais, países e regiões que são apoiadas pela API de Pesquisa Visual Bing. A API de Pesquisa Visual Bing suporta mais de três dezenas de países/regiões, muitos com mais de uma língua.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: 6d93b6d024fc966f3b260602f4798a2d77669a35
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96501163"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>Suporte linguístico e regional para a API de Pesquisa Visual de Bing

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Bing Visual Search API suporta mais de três dezenas de países/regiões, muitos com mais de uma língua. Cada pedido deve incluir o país/região do utilizador e a linguagem de eleição. Conhecer o mercado do utilizador ajuda a Bing a devolver os resultados apropriados. Se não especificar um país/região e uma língua, bing faz o melhor esforço para determinar o país/região e a língua do utilizador. Como os resultados podem conter ligações com Bing, conhecer o país/região e a língua pode fornecer uma experiência de utilizador Bing localizada preferencial se o utilizador clicar nos links Bing.

Para especificar o país/região e a língua, desacordo o `mkt` parâmetro de consulta (mercado) para um código a partir da tabela **Mercados** abaixo. O mercado especifica tanto um país/região como uma língua. Se o utilizador preferir ver o texto do visor num idioma diferente, desate `setLang` o parâmetro de consulta para o código de idioma apropriado.

Em alternativa, pode especificar o país/região utilizando o `cc` parâmetro de consulta. Se especificar um país/região, também deve especificar um ou mais códigos linguísticos utilizando o `Accept-Language` cabeçalho HTTP. As línguas apoiadas variam por país/região; são dadas para cada país na tabela Mercados.



> [!NOTE]
> Aplicam-se as seguintes restrições ao mercado:
>
> - As anotações de reconhecimento de imagem estão disponíveis apenas em inglês.
> - Receitas, compras e insights incluindo páginas estão disponíveis apenas no mercado en-US.


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