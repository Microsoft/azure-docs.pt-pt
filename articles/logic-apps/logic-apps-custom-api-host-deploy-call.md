---
title: Ligue para APIs web personalizados & APIs REST
description: Ligue para a sua própria web APIs & REST APIs de Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 05/13/2020
ms.openlocfilehash: 7b4d00e8c0366d10fddafa66db699c1a59fd9ad7
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659784"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>Implementar e chamar APIs personalizados de fluxos de trabalho em Aplicações Lógicas Azure

Depois de [criar as suas próprias APIs](./logic-apps-create-api-app.md) para usar nos fluxos de trabalho da sua aplicação lógica, precisa de implementar essas APIs antes de as poder ligar. Pode implementar as suas APIs como [aplicações web](../app-service/overview.md), mas considere implementar as suas APIs como [aplicações API](../app-service/app-service-web-tutorial-rest-api.md), que facilitam o seu trabalho quando constrói, hospeda e consome APIs na nuvem e nas instalações. Não tem de alterar nenhum código nas suas APIs - basta implementar o seu código para uma aplicação API. Você pode hospedar as suas APIs no [Azure App Service](../app-service/overview.md), uma oferta de plataforma-as-a-service (PaaS) que fornece hospedagem aPi altamente escalável e fácil.

Embora possa ligar para qualquer API de uma aplicação lógica, para a melhor experiência, adicione [metadados Swagger](https://swagger.io/specification/) que descrevam as operações e parâmetros da sua API. Este documento Swagger ajuda o seu API a integrar-se mais facilmente e a trabalhar melhor com aplicações lógicas.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Implemente a sua API como uma aplicação web ou app API

Antes de poder ligar para a Sua API personalizada a partir de uma aplicação lógica, implemente o seu API como uma aplicação web ou app API para o Azure App Service. Para tornar o seu documento Swagger legível pelo Logic Apps Designer, delineie as propriedades de definição API e ligue a [partilha de recursos de origem cruzada (CORS)](../app-service/overview.md) para a sua aplicação web ou app API.

1. No [portal Azure,](https://portal.azure.com)selecione a sua aplicação web ou a aplicação API.

2. No menu de aplicações que abre, no âmbito da **API,** escolha a **definição API**. Delineie a definição de **API** para o URL para o seu ficheiro swagger.json.

   Normalmente, o URL aparece neste formato:`https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Link para o documento Swagger para a sua API personalizada](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. Em **API,** escolha **CORS**. Definir a política CORS para **as origens permitidas** para **'*'** (permitir todos).

   Esta definição permite pedidos do Logic App Designer.

   ![Permitir pedidos do Logic App Designer para a sua API personalizada](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Para mais informações, consulte [Host a RESTful API com CORS in Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Ligue para a sua API personalizada a partir de fluxos de trabalho de aplicações lógicas

Depois de configurar as propriedades de definição API e CORS, os gatilhos e ações personalizados da API devem estar disponíveis para que possa incluir no fluxo de trabalho da sua aplicação lógica. 

*  Para ver websites que tenham URLs OpenAPI, pode navegar nos seus websites de subscrição no Logic Apps Designer.

*  Para visualizar as ações e inputs disponíveis apontando para um documento Swagger, utilize a [ação HTTP + Swagger](../connectors/connectors-native-http-swagger.md).

*  Para ligar para qualquer API, incluindo APIs que não tenham ou exponha um documento Swagger, pode sempre criar um pedido com a [ação HTTP](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do conector personalizado](../logic-apps/custom-connector-overview.md)
