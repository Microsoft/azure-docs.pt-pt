---
title: Plano de continuidade de negócios - QnA Maker
description: O principal objetivo do plano de continuidade do negócio é criar um ponto final resiliente da base de conhecimento, o que garantiria que não haveria tempo de descanso para o Bot ou para a aplicação que o consumisse.
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 5d1501ecc42fe948959075cec7d728f6c9df908a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80887078"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Crie um plano de continuidade de negócios para o seu serviço QnA Maker

O principal objetivo do plano de continuidade do negócio é criar um ponto final resiliente da base de conhecimento, o que garantiria que não haveria tempo de descanso para o Bot ou para a aplicação que o consumisse.

## <a name="business-continuity-with-traffic-manager"></a>Continuidade do negócio com gestor de tráfego

> [!div class="mx-imgBorder"]
> ![QnA Maker bcp plano](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

A ideia de alto nível, tal como acima representada, é a seguinte:

1. Criar dois [serviços paralelos da QnA Maker](set-up-qnamaker-service-azure.md) em [regiões emparelhadas de Azure.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)

2. [Faça backup](../../../app-service/manage-backup.md) do seu serviço primário de QnA Maker App e [restaure-o](../../../app-service/web-sites-restore.md) na configuração secundária. Isto garantirá que ambas as configurações funcionem com o mesmo nome de anfitrião e chaves.

3. Mantenha os índices de pesquisa primário e secundário do Azure sincronizados. Use a amostra GitHub [aqui](https://github.com/pchoudhari/QnAMakerBackupRestore) para ver como restaurar os índices Azure de backup.

4. Recue os Insights de Aplicação utilizando [a exportação contínua](../../../application-insights/app-insights-export-telemetry.md).

5. Uma vez criadas as pilhas primárias e secundárias, utilize o gestor de [tráfego](../../../traffic-manager/traffic-manager-overview.md) para configurar os dois pontos finais e configurar um método de encaminhamento.

6. Você precisaria criar um Transport Layer Security (TLS), anteriormente conhecido como Secure Sockets Layer (SSL), certificado para o seu ponto final do gestor de tráfego. [Ligue o certificado TLS/SSL](../../../app-service/configure-ssl-bindings.md) nos seus serviços de Aplicação.

7. Por fim, utilize o ponto final do gestor de tráfego no seu Bot ou App.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Escolher capacidade](./improve-knowledge-base.md)