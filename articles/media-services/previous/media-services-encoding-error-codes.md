---
title: Azure Media Services codificacódigos de erro Microsoft Docs
description: Este tópico lista códigos de erro que poderiam ser devolvidos no caso de um erro ter sido encontrado durante a execução da tarefa de codificação..
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ce4e939f-5aee-41f9-859d-e4429815e9f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 5c038f0be31acea52c2ef07d43f0dbaf3434a371
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "64709520"
---
# <a name="encoding-error-codes"></a>Encoding error codes (Códigos de erro de codificação)

A tabela a seguir enumera códigos de erro que poderiam ser devolvidos no caso de ter sido encontrado um erro durante a execução da tarefa de codificação.  Para obter detalhes de erro no seu código .NET, utilize a classe [ErrorDetails.](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) Para obter detalhes de erro no seu código REST, utilize a API REST [ErrorDetail.](https://msdn.microsoft.com/library/jj853026.aspx)

| ErrorDetail.Code | Possíveis causas de erro |
| --- | --- |
| Desconhecido |Erro desconhecido ao executar a tarefa |
| ErrorDownloadingInputAssetMalformedContent |Categoria de erros que cobre erros no descarregamento de ativos de entrada tais como nomes de ficheiros errados, ficheiros de comprimento zero, formatos incorretos e assim por diante. |
| ErrorDownloadingInputAssetServiceFailure |Categoria de erros que cobre problemas no lado do serviço - por exemplo, erros de rede ou armazenamento durante o download. |
| Configuração errorParsing |Categoria de erros \<em que a tarefa ver cref="MediaTask.PrivateData"/> (configuração) não é válida, por exemplo, a configuração não é um predefinido de sistema válido ou contém XML inválido. |
| ErrorExecuteTaskMalformedContent |Categoria de erros durante a execução da tarefa onde problemas dentro dos ficheiros de mídia de entrada causam falha. |
| ErrorExecutingTaskUnsupportedFormat |Categoria de erros em que o processador de mídia não pode processar os ficheiros fornecidos - formato de mídia não suportado ou não corresponde à Configuração. Por exemplo, tentar produzir uma saída apenas áudio a partir de um ativo que tem apenas vídeo |
| Tarefa de processamento de erros |Categoria de outros erros que o processador de mídia encontra durante o processamento da tarefa que não estão relacionadas com o conteúdo. |
| ErrorUploadoutputAsset |Categoria de erros ao carregar o ativo de saída |
| ErrorCancelingTask |Categoria de erros para cobrir falhas ao tentar cancelar a Tarefa |
| TransientError |Categoria de erros para cobrir questões transitórias (por exemplo. problemas temporários de networking com o Armazenamento Azure) |

Para obter ajuda da equipa de **Media Services,** abra um bilhete de [apoio.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Artigos relacionados
* [Execute tarefas avançadas de codificação personalizando predefinições padrão do Media Encoder](media-services-custom-mes-presets-with-dotnet.md)
* [Quotas e Limitações](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
