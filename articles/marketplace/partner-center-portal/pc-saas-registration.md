---
title: Registrar um aplicativo SaaS | Azure Marketplace
description: Explica como registrar um aplicativo SaaS usando o portal do Azure.
services: Azure, Marketplace, Cloud Partner Portal, Azure portal
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: b2e02e42ab63f893574ca5217fd2f36c7481aabd
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827947"
---
# <a name="register-a-saas-application"></a>Registar uma aplicação SaaS

Este artigo explica como registrar um aplicativo SaaS usando o Microsoft [portal do Azure](https://portal.azure.com/).  Após um registro bem-sucedido, você receberá um token de segurança Azure Active Directory (Azure AD) que pode ser usado para acessar as APIs de preenchimento de SaaS.  Para obter mais informações sobre o Azure AD, consulte [o que é autenticação?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>Fluxo de autenticação de serviço a serviço

O diagrama a seguir mostra o fluxo de assinatura de um novo cliente e quando essas APIs são usadas:

![Fluxo de API da oferta de SaaS](./media/saas-offer-publish-api-flow-v1.png)

O Azure não impõe nenhuma restrição na autenticação que o serviço SaaS expõe para seus usuários finais. No entanto, a autenticação com as APIs de preenchimento de SaaS é executada com um token de segurança do Azure AD, normalmente obtido com o registro do aplicativo SaaS por meio do portal do Azure. 


## <a name="register-an-azure-ad-secured-app"></a>Registrar um aplicativo protegido pelo Azure AD

Qualquer aplicação que pretenda utilizar as capacidades do Azure AD, tem de estar registada previamente no inquilino do Azure AD. Esse processo de registro envolve a concessão de detalhes do Azure AD sobre seu aplicativo, como a URL onde ele está localizado, a URL para enviar respostas depois que um usuário é autenticado, o URI que identifica o aplicativo e assim por diante.  Para registrar um novo aplicativo usando o portal do Azure, execute as seguintes etapas:

1.  Iniciar sessão no [portal do Azure](https://portal.azure.com/).
2.  Se sua conta fornecer acesso a mais de um, clique em sua conta no canto superior direito e defina a sessão do portal para o locatário do Azure AD desejado.
3.  No painel de navegação esquerdo, clique no serviço **Azure Active Directory** , clique em **registros de aplicativo**e clique em **novo registro de aplicativo**.

    ![Registros de aplicativo SaaS do AD](./media/saas-offer-app-registration-v1.png)

4.  Na página criar, insira as informações de registro do seu aplicativo\'s:
    -   **Nome**: Insira um nome de aplicativo significativo
    -   **Tipo de aplicativo**: 
        - Selecione **Nativa** para [aplicações cliente](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) que estejam instaladas localmente num dispositivo. Esta definição é utilizada para [clientes nativos](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) públicos de OAuth.
        - Selecione **aplicativo Web/API** para [aplicativos cliente](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) e [aplicativos de recurso/API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) que estão instalados em um servidor seguro. Essa configuração é usada para [clientes Web](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) confidenciais OAuth e [clientes baseados em agente de usuário](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client)público.
        A mesma aplicação também pode expor um cliente e um recurso/API.
    -   **URL de logon**: para aplicativos Web/aplicativo de API, forneça a URL base do seu aplicativo. Por exemplo, **http://localhost:31544** pode ser a URL de um aplicativo Web em execução no computador local. Os usuários usariam essa URL para entrar em um aplicativo cliente Web.
    -   **URI de redirecionamento**: para aplicativos nativos, forneça o URI usado pelo Azure ad para retornar respostas de token. Insira um valor específico para seu aplicativo, por exemplo **http://MyFirstAADApp** .

        ![Registros de aplicativo SaaS do AD](./media/saas-offer-app-registration-v1-2.png)

        Para obter exemplos específicos para aplicativos Web ou aplicativos nativos, Confira as configurações guiadas de início rápido que estão disponíveis *na seção Introdução* do [Guia de desenvolvedores do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

5.  Quando terminar, clique em **Criar**. O Azure AD atribui uma ID de *aplicativo* exclusiva ao seu aplicativo e você\'Redirecionado para a página de registro principal do aplicativo\'s. Dependendo de se a sua aplicação é do tipo nativo ou Web, dispõe de várias opções para lhe adicionar mais capacidades.

>[!Note]
>Por padrão, o aplicativo registrado recentemente é configurado para permitir que somente usuários do mesmo locatário entrem em seu aplicativo.


## <a name="using-the-azure-ad-security-token"></a>Usando o token de segurança do Azure AD

Depois de registrar seu aplicativo, você pode solicitar um token de segurança do Azure AD programaticamente.  O editor deve usar esse token e fazer uma solicitação para resolvê-lo.  Ao usar as várias APIs de preenchimento, o parâmetro de consulta de token está na URL quando o usuário é redirecionado para o site SaaS do Azure.  Esse token só é válido por uma hora.  Além disso, você deve decodificar por URL o valor do token do navegador antes de usá-lo.

Para obter mais informações sobre esses tokens, consulte [Azure Active Directory tokens de acesso](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Obter um token baseado no aplicativo do Azure AD

Método HTTP

`POST`

*Request URL* (URL do pedido)

**https://login.microsoftonline.com/ *{tenantid}* /oauth2/token**

*Parâmetro de URI*

|  **Parameter name** (Nome do parâmetro)  | **Necessário**  | **Descrição**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | Verdadeiro          | ID do locatário do aplicativo do AAD registrado   |
|  |  |  |


*Cabeçalho da solicitação*

|  **Nome do cabeçalho**  | **Necessário** |  **Descrição**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Tipo de conteúdo     | Verdadeiro         | Tipo de conteúdo associado à solicitação. O valor padrão é `application/x-www-form-urlencoded`.  |
|  |  |  |


*Corpo da solicitação*

| **Nome da propriedade**   | **Necessário** |  **Descrição**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | Verdadeiro         | Tipo de concessão. O valor padrão é `client_credentials`.                    |
|  Client_id          | Verdadeiro         |  Identificador de cliente/aplicativo associado ao aplicativo do Azure AD.                  |
|  client_secret      | Verdadeiro         |  Senha associada ao aplicativo do Azure AD.                               |
|  Recurso           | Verdadeiro         |  Recurso de destino para o qual o token é solicitado. O valor padrão é `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Resposta*

|  **Nome**  | **Tipo**       |  **Descrição**    |
| ---------- | -------------  | ------------------- |
| 200 OK    | TokenResponse  | Solicitação bem-sucedida   |
|  |  |  |

*TokenResponse*

Token de resposta de exemplo:

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "62d94f6c-d599-489b-a797-3e10e42fbe22",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```


## <a name="next-steps"></a>Passos seguintes

Seu aplicativo protegido pelo Azure AD agora pode usar a [API de preenchimento de SaaS versão 2](./pc-saas-fulfillment-api-v2.md).
