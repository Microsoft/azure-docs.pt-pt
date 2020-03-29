---
title: Retry logic in the Media Services SDK for .NET [ Microsoft Docs
description: O tema dá uma visão geral da lógica de retry no SDK de Serviços de Media para .NET.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61094658"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Redelógica de retry no SDK de Serviços de Media para .NET  

Ao trabalhar com os serviços do Microsoft Azure, podem ocorrer falhas transitórias. Se ocorrer uma falha transitória, na maioria dos casos, após algumas tentativas, a operação tem sucesso. O Media Services SDK para .NET implementa a lógica de retry para lidar com falhas transitórias associadas a exceções e erros que são causados por pedidos web, executar consultas, salvar alterações e operações de armazenamento.  Por predefinição, o Media Services SDK para .NET executa quatro repetições antes de voltar a lançar a exceção à sua aplicação. O código da sua aplicação deve então lidar corretamente com esta exceção.  

 Segue-se uma breve orientação das políticas de Pedido web, armazenamento, consulta e saveChanges:  

* A política de armazenamento é utilizada para operações de armazenamento de blob (uploads ou download de ficheiros de ativos).  
* A política de Pedido web é utilizada para pedidos genéricos web (por exemplo, para obter um símbolo de autenticação e resolver o ponto final do cluster dos utilizadores).  
* A política de consulta é usada para consulta de entidades da REST (por exemplo, mediaContext.Assets.Where(...)).  
* A política SaveChanges é usada para fazer qualquer coisa que mude os dados dentro do serviço (por exemplo, criar uma entidade a atualizar uma entidade, chamando uma função de serviço para uma operação).  
  
  Este tópico lista tipos de exceção e códigos de erro que são tratados pelo Media Services SDK para a lógica de retry .NET.  

## <a name="exception-types"></a>Tipos de exceção
O quadro seguinte descreve exceções que o Media Services SDK para manuseios .NET ou não trata de algumas operações que podem causar falhas transitórias.  

| Exceção | Pedido web | Storage | Consulta | SaveChanges |
| --- | --- | --- | --- | --- |
| Exceção web<br/>Para mais informações, consulte a secção de códigos de [estado WebException.](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) |Sim |Sim |Sim |Sim |
| DataServiceClientException<br/> Para mais informações, consulte [os códigos](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)de estado de erro HTTP . |Não |Sim |Sim |Sim |
| DataServiceQueryException<br/> Para mais informações, consulte [os códigos](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)de estado de erro HTTP . |Não |Sim |Sim |Sim |
| DataServiceRequestException<br/> Para mais informações, consulte [os códigos](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)de estado de erro HTTP . |Não |Sim |Sim |Sim |
| DataServiceTransportException |Não |Não |Sim |Sim |
| TimeoutException |Sim |Sim |Sim |Não |
| TomadaExcepção |Sim |Sim |Sim |Sim |
| Arrecadação Exceção |Não |Sim |Não |Não |
| IoException |Não |Sim |Não |Não |

### <a name="webexception-status-codes"></a><a name="WebExceptionStatus"></a>Códigos de estado da WebException
A tabela que se segue mostra para as quais o erro WebException codifica a lógica de retry. A enumeração [WebExceptionStatus](https://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) define os códigos de estado.  

| Estado | Pedido web | Storage | Consulta | SaveChanges |
| --- | --- | --- | --- | --- |
| Falha de ligação |Sim |Sim |Sim |Sim |
| Falha na resolução de nomes |Sim |Sim |Sim |Sim |
| ProxyNameResolutionFailure |Sim |Sim |Sim |Sim |
| EnvioFalha |Sim |Sim |Sim |Sim |
| PipelineFailure |Sim |Sim |Sim |Não |
| Conexão Fechada |Sim |Sim |Sim |Não |
| KeepAliveFail |Sim |Sim |Sim |Não |
| Erro Desconhecido |Sim |Sim |Sim |Não |
| Receber Falha |Sim |Sim |Sim |Não |
| Pedido Cancelado |Sim |Sim |Sim |Não |
| Tempo limite |Sim |Sim |Sim |Não |
| Erro protocolado <br/>A retentativa no ProtocolError é controlada pelo manuseamento do código de estado HTTP. Para mais informações, consulte [os códigos](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)de estado de erro HTTP . |Sim |Sim |Sim |Sim |

### <a name="http-error-status-codes"></a><a name="HTTPStatusCode"></a>Códigos de estado de erro HTTP
Quando as operações de Query e SaveChanges lançam DataServiceClientException, DataServiceQueryException ou DataServiceQueryException, o código de erro HTTP é devolvido na propriedade StatusCode.  A tabela que se segue mostra quais códigos de erro a lógica de retry é implementada.  

| Estado | Pedido web | Storage | Consulta | SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |Não |Sim |Não |Não |
| 403 |Não |Sim<br/>Manusear repetições com esperas mais longas. |Não |Não |
| 408 |Sim |Sim |Sim |Sim |
| 429 |Sim |Sim |Sim |Sim |
| 500 |Sim |Sim |Sim |Não |
| 502 |Sim |Sim |Sim |Não |
| 503 |Sim |Sim |Sim |Sim |
| 504 |Sim |Sim |Sim |Não |

Se quiser analisar a implementação real do Media Services SDK para a lógica de retry .NET, consulte [azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

