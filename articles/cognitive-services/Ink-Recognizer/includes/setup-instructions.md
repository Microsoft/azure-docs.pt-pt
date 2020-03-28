---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 942bcc6b150f990f9a9acab0d4ef68bfb6125c1b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71996851"
---
>[!NOTE]
> Pontos finais para recursos criados após 1 de julho de 2019 utilizam o formato de subdomínio personalizado mostrado abaixo. Para mais informações e uma lista completa de pontos finais regionais, consulte [nomes de subdomínio personalizado para Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Os Serviços Cognitivos Azure são representados por recursos Azure que subscreve. Crie um recurso para o Reconhecimento de Tinta utilizando o [portal Azure](../../cognitive-services-apis-create-account.md). 

* Também pode obter uma chave de [teste](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias de graça. Depois de se inscrever, ele e um ponto final estarão disponíveis no site do [Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/)

Depois de criar um recurso, obtenha o seu ponto final e a sua chave abrindo o seu recurso no [portal Azure](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade), e clicando em **Quick start**.

Criar duas [variáveis ambientais:](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)

* `INK_RECOGNITION_SUBSCRIPTION_KEY`- O ponto final para o seu recurso. Vai ficar assim: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

* `INK_RECOGNITION_ENDPOINT`- A chave de subscrição para autenticar os seus pedidos.   
