---
title: Implementar e chamar APIs web & REST APIs de Aplicações Lógicas Azure
description: Implementar e chamar APIs web & REST APIs para fluxos de trabalho de integração do sistema em Aplicações lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/26/2017
ms.openlocfilehash: d1305be54a22b1460000a357074cbb1f67123bd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790746"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>Implementar e chamar APIs personalizados de fluxos de trabalho em Aplicações Lógicas Azure

Depois de [criar APIs personalizados](./logic-apps-create-api-app.md) para utilização em fluxos de trabalho de aplicações lógicas, tem de implementar as suas APIs antes de as poder ligar. Pode implementar as suas APIs como [aplicações web](../app-service/overview.md), mas considere implementar as suas APIs como [aplicações API](../app-service/app-service-web-tutorial-rest-api.md), que facilitam o seu trabalho quando constrói, hospeda e consome APIs na nuvem e nas instalações. Não tem de alterar nenhum código nas suas APIs - basta implementar o seu código para uma aplicação API. Você pode hospedar as suas APIs no [Azure App Service](../app-service/overview.md), uma oferta de plataforma-as-a-service (PaaS) que fornece hospedagem aPi altamente escalável e fácil.

Embora possa ligar para qualquer API de uma aplicação lógica, para a melhor experiência, adicione [metadados OpenAPI (anteriormente Swagger)](https://swagger.io/specification/) que descrevam as operações e parâmetros da sua API. Este ficheiro OpenAPI ajuda a sua API a integrar-se mais facilmente e a trabalhar melhor com aplicações lógicas.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Implemente a sua API como uma aplicação web ou app API

Antes de poder ligar para a Sua API personalizada a partir de uma aplicação lógica, implemente o seu API como uma aplicação web ou app API para o Azure App Service. Além disso, para tornar o seu ficheiro OpenAPI legível pelo Logic Apps Designer, delineie as propriedades de definição aPI e ligue a [partilha de recursos de origem cruzada (CORS)](../app-service/overview.md) para a sua aplicação web ou app API.

1. No [portal Azure,](https://portal.azure.com)selecione a sua aplicação web ou a aplicação API.

2. No menu de aplicações que abre, no âmbito da **API,** escolha a **definição API**. Delineie a definição de **API** para o URL para o seu ficheiro OpenAPI swagger.json.

   Normalmente, o URL aparece neste formato:`https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Link para openAPI arquivo para a sua API personalizada](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. Em **API,** escolha **CORS**. Definir a política CORS para **as origens permitidas** para **'*'** (permitir todos).

   Esta definição permite pedidos do Logic App Designer.

   ![Permitir pedidos do Logic App Designer para a sua API personalizada](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Para mais informações, consulte [Host a RESTful API com CORS in Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Ligue para a sua API personalizada a partir de fluxos de trabalho de aplicações lógicas

Depois de configurar as propriedades de definição API e CORS, os gatilhos e ações personalizados da API devem estar disponíveis para que possa incluir no fluxo de trabalho da sua aplicação lógica. 

*  Para ver websites que tenham URLs OpenAPI, pode navegar nos seus websites de subscrição no Logic Apps Designer.

*  Para visualizar as ações e inputs disponíveis apontando para um documento OpenAPI, utilize a [ação HTTP + Swagger](../connectors/connectors-native-http-swagger.md).

*  Para ligar para qualquer API, incluindo APIs que não tenham ou exponha um documento OpenAPI, pode sempre criar um pedido com a [ação HTTP](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do conector personalizado](../logic-apps/custom-connector-overview.md)