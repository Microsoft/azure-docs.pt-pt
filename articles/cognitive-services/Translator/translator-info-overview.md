---
title: O que é a API de Texto do Microsoft Translator? -API de texto do translator
titlesuffix: Azure Cognitive Services
description: Integre a API de Texto do Microsoft Translator nas suas aplicações, sites, ferramentas e outras soluções para oferecer experiências de utilizador de vários idiomas.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 02/21/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 79f0474950cf1f48f3552b1ae843a29b5d8dbb24
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57883377"
---
# <a name="what-is-translator-text-api"></a>O que é a API de Texto do Microsoft Translator?

A API de Texto do Microsoft Translator é fácil de integrar nas suas aplicações, sites, ferramentas e soluções. Pode adicionar experiências de utilizador de vários idiomas em [mais de 60 idiomas](languages.md) e pode ser utilizada em qualquer plataforma de hardware com qualquer sistema operativo para a tradução de idioma de texto para texto.

A API de Texto do Microsoft Translator faz parte da coleção de [APIs dos Serviços Cognitivos](https://docs.microsoft.com/azure/#pivot=products&panel=ai) do Azure de aprendizagem automática e algoritmos de IA na cloud, e é prontamente consumível nos seus projetos de desenvolvimento.

## <a name="about-microsoft-translator"></a>Sobre o Microsoft Translator

O Microsoft Translator é um serviço de tradução automática com base na cloud. No serviço de núcleo está a API de Texto do Microsoft Translator, que potencializa vários produtos e serviços da Microsoft, e é utilizada por milhares de empresas em todo o mundo nas suas aplicações e fluxos de trabalho, o que permite que o conteúdo alcance o público global.

A tradução de voz, com tecnologia da API de Texto do Microsoft Translator, também está disponível através do [Serviço de Voz da Microsoft](https://docs.microsoft.com/azure/cognitive-services/speech-service/). Ele combina as funcionalidades da API de voz do Translator e o serviço de voz personalizada num serviço totalmente personalizável e unificado. O Serviço de Voz está a substituir a API de Voz do Microsoft Translator, que será desativada a 15 de Outubro de 2019.

## <a name="language-support"></a>Suporte de idiomas

O Microsoft Translator suporta vários idiomas para a tradução, transliteração, deteção de idioma e dicionários. Veja o [suporte de idiomas](language-support.md) para obter uma lista completa ou aceda à lista de forma programática com a [API REST](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).  

## <a name="microsoft-translator-neural-machine-translation"></a>Tradução Automática Neural do Microsoft Translator

A Tradução Automática Neural (NMT) é o novo padrão para traduções automáticas com tecnologia de IA de alta qualidade. Ela substitui a tecnologia de Tradução Automática de Estatísticas (SMT) legada que atingiu um patamar de qualidade em meados de 2010.

A NMT oferece melhores traduções que a SMT, não apenas do ponto de vista da qualidade de tradução em bruto, mas também porque soam mais fluentes e humanas. O motivo principal desta fluidez deve-se à NMT utilizar o contexto completo de uma frase para traduzir palavras. A SMT utilizou apenas o contexto imediato de algumas palavras antes e depois de cada palavra.

Os modelos da NMT são o núcleo da API e não estão visíveis para os utilizadores finais. A única diferença notável é a melhoria da qualidade da tradução, especialmente em idiomas como chinês, japonês e árabe.

Saiba mais sobre [como funciona a NMT](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="language-customization"></a>Personalização de idioma

Pode ser utilizada uma extensão do principal serviço do Microsoft Translator, o Tradutor Personalizado, em conjunto com a API de Texto do Translator para ajudar a personalizar o sistema de tradução neural e melhorar a tradução para a sua terminologia e estilo específicos.

Com o Tradutor Personalizado, pode criar sistemas de tradução que suportam a terminologia utilizada na sua própria empresa ou setor. O sistema de tradução personalizada será, em seguida, integrado facilmente nas suas aplicações existentes, fluxos de trabalho e sites nos vários tipos de dispositivos, através da API Texto do Microsoft Translator normal, com o parâmetro de categoria.

Saiba mais sobre a [personalização de idiomas](customization.md)

## <a name="next-steps"></a>Passos Seguintes

- [Inscrever-se](translator-text-how-to-signup.md) para obter uma chave de acesso.
- [Referência da API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) fornece a documentação técnica para as APIs.
- [Detalhes dos preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
