---
title: Lógica de repetição no SDK de serviços de multimédia para .NET | Documentos da Microsoft
description: O tópico fornece uma descrição geral da lógica de repetição no SDK de serviços de multimédia para .NET.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 63715f668438519131eba5bfff7aa38fc73267d0
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294489"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Lógica de repetição no SDK de serviços de multimédia para .NET  

Ao trabalhar com os serviços do Microsoft Azure, as falhas transitórias podem ocorrer. Se ocorrer uma falha transitória, na maioria dos casos, depois de algumas tentativas a operação for concluída com êxito. O SDK de serviços de multimédia para .NET implementa a lógica de repetição para lidar com falhas transitórias associadas com exceções e erros causados por solicitações da web, execução de consultas, a guardar as alterações e operações de armazenamento.  Por predefinição, o SDK de serviços de multimédia para .NET executa quatro tentativas de repetição antes de voltar a gerar a exceção à sua aplicação. O código na sua aplicação, em seguida, tem de processar essa exceção corretamente.  

 Segue-se uma breve diretriz de diretivas de solicitação da Web, armazenamento, consulta e SaveChanges:  

* A política de armazenamento é utilizada para operações de armazenamento de BLOBs (carregamentos ou transferências de ficheiros de elemento).  
* A política de pedido da Web é utilizada para solicitações da web genérico (por exemplo, para obter um token de autenticação e resolver o ponto final de cluster de usuários).  
* A política de consulta é usada para consultar entidades de REST (por exemplo, mediaContext.Assets.Where(...)).  
* A política de SaveChanges é utilizada para fazer nada que altera os dados no serviço (por exemplo, criar uma entidade a atualizar uma entidade, chamar uma função de serviço para uma operação).  
  
  Este tópico lista os tipos de exceção e lógica de repetição de códigos de erro que são processados pelo SDK de serviços de multimédia para .NET.  

## <a name="exception-types"></a>Tipos de exceção
A tabela seguinte descreve as exceções que o SDK de serviços de multimédia para .NET manipula ou não manipula para algumas operações que podem causar falhas transitórias.  

| Exceção | Solicitação da Web | Armazenamento | Consulta | SaveChanges |
| --- | --- | --- | --- | --- |
| WebException<br/>Para obter mais informações, consulte a [códigos de estado de exceção WebException](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) secção. |Sim |Sim |Sim |Sim |
| DataServiceClientException<br/> Para obter mais informações, consulte [códigos de estado de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Não |Sim |Sim |Sim |
| DataServiceQueryException<br/> Para obter mais informações, consulte [códigos de estado de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Não |Sim |Sim |Sim |
| DataServiceRequestException<br/> Para obter mais informações, consulte [códigos de estado de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Não |Sim |Sim |Sim |
| DataServiceTransportException |Não |Não |Sim |Sim |
| TimeoutException |Sim |Sim |Sim |Não |
| SocketException |Sim |Sim |Sim |Sim |
| StorageException |Não |Sim |Não |Não |
| IOException |Não |Sim |Não |Não |

### <a name="WebExceptionStatus"></a> Códigos de estado de exceção WebException
A tabela seguinte mostra para os códigos de erro de exceção WebException a lógica de repetição é implementada. O [WebExceptionStatus](https://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) enumeração define os códigos de estado.  

| Estado | Solicitação da Web | Armazenamento | Consulta | SaveChanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |Sim |Sim |Sim |Sim |
| NameResolutionFailure |Sim |Sim |Sim |Sim |
| ProxyNameResolutionFailure |Sim |Sim |Sim |Sim |
| SendFailure |Sim |Sim |Sim |Sim |
| PipelineFailure |Sim |Sim |Sim |Não |
| ConnectionClosed |Sim |Sim |Sim |Não |
| KeepAliveFailure |Sim |Sim |Sim |Não |
| UnknownError |Sim |Sim |Sim |Não |
| ReceiveFailure |Sim |Sim |Sim |Não |
| RequestCanceled |Sim |Sim |Sim |Não |
| Tempo Limite (excedido) |Sim |Sim |Sim |Não |
| ProtocolError <br/>A repetição em ProtocolError é controlada pela manipulação de código de estado HTTP. Para obter mais informações, consulte [códigos de estado de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Sim |Sim |Sim |Sim |

### <a name="HTTPStatusCode"></a> Códigos de estado de erro HTTP
Quando as operações de consulta e SaveChanges lançar DataServiceClientException, DataServiceQueryException ou DataServiceQueryException, o código de estado de erro HTTP é devolvido na propriedade StatusCode.  A tabela seguinte mostra quais códigos de erro a lógica de repetição é implementada.  

| Estado | Solicitação da Web | Armazenamento | Consulta | SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |Não |Sim |Não |Não |
| 403 |Não |Sim<br/>Processar as repetições com esperas de mais tempo. |Não |Não |
| 408 |Sim |Sim |Sim |Sim |
| 429 |Sim |Sim |Sim |Sim |
| 500 |Sim |Sim |Sim |Não |
| 502 |Sim |Sim |Sim |Não |
| 503 |Sim |Sim |Sim |Sim |
| 504 |Sim |Sim |Sim |Não |

Se quiser dar uma olhada na implementação real do SDK de serviços do suporte de dados para a lógica de repetição do .NET, veja [azure-sdk-para--serviços de multimédia](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Passos Seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

