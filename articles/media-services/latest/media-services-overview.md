---
title: Descrição geral dos Serviços de Multimédia do Azure v3 | Microsoft Docs
description: Este artigo fornece uma descrição geral de alto nível dos Serviços de Multimédia, além de ligações para artigos para obter mais detalhes.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: serviços de multimédia do azure, transmissão, difusão, em direto, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 09/17/2019
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: c6359cf2401ff198b0242243dbf6dfdf2e35ce47
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244028"
---
# <a name="azure-media-services-v3-overview"></a>Visão geral dos serviços de mídia do Azure v3

Os Serviços de Multimédia do Azure são uma plataforma baseada na cloud que lhe permite criar soluções que possibilitam a transmissão em vídeo com qualidade de transmissão, melhorar a acessibilidade e distribuição, analisar conteúdo e muito mais. Quer seja um programador de aplicações, um centro de atendimento, um organismo público, uma empresa de entretenimento, os Serviços de Multimédia ajudam-no a criar aplicações que permitem experiências de multimédia de qualidade extraordinária para um grande público nos browsers e nos dispositivos móveis atualmente mais populares. 

Os SDKs dos serviços de mídia v3 são baseados na [especificação de openapi (Swagger) dos serviços de mídia v3](https://aka.ms/ams-v3-rest-sdk).

> [!NOTE]
> Atualmente, não pode utilizar o portal do Azure para gerir recursos v3. Utilize a [API REST](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref) ou um dos [SDKs](media-services-apis-overview.md#sdks) suportados.

## <a name="what-can-i-do-with-media-services"></a>O que posso fazer com os Serviços de Multimédia?

Os Serviços de Multimédia permitem-lhe criar uma variedade de fluxos de trabalho de multimédia na cloud. Veja a seguir alguns exemplos do pode fazer com os Serviços de Multimédia.  

* Fornecer vídeos em diferentes formatos para que possam ser reproduzidos numa ampla variedade de dispositivos e browsers. Para disponibilização de transmissão tanto a pedido como em direto para vários clientes (dispositivos móveis, TV, PC, etc.), o conteúdo de áudio e vídeo tem de ser codificado e empacotado adequadamente. Para ver como disponibilizar e transmitir tal conteúdo, veja [Quickstart: Encode and stream files](stream-files-dotnet-quickstart.md) (Início Rápido: Codificar e transmitir ficheiros).
* Transmita em direto eventos desportivos para uma grande audiência online, tais como jogos de futebol, basebol, eventos desportivos escolares e muito mais. 
* Difunda sessões e eventos públicos como, por exemplo, reuniões municipais, assembleias municipais e órgãos legisladores.
* Analise o conteúdo de áudio ou de vídeos registados. Por exemplo, para alcançar uma maior satisfação do cliente, as organizações podem extrair a conversão de voz em texto e criar índices de pesquisa e dashboards. Em seguida, podem extrair informações relativas a queixas comuns, fontes de queixas e outros dados relevantes.
* Crie um serviço de subscrição de vídeo e transmita conteúdo DRM protegido quando um cliente (por exemplo, um estúdio cinematográfico) necessita de restringir o acesso e a utilização de obras proprietárias protegidas por direitos de autor.
* Disponibilize conteúdo offline para reprodução em aviões, comboios e automóveis. Um cliente poderá ter de transferir o conteúdo para o telemóvel ou tablet para reprodução posterior quando se prevê que esteja desligado da rede.
* Implemente uma plataforma de vídeo de e-learning educativo com os Serviços de Multimédia do Azure e as [APIs Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/#pivot=products&panel=ai) para legendagem de conversão de voz em texto, tradução para vários idiomas, etc. 
* Use os serviços de mídia do Azure junto com o [azure API de serviços cognitivos](https://docs.microsoft.com/azure/#pivot=products&panel=ai) para adicionar legendas e legendas a vídeos para atender a um público mais amplo (por exemplo, pessoas com deficiências auditivas ou pessoas que desejam ler em um idioma diferente).
* Habilite a CDN do Azure para obter uma grande escala para lidar melhor com cargas de alta interativas (por exemplo, o início de um evento de lançamento do produto). 

## <a name="how-can-i-get-started-with-v3"></a>Como posso começar a utilizar a v3? 

Saiba como codificar e criar pacotes de conteúdos, transmitir vídeos em fluxo a pedido, transmitir em direto e analisar os seus vídeos com os Serviços de Multimédia v3. Tutoriais, referências de API e outra documentação mostram-lhe como fornecer de forma segura transmissões em fluxo de vídeo ou áudio a pedido e live dimensionáveis para milhões de utilizadores.

> [!TIP]
> Antes de começar a desenvolver, examine:<br/>[conceitos fundamentais](concepts-overview.md) do *  (inclui conceitos importantes: empacotamento, codificação, proteção, etc.)<br/>* [desenvolvendo com APIs dos serviços de mídia v3](media-services-apis-overview.md) (inclui informações sobre como acessar APIs, convenções de nomenclatura, etc.)

### <a name="quickstarts"></a>Inícios rápidos  

Os guias de início rápido mostram as instruções fundamentais do dia 1 para que novos clientes experimentem rapidamente os serviços de mídia.

* [Transmitir arquivos de vídeo-.NET](stream-files-dotnet-quickstart.md)
* [Transmitir arquivos de vídeo-CLI](stream-files-cli-quickstart.md)
* [Arquivos de vídeo de fluxo – node. js](stream-files-nodejs-quickstart.md)
    
### <a name="tutorials"></a>Tutoriais 

Os tutoriais mostram procedimentos baseados em cenários para algumas das principais tarefas dos serviços de mídia.

* [Codificar o arquivo remoto e o vídeo de fluxo – REST](stream-files-tutorial-with-rest.md)
* [Codificar arquivo carregado e vídeo de fluxo-.NET](stream-files-tutorial-with-api.md)
* [Fluxo dinâmico-.NET](stream-live-tutorial-with-api.md)
* [Analise seu vídeo-.NET](analyze-videos-tutorial-with-api.md)
* [Criptografia dinâmica AES-128-.NET](protect-with-aes128.md)
    
### <a name="samples"></a>Exemplos

Use [este navegador de exemplos](https://docs.microsoft.com/samples/browse/?products=azure-media-services) para procurar exemplos de código dos serviços de mídia do Azure.

### <a name="how-to-guides"></a>Guias de procedimentos

Os artigos contêm exemplos de código que demonstram como concluir uma tarefa. Nesta seção, você encontrará muitos exemplos, aqui estão apenas alguns deles:

* [Criar uma conta-CLI](create-account-cli-how-to.md)
* [APIs de acesso-CLI](access-api-cli-how-to.md)
* [Codificar com HTTPS como entrada de trabalho-.NET](job-input-from-http-how-to.md)  
* [Monitorar eventos-Portal](monitor-events-portal-how-to.md)
* [Criptografar dinamicamente com multi-DRM-.NET](protect-with-drm.md) 
* [Como codificar com uma Transform-CLI personalizada](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, envie comentários, obtenha atualizações

Confira o artigo [da Comunidade dos serviços de mídia do Azure](media-services-community.md) para ver diferentes maneiras que você pode fazer perguntas, fornecer comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre os conceitos fundamentais](concepts-overview.md)

