---
title: Crie clips a com o Azure Media Clipper | Documentos da Microsoft
description: Descrição geral dos Azure Media Clipper, uma ferramenta para criação clipes de mídia dos recursos
services: media-services
keywords: Clip; subclip; codificação; suporte de dados
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 35f1f359b44af00000ccd9047673b80ca541d376
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57998200"
---
# <a name="create-clips-with-azure-media-clipper"></a>Crie clips a com o Azure Media Clipper 

Azure Media Clipper é uma biblioteca de JavaScript gratuita que permite que os desenvolvedores da web fornecer aos usuários uma interface para a criação de clips de suporte de dados. Essa ferramenta pode ser integrada em qualquer página da web e fornece APIs para o carregamento de recursos e submeter tarefas de recorte.

Azure Media Clipper permite-lhe:
- Cortar o pré- slate e pós-imagem fixa ao vivo arquivos 
- Compor destaques de vídeo do AMS eventos em direto, arquivos em direto ou ficheiros VOD fMP4 
- Concatenar vídeos de várias origens 
- Produzir clipes de resumos dos seus recursos de suporte de dados do AMS 
- Recortar vídeos com a precisão do quadro 
- Gerar filtros do manifesto dinâmico sobre existente em direto e de ativos VOD com precisão de grupo de imagens (GOP) 
- Produzir tarefas de codificação contra os ativos na sua conta dos media services

Para pedir novas funcionalidades, fornecer ideias ou comentários, submeter para [UserVoice para serviços de multimédia do Azure](https://aka.ms/amsvoice/). Se tiver problemas específicos, perguntas ou localizar uma linha em todos os bugs, largue o suporte de dados de serviços da Equipe e amcinfo@microsoft.com.

A imagem seguinte ilustra a interface de Clipper: ![Azure Media Clipper](media/media-services-azure-media-clipper-overview/media-services-azure-media-clipper-interface.PNG)

## <a name="release-notes"></a>Notas de versão
Consulte a lista a seguir para a mensagem de blogue Clipper, vários problemas conhecidos e registo de alterações para a versão mais recente do Clipper:
- [Mensagem de blogue](https://azure.microsoft.com/blog/azure-media-clipper/)
- [Lista de problemas conhecidos](https://amp.azure.net/libs/amc/latest/docs/known_issues.html)
- [Registo de alterações](https://amp.azure.net/libs/amc/latest/docs/changelog.html)

## <a name="browser-support"></a>Suporte do browser
Azure Media Clipper é criada usando tecnologias do HTML5 modernos e suporta os seguintes browsers:

- Microsoft Edge 13+
- Internet Explorer 11+
- Chrome 54 +
- Safari 10+
- Firefox 50+

> [!NOTE]
> Apenas HTML5 reprodução de fluxos dos serviços de multimédia do Azure é atualmente suportada.

## <a name="language-support"></a>Suporte de idiomas
O widget Clipper está disponível nos seguintes 18 idiomas:
- Chinês (Simplificado)
- Chinês (Tradicional)
- Checo
- Holandês, Flemish
- Português
- Francês
- Alemão
- Húngaro
- Italiano
- Japonês
- Coreano
- Polaco
- Português (Brasil)
- Português (Portugal)
- Russo
- Espanhol
- Sueco
- Turco

## <a name="next-steps"></a>Passos Seguintes
Para começar a utilizar o Azure Media Clipper, leia os [introdução ao](media-services-azure-media-clipper-getting-started.md) artigo para obter detalhes sobre como implementar o widget.
