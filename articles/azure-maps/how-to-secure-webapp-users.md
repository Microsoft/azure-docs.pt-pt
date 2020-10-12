---
title: Como garantir uma aplicação web com um único sign-in interativo
titleSuffix: Azure Maps
description: Como configurar uma aplicação web que suporta o Azure AD um único sign-on com a Azure Maps Web SDK usando o protocolo OpenID Connect.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 50194341d1d34da4b02558461f532ae64b941b16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319627"
---
# <a name="secure-a-web-application-with-user-sign-in"></a>Garantir uma aplicação web com o utilizador iniciar s-in

O seguinte guia diz respeito a uma aplicação que está hospedada em servidores web, mantém vários cenários de negócio e implementa para servidores web. A aplicação tem a obrigação de fornecer recursos protegidos protegidos apenas aos utilizadores da AD Azure. O objetivo do cenário é permitir que a aplicação web autente para Azure AD e ligue para Azure Maps REST APIs em nome do utilizador.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Criar um registo de inscrição no Azure AD

Tem de criar a aplicação web em Azure AD para que os utilizadores entrem. Esta aplicação web irá então delegar o acesso do utilizador às APIs do Azure Maps REST.

1. No portal Azure, na lista de serviços da Azure, selecione **Azure Ative Directory**  >  **App registra**  >  **Novas inscrições**.  

    > [!div class="mx-imgBorder"]
    > ![Registo da aplicação](./media/how-to-manage-authentication/app-registration.png)

2. Introduza um **Nome**, escolha um **tipo de conta de suporte,** forneça um URI de redirecionamento que representará o url que a Azure AD emitirá o token e é o url onde o controlo do mapa está hospedado. Para mais detalhes, consulte cenário AD [Azure: web app que assina nos utilizadores](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-overview). Complete os passos fornecidos a partir do cenário AZure AD.  

3. Uma vez concluído o registo da aplicação, confirme que o registo da inscrição funciona para os utilizadores. Uma vez que o sismo funciona, então o pedido pode ser concedido acesso delegado às APIs DE REST do Azure Maps.
    
4.  Para atribuir permissões delegadas da API ao Azure Maps, aceda à aplicação. Em seguida, selecione **permissões API**  >  **Adicione uma permissão**. Nos **APIs a minha organização utiliza,** procure e selecione **Azure Maps.**

    > [!div class="mx-imgBorder"]
    > ![Adicionar permissões de API de aplicativo](./media/how-to-manage-authentication/app-permissions.png)

5. Selecione a caixa de verificação ao lado **do Access Azure Maps**e, em seguida, selecione **Permissões de adicionar**.

    > [!div class="mx-imgBorder"]
    > ![Selecione permissões de API de aplicativo](./media/how-to-manage-authentication/select-app-permissions.png)

6. Habilitar a aplicação web a ligar para Azure Maps REST APIs configurando o registo da aplicação com um segredo de aplicação, Para etapas detalhadas, consulte [uma aplicação web que chama APIs web: Registo de aplicações](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-app-registration). É necessário um segredo para autenticar a Azure AD em nome do utilizador. O certificado de registo de aplicações ou segredo deve ser armazenado numa loja segura para a aplicação web para recuperar para autenticar a Azure AD. 
   
   * Se a aplicação já tiver configurado um registo de aplicações AD Azure e um segredo este passo pode ser ignorado.

> [!Tip]
> Se a aplicação estiver hospedada num ambiente Azure, recomendamos a utilização [de identidades geridas para recursos Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) e uma instância Azure Key Vault para aceder a segredos, [adquirindo um token de acesso para](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token) aceder a segredos ou certificados do Azure Key Vault. Para ligar ao Cofre da Chave Azure para recuperar segredos, consulte [tutorial para se conectar através da identidade gerida.](https://docs.microsoft.com/azure/key-vault/general/tutorial-net-create-vault-azure-web-app)
   
7. Implementar um ponto final seguro para o Azure Maps Web SDK aceder a um token. 
   
   * Para obter um controlador de fichas de amostra, consulte [amostras Azure Maps AD Azure](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/blob/master/src/OpenIdConnect/AzureMapsOpenIdConnectv1/AzureMapsOpenIdConnect/Controllers/TokenController.cs). 
   * Para uma implementação não-AspNetCore ou outra, consulte [o Token adquirir para a aplicação a](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token) partir da documentação AD AZure.
   * O ponto final de token seguro é responsável por devolver um token de acesso para o utilizador autenticado e autorizado a ligar para a Azure Maps REST APIs.

8. Configure O controlo de acesso baseado em funções Azure para utilizadores ou grupos. Consulte [o acesso baseado em funções de subvenção para os utilizadores](#grant-role-based-access-for-users-to-azure-maps).

9. Configure a página de aplicação web com o Azure Maps Web SDK para aceder ao ponto final de token seguro. 

```javascript
var map = new atlas.Map("map", {
        center: [-122.33, 47.64],
        zoom: 12,
        language: "en-US",
        authOptions: {
            authType: "anonymous",
            clientId: "<insert>",  // azure map account client id
            getToken: function (resolve, reject, map) {
                var xhttp = new XMLHttpRequest();
                xhttp.open("GET", "/api/token", true); // the url path maps to the token endpoint.
                xhttp.onreadystatechange = function () {
                    if (this.readyState === 4 && this.status === 200) {
                        resolve(this.responseText);
                    } else if (this.status !== 200) {
                        reject(this.responseText);
                    }
                };

                xhttp.send();
            }
        }
    });
    map.events.add("tokenacquired", function () {
        console.log("token acquired");
    });
    map.events.add("error", function (err) {
        console.log(JSON.stringify(err.error));
    });
```

[!INCLUDE [grant role access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Passos seguintes

Maior compreensão do cenário de aplicação web:
> [!div class="nextstepaction"]
> [Cenário: aplicação web que assina nos utilizadores](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-overview)

Encontre as métricas de utilização da API para a sua conta Azure Maps:
> [!div class="nextstepaction"]
> [Ver métricas de utilização](how-to-view-api-usage.md)

Explore amostras que mostram como integrar a Azure AD com Azure Maps:
> [!div class="nextstepaction"]
> [Azure Maps Azure AD Web App Samples](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/OpenIdConnect)
