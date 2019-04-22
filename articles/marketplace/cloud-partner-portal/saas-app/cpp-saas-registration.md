---
title: Registar uma aplicação SaaS - Azure Marketplace | Documentos da Microsoft
description: Explica como registar uma aplicação SaaS com o portal do Azure.
services: Azure, Marketplace, Cloud Partner Portal, Azure portal
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: pbutlerm
ms.openlocfilehash: fbc542ea2ed76d99d551d668b00bad1fb3719a9f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59012939"
---
# <a name="register-a-saas-application"></a>Registar uma aplicação SaaS

Este artigo explica como registar uma aplicação SaaS com o Microsoft [portal do Azure](https://portal.azure.com/).  Depois de um registo com êxito, irá receber um token de segurança do Azure Active Directory (Azure AD) que pode utilizar para aceder às APIs de preenchimento do SaaS.  Para obter mais informações sobre o Azure AD, consulte [o que é a autenticação?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>Fluxo de autenticação de serviço a serviço

O diagrama seguinte mostra o fluxo de subscrição de um novo cliente e quando essas APIs são utilizadas:

![SaaS oferecem o fluxo de API](./media/saas-offer-publish-api-flow-v1.png)

Azure não impõe quaisquer restrições a autenticação que o serviço SaaS expõe a seus usuários finais. No entanto, a autenticação com as APIs de preenchimento de SaaS é executada com um token de segurança do Azure AD, normalmente obtido registrando-se a aplicação SaaS através do portal do Azure. 


## <a name="register-an-azure-ad-secured-app"></a>Registar uma aplicação protegida por AD do Azure

Qualquer aplicação que pretenda utilizar as capacidades do Azure AD, tem de estar registada previamente no inquilino do Azure AD. Este processo de registro envolve a fornecer os detalhes do Azure AD sobre a sua aplicação, como a URL onde este se encontra, o URL para enviar respostas após um utilizador é autenticado, o URI que identifica a aplicação e assim por diante.  Para registar uma nova aplicação com o portal do Azure, execute os seguintes passos:

1.  Inicie sessão no [portal do Azure](https://portal.azure.com/).
2.  Se a sua conta permitir aceder a mais de uma, clique na sua conta no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
3.  No painel de navegação do lado esquerdo, clique nas **do Azure Active Directory** do serviço, clique em **registos das aplicações**e clique em **novo registo de aplicação**.

    ![Registos de aplicações do AD de SaaS](./media/saas-offer-app-registration-v1.png)

4.  Na página de criar, introduza o seu aplicativo\'informações de registo de s:
    -   **Nome**: Introduza um nome de aplicação com significado
    -   **Tipo de aplicação**: 
        - Selecione **Nativa** para [aplicações cliente](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) que estejam instaladas localmente num dispositivo. Esta definição é utilizada para [clientes nativos](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) públicos de OAuth.
        - Selecione **aplicação Web / API** para [aplicativos cliente](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) e [aplicações de API derecursos/](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) que são instaladas num servidor seguro. Esta definição é utilizada para OAuth confidencial [clientes web](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) e públicas [baseada no utilizador-agente clientes](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client).
        A mesma aplicação também pode expor um cliente e um recurso/API.
    -   **Início de sessão no URL**: Para aplicações de aplicação/API da Web, indique o URL de base da sua aplicação. Por exemplo, **http://localhost:31544** pode ser o URL para uma aplicação web em execução no seu computador local. Os utilizadores, em seguida, usaria este URL para iniciar sessão a uma aplicação de cliente da web.
    -   **URI de redirecionamento**: Para aplicações nativas, forneça o URI utilizado pelo Azure AD para devolver respostas token. Introduza um valor específico para seu aplicativo, por exemplo **http://MyFirstAADApp**.

        ![Registos de aplicações do AD de SaaS](./media/saas-offer-app-registration-v1-2.png)

        Para obter exemplos específicos de aplicações web ou aplicações nativas, veja o guia de introdução orientada instalações que estão disponíveis no *começar* secção a [guia para programadores do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

5.  Quando terminar, clique em **Criar**. Do Azure AD atribui um exclusivo *ID da aplicação* às suas aplicações e\'re direcionado para seu aplicativo\'página registo principal. Dependendo de se a sua aplicação é do tipo nativo ou Web, dispõe de várias opções para lhe adicionar mais capacidades.

>[!Note]
>Por predefinição, a aplicação recentemente registada é configurada para permitir que apenas os utilizadores a partir do mesmo inquilino para iniciar sessão na sua aplicação.


## <a name="using-the-azure-ad-security-token"></a>Com o token de segurança do Azure AD

Depois de registar o seu aplicativo, por meio de programação, pode pedir um token de segurança do Azure AD.  O publicador é esperado que utilize este token e fazer um pedido para resolvê-lo.  Ao usar as diversas APIs de preenchimento, o parâmetro de consulta de token está na URL quando o utilizador é redirecionado para o Web site de SaaS do Azure.  Este token só é válido durante uma hora.  Além disso, deve URL descodificar o valor do token do navegador antes de o utilizar.

Para obter mais informações sobre estes tokens, consulte [tokens de acesso do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Obter um token com base na aplicação do Azure AD

Método HTTP

`GET`

*Request URL* (URL do pedido)

**https://login.microsoftonline.com/*{tenantId}*/oauth2/token**

*Parâmetro URI*

|  **Parameter name** (Nome do parâmetro)  | **Necessário**  | **Descrição**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | Verdadeiro          | ID do inquilino da aplicação registada do AAD   |
|  |  |  |


*Cabeçalho do pedido*

|  **Nome do cabeçalho**  | **Necessário** |  **Descrição**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | Verdadeiro         | Tipo de conteúdo associado à solicitação. O valor predefinido é `application/x-www-form-urlencoded`.  |
|  |  |  |


*Corpo do pedido*

| **Nome da propriedade**   | **Necessário** |  **Descrição**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | Verdadeiro         | Tipo de concessão. O valor predefinido é `client_credentials`.                    |
|  Client_id          | Verdadeiro         |  Identificador de cliente/aplicação associado à aplicação do Azure AD.                  |
|  client_secret      | Verdadeiro         |  Palavra-passe associado à aplicação do Azure AD.                               |
|  Recurso           | Verdadeiro         |  Recurso de destino para a qual o token é solicitado. O valor predefinido é `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Resposta*

|  **Nome**  | **Tipo**       |  **Descrição**    |
| ---------- | -------------  | ------------------- |
| 200 OK    | TokenResponse  | Pedido efetuado com êxito   |
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


## <a name="next-steps"></a>Passos Seguintes

A aplicação protegida por AD do Azure pode agora utilizar o [SaaS preenchimento API versão 2](./cpp-saas-fulfillment-api-v2.md).
