---
title: Como garantir o dispositivo restrito de entrada com Azure AD e Azure Maps REST APIs
titleSuffix: Azure Maps
description: Como configurar uma aplicação sem navegador que suporta o sismo no AD AZure e chama APIs de REST Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: e62a5c984afb434b8c47b5ee8c5c66c61485dbfc
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090442"
---
# <a name="secure-an-input-constrained-device-with-azure-ad-and-azure-maps-rest-apis"></a>Proteja um dispositivo restrito de entrada com Azure AD e Azure Maps REST APIs

Este guia discute como proteger aplicações ou dispositivos públicos que não podem armazenar segredos de forma segura ou aceitar a entrada do navegador. Este tipo de aplicações insendo-se na categoria de IoT ou internet das coisas. Alguns exemplos destas aplicações podem incluir: dispositivos Smart TV ou aplicações de dados de sensores. 

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Criar um registo de inscrição no Azure AD

> [!NOTE]
> * **Leitura pré-requisito:** [Cenário: App de ambiente de trabalho que chama APIs web](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-overview)
> * O seguinte cenário utiliza o fluxo de código do dispositivo, que não envolve um navegador web para adquirir um token.

Crie a aplicação baseada no dispositivo em Azure AD para ativar o sinal de Azure AD. Esta aplicação terá acesso às APIs de REST do Azure Maps.

1. No portal Azure, na lista de serviços da Azure, selecione **Azure Ative Directory**  >  **App registra**  >  **Novas inscrições**.  

    > [!div class="mx-imgBorder"]
    > ![Registo da aplicação](./media/how-to-manage-authentication/app-registration.png)

2. Insira um **Nome**, escolha **contas neste diretório organizacional apenas** como o **tipo de conta suportada.** Em **Redirecionar URIs**, especifique **cliente público /nativo (mobile & desktop)** em seguida, adicione ao `https://login.microsoftonline.com/common/oauth2/nativeclient` valor. Para mais detalhes, consulte a aplicação Azure AD [Desktop que chama APIs web: Registo de aplicações](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration). Em seguida, **registe** o pedido.

    > [!div class="mx-imgBorder"]
    > ![Adicione detalhes de registo de aplicativos para nome e redirecione uri](./media/azure-maps-authentication/devicecode-app-registration.png)

3. Navegue para **autenticação** e permita **a aplicação Treat como cliente público.** Isto permitirá a autenticação do código do dispositivo com Azure AD.
    
    > [!div class="mx-imgBorder"]
    > ![Ativar o registo de aplicações como cliente público](./media/azure-maps-authentication/devicecode-public-client.png)

4.  Para atribuir permissões delegadas da API ao Azure Maps, aceda à aplicação. Em seguida, selecione **permissões API**  >  **Adicione uma permissão**. Nos **APIs a minha organização utiliza,** procure e selecione **Azure Maps.**

    > [!div class="mx-imgBorder"]
    > ![Adicionar permissões de API de aplicativo](./media/how-to-manage-authentication/app-permissions.png)

5. Selecione a caixa de verificação ao lado **do Access Azure Maps**e, em seguida, selecione **Permissões de adicionar**.

    > [!div class="mx-imgBorder"]
    > ![Selecione permissões de API de aplicativo](./media/how-to-manage-authentication/select-app-permissions.png)

6. Configure O controlo de acesso baseado em funções (Azure RBAC) para utilizadores ou grupos. Consulte [o acesso baseado em funções grant para utilizadores ao Azure Maps](#grant-role-based-access-for-users-to-azure-maps).

7. Adicione código para adquirir o fluxo de fichas na aplicação, para detalhes de implementação ver [fluxo de código do dispositivo](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-acquire-token#device-code-flow). Ao adquirir fichas, refira-se ao âmbito: `user_impersonation` que foi selecionado em passos anteriores.

> [!Tip]
> Utilize a Microsoft Authentication Library (MSAL) para adquirir fichas de acesso. Consulte recomendações na [aplicação Desktop que chama APIs web: Configuração de código](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-configuration)

8. Compôs o pedido HTTP com o token adquirido da Azure AD, e enviou um pedido com um cliente HTTP válido.

### <a name="sample-request"></a>Pedido de amostra
Aqui está um corpo de pedido de amostra para carregar uma simples Geofence representada como geometria do círculo usando um ponto central e um raio.

```http
POST /mapData/upload?api-version=1.0&dataFormat=geojson
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

 O corpo de pedido de amostra abaixo está na GeoJSON:
```json
{
    "type": "FeatureCollection",
    "features": [{
        "type": "Feature",
        "geometry": {
            "type": "Point",
            "coordinates": [-122.126986, 47.639754]
        },
        "properties": {
            "geometryId": "001",
            "radius": 500
        }
    }]
}
```

### <a name="sample-response"></a>Resposta da amostra:

Cabeçalhos:
```http
Location: https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0
Access-Control-Expose-Headers: Location
```

Corpo:
```json
{
  "operationId": "{operationId}",
  "status": "Succeeded",
  "created": "2020-01-02 1:02:03 AM +00:00",
  "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{resourceId}?api-version=1.0"
}
```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Passos seguintes

Encontre as métricas de utilização da API para a sua conta Azure Maps:
> [!div class="nextstepaction"]
> [Ver métricas de utilização](how-to-view-api-usage.md)
