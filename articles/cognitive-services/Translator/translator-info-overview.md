---
title: Serviço microsoft tradutor
titlesuffix: Azure Cognitive Services
description: Integre o Tradutor nas suas aplicações, websites, ferramentas e outras soluções para proporcionar experiências multi-idiomas ao utilizador.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.subservice: translator-text
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: tradutor, tradução de texto, tradução automática, serviço de tradução
ms.openlocfilehash: ec76aa7554110b7440eb825f2d5e86ae2da6baa2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657727"
---
# <a name="what-is-the-translator-service"></a>O que é o serviço de Tradutor?

O Tradutor é um serviço de tradução automática baseado em nuvem e faz parte da família [Azure Cognitive Services](../../index.yml?panel=ai&pivot=products) de APIs cognitivos usados para construir aplicações inteligentes. O tradutor é fácil de integrar nas suas aplicações, websites, ferramentas e soluções. Permite-lhe adicionar experiências multi-idiomas de utilizador em [90 idiomas e dialetos](./language-support.md) e pode ser usado para tradução de texto com qualquer sistema operativo.

Esta documentação contém os seguintes tipos de artigos:  

* [**Os quickstarts**](quickstart-translator.md) estão a iniciar instruções para guiá-lo através da realização de pedidos ao serviço.  
* [**Os guias de como fazer**](translator-how-to-signup.md) contêm instruções para a utilização do serviço de formas mais específicas ou personalizadas.  
* [**Os conceitos**](character-counts.md) fornecem explicações aprofundadas sobre a funcionalidade e funcionalidades do serviço.  
* [**Os tutoriais**](tutorial-wpf-translation-csharp.md) são guias mais longos que mostram como usar o serviço como componente em soluções de negócio mais amplas.  


## <a name="about-microsoft-translator"></a>Sobre o Microsoft Translator

O tradutor alimenta muitos produtos e serviços da Microsoft, e é usado por milhares de empresas em todo o mundo nas suas aplicações e fluxos de trabalho.

A tradução da fala, alimentada por Tradutor, também está disponível através do [serviço Azure Speech](../speech-service/index.yml). Combina a funcionalidade da API de Discurso do Tradutor e do Serviço de Discurso Personalizado num serviço unificado e totalmente personalizável. 

## <a name="language-support"></a>Suporte de idiomas

O tradutor fornece suporte multi-linguístico para tradução de texto, transliteração, deteção de línguas e dicionários. Veja o [suporte de idiomas](language-support.md) para obter uma lista completa ou aceda à lista de forma programática com a [API REST](./reference/v3-0-languages.md).  

## <a name="microsoft-translator-neural-machine-translation"></a>Tradução Automática Neural do Microsoft Translator

A Tradução Automática Neural (NMT) é o novo padrão para traduções automáticas com tecnologia de IA de alta qualidade. Ela substitui a tecnologia de Tradução Automática de Estatísticas (SMT) legada que atingiu um patamar de qualidade em meados de 2010.

A NMT oferece melhores traduções que a SMT, não apenas do ponto de vista da qualidade de tradução em bruto, mas também porque soam mais fluentes e humanas. O motivo principal desta fluidez deve-se à NMT utilizar o contexto completo de uma frase para traduzir palavras. A SMT utilizou apenas o contexto imediato de algumas palavras antes e depois de cada palavra.

Os modelos da NMT são o núcleo da API e não estão visíveis para os utilizadores finais. A única diferença notável é a melhoria da qualidade da tradução, especialmente em idiomas como chinês, japonês e árabe.

Saiba mais sobre [o funcionamento da NMT.](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="improve-translations-with-custom-translator"></a>Melhorar as traduções com o Tradutor Personalizado

 [Custom Tradutor](customization.md), uma extensão do serviço Tradutor, pode ser usado para personalizar o sistema de tradução neural e melhorar a tradução para a sua terminologia e estilo específicos.

Com o Custom Tradutor, pode construir sistemas de tradução para lidar com a terminologia utilizada no seu próprio negócio ou indústria. O seu sistema de tradução personalizado pode facilmente integrar-se com as suas aplicações, fluxos de trabalho, websites e dispositivos existentes, através do Tradutor regular, utilizando o parâmetro da categoria.

## <a name="next-steps"></a>Passos seguintes

- [Crie um serviço de Tradutor](./translator-how-to-signup.md) para obter as suas chaves de acesso e ponto final.
- Experimente o nosso [Quickstart](quickstart-translator.md) para ligar rapidamente para o serviço de Tradutor.
- [A referência da API](./reference/v3-0-reference.md) fornece a documentação técnica para as APIs.
- [Detalhes dos preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
