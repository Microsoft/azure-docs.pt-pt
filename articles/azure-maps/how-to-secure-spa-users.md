---
title: Como garantir uma aplicação de uma única página com o pedido de s indicador
titleSuffix: Azure Maps
description: Como configurar uma aplicação de uma única página que suporta o Azure AD um único sign-on com Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 0ba2e23e8121a76ec281b5e411819ee7d450cbe0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319695"
---
# <a name="secure-a-single-page-application-with-user-sign-in"></a>Proteja uma aplicação de uma única página com o pedido de s indicador

O seguinte guia diz respeito a uma aplicação que está hospedada num servidor de conteúdo ou que tem dependências mínimas do servidor web. A aplicação fornece recursos protegidos protegidos apenas aos utilizadores da AD Azure. O objetivo do cenário é permitir que a aplicação web autente para Azure AD e ligue para Azure Maps REST APIs em nome do utilizador.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Criar um registo de inscrição no Azure AD

Crie a aplicação web em Azure AD para os utilizadores iniciarem sessão. A aplicação web delega o acesso do utilizador às APIs do Azure Maps REST.

1. No portal Azure, na lista de serviços da Azure, selecione **Azure Ative Directory**  >  **App registra**  >  **Novas inscrições**.  

    > [!div class="mx-imgBorder"]
    > ![Registo da aplicação](./media/how-to-manage-authentication/app-registration.png)

2. Introduza um **Nome**, escolha um **tipo de conta de suporte,** forneça um URI de redirecionamento que representará o url que a Azure AD emitirá o token e é o url onde o controlo do mapa está hospedado. Para obter uma amostra detalhada, consulte [as amostras Azure Maps Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant). Em seguida, selecione **Registar**.  

3. Para atribuir permissões delegadas da API ao Azure Maps, aceda à aplicação. Em seguida, nos **registos da App,** selecione **permissões API**  >  **Adicione uma permissão**. Nos **APIs a minha organização utiliza,** procure e selecione **Azure Maps.**

    > [!div class="mx-imgBorder"]
    > ![Adicionar permissões de API de aplicativo](./media/how-to-manage-authentication/app-permissions.png)

4. Selecione a caixa de verificação ao lado **do Access Azure Maps**e, em seguida, selecione **Permissões de adicionar**.

    > [!div class="mx-imgBorder"]
    > ![Selecione permissões de API de aplicativo](./media/how-to-manage-authentication/select-app-permissions.png)

5. Ativar `oauth2AllowImplicitFlow` . Para o habilitar, na secção **Manifesto** do registo da sua aplicação, definido `oauth2AllowImplicitFlow` para `true` .

6. Copie o ID da aplicação AD Azure e o ID do inquilino Azure AD a partir do registo da aplicação para usar na Web SDK. Adicione os dados de registo da aplicação AZure AD e `x-ms-client-id` a conta Azure Map à Web SDK.

    ```javascript
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js" />
        <script>
            var map = new atlas.Map("map", {
                center: [-122.33, 47.64],
                zoom: 12,
                language: "en-US",
                authOptions: {
                    authType: "aad",
                    clientId: "<insert>",  // azure map account client id
                    aadAppId: "<insert>",  // azure ad app registration id
                    aadTenant: "<insert>", // azure ad tenant id
                    aadInstance: "https://login.microsoftonline.com/"
                }
            });
        </script>   
    ```

7. Configure O controlo de acesso baseado em funções Azure para utilizadores ou grupos. Consulte as [seguintes secções para permitir o RBAC](#grant-role-based-access-for-users-to-azure-maps).
   
[!INCLUDE [grant role access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Passos seguintes

Maior compreensão do cenário de aplicação de uma página única:
> [!div class="nextstepaction"]
> [Aplicação de página única](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-overview)

Encontre as métricas de utilização da API para a sua conta Azure Maps:
> [!div class="nextstepaction"]
> [Ver métricas de utilização](how-to-view-api-usage.md)

Explore amostras que mostram como integrar a Azure AD com Azure Maps:
> [!div class="nextstepaction"]
> [Amostras de mapas de Azure](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant)
