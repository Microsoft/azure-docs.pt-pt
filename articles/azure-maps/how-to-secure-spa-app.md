---
title: Como garantir uma aplicação de uma única página com o sign-in não interativo
titleSuffix: Azure Maps
description: Como configurar uma aplicação de uma única página com o controlo de acesso baseado em Ad Role não interativo e com o Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-javascript
ms.openlocfilehash: 5b7f26a03c117620be7c16abaf689763e370e5ba
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285701"
---
# <a name="how-to-secure-a-single-page-application-with-non-interactive-sign-in"></a>Como garantir uma aplicação de uma única página com o sign-in não interativo

O seguinte guia diz respeito a uma aplicação que utiliza o Azure Ative Directory (Azure AD) para fornecer um token de acesso às aplicações do Azure Maps quando o utilizador não pode iniciar seducação no Azure AD. Este fluxo requer o alojamento de um serviço web que deve ser protegido apenas para ser acedido pela aplicação web de uma página única. Existem múltiplas implementações que podem realizar a autenticação para a Azure AD. Este guia aproveita o produto, Azure Function para adquirir fichas de acesso.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

> [!Tip]
> Os mapas Azure podem suportar fichas de acesso a partir de fluxos de entrada/interação do utilizador. Os fluxos interativos permitem um âmbito mais restrito de revogação de acessos e gestão secreta.

## <a name="create-azure-function"></a>Criar a Função do Azure

Crie uma aplicação de serviço web segura que seja responsável pela autenticação da Azure AD. 

1. Criar uma função no portal Azure. Para obter mais informações, consulte [Criar Função Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function).

2. Configure a política CORS sobre a função Azure para ser acessível pela aplicação web de página única. Isto irá proteger os clientes do navegador às origens permitidas da sua aplicação web. Consulte [a funcionalidade ADD CORS](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-rest-api#add-cors-functionality).

3. [Adicione uma identidade atribuída](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity) ao sistema na função Azure para permitir a criação de um diretor de serviço para autenticar a Azure AD.  

4. Conceder acesso baseado em funções para a identidade atribuída ao sistema para a conta Azure Maps. Consulte [o acesso baseado em funções grant](#grant-role-based-access) para mais detalhes.

5. Escreva código para a função Azure para obter tokens de acesso Azure Maps utilizando identidade atribuída ao sistema com um dos mecanismos suportados ou o protocolo REST. Ver [Obter fichas para recursos Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity)

    Um exemplo de protocolo DE REPOUSO de amostra:

    ```http
    GET /MSI/token?resource=https://atlas.microsoft.com/&api-version=2019-08-01 HTTP/1.1
    Host: localhost:4141
    ```

    Resposta da amostra:

    ```http
    HTTP/1.1 200 OK
    Content-Type: application/json

    {
        "access_token": "eyJ0eXAi…",
        "expires_on": "1586984735",
        "resource": "https://atlas.microsoft.com/",
        "token_type": "Bearer",
        "client_id": "..."
    }
    ```

6. Configure a segurança para a função Azure HttpTrigger

   * [Criar uma chave de acesso de função](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#authorization-keys)
   * [Fixar o ponto final HTTP](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#secure-an-http-endpoint-in-production) para a função Azure na produção.
   
7. Configure aplicação web Azure Maps Web SDK. 

    ```javascript
    //URL to custom endpoint to fetch Access token
    var url = 'https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>';

    var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                view: "Auto",
            authOptions: {
                authType: "anonymous",
                clientId: "<insert>", // azure map account client id
                getToken: function(resolve, reject, map) {
                    fetch(url).then(function(response) {
                        return response.text();
                    }).then(function(token) {
                        resolve(token);
                    });
                }
            }
        });

        // use the following events to debug, you can remove them at any time.
        map.events.add("tokenacquired", function () {
            console.log("token acquired");
        });
        map.events.add("error", function (err) {
            console.log(JSON.stringify(err.error));
        });
    ```

## <a name="grant-role-based-access"></a>Concessão de acesso baseado em funções

Concede *o controlo de acesso baseado em funções* (RBAC) atribuindo a identidade atribuída ao sistema a uma ou mais definições de funções Azure. Para ver as definições de funções rbac que estão disponíveis para O Azure Maps, vá ao **Controlo de Acesso (IAM)**. Selecione **Roles**, e, em seguida, procure por papéis que comecem com *Azure Maps*.

1. Aceda à sua **Conta Azure Maps.** Selecione a atribuição de função **do controlo de acesso (IAM).**  >  **Role assignment**

    > [!div class="mx-imgBorder"]
    > ![Grant RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. No separador **Role assignments,** em **Role**, selecione uma definição de função incorporada no Azure Maps, como O Leitor **de Dados Azure Maps** ou **Azure Maps Data Contributor**. No **acesso de Atribuir acesso a**, selecione App de **função**. Selecione o principal pelo nome. Em seguida, selecione **Guardar**.

   * Consulte detalhes em [Adicionar ou remover atribuições de funções](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

> [!WARNING]
> As definições de funções incorporadas Azure Maps proporcionam um acesso de autorização muito grande a muitas APIs DE REST Azure Maps. Para restringir o acesso das APIs ao mínimo, consulte [criar uma definição de função personalizada e atribuir a identidade atribuída ao sistema](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) à definição de função personalizada. Isto permitirá o menor privilégio necessário para a aplicação aceder ao Azure Maps.

## <a name="next-steps"></a>Passos seguintes

Maior compreensão do cenário de aplicação de página única:
> [!div class="nextstepaction"]
> [Aplicação de página única](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-overview)

Encontre as métricas de utilização da API para a sua conta Azure Maps:
> [!div class="nextstepaction"]
> [Ver métricas de utilização](how-to-view-api-usage.md)

Explore outras amostras que mostram como integrar o Azure AD com a Azure Maps:
> [!div class="nextstepaction"]
> [Amostras de mapas de Azure](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ClientGrant)
