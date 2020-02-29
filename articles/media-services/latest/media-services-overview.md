---
title: Visão geral do Azure Media Services v3
titleSuffix: Azure Media Services
description: Uma visão geral de alto nível do Azure Media Services v3 com ligações a quickstarts, tutoriais e amostras de código.
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
ms.date: 02/03/2020
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: df1602fe82b4a625dd5f01de2bd7236816fbb0ac
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164666"
---
# <a name="azure-media-services-v3-overview"></a>Visão geral do Azure Media Services v3

Os Serviços de Multimédia do Azure são uma plataforma baseada na cloud que lhe permite criar soluções que possibilitam a transmissão em vídeo com qualidade de transmissão, melhorar a acessibilidade e distribuição, analisar conteúdo e muito mais. Quer seja um programador de aplicações, um call center, uma agência governamental ou uma empresa de entretenimento, o Media Services ajuda-o a criar aplicações que oferecem experiências mediáticas de excelente qualidade a grandes audiências nos dispositivos e navegadores móveis mais populares de hoje.

Os Serviços de Media v3 SDKs baseiam-se na [Especificação OpenAPI (Swagger)](https://aka.ms/ams-v3-rest-sdk)dos Media Services V3 .

> [!NOTE]
> Atualmente, não é possível utilizar o portal Azure para gerir os recursos v3. Utilize a [API REST](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref) ou um dos [SDKs](media-services-apis-overview.md#sdks) suportados.

## <a name="compliance-privacy-and-security"></a>Conformidade, Privacidade e Segurança

Como um lembrete importante, você deve cumprir todas as leis aplicáveis na sua utilização da Azure Media Services, e você não pode usar Serviços de Media ou qualquer serviço Azure de uma forma que viole os direitos dos outros, ou que pode ser prejudicial para os outros.

Antes de enviar qualquer vídeo/imagem para os Media Services, deve ter todos os direitos adequados para utilizar o vídeo/imagem, incluindo, sempre que tal seja exigido por lei, todos os consentimentos necessários de indivíduos (se houver) no vídeo/imagem, para uso, processamento e armazenamento dos seus dados nos Media Services e Azure. Algumas jurisdições podem impor requisitos legais especiais para a recolha, processamento e armazenamento on-line de determinadas categorias de dados, tais como dados biométricos. Antes de utilizar os Serviços de Media e azure para o tratamento e armazenamento de quaisquer dados sujeitos a requisitos legais especiais, deve garantir o cumprimento de quaisquer requisitos legais que possam ser aplicáveis a Si.

Para saber sobre conformidade, privacidade e segurança nos Serviços de Media visite o Microsoft [Trust Center](https://www.microsoft.com/trust-center/?rtc=1). Para as obrigações de privacidade da Microsoft, práticas de tratamento e retenção de dados, incluindo como eliminar os seus dados, por favor, consulte a Declaração de [Privacidade](https://privacy.microsoft.com/PrivacyStatement)da Microsoft, os Termos dos [Serviços Online](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST") e o Tratamento de Dados [Addendum](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Ao utilizar os Serviços de Comunicação Social, concorda em ficar vinculado pela OST, DPA e pela Declaração de Privacidade.
 
## <a name="what-can-i-do-with-media-services"></a>O que posso fazer com os Serviços de Multimédia?

A Media Services permite-lhe construir uma variedade de fluxos de trabalho mediáticos na nuvem. Alguns exemplos do que pode fazer com os Serviços de Media incluem:

* Fornecer vídeos em diferentes formatos para que possam ser reproduzidos numa ampla variedade de dispositivos e browsers. Tanto para a entrega a pedido como para o streaming ao vivo a vários clientes (dispositivos móveis, TV, PC, e assim por diante), o conteúdo de vídeo e áudio precisa de ser codificado e embalado adequadamente. Para ver como disponibilizar e transmitir tal conteúdo, veja [Quickstart: Encode and stream files](stream-files-dotnet-quickstart.md) (Início Rápido: Codificar e transmitir ficheiros).
* Transmita eventos desportivos ao vivo para uma grande audiência online, como futebol, basebol, esportes universitários e de liceu, e muito mais.
* Transmitir reuniões e eventos públicos, como câmaras municipais, reuniões da câmara municipal e órgãos legislativos.
* Analise o conteúdo de áudio ou de vídeos registados. Por exemplo, para alcançar uma maior satisfação do cliente, as organizações podem extrair a conversão de voz em texto e criar índices de pesquisa e dashboards. Em seguida, podem extrair informações relativas a queixas comuns, fontes de queixas e outros dados relevantes.
* Crie um serviço de subscrição de vídeo e transmita conteúdo DRM protegido quando um cliente (por exemplo, um estúdio cinematográfico) necessita de restringir o acesso e a utilização de obras proprietárias protegidas por direitos de autor.
* Disponibilize conteúdo offline para reprodução em aviões, comboios e automóveis. Um cliente poderá ter de transferir o conteúdo para o telemóvel ou tablet para reprodução posterior quando se prevê que esteja desligado da rede.
* Implementar uma plataforma de vídeo educacional de e-learning com azure Media Services e [Azure Cognitive Services APIs](https://docs.microsoft.com/azure/#pivot=products&panel=ai) para legendagem fala-a-texto, traduzindo para várias línguas, e assim por diante.
* Utilize o Azure Media Services juntamente com as APIs dos [Serviços Cognitivos Azure](https://docs.microsoft.com/azure/#pivot=products&panel=ai) para adicionar legendas e legendas a vídeos para atender a um público mais vasto (por exemplo, pessoas com deficiências auditivas ou pessoas que queiram ler numa língua diferente).
* Habilitar o CDN azure a alcançar uma grande escala para lidar melhor com cargas elevadas instantâneas (por exemplo, o início de um evento de lançamento do produto).

## <a name="how-can-i-get-started-with-v3"></a>Como posso começar a utilizar a v3? 

Saiba como codificar e embalar conteúdos, transmitir vídeos a pedido, transmitir em direto e analisar os seus vídeos com o Media Services v3. Tutoriais, referências de API e outra documentação mostram-lhe como fornecer de forma segura transmissões em fluxo de vídeo ou áudio a pedido e live dimensionáveis para milhões de utilizadores.

> [!TIP]
> Antes de começar a desenvolver, reveja:<br/>* [Conceitos fundamentais](concepts-overview.md) (incude conceitos importantes, como embalagens, codificação e proteção)<br/>* [Desenvolvimento com Serviços de Media v3 APIs](media-services-apis-overview.md) (inclui informações sobre o acesso a APIs, convenções de nomeação, e assim por diante)

### <a name="sdks"></a>SDKs

Comece a desenvolver-se com [os SDKs de clientes V3 da Azure Media Services.](media-services-apis-overview.md#sdks)

### <a name="quickstarts"></a>Inícios rápidos  

Os quickstarts mostram instruções fundamentais do dia 1 para os novos clientes experimentarem rapidamente os Serviços de Media.

* [Stream ficheiros de vídeo - .NET](stream-files-dotnet-quickstart.md)
* [Stream ficheiros de vídeo - CLI](stream-files-cli-quickstart.md)
* [Stream video files - Node.js](stream-files-nodejs-quickstart.md)

### <a name="tutorials"></a>Tutoriais

Os tutoriais mostram procedimentos baseados em cenários para algumas das principais tarefas dos Serviços de Media.

* [Codificar ficheiro remoto e vídeo de streaming - REST](stream-files-tutorial-with-rest.md)
* [Codificar ficheiro carregado e transmitir vídeo - .NET](stream-files-tutorial-with-api.md)
* [Stream live - .NET](stream-live-tutorial-with-api.md)
* [Analise o seu vídeo - .NET](analyze-videos-tutorial-with-api.md)
* [Encriptação dinâmica AES-128 - .NET](protect-with-aes128.md)

### <a name="samples"></a>Amostras

Utilize [este navegador de amostras](https://docs.microsoft.com/samples/browse/?products=azure-media-services) para navegar em amostras de código do Azure Media Services.

### <a name="how-to-guides"></a>Guias de procedimentos

Como orientar os guias contêm amostras de código que demonstram como completar uma tarefa. Nesta secção, você encontrará muitos exemplos. Aqui estão alguns deles:

* [Criar uma conta - CLI](create-account-cli-how-to.md)
* [ACESSO APIs - CLI](access-api-cli-how-to.md)
* [Codificar com HTTPS como entrada de emprego - .NET](job-input-from-http-how-to.md)  
* [Monitorizar eventos - Portal](monitor-events-portal-how-to.md)
* [Criptografe dinamicamente com multi-DRM - .NET](protect-with-drm.md) 
* [Como codificar com uma transformação personalizada - CLI](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Confira o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Serviços de Media.

## <a name="next-steps"></a>Passos seguintes

[Conheça conceitos fundamentais](concepts-overview.md)
