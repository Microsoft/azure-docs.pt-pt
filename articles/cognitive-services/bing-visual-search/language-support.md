---
title: Suporte linguístico - Bing Visual Search API
titleSuffix: Azure Cognitive Services
description: Uma lista de línguas naturais, países e regiões que são apoiados pela API de Pesquisa Visual bing. A API de Pesquisa Visual Bing suporta mais de três dezenas de países/regiões, muitos com mais de uma língua.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: b17341bc234ff3dfecc2c6dcd84ef77116a95d61
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "68883553"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>Suporte de língua e região para a API de Pesquisa Visual Bing

A Bing Visual Search API suporta mais de três dezenas de países/regiões, muitos com mais de uma língua. Cada pedido deve incluir o país/região do utilizador e a linguagem de eleição. Conhecer o mercado do utilizador ajuda bing a devolver os resultados apropriados. Se não especificar um país/região e uma língua, bing faz o melhor esforço para determinar o país/região e a língua do utilizador. Como os resultados podem conter links para Bing, saber que o país/região e a língua podem fornecer uma experiência de utilizador de Bing localizada preferida se o utilizador clicar nos links Bing.

Para especificar o país/região `mkt` e a língua, coloque o parâmetro de consulta (mercado) num código da tabela **Markets** abaixo. O mercado especifica tanto um país/região como uma língua. Se o utilizador preferir ver texto de exibição num idioma diferente, defina `setLang` o parâmetro de consulta para o código de idioma apropriado.

Em alternativa, pode especificar o país/região utilizando o `cc` parâmetro de consulta. Se especificar um país/região, também deve especificar um `Accept-Language` ou mais códigos linguísticos utilizando o cabeçalho HTTP. As línguas apoiadas variam por país/região; são dadas para cada país na tabela Mercados.



> [!NOTE]
> Aplicam-se as seguintes restrições ao mercado:
>
> - As anotações de reconhecimento de imagem estão disponíveis apenas em inglês.
> - Receitas, compras e páginas que incluam insights estão disponíveis apenas no mercado en-EUA.


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
|RAE de Hong Kong|HK|
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
|Estados Unidos|EUA|


## <a name="markets"></a>Mercados

|Pais/região|Idioma|Código de Mercado|
|-------|--------|-----------|
|Argentina|Espanhol|es-AR|
|Austrália|Inglês|en-UA|
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
|RAE de Hong Kong|Chinês Tradicional|zh-HK|
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
|Estados Unidos|Inglês|pt-PT|
|Estados Unidos|Espanhol|es-EUA|
