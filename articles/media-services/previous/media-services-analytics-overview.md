---
title: Análise de Mídia na plataforma de serviços de mídia | Microsoft Docs
description: Visão geral da visualização pública do Análise de Mídia, uma coleção de serviços de fala e pesquisa Visual computacional em escala empresarial, conformidade, segurança e alcance global
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: c56e3781-8510-4f7f-b5ff-a218c1bb6f4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.reviewer: milanga; johndeu
ms.openlocfilehash: 66edbc872fe5f1d4320107aa21ed2792bdd4fecc
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083896"
---
# <a name="media-analytics-on-the-media-services-platform"></a>Análise de Mídia na plataforma de serviços de mídia 

## <a name="retirement-plans"></a>Planos de aposentadoria

> [!IMPORTANT]
> Os seguintes processadores de mídia herdados serão desativados no 2020, consulte os detalhes na tabela a seguir. 

|Nome do processador de mulitmédia|Data de aposentadoria|Notas adicionais|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)| 1º de janeiro de 2020|Esse processador de mídia será substituído pelos [serviços de mídia do Azure Video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Para obter mais informações, consulte [migrar do Azure Media indexer 2 para os serviços de mídia do Azure Video indexer](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1º de outubro de 2020|Esse processador de mídia será substituído pelos [serviços de mídia do Azure Video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Para obter mais informações, consulte [migrar do Azure Media indexer para os serviços de mídia do Azure Video indexer](migrate-indexer-v1-v2.md)
 
## <a name="overview"></a>Descrição geral

Mais organizações estão usando vídeos como o meio preferido para treinar seus funcionários, envolver seus clientes e documentar funções de negócios. A computação em nuvem fornece uma maneira de armazenar, transmitir e acessar esses arquivos de mídia grandes. Mas, à medida que a biblioteca de conteúdo de vídeo de uma empresa cresce, ela precisa de um meio igualmente eficaz de extrair informações do conteúdo. 

Para atender essa crescente necessidade, os serviços de mídia do Azure oferecem Análise de Mídia do Azure. A Análise de Multimédia é um conjunto de componentes de voz e visão que facilitam a derivação de conhecimentos acionáveis por parte das organizações e empresas dos respetivos ficheiros de vídeo. Criado usando os componentes principais da plataforma de serviços de mídia, Análise de Mídia pode lidar com o processamento de mídia em escala no primeiro dia.

Com o Análise de Mídia, os desenvolvedores podem rapidamente colocar a funcionalidade avançada de vídeo em aplicativos. Ele fornece ambientes corporativos com dimensionamento completo, conformidade, segurança e alcance global exigidos por grandes organizações.

O diagrama a seguir mostra Análise de Mídia e outras partes principais da plataforma de serviços de mídia. 

![Fluxo de trabalho do VoD](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

Os processadores de multimédia de Análise de Multimédia produzem ficheiros MP4 ou ficheiros JSON. Se um processador de mídia produzir um arquivo MP4, você poderá baixar o arquivo progressivamente. Se um processador de mídia produzir um arquivo JSON, você poderá baixar o arquivo do armazenamento de BLOBs do Azure. 

## <a name="media-analytics-services"></a>Serviços Análise de Mídias

### <a name="indexer"></a>Indexador
Com Azure Media Indexer, você pode tornar o conteúdo pesquisável e gerar faixas de legenda oculta. Em comparação com a versão anterior, Azure Media Indexer 2 Preview tem uma indexação mais rápida e suporte mais amplo a idiomas. Os idiomas com suporte incluem Inglês, espanhol, francês, alemão, italiano, chinês, Português e árabe. Para obter informações detalhadas e exemplos, consulte [processar vídeos com Azure Media indexer 2](media-services-process-content-with-indexer2.md).
### <a name="motion-detector"></a>Detetor de Movimento
Você pode usar o detector de movimento para detectar o movimento em um vídeo com planos de fundo estacionários. Isso possibilita a verificação de falsos positivos em eventos de movimento detectados por câmeras de vigilância. Para obter informações detalhadas e exemplos, consulte [detecção de movimento para análise de mídia do Azure](media-services-motion-detection.md).
### <a name="face-detector"></a>Detetor de Rosto
Usando o face detector, você pode detectar as faces das pessoas e suas emoções, incluindo felicidade, tristeza e surpresa. Isso tem vários aplicativos úteis do setor, descritos posteriormente, incluindo agregação e análise de reações de pessoas que participam de um evento. Para obter informações detalhadas e exemplos, consulte [detecção facial e de emoções para análise de mídia do Azure](media-services-face-and-emotion-detection.md).
### <a name="video-summarization"></a>Resumo de vídeo
O resumo de vídeo pode ajudá-lo a criar resumos de vídeos longos selecionando automaticamente trechos interessantes do vídeo de origem. Essa capacidade é útil quando você deseja fornecer uma visão geral rápida do que esperar em um vídeo longo. Para obter informações detalhadas e exemplos, consulte [usar Azure Media Video thumbnails para criar um resumo de vídeo](media-services-video-summarization.md).
### <a name="optical-character-recognition"></a>Reconhecimento ótico de carateres
Com o OCR de mídia do Azure (reconhecimento óptico de caracteres), você pode converter o conteúdo de texto em arquivos de vídeo em texto digital editável e pesquisável. Em seguida, você pode automatizar a extração de metadados significativos do sinal de vídeo de sua mídia.
### <a name="scalable-face-redaction"></a>Edição facial escalonável
Azure Media Redactor é um processador de mídia Análise de Mídia que oferece uma edição facial escalonável na nuvem. Ao usar a edição facial, você pode modificar seu vídeo para se desfocar em rostos de indivíduos selecionados. Talvez você queira usar o serviço de edição facial em mídia de notícias ou quando a segurança pública estiver envolvida. Alguns minutos de seqüência de imagens que contém vários rostos podem levar horas para serem editados manualmente, mas com esse serviço, a edição facial leva apenas algumas etapas simples. Para obter mais informações, consulte o artigo [redação faces com análise de mídia do Azure](media-services-face-redaction.md) .
### <a name="content-moderation"></a>Moderação de Conteúdos
O Azure Content Moderator permite que você use a moderação assistida por computador para seus vídeos. Por exemplo, poderá querer detetar possível conteúdo para adultos nos vídeos e rever o conteúdo sinalizado pelas suas equipas de moderação humana. O moderar manual de vídeos para conteúdo indesejável é uma tarefa cara e demorada. Com esse serviço e as ferramentas de revisão associadas, você combina a moderação assistida por computador com recursos humanos no loop para obter melhores resultados com eficiência e economia. Para saber mais, confira o artigo [processar seus vídeos com o Azure Content moderator](media-services-content-moderation.md) .

## <a name="common-scenarios"></a>Cenários comuns
Análise de Mídia pode ajudar organizações e empresas a obter novas informações do vídeo e gerenciar com mais eficiência grandes volumes de conteúdo de vídeo. Aqui estão vários cenários:

* **Call Centers**. Mesmo com o advento da mídia social, os call centers de clientes ainda facilitam um grande percentual de transações de serviço do cliente. Codificados nesses dados de áudio há uma grande quantidade de informações do cliente que podem ser analisadas para atingir maior satisfação do cliente. Usando o indexador de mídia, as organizações podem extrair texto e criar índices e painéis de pesquisa. Em seguida, eles podem extrair inteligência em relação a reclamações comuns, fontes de reclamações e outros dados relevantes.
* **Moderação de conteúdo gerada pelo usuário**. Desde a mídia de notícias até os departamentos de polícia, muitas organizações têm portais voltados ao público que aceitam mídias geradas pelo usuário, como vídeos e imagens. O volume de conteúdo pode ser propicodo devido a eventos inesperados. Nesses cenários, é difícil conduzir revisões manuais efetivas de conteúdo para fins de adequação. Os clientes podem contar com o serviço de moderação de conteúdo para se concentrar no conteúdo apropriado.

## <a name="media-analytics-media-processors"></a>Processadores de mídia Análise de Mídia
Esta seção lista os processadores de mídia Análise de Mídia e mostra como usar o .NET ou REST para obter um objeto de processador de mídia (MP).

### <a name="mp-names"></a>Nomes de MP
* Versão prévia do Azure Media Indexer 2
* Azure Media Indexer
* Azure Media Face Detector
* Azure Media Motion Detector
* Azure Media Video Thumbnails
* Azure Media OCR
* Content Moderator de mídia do Azure

### <a name="net"></a>.NET
A função a seguir usa um dos nomes de MP especificados e retorna um objeto MP.

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
            .Where(p => p.Name == mediaProcessorName)
            .ToList()
            .OrderBy(p => new Version(p.Version))
            .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }


### <a name="rest"></a>REST
Pedido:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20OCR' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.12
    Host: media.windows.net

Resposta:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:074c3899-d9fb-448f-9ae1-4ebcbe633056",
             "Description":"Azure Media OCR",
             "Name":"Azure Media OCR",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

## <a name="demos"></a>Demonstrações
Consulte [demonstrações de análise de mídia do Azure](https://azuremedialabs.azurewebsites.net/demos/Analytics.html).

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Artigos relacionados
Consulte [comunicado de análise dos serviços de mídia](https://azure.microsoft.com/blog/introducing-azure-media-analytics/).

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

## <a name="next-steps"></a>Passos seguintes
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
