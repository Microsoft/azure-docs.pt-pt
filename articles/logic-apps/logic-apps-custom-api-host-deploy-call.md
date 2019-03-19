---
title: Implementar e chamar web APIs e as APIs REST do Azure Logic Apps | Documentos da Microsoft
description: Implementar e chamar web APIs e as APIs REST para o sistema integratio fluxos de trabalho no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, stepsic, LADocs
ms.topic: article
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.date: 05/26/2017
ms.openlocfilehash: a9049ba1fbd7d3bdce061d277f6a7a02d9b1e4b7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880069"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>Implementar e chamar APIs personalizadas de fluxos de trabalho no Azure Logic Apps

Depois de [criar APIs personalizadas](./logic-apps-create-api-app.md) para utilização em fluxos de trabalho de aplicação de lógica, tem de implementar as suas APIs antes de poder chamá-los. Pode implementar as suas APIs conforme [aplicações web](../app-service/overview.md), mas considere implementar as suas APIs conforme [aplicações API](../app-service/app-service-web-tutorial-rest-api.md), que tornar seu trabalho mais fácil quando criar, aloja e consumir APIs na cloud e no local. Não precisa alterar qualquer código em suas APIs - apenas implementar seu código numa aplicação API. Pode alojar as suas APIs num [App Service do Azure](../app-service/overview.md), uma plataforma-como-um-serviço (PaaS que fornece fácil, altamente dimensionável e que aloja a API da oferta).

Embora pode chamar qualquer API a partir de uma aplicação lógica, a melhor experiência, adicione [OpenAPI (anteriormente Swagger) metadados](https://swagger.io/specification/) que descreve a API de operações e os parâmetros. Este ficheiro OpenAPI ajuda a sua API integrar mais facilmente e funcionam melhor com o logic apps.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Implementar a sua API como uma aplicação web ou a aplicação de API

Antes de poder chamar a API personalizada a partir de uma aplicação lógica, implemente a sua API como uma aplicação web ou a aplicação API App Service do Azure. Além disso, para tornar o OpenAPI do ficheiro ser lido pelo Designer de aplicações lógicas, defina as propriedades de definição de API e ative [recursos de várias origens (CORS) de partilha](../app-service/overview.md) para a sua aplicação web ou a aplicação API.

1. Na [portal do Azure](https://portal.azure.com), selecione a aplicação web ou a aplicação API.

2. No menu da aplicação que se abre, em **API**, escolha **definição da API**. Definir o **localização da definição de API** para o URL para o ficheiro swagger de OpenAPI.

   Normalmente, o URL é apresentado no seguinte formato: `https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Ligar ao ficheiro de OpenAPI para a API personalizada](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. Sob **API**, escolha **CORS**. Definir a política CORS para **permitido origens** ao **' *'** (permitir todas).

   Esta definição permite pedidos de Estruturador da aplicação lógica.

   ![Permitir pedidos a partir do Estruturador da aplicação lógica para a API personalizada](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Para obter mais informações, consulte [alojar uma API RESTful com CORS no App Service do Azure](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Chamar a API personalizada a partir de lógica de fluxos de trabalho de aplicação

Depois de configurar as propriedades de definição de API e o CORS, acionadores e ações a API personalizada devem estar disponíveis incluir no seu fluxo de trabalho de aplicação lógica. 

*  Para ver os sites que tem URLs de OpenAPI, pode procurar os Web sites de subscrição no Designer de aplicações lógicas.

*  Para ver as ações disponíveis e entradas apontando num documento OpenAPI, utilize o [HTTP + Swagger ação](../connectors/connectors-native-http-swagger.md).

*  Para chamar qualquer API, incluindo APIs que não têm ou expor um documento OpenAPI, pode sempre criar um pedido com o [ação de HTTP](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>Passos Seguintes

* [Descrição geral do conector personalizado](../logic-apps/custom-connector-overview.md)