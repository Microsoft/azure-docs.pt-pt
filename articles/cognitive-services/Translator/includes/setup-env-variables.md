---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: a936afc611462b6ec3c3de9021d517ccf66f666b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586551"
---
## <a name="set-up"></a>Configurar

### <a name="create-a-translator-resource"></a>Criar um recurso tradutor

Os Serviços Cognitivos Azure são representados por recursos Azure que subscreve. Crie um recurso para tradutor utilizando o [portal Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [o Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na sua máquina local. Também pode:

* Obtenha uma chave de [teste](https://azure.microsoft.com/try/cognitive-services) válida por 7 dias de graça. Depois de se inscrever, estará disponível no site do Azure.
* Veja um recurso existente no [portal Azure.](https://portal.azure.com/)

Depois de obter uma chave da sua subscrição ou recurso experimental, crie duas [variáveis ambientais:](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY`- A chave de subscrição do seu recurso Tradutor.
* `TRANSLATOR_TEXT_ENDPOINT`- O ponto final global para tradutor. Utilize`https://api.cognitive.microsofttranslator.com/`.
