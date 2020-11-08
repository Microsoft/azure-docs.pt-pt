---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: c68e5b7ab24e2d7e7f30ddc356ae3c4382137507
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369084"
---
>[!NOTE]
> Os pontos finais para recursos criados após 1 de julho de 2019 utilizam o formato de subdomínio personalizado apresentado abaixo. Para obter mais informações e uma lista completa de pontos finais regionais, consulte [os nomes de subdomínio personalizados para serviços cognitivos.](../../cognitive-services-custom-subdomains.md) 

Os Serviços Cognitivos Azure são representados por recursos Azure que subscreve. Crie um recurso para o Reconhecimento de Tintas utilizando o [portal Azure](../../cognitive-services-apis-create-account.md).

Depois de criar um recurso, obtenha o seu ponto final e a chave abrindo o seu recurso no [portal Azure](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade), e clicando em **início rápido**.

Criar [duas variáveis ambientais:](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)

* `INK_RECOGNITION_SUBSCRIPTION_KEY` - A chave de subscrição para autenticar os seus pedidos. 

* `INK_RECOGNITION_ENDPOINT` - O ponto final do seu recurso. Terá o seguinte aspeto: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`