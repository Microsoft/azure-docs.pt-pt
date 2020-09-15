---
title: Serviço microsoft tradutor
titlesuffix: Azure Cognitive Services
description: Integre o Tradutor nas suas aplicações, websites, ferramentas e outras soluções para proporcionar experiências multi-idiomas ao utilizador.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 09/11/2020
ms.author: swmachan
keywords: tradutor, tradução de texto, tradução automática, serviço de tradução
ms.openlocfilehash: a3c82546c8aa6467aee0a3e4bb59d68b0123a9c2
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90084537"
---
# <a name="what-is-the-translator-service"></a>O que é o serviço de Tradutor?

O Tradutor é um serviço de tradução automática baseado em nuvem e faz parte da família [Azure Cognitive Services](https://docs.microsoft.com/azure/?pivot=products&panel=ai) de APIs cognitivos usados para construir aplicações inteligentes. O tradutor é fácil de integrar nas suas aplicações, websites, ferramentas e soluções. Permite-lhe adicionar experiências multi-idiomas de utilizadores em [mais de 70 idiomas](languages.md), e pode ser usado em qualquer plataforma de hardware com qualquer sistema operativo para tradução de texto.

## <a name="about-microsoft-translator"></a>Sobre o Microsoft Translator

O tradutor alimenta uma série de produtos e serviços da Microsoft, e é usado por milhares de empresas em todo o mundo nas suas aplicações e fluxos de trabalho, o que permite que os seus conteúdos cheguem a um público global.

A tradução da fala, alimentada por Tradutor, também está disponível através do [serviço Azure Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/). Combina a funcionalidade da API de Discurso do Tradutor e do Serviço de Discurso Personalizado num serviço unificado e totalmente personalizável. 

## <a name="language-support"></a>Suporte de idiomas

O tradutor fornece suporte multi-linguístico para tradução de texto, transliteração, deteção de línguas e dicionários. Veja o [suporte de idiomas](language-support.md) para obter uma lista completa ou aceda à lista de forma programática com a [API REST](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).  

## <a name="microsoft-translator-neural-machine-translation"></a>Tradução Automática Neural do Microsoft Translator

A Tradução Automática Neural (NMT) é o novo padrão para traduções automáticas com tecnologia de IA de alta qualidade. Ela substitui a tecnologia de Tradução Automática de Estatísticas (SMT) legada que atingiu um patamar de qualidade em meados de 2010.

A NMT oferece melhores traduções que a SMT, não apenas do ponto de vista da qualidade de tradução em bruto, mas também porque soam mais fluentes e humanas. O motivo principal desta fluidez deve-se à NMT utilizar o contexto completo de uma frase para traduzir palavras. A SMT utilizou apenas o contexto imediato de algumas palavras antes e depois de cada palavra.

Os modelos da NMT são o núcleo da API e não estão visíveis para os utilizadores finais. A única diferença notável é a melhoria da qualidade da tradução, especialmente em idiomas como chinês, japonês e árabe.

Saiba mais sobre [o funcionamento da NMT.](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="improve-translations-with-custom-translator"></a>Melhorar as traduções com o Tradutor Personalizado

Uma extensão do serviço Tradutor, Tradutor Personalizado pode ser usado em conjunto com o Tradutor para ajudá-lo a personalizar o sistema de tradução neural e melhorar a tradução para a sua terminologia e estilo específicos.

Com o Tradutor Personalizado, pode criar sistemas de tradução que suportam a terminologia utilizada na sua própria empresa ou setor. O seu sistema de tradução personalizado irá então integrar-se facilmente nas suas aplicações, fluxos de trabalho e websites existentes, através de vários tipos de dispositivos, através do Tradutor regular, utilizando o parâmetro da categoria.

Saiba mais sobre [o Tradutor Personalizado.](customization.md)

## <a name="next-steps"></a>Passos seguintes

- [Inscreva-se](translator-text-how-to-signup.md) para uma chave de acesso.
- Experimente o nosso [Quickstart](quickstart-translator.md) para ligar rapidamente para o serviço de Tradutor.
- [A referência da API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) fornece a documentação técnica para as APIs.
- [Detalhes dos preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
