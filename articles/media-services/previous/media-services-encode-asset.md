---
title: Descrição geral e comparação do Azure no codificadores de multimédia a pedido | Documentos da Microsoft
description: Este tópico apresenta uma descrição geral e comparação do Azure no codificadores de multimédia a pedido.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: e6bfc068-fa46-4d68-b1ce-9092c8f3a3c9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: 7acf4a0f937817b131763e040d452366c520fa6f
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57247601"
---
# <a name="overview-and-comparison-of-azure-on-demand-media-encoders"></a>Descrição geral e comparação do Azure no codificadores de multimédia a pedido 

## <a name="encoding-overview"></a>Descrição geral da codificação
Serviços de multimédia do Azure fornece várias opções para a codificação de multimédia na cloud.

Quando a começar a utilizar os serviços de multimédia, é importante compreender a diferença entre codecs e formatos de arquivo.
Codecs são o software que implementa os algoritmos de compactação/descompactação enquanto os formatos de arquivo são contentores que contêm o vídeo compactado.

Os Media Services fornecem um empacotamento dinâmico permite-lhe fornecer o conteúdo de velocidade de transmissão adaptável MP4 ou Smooth Streaming com codificação de transmissão em fluxo em formatos suportados pelos Media Services (MPEG DASH, HLS, Smooth Streaming) sem ter de voltar o pacote para estes formatos de transmissão em fluxo.

> [!NOTE]
> Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 

Serviços de multimédia suportam o seguinte no codificadores a pedido que são descritos neste artigo:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Fluxo de Trabalho Premium do Codificador de Multimédia](media-services-encode-asset.md#media-encoder-premium-workflow)

Este artigo fornece uma breve descrição geral de demanda codificadores de multimédia e fornece ligações para artigos que fornecem informações mais detalhadas. O tópico também fornece a comparação dos codificadores.

>[!NOTE]
>Por predefinição, cada conta de Media Services pode ter uma tarefa de codificação Active Directory ao mesmo tempo. Pode reservar as unidades de codificação que permitem que tenha várias tarefas de codificação em execução em simultâneo, um para cada unidade reservada codificação de que compra. Para obter informações, consulte [dimensionamento de unidades de codificação](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Media Encoder Standard
### <a name="how-to-use"></a>Como utilizar
[Como codificar com o Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Formatos
[Formatos e codecs do](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Predefinições
Codificador de multimédia Standard está configurado com uma das configurações predefinidas de codificador descritas [aqui](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Metadados de entrada e saído
Os metadados de entrada de codificadores é descrito [aqui](media-services-input-metadata-schema.md).

Os metadados de saída de codificadores é descrito [aqui](media-services-output-metadata-schema.md).

### <a name="generate-thumbnails"></a>Gerar miniaturas
Para obter informações, consulte [como gerar miniaturas com o Media Encoder Standard](media-services-advanced-encoding-with-mes.md#thumbnails).

### <a name="trim-videos-clipping"></a>Cortar os vídeos (recorte)
Para obter informações, consulte [como cortar vídeos com o Media Encoder Standard](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Criar sobreposições
Para obter informações, consulte [como criar sobreposições com o Media Encoder Standard](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Consulte também
[O blog de serviços de multimédia](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Fluxo de Trabalho Premium de Codificador de Multimédia
### <a name="overview"></a>Descrição geral
[Apresentando o Encoding de multimédia do Azure, serviços Premium](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>Como utilizar
Media Encoder Premium Workflow está configurado com fluxos de trabalho complexos. Ficheiros de fluxo de trabalho poderia ser criados e atualização com o [Designer de fluxo de trabalho](media-services-workflow-designer.md) ferramenta.

[Como utilizar o Encoding de multimédia do Azure, serviços Premium](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Problemas conhecidos
Se o seu vídeo de entrada não contém legendagem de áudio, a saída Asset ainda conterá um ficheiro vazio do TTML.


## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Artigos relacionados
* [Executar tarefas de codificação avançadas ao personalizar as predefinições do Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Quotas e limitações](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
