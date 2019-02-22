---
title: Serviços de multimédia do Azure v3 perguntas mais frequentes | Documentos da Microsoft
description: Este artigo responde às perguntas mais frequentes de v3 de serviços de multimédia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/20/2019
ms.author: juliako
ms.openlocfilehash: a815d03dd4e7163d4d9f00ce8f9c16f1b3055ce9
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652336"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Serviços de multimédia do Azure v3 perguntas mais frequentes

Este artigo responde às perguntas mais frequentes de v3 de serviços de multimédia do Azure (AMS).

## <a name="v3-apis"></a>v3 APIs

### <a name="how-do-i-configure-media-reserved-units"></a>Como posso configurar a unidades reservadas de multimédia?

Para a análise de áudio e tarefas de análise de vídeo que são acionados por serviços de multimédia v3 ou Video Indexer, recomenda-se elevada para aprovisionar a sua conta com 10 MRUs de S3. Se precisar de mais de 10 S3 MRUs, abra um pedido de suporte através do [portal do Azure](https://portal.azure.com/).

Para obter detalhes, consulte [Dimensionar processamento de multimédia com a CLI](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>O que é o método recomendado para vídeos do processo?

Uso [transforma](https://docs.microsoft.com/rest/api/media/transforms) para configurar as tarefas comuns de codificação ou analisar vídeos. Cada **transformar** descreve uma recipe ou um fluxo de trabalho de tarefas para processar os ficheiros de vídeos ou áudio. R [tarefa](https://docs.microsoft.com/rest/api/media/jobs) é o pedido real para os serviços de multimédia para aplicar a **transformar** para um determinado conteúdo vídeo ou áudio de entrada. Quando a transformação tiver sido criada, pode submeter trabalhos com APIs de serviços de suporte de dados ou qualquer um dos SDKs publicados. Para obter mais informações, consulte [transforma e tarefas](transforms-jobs-concept.md).

### <a name="how-does-pagination-work"></a>Como funciona a paginação?

Ao utilizar a paginação, deve sempre utilizar a ligação seguinte enumere a coleção e não dependem de um tamanho de página em particular. Para obter detalhes e exemplos, consulte [filtragem, ordenação, paginação](entities-overview.md).

## <a name="live-streaming"></a>Transmissão em direto 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Como inserir quebras/vídeos e slates da imagem durante a transmissão em direto?

Codificação em direto do serviços de multimédia v3 ainda não suporta inserção de slates vídeo ou imagem durante a transmissão em direto. 

Pode utilizar um [locais codificador em direto](recommended-on-premises-live-encoders.md) para mudar o vídeo de origem. Muitas aplicações fornecem a capacidade de mudar de origens, incluindo Telestream Wirecast, comutador Studio (no iOS), OBS Studio (aplicação gratuita) e muito mais.

## <a name="content-protection"></a>Proteção de conteúdo

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Como e onde obter JWT token antes de o utilizar para a licença de pedido ou a chave?

1. Para a produção, tem de ter um Secure Token serviços (STS) (serviço web) que emite o token JWT mediante um pedido HTTPS. Para teste, poderia usar o código mostrado na **GetTokenAsync** método definido na [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Leitor terá de fazer um pedido, após um utilizador é autenticado, para o STS para esse token e atribua-a como o valor do token. Pode utilizar o [API de leitor de multimédia do Azure](https://amp.azure.net/libs/amp/latest/docs/).

* Para obter um exemplo de execução do STS, com a chave simétrica e assimétrica, consulte [ http://aka.ms/jwt ](https://aka.ms/jwt). 
* Para obter um exemplo de um player com base no leitor de multimédia do Azure com esse token JWT, consulte [ http://aka.ms/amtest ](https://aka.ms/amtest) (expandir a ligação de "player_settings" para ver a entrada de token).

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Como autorizar pedidos para transmitir vídeos em fluxo com encriptação AES?

A abordagem correta é tirar partido do STS (serviço Token seguro):

STS, dependendo do perfil de usuário, adicione afirmações diferentes (por exemplo, "Premium User", "Utilizador básico", "Utilizador de avaliação gratuita"). Com afirmações diferentes num JWT, o utilizador pode ver o conteúdo diferente. Obviamente, para conteúdo diferente/recurso, o ContentKeyPolicyRestriction terá o RequiredClaims correspondente.

Utilize APIs de serviços de suporte de dados do Azure para configurar/chave de licença de entrega e a criptografia de seus ativos (conforme mostrado na [este exemplo](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs).

Para obter mais informações, consulte:

- [Descrição geral da proteção de conteúdo](content-protection-overview.md)
- [Criação de um sistema de proteção de conteúdo multi-DRM com controlo de acesso](design-multi-drm-system-with-access-control.md)

## <a name="media-services-v2-vs-v3"></a>Serviços de multimédia v2 vs v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Pode utilizar o portal do Azure para gerir os recursos de v3?

Ainda não. Pode utilizar um dos SDKs suportados. Veja tutoriais e amostras neste conjunto de documentos.

### <a name="is-there-an-assetfile-concept-in-v3"></a>Existe um conceito de AssetFile na v3?

Os AssetFiles foram removidas da API do AMS para separar os serviços de multimédia da dependência do SDK de armazenamento. Agora o armazenamento, não os serviços de multimédia, mantém as informações que pertence no armazenamento. 

Para obter mais informações, consulte [migre para o serviços de multimédia v3](migrate-from-v2-to-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Em que passei a encriptação do armazenamento do lado do cliente?

Recomenda-se agora para utilizar a encriptação de armazenamento do lado do servidor (o que está ativada por predefinição). Para obter mais informações, consulte [encriptação do serviço de armazenamento do Azure para dados Inativos](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Descrição geral do serviços de multimédia v3](media-services-overview.md)
