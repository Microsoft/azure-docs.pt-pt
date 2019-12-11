---
title: Plano de continuidade de negócios-QnA Maker
titleSuffix: Azure Cognitive Services
description: O objetivo principal do plano de continuidade de negócios é criar um ponto de extremidade de base de dados de conhecimento resiliente, o que não garantiria tempo de inatividade para o bot ou para o aplicativo que o consome.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: a31076d17ebaf8020fe4b3f82594b98a7c3bde74
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977459"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Criar um plano de continuidade de negócios para seu serviço de QnA Maker

O objetivo principal do plano de continuidade de negócios é criar um ponto de extremidade de base de dados de conhecimento resiliente, o que não garantiria tempo de inatividade para o bot ou para o aplicativo que o consome.

![QnA Maker plano bcp](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

A ideia de alto nível, como representada acima, é a seguinte:

1. Configure dois serviços de [QnA Maker](../How-To/set-up-qnamaker-service-azure.md) paralelos em [regiões emparelhadas do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. Mantenha os índices primário e secundário do Azure Search sincronizados. Use o exemplo do GitHub [aqui](https://github.com/pchoudhari/QnAMakerBackupRestore) para ver como fazer backup-restaurar índices do Azure.

3. Faça backup do Application Insights usando a [exportação contínua](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. Depois que as pilhas primária e secundária tiverem sido configuradas, use o [Gerenciador de tráfego](https://docs.microsoft.com/azure/traffic-manager/) para configurar os dois pontos de extremidade e configurar um método de roteamento.

5. Você precisaria criar um certificado de protocolo SSL (SSL) para o ponto de extremidade do Gerenciador de tráfego. [Associe o certificado SSL](https://docs.microsoft.com/azure/app-service/configure-ssl-bindings) nos serviços de aplicativo.

6. Por fim, use o ponto de extremidade do Gerenciador de tráfego em seu bot ou aplicativo.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Escolha a capacidade para sua implantação de QnA Maker](../Tutorials/choosing-capacity-qnamaker-deployment.md)
