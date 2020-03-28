---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 11abd52681d7c9962af4e5bf0728f97b256223c1
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122902"
---
## <a name="create-a-personalizer-azure-resource"></a>Criar um recurso Personalizer Azure

Crie um recurso para personalizar utilizando o [portal Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [o Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na sua máquina local. Também pode:

* Obtenha uma chave de [teste](https://azure.microsoft.com/try/cognitive-services) válida por 7 dias de graça. Depois de se inscrever, estará disponível no site do [Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/)
* Veja o seu recurso no [portal Azure.](https://portal.azure.com/)

Depois de obter uma chave da sua subscrição ou recurso experimental, crie duas [variáveis ambientais:](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)

* `PERSONALIZER_RESOURCE_KEY`para a chave de recursos.
* `PERSONALIZER_RESOURCE_ENDPOINT`para o ponto final do recurso.

No portal Azure, tanto os valores chave como os valores finais estão disponíveis na página **de arranque rápido.**
