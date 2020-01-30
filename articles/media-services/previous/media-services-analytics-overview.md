---
title: Media Analytics na plataforma Media Services Microsoft Docs
description: Visão geral da visualização pública do Media Analytics, uma coleção de serviços de fala e visão computacional à escala empresarial, conformidade, segurança e alcance global
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
ms.openlocfilehash: 4b0d360c11313e086f6ec26e5ee46b8d6f49869a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844348"
---
# <a name="media-analytics-on-the-media-services-platform"></a>Media Analytics na plataforma Media Services 

## <a name="retirement-plans"></a>Planos de reforma

> [!IMPORTANT]
> O seguinte processador de mídia legado vai ser retirado em 2020, ver detalhes na tabela seguinte. 

|Nome do processador de mulitmédia|Data de aposentadoria|Notas adicionais|
|---|---|
|[Azure Media Indexer](media-services-index-content.md)|1º de outubro de 2020|Esse processador de mídia será substituído pelos [serviços de mídia do Azure Video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Para obter mais informações, consulte [migrar do Azure Media indexer para os serviços de mídia do Azure Video indexer](migrate-indexer-v1-v2.md)
 
## <a name="overview"></a>Visão geral

Mais organizações estão a usar o vídeo como meio preferido para formar os seus colaboradores, envolver os seus clientes e documentar funções empresariais. A computação em nuvem fornece uma forma de armazenar, transmitir e aceder a estes grandes ficheiros de mídia. Mas à medida que a biblioteca de conteúdos de vídeo de uma empresa cresce, precisa de um meio igualmente eficaz de extrair insights do conteúdo. 

Para responder a esta crescente necessidade, a Azure Media Services oferece azure Media Analytics. A Análise de Multimédia é um conjunto de componentes de voz e visão que facilitam a derivação de conhecimentos acionáveis por parte das organizações e empresas dos respetivos ficheiros de vídeo. Construído utilizando os componentes da plataforma core Media Services, o Media Analytics pode lidar com o processamento de mídia em escala no primeiro dia.

Com o Media Analytics, os desenvolvedores podem rapidamente trazer funcionalidades avançadas de vídeo para aplicações. Proporciona aos ambientes empresariais a escala completa, conformidade, segurança e alcance global exigidos pelas grandes organizações.

O diagrama seguinte mostra media Analytics e outras partes principais da plataforma Media Services. 

![Fluxo de trabalho do VoD](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

Os processadores de multimédia de Análise de Multimédia produzem ficheiros MP4 ou ficheiros JSON. Se um processador de mídia produzir um ficheiro MP4, pode descarregar progressivamente o ficheiro. Se um processador de mídia produzir um ficheiro JSON, pode descarregar o ficheiro a partir do armazenamento do Azure Blob. 

## <a name="media-analytics-services"></a>Serviços Media Analytics

### <a name="indexer"></a>Indexador
Com o Azure Media Indexer, pode tornar o conteúdo pesquisável e gerar faixas de legendagem fechada. Para obter informações detalhadas e exemplos, consulte [Indexing Media Files com O Indexante](media-services-index-content.md)dos Media Azure .

### <a name="motion-detector"></a>Detetor de Movimento
Pode utilizar o Detetor de Movimento para detetar movimento num vídeo com fundos estacionários. Isto permite verificar se há falsos positivos em eventos de movimento detetados pelas câmaras de vigilância. Para obter informações detalhadas e exemplos, consulte a [deteção de movimento para Azure Media Analytics](media-services-motion-detection.md).

### <a name="face-detector"></a>Detetor de Rosto
Ao usar o Detetor facial, pode detetar os rostos das pessoas e as suas emoções, incluindo felicidade, tristeza e surpresa. Isto tem várias aplicações úteis do setor, descritas mais tarde, incluindo agregação e análise de reações de pessoas presentes num evento. Para obter informações detalhadas e exemplos, consulte [A deteção de Rosto e Emoção para azure Media Analytics](media-services-face-and-emotion-detection.md).

### <a name="video-summarization"></a>Resumo de vídeo
A resumição de vídeo pode ajudá-lo a criar resumos de longos vídeos selecionando automaticamente snippets interessantes do vídeo de origem. Esta capacidade é útil quando se pretende fornecer uma visão geral rápida do que esperar num longo vídeo. Para obter informações detalhadas e exemplos, consulte A utilização de Miniaturas de [Vídeo Azure Media para criar resumição](media-services-video-summarization.md)de vídeo .
### <a name="optical-character-recognition"></a>Reconhecimento ótico de carateres
Com o Azure Media OCR (reconhecimento ótico de caracteres), pode converter conteúdo de texto em ficheiros de vídeo em texto digital editável e pesquisável. Em seguida, pode automatizar a extração de metadados significativos a partir do sinal de vídeo dos seus meios de comunicação.
### <a name="scalable-face-redaction"></a>Redação facial escalável
Azure Media Redator é um processador de media Media Analytics que oferece redação facial escalável na nuvem. Ao utilizar a redação facial, pode modificar o seu vídeo para desfocar rostos de indivíduos selecionados. É melhor utilizar o serviço de redação facial nos meios de comunicação social ou quando a segurança pública estiver envolvida. Alguns minutos de filmagens que contêm várias faces podem levar horas a redigir manualmente, mas com este serviço, a redação facial dá apenas alguns passos simples. Para mais informações, consulte o Redact face com o artigo [da Azure Media Analytics.](media-services-face-redaction.md)

### <a name="content-moderation"></a>Moderação de Conteúdos
O Azure Content Moderator permite-lhe utilizar moderação assistida por máquinas para os seus vídeos. Por exemplo, poderá querer detetar possível conteúdo para adultos nos vídeos e rever o conteúdo sinalizado pelas suas equipas de moderação humana. Moderar manualmente vídeos para conteúdos indesejáveis é uma tarefa morosa e dispendiosa. Com este serviço e ferramentas de revisão associadas, combina a moderação assistida por máquinas com capacidades humanas no loop para obter os melhores resultados de forma eficiente e económica. Para saber mais, consulte o Processo dos seus vídeos com o artigo do Moderador de [Conteúdo Do Azure.](media-services-content-moderation.md)

## <a name="common-scenarios"></a>Cenários comuns
O Media Analytics pode ajudar organizações e empresas a recolher novas informações a partir de vídeo e gerir de forma mais eficaz grandes volumes de conteúdos de vídeo. Aqui estão vários cenários:

* **Call centers.** Mesmo com o advento das redes sociais, os call centers do cliente ainda facilitam uma grande percentagem de transações de atendimento ao cliente. Codificada nestes dados áudio é uma grande quantidade de informação do cliente que pode ser analisada para obter uma maior satisfação do cliente. Ao utilizar o Media Indexer, as organizações podem extrair texto e construir índices de pesquisa e dashboards. Depois podem extrair informações em torno de queixas comuns, fontes de reclamações e outros dados relevantes.
* **Moderação de conteúdo gerada pelo utilizador.** Desde os meios de comunicação social até aos departamentos de polícia, muitas organizações têm portais virados para o público que aceitam meios gerados pelo utilizador, como vídeos e imagens. O volume de conteúdo pode aumentar devido a eventos inesperados. Nestes cenários, é difícil proceder a revisões manuais eficazes dos conteúdos para a adequação. Os clientes podem contar com o serviço de moderação de conteúdos para se concentrarem em conteúdos adequados.

## <a name="media-analytics-media-processors"></a>Processadores de mídia Media Analytics
Esta secção lista os processadores de mídia Media Analytics e mostra como utilizar .NET ou REST para obter um objeto de processador de mídia (MP).

### <a name="mp-names"></a>Nomes de MP

* Indexador de Multimédia do Azure
* Azure Media Face Detector
* Azure Media Motion Detector
* Azure Media Video Thumbnails
* Azure Media OCR
* Moderador de Conteúdo dos Meios de Comunicação Social Azure

### <a name="net"></a>.NET
A função seguinte pega num dos nomes de MP especificados e devolve um objeto MP.

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
    x-ms-version: 2.19
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
Ver [demos Azure Media Analytics.](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Artigos relacionados
Ver [Comunicado de Media Services Analytics](https://azure.microsoft.com/blog/introducing-azure-media-analytics/).

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

## <a name="next-steps"></a>Passos seguintes
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
