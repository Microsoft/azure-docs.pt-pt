---
title: Registe uma aplicação SaaS Mercado Azure
description: Explica como registar uma aplicação SaaS utilizando o portal Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 9c20fe34e108de95a34aabea56390e8a6f0d858f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275718"
---
# <a name="register-a-saas-application"></a>Registar uma aplicação SaaS

Este artigo explica como registar uma aplicação SaaS utilizando o portal Microsoft [Azure](https://portal.azure.com/).  Após uma inscrição bem sucedida, receberá um sinal de segurança do Azure Ative Directory (Azure AD) que pode utilizar para aceder às APIs de Realização do SaaS.  Para mais informações sobre a AD Azure, consulte [o que é a autenticação?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>Fluxo de autenticação serviço-a-serviço

O diagrama seguinte mostra o fluxo de subscrição de um novo cliente e quando estas APIs são utilizadas:

![SaaS oferecem fluxo DeAPi](./media/saas-offer-publish-api-flow-v1.png)

A Azure não impõe quaisquer restrições à autenticação que o serviço SaaS expõe aos seus utilizadores finais. No entanto, a autenticação com as APIs de Cumprimento do SaaS é realizada com um token de segurança Azure AD, tipicamente obtido através do registo da app SaaS através do portal Azure. 


## <a name="register-an-azure-ad-secured-app"></a>Registe uma aplicação azure ad-secured

Qualquer aplicação que pretenda utilizar as capacidades do Azure AD, tem de estar registada previamente no inquilino do Azure AD. Este processo de registo envolve dar detalhes da AD Azure sobre a sua aplicação, como o URL onde está localizado, o URL para enviar respostas após a autenticação de um utilizador, o URI que identifica a app, e assim por diante.  Para registar uma nova aplicação utilizando o portal Azure, execute os seguintes passos:

1.  Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2.  Se a sua conta lhe der acesso a mais de um, clique na sua conta no canto superior direito e detete a sua sessão do portal para o desejado inquilino da AD Azure.
3.  No painel de navegação à esquerda, clique no serviço **de Diretório Ativo Azure,** clique em registos de **Aplicações,** e clique em **Nova inscrição**de aplicação .

    ![Inscrições na aplicação DaAS](./media/saas-offer-app-registration-v1.png)

4.  Na página Criar, insira as informações de inscrição da sua candidatura:\'
    -   **Nome**: Introduza um nome de candidatura significativo
    -   **Tipo de aplicação:** 
        - Selecione **Nativa** para [aplicações cliente](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) que estejam instaladas localmente num dispositivo. Esta definição é utilizada para [clientes nativos](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) públicos de OAuth.
        - Selecione **aplicação Web / API** para [aplicações de clientes](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) e aplicações de [recursos/API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) que estão instaladas num servidor seguro. Esta definição é utilizada para [clientes web](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) confidenciais da OAuth e clientes [públicos baseados em agentes de utilizador.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client)
        A mesma aplicação também pode expor um cliente e um recurso/API.
    -   **URL de acesso**: Para aplicações Web/API, forneça o URL base da sua aplicação. Por exemplo, **http://localhost:31544** pode ser o URL para uma aplicação web que funciona na sua máquina local. Os utilizadores usariam então este URL para iniciar sessão numa aplicação de cliente web.
    -   **Redirecione o URI**: Para aplicações nativas, forneça o URI utilizado pela Azure AD para devolver respostas simbólicas. Introduza um valor específico da **http://MyFirstAADApp**sua aplicação, por exemplo.

        ![Inscrições na aplicação DaAS](./media/saas-offer-app-registration-v1-2.png)

        Para exemplos específicos de aplicações web ou aplicações nativas, consulte as configurações guiadas de arranque rápido que estão disponíveis na secção *Get Started* do Guia de Desenvolvedores da [AD Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

5.  Quando terminar, clique em **Criar**. A Azure AD atribui um ID de\' *aplicação* único à sua aplicação e é levado para a página principal de registo da sua candidatura.\' Dependendo de se a sua aplicação é do tipo nativo ou Web, dispõe de várias opções para lhe adicionar mais capacidades.

>[!Note]
>Por predefinição, a aplicação recém-registada está configurada para permitir que apenas os utilizadores do mesmo inquilino possam iniciar sessão na sua aplicação.


## <a name="using-the-azure-ad-security-token"></a>Usando o símbolo de segurança da AD Azure

Uma vez registado o seu pedido, pode solicitar programáticamente um sinal de segurança da AD Azure.  Espera-se que a editora use este símbolo e faça um pedido para resolvê-lo.  Ao utilizar as várias APIs de Realização, o parâmetro de consulta simbólica está no URL quando o utilizador é redirecionado para o website da SaaS a partir do Azure.  Este token só é válido por uma hora.  Além disso, deve descodificar o valor simbólico do navegador antes de o utilizar.

Para obter mais informações sobre estas fichas, consulte os tokens de acesso ao [Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Obtenha um símbolo com base na aplicação Azure AD

Método HTTP

`POST`

*Request URL* (URL do pedido)

**https://login.microsoftonline.com/*{tenantId}*/oauth2/token**

*Parâmetro URI*

|  **Parameter name** (Nome do parâmetro)  | **Necessário**  | **Descrição**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| inquilinoId             | Verdadeiro          | Identificação do inquilino do pedido de AAD registado   |
|  |  |  |


*Cabeçalho do pedido*

|  **Nome do cabeçalho**  | **Necessário** |  **Descrição**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | Verdadeiro         | Tipo de conteúdo associado ao pedido. O valor predefinido é `application/x-www-form-urlencoded`.  |
|  |  |  |


*Solicitar corpo*

| **Nome de propriedade**   | **Necessário** |  **Descrição**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | Verdadeiro         | Tipo de concessão. O valor predefinido é `client_credentials`.                    |
|  Client_id          | Verdadeiro         |  Identificador cliente/app associado à aplicação Azure AD.                  |
|  client_secret      | Verdadeiro         |  Senha associada à aplicação Azure AD.                               |
|  Recurso           | Verdadeiro         |  Recurso-alvo para o qual o símbolo é solicitado. O valor predefinido é `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Resposta*

|  **Nome**  | **Tipo**       |  **Descrição**    |
| ---------- | -------------  | ------------------- |
| 200 OK    | Resposta token  | Pedido foi bem sucedido   |
|  |  |  |

*Resposta token*

Ficha de resposta da amostra:

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

A sua aplicação azure ad-secured pode agora usar a Versão API de [Realização saaS 2](./pc-saas-fulfillment-api-v2.md).
