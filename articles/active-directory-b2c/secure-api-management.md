---
title: Proteger uma API de gerenciamento de API do Azure usando Azure Active Directory B2C
description: Saiba como usar tokens de acesso emitidos pelo Azure Active Directory B2C para proteger um ponto de extremidade da API de gerenciamento de API do Azure.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4c42959d46aa522042275456a87e590f9e009348
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183059"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Proteger uma API de gerenciamento de API do Azure com Azure AD B2C

Saiba como restringir o acesso à sua API do APIM (gerenciamento de API do Azure) para clientes que se autenticaram com o Azure Active Directory B2C (Azure AD B2C). Siga as etapas neste artigo para criar e testar uma política de entrada no APIM que restringe o acesso apenas às solicitações que incluem um token de acesso válido emitido por Azure AD B2C.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa dos seguintes recursos em vigor antes de continuar com as etapas neste artigo:

* [Azure AD B2C locatário](tutorial-create-tenant.md)
* [Aplicativo registrado](tutorial-register-applications.md) em seu locatário
* [Fluxos de usuário criados](tutorial-create-user-flows.md) em seu locatário
* [API publicada](../api-management/import-and-publish.md) no gerenciamento de API do Azure
* [Postmaster](https://www.getpostman.com/) para testar o acesso protegido (opcional)

## <a name="get-azure-ad-b2c-application-id"></a>Obter Azure AD B2C ID do aplicativo

Quando você protege uma API no gerenciamento de API do Azure com Azure AD B2C, você precisa de vários valores para a [política de entrada](../api-management/api-management-howto-policies.md) que você cria em APIM. Primeiro, registre a ID do aplicativo de um aplicativo que você criou anteriormente em seu locatário Azure AD B2C. Se você estiver usando o aplicativo criado nos pré-requisitos, use a ID do aplicativo para *webbapp1*.

1. Navegue até seu locatário do Azure AD B2C no [portal do Azure](https://portal.azure.com).
1. Em **gerenciar**, selecione **aplicativos**.
1. Registre o valor na **ID do aplicativo** para *webapp1* ou outro aplicativo que você criou anteriormente.

  ![Local da ID do aplicativo do aplicativo B2C no portal do Azure](media/secure-apim-with-b2c-token/portal-02-app-id.png)

## <a name="get-token-issuer-endpoint"></a>Obter ponto de extremidade do emissor do token

Em seguida, obtenha a URL de configuração conhecida para um dos seus Azure AD B2C fluxos de usuário. Você também precisa do URI do ponto de extremidade do emissor do token para o qual você deseja dar suporte no gerenciamento de API do Azure.

1. Navegue até seu locatário do Azure AD B2C no [portal do Azure](https://portal.azure.com).
1. Em **políticas**, selecione **fluxos de usuário (políticas)** .
1. Selecione uma política existente, por exemplo, *B2C_1_signupsignin1*, em seguida, selecione **executar fluxo de usuário**.
1. Registre a URL no hiperlink exibido no cabeçalho **executar fluxo de usuário** próximo à parte superior da página. Essa URL é o ponto de extremidade de descoberta conhecido do OpenID Connect para o fluxo do usuário e você o usa na próxima seção ao configurar a política de entrada no gerenciamento de API do Azure.

    ![Hiperlink de URI conhecido na página executar agora do portal do Azure](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. Selecione o hiperlink para navegar até a página de configuração bem-conhecida do OpenID Connect.
1. Na página que é aberta no navegador, registre o `issuer` valor, por exemplo:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    Você usará esse valor na próxima seção quando configurar sua API no gerenciamento de API do Azure.

Agora você deve ter duas URLs registradas para uso na próxima seção: a URL do ponto de extremidade de configuração bem conhecida do OpenID Connect e o URI do emissor. Por exemplo:

```
https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1
https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Configurar a política de entrada no gerenciamento de API do Azure

Agora você está pronto para adicionar a política de entrada no gerenciamento de API do Azure que valida chamadas de API. Ao adicionar uma política de [validação de JWT](../api-management/api-management-access-restriction-policies.md#ValidateJWT) que verifica o público e o emissor em um token de acesso, você pode garantir que somente as chamadas à API com um token válido sejam aceitas.

1. Navegue até sua instância de gerenciamento de API do Azure no [portal do Azure](https://portal.azure.com).
1. Selecione **APIs**.
1. Selecione a API que você deseja proteger com Azure AD B2C.
1. Selecione o separador **Design**.
1. Em **processamento de entrada**, selecione **\< / \>** para abrir o editor de código de política.
1. Coloque a marca `<validate-jwt>` a seguir dentro `<inbound>` da política.

    1. Atualize o `url` valor `<openid-config>` no elemento com a URL de configuração conhecida de sua política.
    1. Atualize o `<audience>` elemento com a ID do aplicativo que você criou anteriormente em seu locatário B2C (por exemplo, *webapp1*).
    1. Atualize o `<issuer>` elemento com o ponto de extremidade do emissor do token que você registrou anteriormente.

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                </issuers>
            </validate-jwt>
            <base />
        </inbound>
        <backend> <base /> </backend>
        <outbound> <base /> </outbound>
        <on-error> <base /> </on-error>
    </policies>
    ```

## <a name="validate-secure-api-access"></a>Validar o acesso à API segura

Para garantir que somente chamadores autenticados possam acessar sua API, você pode validar sua configuração de gerenciamento de API do Azure chamando a API com o [postmaster](https://www.getpostman.com/).

Para chamar a API, você precisa de um token de acesso emitido por Azure AD B2C e de uma chave de assinatura APIM.

### <a name="get-an-access-token"></a>Obter um token de acesso

Primeiro, você precisa de um token emitido por Azure ad B2C para usar `Authorization` no cabeçalho no postmaster. Você pode obter uma usando o recurso **executar agora** do seu fluxo de usuário de inscrição/entrada que você deve ter criado como um dos pré-requisitos.

1. Navegue até seu locatário do Azure AD B2C no [portal do Azure](https://portal.azure.com).
1. Em **políticas**, selecione **fluxos de usuário (políticas)** .
1. Selecione um fluxo de usuário de inscrição/entrada existente, por exemplo, *B2C_1_signupsignin1*.
1. Para **aplicativo**, selecione *webapp1*.
1. Para **URL de resposta**, `https://jwt.ms`escolha.
1. Selecione **executar fluxo de usuário**.

    ![Executar a página de fluxo de usuário para entrar no fluxo de usuário de entrada no portal do Azure](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. Conclua o processo de início de sessão. Você deve ser redirecionado para `https://jwt.ms`.
1. Registre o valor de token codificado exibido no navegador. Você usa esse valor de token para o cabeçalho de autorização no postmaster.

    ![Valor de token codificado exibido em jwt.ms](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>Obter chave de assinatura de API

Um aplicativo cliente (neste caso, o postmaster) que chama uma API publicada deve incluir uma chave de assinatura de gerenciamento de API válida em suas solicitações HTTP para a API. Para obter uma chave de assinatura para incluir em sua solicitação HTTP do postmaster:

1. Navegue até a instância do serviço de gerenciamento de API do Azure no [portal do Azure](https://portal.azure.com).
1. Selecione **Subscrições**.
1. Selecione as reticências **do produto: Ilimitado**e selecione **Mostrar/ocultar chaves**.
1. Registre a **chave primária** do produto. Você usa essa chave para o `Ocp-Apim-Subscription-Key` cabeçalho em sua solicitação HTTP no postmaster.

![Página chave de assinatura com as chaves mostrar/ocultar selecionadas em portal do Azure](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>Testar uma chamada à API segura

Com o token de acesso e a chave de assinatura APIM registrada, agora você está pronto para testar se você configurou corretamente o acesso seguro à API.

1. Crie uma nova `GET` solicitação no [postmaster](https://www.getpostman.com/). Para a URL de solicitação, especifique o ponto de extremidade da lista de alto-falantes da API que você publicou como um dos pré-requisitos. Por exemplo:

    `https://contosoapim.azure-api.net/conference/speakers`

1. Em seguida, adicione os seguintes cabeçalhos:

    | Chave | Value |
    | --- | ----- |
    | `Authorization` | Valor de token codificado que você registrou anteriormente, `Bearer ` prefixado com (inclua o espaço após "portador") |
    | `Ocp-Apim-Subscription-Key` | Chave de assinatura do APIM que você registrou anteriormente |

    Sua URL de solicitação **Get** e os **cabeçalhos** devem ser semelhantes a:

    ![Interface do usuário do postmaster mostrando a URL de solicitação GET e os cabeçalhos](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Selecione o botão **Enviar** no postmaster para executar a solicitação. Se você tiver configurado tudo corretamente, deverá receber uma resposta JSON com uma coleção de alto-falantes de conferência (mostrados aqui truncados):

    ```JSON
    {
      "collection": {
        "version": "1.0",
        "href": "https://conferenceapi.azurewebsites.net:443/speakers",
        "links": [],
        "items": [
          {
            "href": "https://conferenceapi.azurewebsites.net/speaker/1",
            "data": [
              {
                "name": "Name",
                "value": "Scott Guthrie"
              }
            ],
            "links": [
              {
                "rel": "http://tavis.net/rels/sessions",
                "href": "https://conferenceapi.azurewebsites.net/speaker/1/sessions"
              }
            ]
          },
    [...]
    ```

### <a name="test-an-insecure-api-call"></a>Testar uma chamada à API não segura

Agora que você fez uma solicitação bem-sucedida, teste o caso de falha para garantir que as chamadas para sua API com um token *inválido* sejam rejeitadas conforme o esperado. Uma maneira de executar o teste é adicionar ou alterar alguns caracteres no valor do token e, em seguida, executar a `GET` mesma solicitação que antes.

1. Adicione vários caracteres ao valor do token para simular um token inválido. Por exemplo, adicione "INVALID" ao valor do token:

    ![Seção de cabeçalhos da interface do usuário do postmaster mostrando o inválido adicionado ao token](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. Selecione o botão **Enviar** para executar a solicitação. Com um token inválido, o resultado esperado é um `401` código de status não autorizado:

    ```JSON
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

Se você vir o `401` código de status, verificou que somente os chamadores com um token de acesso válido emitido por Azure ad B2C podem fazer solicitações bem-sucedidas para sua API de gerenciamento de API do Azure.

## <a name="support-multiple-applications-and-issuers"></a>Suporte a vários aplicativos e emissores

Normalmente, vários aplicativos interagem com uma única API REST. Para permitir que vários aplicativos Chamem sua API, adicione suas IDs de aplicativo `<audiences>` ao elemento na política de entrada APIM.

```XML
<!-- Accept requests from multiple applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

Da mesma forma, para dar suporte a vários emissores de token, `<audiences>` adicione seus URIs de ponto de extremidade ao elemento na política de entrada APIM.

```XML
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>Migrar para o b2clogin.com

Se você tiver uma API APIM que valida tokens emitidos pelo ponto `login.microsoftonline.com` de extremidade herdado, você deve migrar a API e os aplicativos que o chamam para usar tokens emitidos pelo [b2clogin.com](b2clogin.md).

Você pode seguir este processo geral para executar uma migração em etapas:

1. Adicione suporte em sua política de entrada do APIM para tokens emitidos por b2clogin.com e login.microsoftonline.com.
1. Atualize seus aplicativos um de cada vez para obter tokens do ponto de extremidade b2clogin.com.
1. Depois que todos os seus aplicativos estiverem obtendo corretamente tokens do b2clogin.com, remova o suporte para tokens emitidos por login.microsoftonline.com da API.

O exemplo de política de entrada APIM a seguir ilustra como aceitar tokens emitidos por b2clogin.com e login.microsoftonline.com. Além disso, ele dá suporte a solicitações de API de dois aplicativos.

```XML
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
            </issuers>
        </validate-jwt>
        <base />
    </inbound>
    <backend> <base /> </backend>
    <outbound> <base /> </outbound>
    <on-error> <base /> </on-error>
</policies>
```

## <a name="next-steps"></a>Passos Seguintes

Para obter detalhes adicionais sobre as políticas de gerenciamento de API do Azure, consulte o [índice de referência da política APIM](../api-management/api-management-policies.md).

Você pode encontrar informações sobre como migrar APIs da Web baseadas em OWIN e seus aplicativos para b2clogin.com em [migrar uma API Web baseada em OWIN para b2clogin.com](multiple-token-endpoints.md).
