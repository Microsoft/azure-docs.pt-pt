---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: c737447c3a3bd2d76d3ed620b7c61aaa81250130
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70393824"
---
## <a name="set-up"></a>Configuração

### <a name="create-a-translator-text-resource"></a>Criar um recurso de Tradução de Texto

Os serviços cognitivas do Azure são representados pelos recursos do Azure que você assina. Crie um recurso para Tradução de Texto usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) no computador local. Também pode:

* Obtenha uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services) válida por 7 dias gratuitamente. Depois de se inscrever, ele estará disponível no site do Azure.
* Exiba um recurso existente no [portal do Azure](https://portal.azure.com/).

Depois de obter uma chave de sua assinatura ou recurso de avaliação, crie duas [variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY`-A chave de assinatura para o recurso de Tradução de Texto.
* `TRANSLATOR_TEXT_ENDPOINT`-O ponto de extremidade global para Tradução de Texto. Use `https://api.cognitive.microsofttranslator.com/`o.
