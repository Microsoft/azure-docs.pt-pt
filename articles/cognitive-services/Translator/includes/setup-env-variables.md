---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: c737447c3a3bd2d76d3ed620b7c61aaa81250130
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "70393824"
---
## <a name="set-up"></a>Configurar

### <a name="create-a-translator-text-resource"></a>Criar um recurso de texto tradutor

Os Serviços Cognitivos Azure são representados por recursos Azure que subscreve. Crie um recurso para o Texto tradutor utilizando o [portal Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [o Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na sua máquina local. Também pode:

* Obtenha uma chave de [teste](https://azure.microsoft.com/try/cognitive-services) válida por 7 dias de graça. Depois de se inscrever, estará disponível no site do Azure.
* Veja um recurso existente no [portal Azure.](https://portal.azure.com/)

Depois de obter uma chave da sua subscrição ou recurso experimental, crie duas [variáveis ambientais:](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY`- A chave de subscrição do seu recurso De Texto tradutor.
* `TRANSLATOR_TEXT_ENDPOINT`- O ponto final global do Texto tradutor. Utilize`https://api.cognitive.microsofttranslator.com/`.
