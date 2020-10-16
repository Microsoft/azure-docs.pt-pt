---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 6bda702a90d1204de6f2b80ced9a4704f1e8426d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711427"
---
| Nome da dimensão | Descrição |
| ------------------- | ----------------- |
| **GeoTipo** | Transação do cluster primário ou secundário. Os valores disponíveis incluem **Primário** e **Secundário.** Aplica-se ao Read Access Geo Redundant Storage (RA-GRS) ao ler objetos de inquilino secundário. |
| **Tipo de Resposta** | Tipo de resposta a transações. Os valores disponíveis incluem: <br/><br/> <li>**ServerOtherError**: todos os outros erros do lado do servidor, exceto os descritos </li> <li>**ServerBusyError**: pedido autenticado que devolveu um código de estado HTTP 503. </li> <li>**ServerTimeoutError**: pedido autenticado com tempo limite excedido que devolveu um código de estado HTTP 500. O tempo limite excedido ocorreu devido a um erro de servidor. </li> <li>**AuthorizationError**: pedido autenticado que falhou devido a acesso não autorizado a dados ou a uma falha de autorização. </li> <li>**NetworkError**: pedido autenticado que falhou devido a erros de rede. Ocorre normalmente quando um cliente fecha prematuramente uma ligação antes da expiração do tempo limite. </li><li>**ClientAccountBandwidthThrottlingError**: O pedido é acelerado em largura de banda por exceder os [limites de escalabilidade da conta de armazenamento](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json).</li><li>**ClientAccountRequestThrottlingError**: O pedido é acelerado na taxa de pedido para exceder os [limites de escalabilidade da conta de armazenamento](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json).<li>**ClientThrottlingError**: Outro erro de estrangulamento do lado do cliente. O ClientAccountBandwidthThrottlingError e o ClientAccountRequestThrottlingError estão excluídos.</li> <li>**ClientTimeoutError**: pedido autenticado com tempo limite excedido que devolveu um código de estado HTTP 500. Se o tempo limite da rede do cliente ou do pedido estiver definido como um valor inferior ao esperado pelo serviço de armazenamento, trata-se de um tempo limite esperado. Caso contrário, é reportado como um ServerTimeoutError. </li> <li>**ClientOtherError**: todos os outros erros do lado do cliente, exceto os descritos. </li> <li>**Success**: pedido com êxito</li> <li> **SuccessWithThrottling**: Pedido de sucesso quando um cliente SMB é estrangulado na primeira tentativa, mas consegue após retrações.</li> |
| **ApiName** | O nome da operação. 
| **Autenticação** | Tipo de autenticação utilizado em transações. Os valores disponíveis incluem: <br/> <li>**ContaKey**: A transação é autenticada com a chave da conta de armazenamento.</li> <li>**SAS**: A transação é autenticada com assinaturas de acesso partilhado.</li> <li>**OAuth**: A transação é autenticada com fichas de acesso OAuth.</li> <li>**Anónimo**: A transação é solicitada anonimamente. Não inclui pedidos de pré-voo.</li> <li>**AnónimoPreflight**: A transação é um pedido de pré-voo.</li> |