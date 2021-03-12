---
title: Azure Media Services codifica códigos de erro | Microsoft Docs
description: Este tópico lista códigos de erro que podem ser devolvidos no caso de um erro ter sido encontrado durante a execução da tarefa de codificação..
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ce4e939f-5aee-41f9-859d-e4429815e9f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 7e6848fb49dd63fa67a639d09754a28dd5953a32
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103013487"
---
# <a name="encoding-error-codes"></a>Encoding error codes (Códigos de erro de codificação)

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

A tabela que se segue lista os códigos de erro que podem ser devolvidos no caso de ter sido encontrado um erro durante a execução da tarefa de codificação.  Para obter detalhes de erro no seu código .NET, utilize a classe [ErrorDetails.](/previous-versions/azure/jj126075(v=azure.100)) Para obter detalhes de erro no seu código REST, utilize a API [ErrorDetail](/rest/api/media/operations/errordetail) REST.

| ErrorDetail.Code | Possíveis causas de erro |
| --- | --- |
| Desconhecido |Erro desconhecido durante a execução da tarefa |
| ErrorDownloadingInputAssetMalformedContent |Categoria de erros que cobre erros no descarregamento de ativos de entrada, tais como nomes de ficheiros maus, ficheiros de comprimento zero, formatos incorretos e assim por diante. |
| ErrorDownloadingInputAssetServiceFailure |Categoria de erros que cobrem problemas no lado do serviço - por exemplo, erros de rede ou armazenamento durante o download. |
| ErroParsingConfiguration |Categoria de erros em que a tarefa \<see cref="MediaTask.PrivateData"/> (configuração) não é válida, por exemplo, a configuração não é uma predefinição válida do sistema ou contém XML inválido. |
| ErroExecutingTaskMalformedContent |Categoria de erros durante a execução da tarefa onde problemas dentro dos ficheiros de meios de entrada causam falha. |
| ErrorExecutingTaskUnsupportedFormat |Categoria de erros em que o processador de mídia não pode processar os ficheiros fornecidos - formato de mídia não suportado, ou não corresponde à Configuração. Por exemplo, tentar produzir uma saída só de áudio a partir de um ativo que tem apenas vídeo |
| ErrorProcessingTask |Categoria de outros erros que o processador de mídia encontra durante o processamento da tarefa que não estão relacionados com o conteúdo. |
| ErrorUploadingOutputAsset |Categoria de erros ao carregar o ativo de saída |
| ErrorCancelingTask |Categoria de erros para cobrir falhas ao tentar cancelar a Tarefa |
| TransitienteError |Categoria de erros para cobrir questões transitórias (por exemplo. questões temporárias de networking com o Azure Storage) |

Para obter ajuda da equipa dos **Serviços de Comunicação,** abra um [bilhete de apoio.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Artigos relacionados
* [Execute tarefas avançadas de codificação personalizando predefinições padrão media Encoder](media-services-custom-mes-presets-with-dotnet.md)
* [Quotas e Limitações](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
