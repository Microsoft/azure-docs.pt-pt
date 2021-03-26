---
title: Orientação de migração codificada
description: Este artigo dá-lhe orientações baseadas em cenários que o ajudarão a migrar da Azure Media Services v2 para v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: bf66723f878f8b277d71577c068afdad56708ad6
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563276"
---
# <a name="encoding-scenario-based-migration-guidance"></a>Orientação de migração baseada em cenários de codificação

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![etapas de migração 2](./media/migration-guide/steps-4.svg)

Este artigo dá-lhe orientações baseadas em cenários que o ajudarão a migrar da Azure Media Services v2 para v3.

## <a name="prerequisites"></a>Pré-requisitos

Antes de alterar o seu fluxo de trabalho de codificação, recomenda-se que compreenda as diferenças na forma como o armazenamento é gerido.  Na AMS V3, a AZure Storage API é utilizada para gerir a(s) conta de armazenamento associada à sua conta de Serviços de Mídia.

> [!NOTE]
> Empregos e tarefas criadas na V2 não aparecem na V3, uma vez que não estão associadas a uma transformação. A recomendação é mudar para v3 transformações e empregos.

## <a name="encoding-workflow-comparison"></a>Comparação de fluxos de trabalho codificação

Dedite alguns minutos a olhar para os fluxogramas abaixo para uma comparação visual dos fluxos de trabalho codificadores para V2 e V3.

### <a name="v2-encoding-workflow"></a>V2 codificando fluxo de trabalho

Clique na imagem abaixo para ver uma versão maior.

[![Fluxo de trabalho de codificação para V2 ](./media/migration-guide/V2-pretty.svg)](./media/migration-guide/V2-pretty.svg#lightbox)

1. Configuração
    1. Criar um ativo ou uso e um ativo existente. Se utilizar um novo ativo, faça o upload do conteúdo para esse ativo. Se utilizar um ativo existente, deverá codificar ficheiros que já existem no ativo.
    2. Obtenha os valores dos seguintes itens:
        - ID ou objeto do processador de mídia
        - Cadeia codificação (nome) do codificar que pretende utilizar
        - ID de ativo de novo ativo OU o ID do ativo do ativo existente
    3. Para monitorização, crie uma subscrição de notificação de emprego ou nível de tarefa ou um manipulador de eventos SDK
2. Crie o trabalho que contém a tarefa ou tarefas. Cada tarefa deve incluir os itens acima e:
    - É necessário criar uma diretiva sobre a necessidade de criar um ativo de saída.  O ativo de saída é criado pelo sistema.
    - Nome opcional para o ativo de saída
3. Submeta o trabalho.
4. Monitorize o trabalho.

### <a name="v3-encoding-workflow"></a>V3 codificando fluxo de trabalho

[![Fluxo de trabalho de codificação para V3](./media/migration-guide/V3-pretty.svg)](./media/migration-guide/V3-pretty.svg#lightbox)

1. Configurar
    1. Criar um ativo ou utilizar um ativo existente. Se utilizar um novo ativo, faça o upload do conteúdo para esse ativo. Se utilizar um ativo existente, deverá codificar ficheiros que já existem no ativo. *Não devias enviar mais conteúdo para esse ativo.*
    1. Criar um ativo de saída.  O ativo de saída é onde os ficheiros codificados e os metadados de entrada e saída serão armazenados.
    1. Obtenha valores para a transformação:
        - Predefinição padrão do codificação do codificação
        - Grupo de recursos AMS
        - Nome da conta AMS
    1. Crie a transformação ou use uma transformação existente.  As transformações são reutilizáveis. Não é necessário criar uma nova transformação cada vez que se quer submeter um emprego.
1. Criar uma tarefa
    1. Para o trabalho, obtenha os valores dos seguintes itens:
        - Transformar nome
        - Base-URI para o URL SAS para o seu ativo, o caminho de origem HTTPs da sua partilha de ficheiros, ou o caminho local dos ficheiros. Também `JobInputAsset` pode usar um nome de ativo como entrada.
        - Nomes de ficheiros
        - Ativos de saída
        - Um grupo de recursos
        - Nome da conta AMS  
1. Utilize [a Grade de Eventos](monitoring/monitor-events-portal-how-to.md) para monitorizar o seu trabalho.
1. Submeta o trabalho.

## <a name="custom-presets-from-v2-to-v3-encoding"></a>Predefinições personalizadas de codificação V2 a V3

Se o seu código V2 chamado Standard Encoder com uma predefinição personalizada, primeiro precisa criar uma nova transformação com a predefinição standard standard encoder antes de submeter um trabalho.

As predefinições personalizadas são agora JSON e já não são baseadas em XML. Recrie a sua predefinição em JSON seguindo o esquema predefinido personalizado, tal como definido na documentação [Transform Open API (Swagger).](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/examples/transforms-create.json)

## <a name="input-and-output-metadata-files-from-an-encoding-job"></a>Ficheiros de metadados de entrada e saída de um trabalho de codificação

Em v2, os ficheiros de entrada e metadados de saída XML são gerados como resultado de um trabalho de codificação. Em v3, o formato de metadados mudou de XML para JSON. Para obter mais informações sobre metadados, consulte [metadados de entrada](input-metadata-schema.md) e [metadados de saída.](output-metadata-schema.md)

## <a name="premium-encoder-to-v3-standard-encoder-or-partner-based-solutions"></a>Premium Encoder para v3 Standard Encoder ou soluções baseadas em parceiros

A V2 API já não suporta o Codificadores Premium. Se usou previamente o codificar Premium Encoding baseado em fluxo de trabalho para codificação HEVC deve migrar para o novo V3 [Standard Encoder](media-encoder-standard-formats.md) com suporte de codificação HEVC.

Se necessitar das funcionalidades avançadas de fluxo de trabalho do Premium Encoder, é encorajado a começar a utilizar uma solução de parceiro de codificação avançada da Azure da [Imagine Communications,](https://imaginecommunications.com) [Telestream](https://www.telestream.net)ou [Bitmovin.](https://bitmovin.com)

## <a name="jobs-with-inputs-that-are-on-https-hosted-urls"></a>Empregos com entradas que estão em URLs hospedados https

Pode agora submeter empregos em V3 a partir de ficheiros armazenados no armazenamento Azure, armazenados localmente ou servidores web externos utilizando o [suporte de entrada de trabalho HTTP(S).](job-input-from-http-how-to.md)

Se anteriormente usou fluxos de trabalho para copiar ficheiros de ficheiros blob da Azure em ativos vazios antes de apresentar empregos, poderá simplificar o seu fluxo de trabalho ao passar um URL SAS para o ficheiro no armazenamento de blob Azure diretamente no trabalho.

## <a name="indexer-v1-audio-transcription-to-the-new-audioanalyzer-basic-mode"></a>Indexer v1 transcrição áudio para o novo "modo básico" audioAnalyzer

Para os clientes que utilizam o processador Indexer v1 na API v2, é necessário criar uma transformação que invoque o novo `AudioAnalyzer` em [modo básico](how-to-create-basic-audio-transform.md) antes de submeter um Job.

## <a name="encoding-transforms-and-jobs-concepts-tutorials-and-how-to-guides"></a>Codificação, transformação e emprego conceitos, tutoriais e como guiar

### <a name="concepts"></a>Conceitos

- [Codificação de vídeo e áudio com Serviços de Media](encoding-concept.md)
- [Formatos e códigos Standard Encoder](media-encoder-standard-formats.md)
- [Codificar com uma escada de bitrate autogerada](autogen-bitrate-ladder.md)
- [Utilize a predefinição de codificação consciente do conteúdo para encontrar o valor bitrate ideal para uma determinada resolução](content-aware-encoding.md)
- [Unidades Reservadas aos Meios de Comunicação Social](concept-media-reserved-units.md)
- [Metadados de entrada](input-metadata-schema.md)
- [Metadados de saída](output-metadata-schema.md)
- [Embalagem dinâmica nos Media Services v3: códigos áudio](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging)

### <a name="tutorials"></a>Tutoriais

- [Tutorial: Codificar um ficheiro remoto com base no URL e transmitir o vídeo - .NET](stream-files-dotnet-quickstart.md)
- [Tutorial: Carregar, codificar e transmitir vídeos com o Media Services v3](stream-files-tutorial-with-api.md)

### <a name="how-to-guides"></a>Guias de procedimentos

- [Criar uma entrada de trabalho a partir de um URL HTTPS](job-input-from-http-how-to.md)
- [Criar uma entrada de trabalho a partir de um arquivo local](job-input-from-local-file-how-to.md)
- [Criar uma transformação áudio básica](how-to-create-basic-audio-transform.md)
- Com o .NET
  - [Como codificar com uma transformação personalizada - .NET](customize-encoder-presets-how-to.md)
  - [Como criar uma sobreposição com o Media Encoder Standard](how-to-create-overlay.md)
  - [Como gerar miniaturas usando o Encoder Standard com .NET](media-services-generate-thumbnails-dotnet.md)
- Com a CLI do Azure
  - [Como codificar com uma transformação personalizada - Azure CLI](custom-preset-cli-howto.md)
- Com o REST
  - [Como codificar com uma transformação personalizada - REST](custom-preset-rest-howto.md)
  - [Como gerar miniaturas usando o Encoder Standard com REST](media-services-generate-thumbnails-rest.md)
- [Subclip um vídeo ao codificar com os Media Services - .NET](subclip-video-dotnet-howto.md)
- [Subclip um vídeo ao codificar com os Media Services - REST](subclip-video-rest-howto.md)

## <a name="samples"></a>Amostras

Também pode [comparar o código V2 e V3 nas amostras de código](migrate-v-2-v-3-migration-samples.md).
