---
title: Descrição geral dos Serviços de Multimédia do Azure v3
titleSuffix: Azure Media Services
description: Uma visão geral de alto nível do Azure Media Services v3 com links para quickstarts, tutoriais e amostras de código.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: serviços de multimédia do azure, transmissão, difusão, em direto, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: mvc
ms.openlocfilehash: 01ec0c7c0d2d060bbd930a099fe3db54de6cb150
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93308971"
---
# <a name="azure-media-services-v3-overview"></a>Descrição geral dos Serviços de Multimédia do Azure v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Os Serviços de Multimédia do Azure são uma plataforma baseada na cloud que lhe permite criar soluções que possibilitam a transmissão em vídeo com qualidade de transmissão, melhorar a acessibilidade e distribuição, analisar conteúdo e muito mais. Quer seja um desenvolvedor de aplicações, um call center, uma agência governamental ou uma empresa de entretenimento, a Media Services ajuda-o a criar aplicações que oferecem experiências mediáticas de excelente qualidade para grandes audiências nos dispositivos móveis e navegadores mais populares da atualidade.

Os Serviços de Comunicação Social v3 SDKs baseiam-se na [Especificação v3 OpenAPI (Swagger) dos Serviços de Comunicação](https://aka.ms/ams-v3-rest-sdk)Social .

> [!NOTE]
> Atualmente, pode utilizar o [portal Azure](https://portal.azure.com/) para: gerir os Serviços de Media v3 [Live Events,](live-events-outputs-concept.md)ver (não gerir) v3 [Ativos,](assets-concept.md) [obter informações sobre o acesso às APIs.](./access-api-howto.md) Para todas as outras tarefas de gestão (por exemplo, [Transformações e Empregos](transforms-jobs-concept.md) e [Proteção de Conteúdos),](content-protection-overview.md)utilize o [REST API](/rest/api/media/), [CLI](/cli/azure/ams)ou um dos [SDKs suportados.](media-services-apis-overview.md#sdks)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Conformidade, Privacidade e Segurança

Como um importante lembrete, deve cumprir todas as leis aplicáveis na sua utilização da Azure Media Services, e não poderá utilizar os Serviços de Comunicação Social ou qualquer serviço Azure de forma a violar os direitos de outros, ou que possam ser prejudiciais para os outros.

Antes de enviar qualquer vídeo/imagem para os Serviços de Comunicação, deve ter todos os direitos adequados de utilização do vídeo/imagem, incluindo, sempre que tal seja exigido por lei, todos os consentimentos necessários de indivíduos (se houver) no vídeo/imagem, para a utilização, processamento e armazenamento dos seus dados nos Serviços de Comunicação Social e Azure. Algumas jurisdições podem impor requisitos legais especiais para a recolha, tratamento e armazenamento online de determinadas categorias de dados, tais como dados biométricos. Antes de utilizar os Media Services e a Azure para o processamento e armazenamento de quaisquer dados sujeitos a requisitos legais especiais, deve garantir o cumprimento de quaisquer requisitos legais que possam ser aplicáveis a Si.

Para saber mais sobre a conformidade, privacidade e segurança nos Serviços de Media, visite o Microsoft [Trust Center.](https://www.microsoft.com/trust-center/?rtc=1) Para as obrigações de privacidade da Microsoft, práticas de tratamento e retenção de dados, incluindo como eliminar os seus dados, por favor reveja a Declaração de [Privacidade](https://privacy.microsoft.com/PrivacyStatement)da Microsoft, [os Termos dos Serviços Online](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST") e a [adenda de processamento de dados](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Ao utilizar os Serviços de Comunicação Social, concorda em ficar vinculado pela OST, DPA e pela Declaração de Privacidade.
 
## <a name="what-can-i-do-with-media-services"></a>O que posso fazer com os Serviços de Multimédia?

Os Serviços de Mídia permitem-lhe construir uma variedade de fluxos de trabalho de mídia na nuvem. Alguns exemplos do que pode fazer com os Serviços de Media incluem:

* Fornecer vídeos em diferentes formatos para que possam ser reproduzidos numa ampla variedade de dispositivos e browsers. Para a entrega em streaming a pedido e ao vivo a vários clientes (dispositivos móveis, TV, PC, e assim por diante), o conteúdo de vídeo e áudio precisa de ser codificado e embalado adequadamente. Para ver como disponibilizar e transmitir tal conteúdo, veja [Quickstart: Encode and stream files](stream-files-dotnet-quickstart.md) (Início Rápido: Codificar e transmitir ficheiros).
* Transmita eventos desportivos ao vivo para um grande público online, como futebol, basebol, esportes universitários e de liceu, e muito mais.
* Transmitir reuniões públicas e eventos, como câmaras municipais, reuniões da câmara municipal e órgãos legislativos.
* Analise o conteúdo de áudio ou de vídeos registados. Por exemplo, para alcançar uma maior satisfação do cliente, as organizações podem extrair a conversão de voz em texto e criar índices de pesquisa e dashboards. Em seguida, podem extrair informações relativas a queixas comuns, fontes de queixas e outros dados relevantes.
* Crie um serviço de subscrição de vídeo e transmita conteúdo DRM protegido quando um cliente (por exemplo, um estúdio cinematográfico) necessita de restringir o acesso e a utilização de obras proprietárias protegidas por direitos de autor.
* Disponibilize conteúdo offline para reprodução em aviões, comboios e automóveis. Um cliente poderá ter de transferir o conteúdo para o telemóvel ou tablet para reprodução posterior quando se prevê que esteja desligado da rede.
* Implementar uma plataforma de vídeo de aprendizagem de e-learning educacional com a Azure Media Services e [a Azure Cognitive Services APIs](../../index.yml?pivot=products&panel=ai) para legendas de voz a texto, traduzindo para multi-línguas, e assim por diante.
* Use o Azure Media Services juntamente com [a Azure Cognitive Services APIs](../../index.yml?pivot=products&panel=ai) para adicionar legendas e legendas a vídeos para atender a um público mais amplo (por exemplo, pessoas com deficiências auditivas ou pessoas que queiram ler numa língua diferente).
* Ativar o Azure CDN para obter uma grande escala para manusear melhor as cargas altas instantâneas (por exemplo, o início de um evento de lançamento do produto).

## <a name="how-can-i-get-started-with-v3"></a>Como posso começar a utilizar a v3?

Aprenda a codificar e embalar conteúdos, transmita vídeos a pedido, transmita em direto e analise os seus vídeos com o Media Services v3. Tutoriais, referências de API e outra documentação mostram-lhe como fornecer de forma segura transmissões em fluxo de vídeo ou áudio a pedido e live dimensionáveis para milhões de utilizadores.

> [!TIP]
> Antes de começar a desenvolver, reveja: [Conceitos fundamentais](concepts-overview.md) que incluem conceitos importantes, como embalagem, codificação e proteção, e [Desenvolvimento com Serviços de Media v3 APIs](media-services-apis-overview.md) que inclui informações sobre o acesso a APIs, convenções de nomeação, e assim por diante.

### <a name="sdks"></a>SDKs

Comece a desenvolver-se com [os SDKs clientes Azure Media Services v3.](media-services-apis-overview.md#sdks)

### <a name="quickstarts"></a>Guias de Início Rápido  

Os quickstarts mostram instruções fundamentais do dia 1 para novos clientes experimentarem rapidamente os Media Services.

* [Transmitir ficheiros de vídeo - .NET](stream-files-dotnet-quickstart.md)
* [Stream de ficheiros de vídeo - CLI](stream-files-cli-quickstart.md)
* [Transmitir ficheiros de vídeo - Node.js](stream-files-nodejs-quickstart.md)

### <a name="tutorials"></a>Tutoriais

Os tutoriais mostram procedimentos baseados em cenários para algumas das principais tarefas dos Serviços de Comunicação Social.

* [Codificar o ficheiro remoto e transmitir o vídeo - REST](stream-files-tutorial-with-rest.md)
* [Codificar o ficheiro carregado e transmitir o vídeo - .NET](stream-files-tutorial-with-api.md)
* [Transmitir em direto - .NET](stream-live-tutorial-with-api.md)
* [Analisar o seu vídeo - .NET](analyze-videos-tutorial-with-api.md)
* [Encriptação dinâmica AES-128 - .NET](protect-with-aes128.md)

### <a name="samples"></a>Amostras

Utilize [este navegador de amostras](/samples/browse/?products=azure-media-services) para navegar em amostras de código do Azure Media Services.

### <a name="how-to-guides"></a>Guias de procedimentos

Os guias de como fazer contêm amostras de código que demonstram como completar uma tarefa. Nesta secção, encontrará muitos exemplos. Aqui estão alguns deles:

* [Criar uma conta - CLI](./create-account-howto.md)
* [Aceder a APIs - CLI](./access-api-howto.md)
* [Codificar com HTTPS como entrada de trabalho - .NET](job-input-from-http-how-to.md)  
* [Monitorizar eventos - Portal](monitor-events-portal-how-to.md)
* [Criptografe dinamicamente com multi-DRM - .NET](protect-with-drm.md) 
* [Como codificar com uma transformação personalizada - CLI](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Consulte o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Media Services.

## <a name="next-steps"></a>Passos seguintes

[Conheça os conceitos fundamentais](concepts-overview.md)
