---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: d0377e3e918a8431c35c1f5b15a1c4b54eeae3b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98760660"
---
## <a name="retrying-after-errors-and-exceptions"></a>Reagem após erros e exceções

Espera-se que implemente as suas próprias políticas de relícola quando ligar para a MSAL. A MSAL faz chamadas HTTP para o serviço AD Azure, e ocasionalmente podem ocorrer falhas. Por exemplo, a rede pode desligar-se ou o servidor está sobrecarregado.  

### <a name="http-429"></a>HTTP 429

Quando o Serviço Token Server (STS) está sobrecarregado com muitos pedidos, retorna o erro HTTP 429 com uma dica sobre quanto tempo pode tentar novamente no `Retry-After` campo de resposta.