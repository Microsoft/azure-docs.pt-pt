---
title: Redatória lógica no SDK dos Serviços de Media para .NET | Microsoft Docs
description: O tópico dá uma visão geral da lógica de reluto no SDK dos Serviços de Media para .NET.
author: IngridAtMicrosoft
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
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: feda0ccfa1dc6d02153b98ad084bd775a055e9e3
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012909"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Redatória lógica no SDK dos Serviços de Media para .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Ao trabalhar com os serviços microsoft Azure, podem ocorrer falhas transitórias. Se ocorrer uma falha transitória, na maioria dos casos, após algumas retrações, a operação terá sucesso. O Media Services SDK para .NET implementa a lógica de reagem para lidar com falhas transitórias associadas a exceções e erros que são causados por pedidos web, execução de consultas, poupança de alterações e operações de armazenamento.  Por predefinição, o Media Services SDK para .NET executa quatro recauchutagens antes de relançar a exceção à sua aplicação. O código da sua aplicação deve então lidar corretamente com esta exceção.  

 Segue-se uma breve orientação das políticas de Pedido Web, Armazenamento, Consulta e SaveChanges:  

* A política de Armazenamento é utilizada para operações de armazenamento de bolhas (uploads ou download de ficheiros de ativos).  
* A política de Pedido web é utilizada para pedidos genéricos da web (por exemplo, para obter um token de autenticação e resolver o ponto final do cluster dos utilizadores).  
* A política de consulta é utilizada para consultar entidades do REST (por exemplo, mediaContext.Assets.Where(...)).  
* A política SaveChanges é utilizada para fazer qualquer coisa que mude os dados dentro do serviço (por exemplo, criar uma entidade que atualize uma entidade, chamando uma função de serviço para uma operação).  
  
  Este tópico lista tipos de exceções e códigos de erro que são tratados pelo SDK dos Serviços de Mídia para a lógica de reluto de .NET.  

## <a name="exception-types"></a>Tipos de exceções
O quadro que se segue descreve exceções que o Media Services SDK para as pegas .NET ou não lida com algumas operações que podem causar falhas transitórias.  

| Exceção | Pedido web | Armazenamento | Consulta | SaveChanges |
| --- | --- | --- | --- | --- |
| WebException<br/>Para obter mais informações, consulte a secção [de códigos de estado WebException.](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) |Yes |Yes |Yes |Yes |
| DataServiceClientExcepção<br/> Para obter mais informações, consulte [os códigos de estado de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |No |Yes |Yes |Yes |
| DataServiceQueryException<br/> Para obter mais informações, consulte [os códigos de estado de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |No |Yes |Yes |Yes |
| DataServiceRequestException<br/> Para obter mais informações, consulte [os códigos de estado de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |No |Yes |Yes |Yes |
| DataServiceTransportException |No |No |Yes |Yes |
| TimeoutExcepção |Yes |Yes |Yes |No |
| SocketException |Yes |Yes |Yes |Yes |
| ArmazenamentoExcepção |No |Yes |No |No |
| IoExcepção |No |Yes |No |No |

### <a name="webexception-status-codes"></a><a name="WebExceptionStatus"></a> Códigos de estado WebException
A tabela a seguir mostra para o qual o erro WebException codifica a lógica de repetição. A enumeração [WebExceptionStatus](/dotnet/api/system.net.webexceptionstatus?view=netcore-3.1) define os códigos de estado.  

| Estado | Pedido web | Armazenamento | Consulta | SaveChanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |Yes |Yes |Yes |Yes |
| NomeResolutionFailure |Yes |Yes |Yes |Yes |
| ProxyNameResolutionFailure |Yes |Yes |Yes |Yes |
| Enviar AFailure |Yes |Yes |Yes |Yes |
| PipelineFailure |Yes |Yes |Yes |No |
| ConexãoClosed |Yes |Yes |Yes |No |
| KeepAliveFailure |Yes |Yes |Yes |No |
| DesconhecidoError |Yes |Yes |Yes |No |
| Receber AProfissilução |Yes |Yes |Yes |No |
| PedidoCanceled |Yes |Yes |Yes |No |
| Tempo Limite |Yes |Yes |Yes |No |
| ProtocoloError <br/>A repetição no ProtocolError é controlada pelo tratamento do código de estado HTTP. Para obter mais informações, consulte [os códigos de estado de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Yes |Yes |Yes |Yes |

### <a name="http-error-status-codes"></a><a name="HTTPStatusCode"></a> Códigos de estado de erro HTTP
Quando as operações de Consulta e SaveChanges lançam DataServiceClientException, DataServiceQueryException ou DataServiceQueryException, o código de estado de erro HTTP é devolvido na propriedade StatusCode.  A tabela a seguir mostra para que códigos de erro a lógica de repetição é implementada.  

| Estado | Pedido web | Armazenamento | Consulta | SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |No |Yes |No |No |
| 403 |No |Yes<br/>Manusear recaímos com esperas mais longas. |No |No |
| 408 |Yes |Yes |Yes |Yes |
| 429 |Yes |Yes |Yes |Yes |
| 500 |Yes |Yes |Yes |No |
| 502 |Yes |Yes |Yes |No |
| 503 |Yes |Yes |Yes |Yes |
| 504 |Yes |Yes |Yes |No |

Se quiser dar uma olhada na implementação real do SDK dos Serviços de Comunicação Social para a lógica de relemque de .NET, consulte [azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
