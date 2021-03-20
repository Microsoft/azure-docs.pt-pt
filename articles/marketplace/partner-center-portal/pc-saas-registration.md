---
title: Registar uma aplicação SaaS - Azure Marketplace
description: Saiba como usar o portal Azure para registar uma aplicação SaaS e receber um sinal de segurança do Azure Ative Directory.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 39a0830806d2d9c7358d175cae703e9c81c45b02
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93130022"
---
# <a name="register-a-saas-application"></a>Registar uma aplicação SaaS

Este artigo explica como registar uma aplicação SaaS utilizando o portal Microsoft [Azure](https://portal.azure.com/) e como obter o token de acesso da editora (token de acesso do Azure Ative Directory). A editora utilizará este token para autenticar a aplicação SaaS, chamando as APIs de Cumprimento saas.  As APIs de Realização utilizam as credenciais de cliente OAuth 2.0 para conceder fluxo no Azure Ative Directory (v1.0) para fazer um pedido de ficha de acesso de serviço.

O Azure Marketplace não impõe quaisquer constrangimentos ao método de autenticação que o seu serviço SaaS utiliza para os utilizadores finais. O fluxo abaixo é necessário apenas para autenticar o Serviço SaaS no Azure Marketplace.

Para obter mais informações sobre a Azure AD (Ative Directory), consulte [o que é a autenticação?](../../active-directory/develop/authentication-vs-authorization.md)

## <a name="register-an-azure-ad-secured-app"></a>Registe uma aplicação azure ad-secureed

Qualquer aplicação que pretenda utilizar as capacidades do Azure AD, tem de estar registada previamente no inquilino do Azure AD. Este processo de registo envolve dar à Azure AD alguns detalhes sobre a sua candidatura. Para registar uma nova aplicação utilizando o portal Azure, execute os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Se a sua conta lhe der acesso a mais de um, clique na sua conta no canto superior direito e deite a sua sessão de portal ao inquilino AD AD desejado.
3. No painel de navegação à esquerda, clique no serviço **Azure Ative Directory,** clique nos **registos da App** e clique em **Novo registo de aplicações.**

    ![Registos de Aplicações AD SaaS](./media/saas-offer-app-registration-v1.png)

4. Na página Criar, insira as \' informações de inscrição do seu pedido:
    -   **Nome**: Introduza um nome de aplicação significativo
    -   **Tipo de aplicação:**  
        
        Selecione **aplicativo Web / API** para [aplicações de clientes)](../../active-directory/develop/developer-glossary.md#client-application)e [aplicações de recursos/API](../../active-directory/develop/developer-glossary.md#resource-server)que são instaladas num servidor seguro. Esta definição é utilizada para [clientes web](../../active-directory/develop/developer-glossary.md#web-client)confidenciais da OAuth e [para clientes públicos baseados em agentes de utilizadores).](../../active-directory/develop/developer-glossary.md#user-agent-based-client)
        A mesma aplicação também pode expor um cliente e um recurso/API.

        Para exemplos específicos de aplicações web, confira as configurações guiadas de arranque rápido que estão disponíveis na secção [Get Started](../../active-directory/develop/quickstart-create-new-tenant.md) do [Azure AD Developers Guide](../../active-directory/develop/index.yml).

5. Quando terminar, clique em **Registar.**  A Azure AD atribui um *ID de aplicação* único à sua nova aplicação. Recomendamos o registo de uma aplicação que aceda apenas à API, e como inquilino único.

6. Para criar o segredo do cliente, navegue para **certificados & página de segredos** e clique **em +Novo segredo de cliente.**  Certifique-se de copiar o valor secreto para usá-lo no seu código.

O **ID da aplicação AD AZure** está associado ao ID da sua editora, por isso certifique-se de que o mesmo *ID da aplicação* é usado em todas as suas ofertas.

>[!Note]
>Se uma editora tiver duas contas diferentes no Partner Center, devem ser utilizados dois IDs de aplicações AD Azure diferentes.  Cada conta de parceiro no Partner Center deve usar um ID de aplicação AD AZure único para todas as ofertas do SaaS que são publicadas através desta conta.

## <a name="how-to-get-the-publishers-authorization-token"></a>Como obter o sinal de autorização da editora

Uma vez registado a sua candidatura, pode solicitar programáticamente o token de autorização da editora (token de acesso AD AD Azure, utilizando o ponto final Azure AD V1). O editor deve usar este símbolo ao chamar as várias APIs de Cumprimento do SaaS. Este símbolo só é válido por uma hora. 

Para obter mais informações sobre estes tokens, consulte [os tokens de acesso do Azure Ative Directory](../../active-directory/develop/access-tokens.md).  Note que no fluxo abaixo do sinal de ponta V1 é utilizado.

### <a name="get-the-token-with-an-http-post"></a>Obtenha o token com um HTTP POST

#### <a name="http-method"></a>Método HTTP

Publicar<br>

##### <a name="request-url"></a>*URL do Pedido* 

`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`

##### <a name="uri-parameter"></a>*Parâmetro URI*

|  Nome do parâmetro    |  Obrigatório         |  Descrição |
|  ---------------   |  ---------------  | ------------ |
|  `tenantId`        |  Verdadeiro      |  Identificação do inquilino do pedido de AAD registado. |

##### <a name="request-header"></a>*Cabeçalho de pedido*

|  Nome do cabeçalho       |  Obrigatório         |  Descrição |
|  ---------------   |  ---------------  | ------------ |
|  `content-type`    |  Verdadeiro      |  Tipo de conteúdo associado ao pedido. O valor predefinido é `application/x-www-form-urlencoded`. |

##### <a name="request-body"></a>*Corpo de pedido*

|  Nome da propriedade     |  Obrigatório         |  Descrição |
|  ---------------   |  ---------------  | ------------ |
|  `grant_type`      |  Verdadeiro      |  Tipo grant. Utilize `"client_credentials"`. |
|  `client_id`       |  Verdadeiro      |  Identificador de cliente/aplicação associado à aplicação Azure AD. |
|  `client_secret`   |  Verdadeiro      |  Segredo associado à aplicação AZure AD. |
|  `resource`        |  Verdadeiro      |  Recurso-alvo para o qual o símbolo é solicitado. Use `20e940b3-4c77-4b0b-9a53-9e16a1b010a7` porque marketplace SaaS API é sempre o recurso alvo neste caso. |

##### <a name="response"></a>*Response*

|  Nome     |  Tipo         |  Description |
|  ------   |  ---------------  | ------------ |
|  200 OK   |  TokenResponse    |  Pedido bem sucedido. |

##### <a name="tokenresponse"></a>*TokenResponse*

Resposta da amostra:

```json
{
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

O `"access_token"` valor de campo na resposta é o que passará como parâmetro de autorização ao chamar todas as `<access_token>` APIs de realização de SaaS e de medição de mercado.

## <a name="next-steps"></a>Passos seguintes

A sua aplicação Azure AD-secured pode agora usar a [Versão API da API de Cumprimento SaaS](./pc-saas-fulfillment-api-v2.md).