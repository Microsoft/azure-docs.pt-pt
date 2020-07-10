---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 54ee19f3e278b424384ff55d7065713584235df1
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86144219"
---
## <a name="set-up"></a>Configurar

### <a name="create-a-translator-resource"></a>Criar um recurso Tradutor

Os Serviços Cognitivos Azure são representados por recursos Azure que subscreve. Crie um recurso para Tradutor utilizando o [portal Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [O CLI Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na sua máquina local. Também pode:

* Ver um recurso existente no [portal Azure](https://portal.azure.com/).

Depois de obter uma chave da sua subscrição ou recurso de teste, crie [duas variáveis ambientais:](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY`- A chave de subscrição do seu recurso Tradutor.
* `TRANSLATOR_TEXT_ENDPOINT`- O ponto final global para tradutor. Utilize`https://api.cognitive.microsofttranslator.com/`.
