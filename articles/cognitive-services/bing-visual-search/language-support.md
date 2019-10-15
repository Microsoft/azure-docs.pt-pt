---
title: Suporte ao idioma-API da Pesquisa Visual do Bing
titleSuffix: Azure Cognitive Services
description: Uma lista de idiomas, países e regiões naturais com suporte no API da Pesquisa Visual do Bing. O API da Pesquisa Visual do Bing dá suporte a mais de três dezenas de países/regiões, muitos com mais de um idioma.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: b17341bc234ff3dfecc2c6dcd84ef77116a95d61
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883553"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>Suporte a idiomas e regiões para o API da Pesquisa Visual do Bing

O API da Pesquisa Visual do Bing dá suporte a mais de três dezenas de países/regiões, muitos com mais de um idioma. Cada solicitação deve incluir o país/região do usuário e o idioma de sua escolha. Saber o mercado do usuário ajuda o Bing a retornar os resultados apropriados. Se você não especificar um país/região e idioma, o Bing fará um melhor esforço para determinar o país/região e idioma do usuário. Como os resultados podem conter links para o Bing, saber o país/região e a linguagem pode fornecer uma experiência de usuário do Bing localizada preferencial se o usuário clicar nos links do Bing.

Para especificar o país/região e idioma, defina o `mkt` parâmetro de consulta (Market) para um código da tabela Markets abaixo. O mercado especifica um país/região e um idioma. Se o usuário preferir ver exibir texto em um idioma diferente, defina `setLang` o parâmetro de consulta para o código de idioma apropriado.

Como alternativa, você pode especificar o país/região usando o `cc` parâmetro de consulta. Se você especificar um país/região, também deverá especificar um ou mais códigos de idioma usando o `Accept-Language` cabeçalho http. Os idiomas com suporte variam de acordo com o país/região; Eles são fornecidos para cada país na tabela de mercados.



> [!NOTE]
> As seguintes restrições de mercado se aplicam:
>
> - As anotações de reconhecimento de imagem estão disponíveis apenas em inglês.
> - Receita, compras e páginas-incluindo informações estão disponíveis apenas no mercado en-US.


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
