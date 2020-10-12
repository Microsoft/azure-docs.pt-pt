---
title: Redatória lógica no SDK dos Serviços de Comunicação Social para .NET / Microsoft Docs
description: O tópico dá uma visão geral da lógica de reluto no SDK dos Serviços de Media para .NET.
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
ms.openlocfilehash: 144db6a5ceaf56a35d3ce11dd54e1dfb4c97d7e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89264118"
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
| WebException<br/>Para obter mais informações, consulte a secção [de códigos de estado WebException.](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) |Sim |Sim |Sim |Sim |
| DataServiceClientExcepção<br/> Para obter mais informações, consulte [os códigos de estado de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Não |Sim |Sim |Sim |
| DataServiceQueryException<br/> Para obter mais informações, consulte [os códigos de estado de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Não |Sim |Sim |Sim |
| DataServiceRequestException<br/> Para obter mais informações, consulte [os códigos de estado de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Não |Sim |Sim |Sim |
| DataServiceTransportException |Não |Não |Sim |Sim |
| TimeoutExcepção |Sim |Sim |Sim |Não |
| SocketException |Sim |Sim |Sim |Sim |
| ArmazenamentoExcepção |Não |Sim |Não |Não |
| IoExcepção |Não |Sim |Não |Não |

### <a name="webexception-status-codes"></a><a name="WebExceptionStatus"></a> Códigos de estado WebException
A tabela a seguir mostra para o qual o erro WebException codifica a lógica de repetição. A enumeração [WebExceptionStatus](/dotnet/api/system.net.webexceptionstatus?view=netcore-3.1) define os códigos de estado.  

| Estado | Pedido web | Armazenamento | Consulta | SaveChanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |Sim |Sim |Sim |Sim |
| NomeResolutionFailure |Sim |Sim |Sim |Sim |
| ProxyNameResolutionFailure |Sim |Sim |Sim |Sim |
| Enviar AFailure |Sim |Sim |Sim |Sim |
| PipelineFailure |Sim |Sim |Sim |Não |
| ConexãoClosed |Sim |Sim |Sim |Não |
| KeepAliveFailure |Sim |Sim |Sim |Não |
| DesconhecidoError |Sim |Sim |Sim |Não |
| Receber AProfissilução |Sim |Sim |Sim |Não |
| PedidoCanceled |Sim |Sim |Sim |Não |
| Tempo Limite |Sim |Sim |Sim |Não |
| ProtocoloError <br/>A repetição no ProtocolError é controlada pelo tratamento do código de estado HTTP. Para obter mais informações, consulte [os códigos de estado de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Sim |Sim |Sim |Sim |

### <a name="http-error-status-codes"></a><a name="HTTPStatusCode"></a> Códigos de estado de erro HTTP
Quando as operações de Consulta e SaveChanges lançam DataServiceClientException, DataServiceQueryException ou DataServiceQueryException, o código de estado de erro HTTP é devolvido na propriedade StatusCode.  A tabela a seguir mostra para que códigos de erro a lógica de repetição é implementada.  

| Estado | Pedido web | Armazenamento | Consulta | SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |Não |Sim |Não |Não |
| 403 |Não |Sim<br/>Manusear recaímos com esperas mais longas. |Não |Não |
| 408 |Sim |Sim |Sim |Sim |
| 429 |Sim |Sim |Sim |Sim |
| 500 |Sim |Sim |Sim |Não |
| 502 |Sim |Sim |Sim |Não |
| 503 |Sim |Sim |Sim |Sim |
| 504 |Sim |Sim |Sim |Não |

Se quiser dar uma olhada na implementação real do SDK dos Serviços de Comunicação Social para a lógica de relemque de .NET, consulte [azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
