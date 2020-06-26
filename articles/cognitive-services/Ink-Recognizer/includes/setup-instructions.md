---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: c202ba1d7363af9791daa801f0c447c49a80859b
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85378405"
---
>[!NOTE]
> Os pontos finais para recursos criados após 1 de julho de 2019 utilizam o formato de subdomínio personalizado apresentado abaixo. Para obter mais informações e uma lista completa de pontos finais regionais, consulte [os nomes de subdomínio personalizados para serviços cognitivos.](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains) 

Os Serviços Cognitivos Azure são representados por recursos Azure que subscreve. Crie um recurso para o Reconhecimento de Tintas utilizando o [portal Azure](../../cognitive-services-apis-create-account.md).

Depois de criar um recurso, obtenha o seu ponto final e a chave abrindo o seu recurso no [portal Azure](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade), e clicando em **início rápido**.

Criar [duas variáveis ambientais:](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)

* `INK_RECOGNITION_SUBSCRIPTION_KEY`- O ponto final do seu recurso. Vai ficar assim: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

* `INK_RECOGNITION_ENDPOINT`- A chave de subscrição para autenticar os seus pedidos.   
