---
title: Criar clipes com o Azure Media Clipper | Microsoft Docs
description: Visão geral do Azure Media Clipper, uma ferramenta para a criação de clipes de mídia de ativos
services: media-services
keywords: clip; subclip; codificação; mídia
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 51f85dffd48e451b477018ef20491f8619a30f25
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685008"
---
# <a name="create-clips-with-azure-media-clipper"></a>Criar clipes com o Azure Media Clipper 

O Azure Media Clipper é uma biblioteca JavaScript gratuita que permite que os desenvolvedores da Web forneçam aos usuários uma interface para a criação de clipes de mídia. Essa ferramenta pode ser integrada a qualquer página da Web e fornece APIs para carregar ativos e enviar trabalhos de recorte.

O Azure Media Clipper permite que você:
- Aparar os arquivos de pintura e do setor de bate-ardósia e ao vivo 
- Compor realces de vídeo de eventos dinâmicos do AMS, arquivos dinâmicos ou arquivos fMP4 VOD 
- Concatenar vídeos de várias fontes 
- Produzir clipes de Resumo de seus ativos de mídia do AMS 
- Recortar vídeos com precisão de quadro 
- Gerar filtros de manifesto dinâmicos por meio de ativos existentes do Live e VOD com precisão de grupo de imagens (GOP) 
- Produzir trabalhos de codificação em relação aos ativos em sua conta de serviços de mídia

Para solicitar novos recursos, forneça ideias ou comentários, envie para o [UserVoice para os serviços de mídia do Azure](https://aka.ms/amsvoice/). Se você tiver problemas específicos, perguntas ou encontrar quaisquer bugs, descarte a equipe dos serviços de mídia em amcinfo@microsoft.com.

A imagem a seguir ilustra a interface do Clipper: ![Azure Media Clipper](media/media-services-azure-media-clipper-overview/media-services-azure-media-clipper-interface.PNG)

## <a name="release-notes"></a>Notas de versão
Consulte a lista a seguir para a postagem do blog Clipper, vários problemas conhecidos e o changelog para a versão mais recente do Clipper:
- [Postagem no blog](https://azure.microsoft.com/blog/azure-media-clipper/)
- [Lista de problemas conhecidos](https://amp.azure.net/libs/amc/latest/docs/known_issues.html)
- [Changelog](https://amp.azure.net/libs/amc/latest/docs/changelog.html)

## <a name="browser-support"></a>Suporte a navegador
O Azure Media Clipper é criado usando tecnologias modernas do HTML5 e dá suporte aos seguintes navegadores:

- Microsoft Edge 13 +
- Internet Explorer 11 +
- Chrome 54 +
- Safari 10 +
- Firefox 50 +

> [!NOTE]
> No momento, somente a reprodução HTML5 de fluxos dos serviços de mídia do Azure é suportada.

## <a name="language-support"></a>Suporte de idiomas
O widget do Clipper está disponível nos 18 idiomas a seguir:
- Chinês (Simplificado)
- Chinês (Tradicional)
- Tcheco
- Holandês, Flamengo
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

## <a name="next-steps"></a>Passos seguintes
Para começar a usar o Azure Media Clipper, leia o artigo [introdução](media-services-azure-media-clipper-getting-started.md) para obter detalhes sobre como implantar o widget.
