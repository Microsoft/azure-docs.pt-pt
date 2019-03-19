---
title: Códigos de erro de codificação dos serviços de multimédia do Azure | Documentos da Microsoft
description: Este tópico lista os códigos de erro que podem ser devolvidos no caso de um erro durante a execução de tarefa de codificação....
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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 7c293ea738c264b31efb1cf8dfca959612355587
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57886580"
---
# <a name="encoding-error-codes"></a>Códigos de erro de codificação

A tabela seguinte lista os códigos de erro que podem ser devolvidos no caso de um erro durante a execução de tarefa de codificação.  Para obter detalhes de erro no seu código .NET, utilize o [ErrorDetails](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) classe. Para obter detalhes de erro no seu código REST, utilize o [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) REST API.

| ErrorDetail.Code | Causas possíveis para erro |
| --- | --- |
| Desconhecidos |Erro desconhecido ao executar a tarefa |
| ErrorDownloadingInputAssetMalformedContent |Categoria de erros que abrange os erros no download do elemento de entrada, tais como nomes de ficheiro incorreto, nenhum arquivo de comprimento incorreto formata e assim por diante. |
| ErrorDownloadingInputAssetServiceFailure |Categoria de erros que aborda problemas no lado do serviço - erros de rede ou armazenamento de exemplo ao transferir. |
| ErrorParsingConfiguration |Categoria de erros de tarefas onde <see cref="MediaTask.PrivateData"/> (configuração) não é válida, por exemplo a configuração não é um sistema válido configuração predefinida ou contém XML inválido. |
| ErrorExecutingTaskMalformedContent |Categoria de erros durante a execução da tarefa onde problemas dentro os ficheiros de suporte de dados de entrada causam a falha. |
| ErrorExecutingTaskUnsupportedFormat |Categoria de erros em que o processador de multimédia não é possível processar os ficheiros fornecidos - formato de multimédia não suportado ou não coincide com a configuração. Por exemplo, tentando produzir um resultado de só de áudio de um elemento que tem apenas vídeo |
| ErrorProcessingTask |Categoria de outros erros que o processador de multimédia encontra durante o processamento da tarefa que estão relacionadas ao conteúdo. |
| ErrorUploadingOutputAsset |Categoria de erros ao carregar o recurso de saída |
| ErrorCancelingTask |Categoria de erros para cobrir falhas durante a tentativa de cancelar a tarefa |
| TransientError |Categoria de erros para cobrir problemas transitórios (ex. problemas de rede temporários com o armazenamento do Azure) |

Para obter ajuda a partir da **dos serviços de multimédia** equipa, abra um [pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Artigos relacionados
* [Executar tarefas de codificação avançadas ao personalizar as predefinições do Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Quotas e limitações](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
