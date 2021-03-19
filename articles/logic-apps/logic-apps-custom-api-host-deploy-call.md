---
title: Ligue para APIs web personalizado & REST APIs
description: Ligue para as suas próprias APIs web & REST APIs de Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 05/13/2020
ms.openlocfilehash: 7b4d00e8c0366d10fddafa66db699c1a59fd9ad7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "83659784"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>Implementar e chamar APIs personalizadas a partir dos fluxos de trabalho no Azure Logic Apps

Depois de [criar as suas próprias APIs](./logic-apps-create-api-app.md) para utilizar nos fluxos de trabalho de aplicações lógicas, precisa de implementar essas APIs antes de poder chamá-las. Pode implementar as suas APIs como [aplicações web,](../app-service/overview.md)mas considere implementar as suas APIs como [aplicações API](../app-service/app-service-web-tutorial-rest-api.md), que facilitam o seu trabalho ao construir, hospedar e consumir APIs na nuvem e nas instalações. Não precisa de alterar nenhum código nas suas APIs - basta implementar o seu código numa aplicação API. Você pode hospedar as suas APIs no [Azure App Service](../app-service/overview.md), uma oferta de plataforma como um serviço (PaaS) que fornece hospedagem API altamente escalável e fácil.

Embora possa ligar para qualquer API a partir de uma aplicação lógica, para a melhor experiência, adicione [metadados Da Swagger](https://swagger.io/specification/) que descrevem as operações e parâmetros da sua API. Este documento Swagger ajuda a sua API a integrar-se mais facilmente e a trabalhar melhor com aplicações lógicas.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Implemente a sua API como uma aplicação web ou app API

Antes de poder ligar para a API personalizada a partir de uma aplicação lógica, implemente a sua API como uma aplicação web ou app API para o Azure App Service. Para tornar o seu documento Swagger legível pelo Logic Apps Designer, desenverde as propriedades de definição API e ligue [a partilha de recursos de origem cruzada (CORS)](../app-service/overview.md) para a sua aplicação web ou app API.

1. No [portal Azure,](https://portal.azure.com)selecione a sua aplicação web ou app API.

2. No menu de aplicações que abre, em **API,** escolha **a definição API**. Desave o local de **definição de API** para o URL para o seu swagger.jsarquivado.

   Normalmente, o URL aparece neste formato: `https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Link para o documento Swagger para a sua API personalizada](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. Sob **a API,** escolha **CORS**. Desaveja a política CORS para **as origens permitidas** **para '*'** (permitir tudo).

   Esta definição permite pedidos do Logic App Designer.

   ![Pedidos de licença do Logic App Designer para a sua API personalizada](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Para mais informações, consulte [a Host a RESTful API com CORS no Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Ligue para a sua API personalizada a partir de fluxos de trabalho de aplicações lógicas

Depois de configurar as propriedades de definição API e CORS, os gatilhos e ações da API personalizadas devem estar disponíveis para que possa incluir no seu fluxo de trabalho de aplicações lógicas. 

*  Para ver websites que tenham URLs OpenAPI, pode navegar nos seus websites de subscrição no Logic Apps Designer.

*  Para visualizar as ações e entradas disponíveis apontando para um documento swagger, utilize a ação [HTTP + Swagger](../connectors/connectors-native-http-swagger.md).

*  Para chamar qualquer API, incluindo APIs que não tenha ou exponha um documento swagger, pode sempre criar um pedido com a [ação HTTP](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do conector personalizado](../logic-apps/custom-connector-overview.md)
