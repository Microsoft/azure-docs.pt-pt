---
title: Implantar e chamar APIs Web & APIs REST de aplicativos lógicos do Azure
description: Implantar e chamar APIs Web & APIs REST para fluxos de trabalho de integração do sistema em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/26/2017
ms.openlocfilehash: d1305be54a22b1460000a357074cbb1f67123bd6
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790746"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>Implantar e chamar APIs personalizadas de fluxos de trabalho em aplicativos lógicos do Azure

Depois de [criar APIs personalizadas](./logic-apps-create-api-app.md) para uso em fluxos de trabalho de aplicativo lógico, você deve implantar suas APIs antes de poder chamá-las. Você pode implantar suas APIs como [aplicativos Web](../app-service/overview.md), mas considere implantar suas APIs como [aplicativos de API](../app-service/app-service-web-tutorial-rest-api.md), o que facilita o trabalho quando você cria, hospeda e consome APIs na nuvem e no local. Você não precisa alterar nenhum código em suas APIs – basta implantar seu código em um aplicativo de API. Você pode hospedar suas APIs no [serviço de Azure app](../app-service/overview.md), uma oferta de PaaS (plataforma como serviço) que fornece Hospedagem de API altamente escalonável e fácil.

Embora você possa chamar qualquer API de um aplicativo lógico, para obter a melhor experiência, adicione os [metadados openapi (anteriormente Swagger)](https://swagger.io/specification/) que descrevem as operações e os parâmetros da API. Esse arquivo OpenAPI ajuda sua API a integrar-se mais facilmente e a trabalhar melhor com aplicativos lógicos.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Implantar sua API como um aplicativo Web ou aplicativo de API

Antes de chamar a API personalizada de um aplicativo lógico, implante sua API como um aplicativo Web ou aplicativo de API para Azure App serviço. Além disso, para tornar o arquivo OpenAPI legível pelo designer de aplicativos lógicos, defina as propriedades de definição de API e ative o [CORS (compartilhamento de recursos entre origens)](../app-service/overview.md) para seu aplicativo Web ou aplicativo de API.

1. Na [portal do Azure](https://portal.azure.com), selecione seu aplicativo Web ou aplicativo de API.

2. No menu do aplicativo que é aberto, em **API**, escolha **definição de API**. Defina o **local de definição de API** como a URL para o arquivo Swagger. JSON do openapi.

   Normalmente, a URL aparece neste formato: `https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Link para o arquivo OpenAPI para sua API personalizada](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. Em **API**, escolha **CORS**. Defina a política CORS para **origens permitidas** como **' * '** (permitir tudo).

   Essa configuração permite solicitações do designer de aplicativo lógico.

   ![Permitir solicitações do designer de aplicativo lógico para sua API personalizada](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Para obter mais informações, consulte [hospedar uma API RESTful com CORS no serviço Azure app](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Chamar sua API personalizada de fluxos de trabalho do aplicativo lógico

Depois de configurar as propriedades de definição de API e CORS, os gatilhos e as ações da API personalizada devem estar disponíveis para você incluir no fluxo de trabalho do aplicativo lógico. 

*  Para exibir sites que têm URLs OpenAPI, você pode procurar seus sites de assinatura no designer de aplicativos lógicos.

*  Para exibir as ações e as entradas disponíveis apontando para um documento do OpenAPI, use a [ação http + Swagger](../connectors/connectors-native-http-swagger.md).

*  Para chamar qualquer API, incluindo APIs que não têm ou expõem um documento OpenAPI, você sempre pode criar uma solicitação com a [ação http](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>Passos seguintes

* [Visão geral do conector personalizado](../logic-apps/custom-connector-overview.md)