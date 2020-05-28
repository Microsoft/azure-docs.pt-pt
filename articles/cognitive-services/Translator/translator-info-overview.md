---
title: O que é o Tradutor? - Tradutor
titlesuffix: Azure Cognitive Services
description: Integre o Tradutor nas suas aplicações, websites, ferramentas e outras soluções para proporcionar experiências de utilizadores multilingidiomas.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 05/26/2020
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: 7044954d2ba67e61172c08c0a77acaaaec3822c6
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83992679"
---
# <a name="what-is-the-translator"></a>O que é o Tradutor?

O Tradutor é fácil de integrar nas suas aplicações, websites, ferramentas e soluções. Pode adicionar experiências de utilizador de vários idiomas em [mais de 60 idiomas](languages.md) e pode ser utilizada em qualquer plataforma de hardware com qualquer sistema operativo para a tradução de idioma de texto para texto.

O Tradutor faz parte da coleção de [Serviços Cognitivos Azure](https://docs.microsoft.com/azure/?pivot=products&panel=ai) de machine learning e algoritmos de IA na nuvem, e é facilmente consumível nos seus projetos de desenvolvimento.

## <a name="about-microsoft-translator"></a>Sobre o Microsoft Translator

O Tradutor é um serviço de tradução automática baseado em nuvem. O serviço principal é o Tradutor, que alimenta uma série de produtos e serviços da Microsoft, e é utilizado por milhares de empresas em todo o mundo nas suas aplicações e fluxos de trabalho, o que permite que o seu conteúdo atinja um público global.

A tradução da fala, alimentada pelo Tradutor, também está disponível através do [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/). Combina a funcionalidade da API do Discurso tradutor e do Serviço de Discurso Personalizado num serviço unificado e totalmente personalizável.O Serviço de Voz está a substituir a API de Voz do Microsoft Translator, que será desativada a 15 de Outubro de 2019.

## <a name="language-support"></a>Suporte de idiomas

O Microsoft Translator suporta vários idiomas para a tradução, transliteração, deteção de idioma e dicionários. Veja o [suporte de idiomas](language-support.md) para obter uma lista completa ou aceda à lista de forma programática com a [API REST](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).  

## <a name="microsoft-translator-neural-machine-translation"></a>Tradução Automática Neural do Microsoft Translator

A Tradução Automática Neural (NMT) é o novo padrão para traduções automáticas com tecnologia de IA de alta qualidade. Ela substitui a tecnologia de Tradução Automática de Estatísticas (SMT) legada que atingiu um patamar de qualidade em meados de 2010.

A NMT oferece melhores traduções que a SMT, não apenas do ponto de vista da qualidade de tradução em bruto, mas também porque soam mais fluentes e humanas. O motivo principal desta fluidez deve-se à NMT utilizar o contexto completo de uma frase para traduzir palavras. A SMT utilizou apenas o contexto imediato de algumas palavras antes e depois de cada palavra.

Os modelos da NMT são o núcleo da API e não estão visíveis para os utilizadores finais. A única diferença notável é a melhoria da qualidade da tradução, especialmente em idiomas como chinês, japonês e árabe.

Saiba mais sobre [como funciona a NMT](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="language-customization"></a>Personalização de idioma

Uma extensão do serviço core Microsoft Tradutor, Custom Tradutor pode ser usado em conjunto com o Tradutor para ajudá-lo a personalizar o sistema de tradução neural e melhorar a tradução para a sua terminologia e estilo específicos.

Com o Tradutor Personalizado, pode criar sistemas de tradução que suportam a terminologia utilizada na sua própria empresa ou setor. O seu sistema de tradução personalizado irá então integrar-se facilmente nas suas aplicações, fluxos de trabalho e websites existentes, em vários tipos de dispositivos, através do Tradutor regular, utilizando o parâmetro de categoria.

Saiba mais sobre a [personalização de idiomas](customization.md)

## <a name="next-steps"></a>Próximos passos

- [Inscrever-se](translator-text-how-to-signup.md) para obter uma chave de acesso.
- [A referência da API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) fornece a documentação técnica para as APIs.
- [Detalhes dos preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
