---
title: Plano de continuidade de negócios - QnA Maker
titleSuffix: Azure Cognitive Services
description: O principal objetivo do plano de continuidade do negócio é criar um ponto final resiliente da base de conhecimento, o que garantiria que não haveria tempo de descanso para o Bot ou para a aplicação que o consumisse.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 2100426923418104c31933bf9d1406a6441d3841
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410919"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Crie um plano de continuidade de negócios para o seu serviço QnA Maker

O principal objetivo do plano de continuidade do negócio é criar um ponto final resiliente da base de conhecimento, o que garantiria que não haveria tempo de descanso para o Bot ou para a aplicação que o consumisse.

![QnA Maker bcp plano](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

A ideia de alto nível, tal como acima representada, é a seguinte:

1. Criar dois [serviços paralelos da QnA Maker](../How-To/set-up-qnamaker-service-azure.md) em [regiões emparelhadas de Azure.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)

2. Mantenha os índices de pesquisa primário e secundário do Azure sincronizados. Use a amostra GitHub [aqui](https://github.com/pchoudhari/QnAMakerBackupRestore) para ver como restaurar os índices Azure de backup.

3. Recue os Insights de Aplicação utilizando [a exportação contínua](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. Uma vez criadas as pilhas primárias e secundárias, utilize o gestor de [tráfego](https://docs.microsoft.com/azure/traffic-manager/) para configurar os dois pontos finais e configurar um método de encaminhamento.

5. Você precisaria criar um Transport Layer Security (TLS), anteriormente conhecido como Secure Sockets Layer (SSL), certificado para o seu ponto final do gestor de tráfego. [Ligue o certificado TLS/SSL](https://docs.microsoft.com/azure/app-service/configure-ssl-bindings) nos seus serviços de Aplicação.

6. Por fim, utilize o ponto final do gestor de tráfego no seu Bot ou App.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Escolha a capactiy](./improve-knowledge-base.md)
