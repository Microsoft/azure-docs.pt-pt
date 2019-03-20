---
title: Análise de multimédia na plataforma dos serviços de multimédia | Documentos da Microsoft
description: Descrição geral da pré-visualização pública da análise de multimédia, uma coleção de serviços de visão de voz e o computador em escala empresarial, conformidade, segurança e alcance global
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
ms.date: 03/14/2019
ms.author: milanga;juliako;johndeu
ms.openlocfilehash: 0c090499ee6ae2aaaa14af18c3c38db28ecb2f3e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57884732"
---
# <a name="media-analytics-on-the-media-services-platform"></a>Análise de multimédia na plataforma dos serviços de multimédia 

## <a name="overview"></a>Descrição geral
Mais organizações estão usando vídeo como o meio preferido para treinar seus funcionários, interaja com os seus clientes e funções de negócios de documentos. Fornece uma forma de armazenar, transmitir em fluxo e aceder a estes ficheiros de suporte de dados grandes de informática na cloud. Mas, à medida que aumenta a biblioteca de uma empresa de conteúdo de vídeo, ele precisa de um meio eficaz de igualmente de extração de informações do conteúdo. 

Para atender a essa necessidade cada vez maior, serviços de multimédia do Azure oferece a análise de multimédia do Azure. A Análise de Multimédia é um conjunto de componentes de voz e visão que facilitam a derivação de conhecimentos acionáveis por parte das organizações e empresas dos respetivos ficheiros de vídeo. Criado usando os componentes principais da plataforma de serviços de multimédia, a análise de multimédia pode processar em escala no primeiro dia de processamento de multimédia.

Análise de multimédia, os desenvolvedores rapidamente proporcionam funcionalidades avançadas de vídeo em aplicativos. Ele oferece ambientes empresariais com a escala completa, conformidade, segurança e alcance global necessária para grandes organizações.

O diagrama seguinte mostra a análise de multimédia e de outras partes principais da plataforma dos serviços de multimédia. 

![Fluxo de trabalho do VoD](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

Os processadores de multimédia de Análise de Multimédia produzem ficheiros MP4 ou ficheiros JSON. Se um processador de multimédia produzir um ficheiro MP4, pode transferir progressivamente o ficheiro. Se um processador de multimédia produzir um ficheiro JSON, pode transferir o ficheiro de armazenamento de Blobs do Azure. 

## <a name="media-analytics-services"></a>Serviços de análise de multimédia

### <a name="indexer"></a>Indexador
Com o indexador de multimédia do Azure, pode tornar o conteúdo pesquisável e gerar legendagem roteiros. Em comparação com a versão anterior, a pré-visualização de 2 de indexador de multimédia do Azure tem idioma mais rapidamente mais amplo e indexação de suporte. Idiomas suportados incluem o inglês, espanhol, francês, alemão, italiano, chinês, português e árabe. Para obter informações detalhadas e exemplos, consulte [processa vídeos com o Azure Media Indexer 2](media-services-process-content-with-indexer2.md).
### <a name="hyperlapse"></a>Hyperlapse
Microsoft Hyperlapse combina estabilização de vídeo e capacidade com Time lapse e rápido, criar vídeos consumo por parte do seu conteúdo de forma longa. Além da criação de vídeos com Time lapse e, pode usar Hyperlapse para criar vídeos estáveis dos vídeos tremidos capturados por meio de celulares e camcorders. Para obter informações detalhadas e exemplos, consulte [Hyperlapse de ficheiros de multimédia com Hyperlapse de multimédia do Azure](media-services-hyperlapse-content.md).
### <a name="motion-detector"></a>Detetor de Movimento
Pode usar o detetor de movimento para detetar movimento num vídeo tenham fundos fixos. Isto torna possível verificar a existência de falsos positivos sobre eventos de movimento detectados por câmaras de vigilância. Para obter informações detalhadas e exemplos, consulte [deteção para a análise de multimédia do Azure de movimento](media-services-motion-detection.md).
### <a name="face-detector"></a>Detetor de Rosto
Com o detetor de rostos, pode detetar rostos de pessoas e emoções das mesmas, incluindo felicidade, tristeza e surpresa. Isso tem vários setor útil aplicativos, descritos mais à frente, incluindo a agregar e analisar as reações de participar de um evento de pessoas. Para obter informações detalhadas e exemplos, consulte [deteção de rostos e emoções para análise de multimédia do Azure](media-services-face-and-emotion-detection.md).
### <a name="video-summarization"></a>Resumo de vídeos
Resumo de vídeos pode ajudar a criar resumos de vídeos longos selecionando automaticamente os trechos de código interessantes do vídeo de origem. Esta capacidade é útil quando deseja fornecer uma descrição geral rápida do que pode esperar de um vídeo longo. Para obter informações detalhadas e exemplos, consulte [Use Azure Media miniaturas de vídeos para criar o resumo de vídeos](media-services-video-summarization.md).
### <a name="optical-character-recognition"></a>Reconhecimento ótico de carateres
Com o OCR de suporte de dados do Azure (reconhecimento ótico de carateres), é possível converter o conteúdo de texto em ficheiros de vídeo em texto digital editável, pesquisável. Em seguida, pode automatizar a extração de metadados significativos do sinal de vídeo do seu suporte de dados.
### <a name="scalable-face-redaction"></a>Redação de rostos dimensionável
O Azure Media Redactor é um processador de multimédia de análise de multimédia que oferece a redação de rostos escalável na cloud. Ao utilizar a redação de rostos, pode modificar o seu vídeo para Desfoca rostos de indivíduos selecionados. Pode querer utilizar o serviço de redação de face no suporte de notícias ou quando está envolvida segurança pública. Alguns minutos de imagens que contém vários rostos de podem demorar horas a edite manualmente, mas com este serviço, a redação de rostos demora apenas alguns passos simples. Para obter mais informações, consulte a [edite rostos com análise de multimédia do Azure](media-services-face-redaction.md) artigo.
### <a name="content-moderation"></a>Moderação de Conteúdos
Moderador de conteúdos do Azure permite-lhe utilizar moderação assistida por computador para os seus vídeos. Por exemplo, pode querer detetar possíveis conteúdos para adultos nos vídeos e rever o conteúdo sinalizado por suas equipes de moderação humana. Manualmente moderadores vídeos para o conteúdo indesejável é uma tarefa demorada e dispendiosa. Com este serviço e ferramentas de revisão associado, combinar moderação assistida por computador com recursos humanos em loop, para obter melhores resultados de forma eficiente e rentável. Para obter mais informações, consulte a [processar os seus vídeos com o Azure Content Moderator](media-services-content-moderation.md) artigo.

## <a name="common-scenarios"></a>Cenários comuns
Análise de multimédia pode ajudar as organizações e empresas obter novas informações do vídeo e muito mais capazes de gerenciar grandes volumes de conteúdo de vídeo. Seguem-se vários cenários:

* **Centros de chamar**. Mesmo com o advento dos meios de comunicação, centrais de atendimento ao cliente facilitam ainda uma grande porcentagem de transações de atendimento ao cliente. Codificado em dados de áudio é uma grande quantidade de informações do cliente que podem ser analisadas para alcançar maior satisfação do cliente. Com o indexador de multimédia, as organizações possam extrair texto e criar índices de pesquisa e dashboards. Em seguida, eles possam extrair inteligência em torno de queixas comuns, fontes de reclamações e outros dados relevantes.
* **Moderação de conteúdos gerados pelo utilizador**. Da mídia para departamentos de polícia, muitas organizações têm portais destinado ao público que aceitam o suporte de dados gerados pelo utilizador, como imagens e vídeos. O volume de conteúdo pode aumentar devido a eventos inesperados. Nestes cenários, é difícil conduzir as revisões manuais em vigor a partir de conteúdo para adequação. Os clientes podem contar com o serviço de moderação de conteúdo para se concentrar no conteúdo que seja apropriado.
* **Vigilância**. Com o crescimento no uso de câmaras IP é fornecido um inventário de cada vez maior de vigilância vídeo. Rever manualmente o vídeo de vigilância está na altura intensiva e propensa a erro humano. Análise de multimédia fornece serviços, como deteção de movimento, deteção de rostos e o Hyperlapse para tornar o processo de revisão, gerir e criar derivados mais fácil.

## <a name="media-analytics-media-processors"></a>Processadores de multimédia de análise de multimédia
Esta seção apresenta uma lista de processadores de multimédia de análise de multimédia e mostra como utilizar o .NET ou REST para obter um objeto de processador (MP) de multimédia.

### <a name="mp-names"></a>Nomes de pacote de gestão
* Pré-visualização de 2 de indexador de multimédia do Azure
* Azure Media Indexer
* Azure Media Hyperlapse
* Azure Media Face Detector
* Azure Media Motion Detector
* Azure Media Video Thumbnails
* Azure Media OCR
* Moderador de conteúdos de multimédia do Azure

### <a name="net"></a>.NET
A seguinte função usa um dos nomes de pacote de gestão especificados e retorna um objeto de pacote de gestão.

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
Ver [demonstrações de análise de multimédia do Azure](https://azuremedialabs.azurewebsites.net/demos/Analytics.html).

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Artigos relacionados
Ver [anúncio de análise de serviços de multimédia](https://azure.microsoft.com/blog/introducing-azure-media-analytics/).

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

## <a name="next-steps"></a>Passos Seguintes
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
