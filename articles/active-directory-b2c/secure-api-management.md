---
title: Garantir uma API de Gestão AZure através da utilização do Azure Ative Directory B2C
description: Saiba como usar os tokens de acesso emitidos pela Azure Ative Directory B2C para garantir um ponto final da API Management API Azure Management.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 60b7bb33dfbf29b7e448887ce992d03009133b2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94953496"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Garantir uma API de Gestão AZURE com Azure AD B2C

Saiba como restringir o acesso à sua API Management (APIM) API a clientes que tenham autenticado com o Azure Ative Directory B2C (Azure AD B2C). Siga os passos deste artigo para criar e testar uma política de entrada na APIM que restringe o acesso apenas aos pedidos que incluem um token de acesso emitido por Azure AD B2C válido.

## <a name="prerequisites"></a>Pré-requisitos

Precisa dos seguintes recursos antes de continuar com os passos deste artigo:

* [Inquilino Azure AD B2C](tutorial-create-tenant.md)
* [Inscrição registada](tutorial-register-applications.md) no seu inquilino
* [Fluxos de utilizador criados](tutorial-create-user-flows.md) no seu inquilino
* [API publicada](../api-management/import-and-publish.md) na Azure API Management
* [Carteiro](https://www.getpostman.com/) para testar acesso garantido (opcional)

## <a name="get-azure-ad-b2c-application-id"></a>Obtenha ID de aplicação Azure AD B2C

Quando você assegura uma API em Azure API Management com Azure AD B2C, você precisa de vários valores para a [política de entrada](../api-management/api-management-howto-policies.md) que você cria na APIM. Primeiro, grave o ID de aplicação de uma aplicação que criou anteriormente no seu inquilino Azure AD B2C. Se estiver a utilizar a aplicação que criou nos pré-requisitos, utilize o ID da aplicação para *webbapp1*.

Para registar uma aplicação no seu inquilino Azure AD B2C, pode utilizar a nossa nova experiência de registos de **Aplicações unificadas** ou a nossa experiência de **Aplicações (Legacy).** [Saiba mais sobre a nova experiência.](./app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[Registos de aplicações](#tab/app-reg-ga/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **as inscrições da App** e, em seguida, selecione o **separador aplicações Possuídas.**
1. Registe o valor na coluna **de ID de Aplicação (cliente)** para *webapp1* ou outra aplicação que tenha criado anteriormente.

#### <a name="applications-legacy"></a>[Candidaturas (Legado)](#tab/applications-legacy/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Em **Gestão**, selecione **Aplicações (Legado)**.
1. Registe o valor na coluna **application ID** para *webapp1* ou outra aplicação que tenha criado anteriormente.

* * *

## <a name="get-token-issuer-endpoint"></a>Obtenha o ponto final do emitente de token

Em seguida, obtenha o conhecido URL de configuração para um dos fluxos de utilizador Azure AD B2C. Também precisa do ponto final do emitente simbólico URI que pretende apoiar na Azure API Management.

1. Navegue pelo seu inquilino Azure AD B2C no [portal Azure](https://portal.azure.com).
1. Em **Políticas**, selecione **fluxos de utilizador**.
1. Selecione uma política existente, por *exemplo, B2C_1_signupsignin1,* em seguida, selecione **Executar o fluxo do utilizador**.
1. Grave o URL na hiperligação exibida sob o fluxo do **utilizador Executar,** aproximando-se do topo da página. Este URL é o ponto final de descoberta bem conhecido do OpenID Connect para o fluxo do utilizador, e utiliza-o na secção seguinte quando configurar a política de entrada na Azure API Management.

    ![Hiperligação URI conhecida na página run now do portal Azure](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. Selecione a hiperligação para navegar na página de configuração do OpenID Connect bem conhecida.
1. Na página que abre no seu navegador, registem o `issuer` valor, por exemplo:

    `https://<tenant-name>.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    Utilize este valor na secção seguinte quando configurar a sua API na Azure API Management.

Deverá agora ter dois URLs gravados para utilização na secção seguinte: o URL de ponto final de configuração bem conhecido do OpenID Connect e o emitente URI. Por exemplo:

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration
https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Configure a política de entrada na Azure API Management

Está agora pronto para adicionar a política de entrada na Azure API Management que valida as chamadas da API. Ao adicionar uma política [de validação JWT](../api-management/api-management-access-restriction-policies.md#ValidateJWT) que verifica o público e o emitente num token de acesso, pode garantir que apenas as chamadas API com um token válido são aceites.

1. Navegue pela sua instância de Gestão API Azure no [portal Azure](https://portal.azure.com).
1. Selecione **APIs**.
1. Selecione a API que pretende fixar com Azure AD B2C.
1. Selecione o separador **Design**.
1. **No processamento de Entrada,** selecione para abrir o editor de código de **\</\>** política.
1. Coloque a `<validate-jwt>` seguinte etiqueta dentro da `<inbound>` apólice.

    1. Atualize o `url` valor do elemento com o conhecido URL de `<openid-config>` configuração da sua política.
    1. Atualize o `<audience>` elemento com o ID da aplicação que criou anteriormente no seu inquilino B2C (por exemplo, *webapp1).*
    1. Atualize o `<issuer>` elemento com o ponto final do emitente simbólico que gravou anteriormente.

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                </issuers>
            </validate-jwt>
            <base />
        </inbound>
        <backend> <base /> </backend>
        <outbound> <base /> </outbound>
        <on-error> <base /> </on-error>
    </policies>
    ```

## <a name="validate-secure-api-access"></a>Validar acesso seguro a API

Para garantir que apenas os chamadores autenticados podem aceder à sua API, pode validar a sua configuração de Gestão API Azure ligando para a API com o [Carteiro.](https://www.getpostman.com/)

Para ligar para a API, precisa tanto de um token de acesso emitido pela Azure AD B2C, como de uma chave de subscrição APIM.

### <a name="get-an-access-token"></a>Obter um token de acesso

Primeiro precisa de um símbolo emitido pela Azure AD B2C para usar no `Authorization` cabeçalho do Carteiro. Pode obter um utilizando a funcionalidade **Run now** do fluxo de utilizador de inscrição/inscrição que deveria ter criado como um dos pré-requisitos.

1. Navegue pelo seu inquilino Azure AD B2C no [portal Azure](https://portal.azure.com).
1. Em **Políticas**, selecione **fluxos de utilizador**.
1. Selecione um fluxo de utilizador de inscrição/inscrição existente, por *exemplo, B2C_1_signupsignin1*.
1. Para **Aplicação**, selecione *webapp1*.
1. Para **URL de resposta,** escolha `https://jwt.ms` .
1. Selecione **Executar o fluxo do utilizador**.

    ![Executar a página de fluxo do utilizador para iniciar súbbed no fluxo do utilizador no portal Azure](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. Conclua o processo de início de sessão. Deve ser redirecionado para `https://jwt.ms` .
1. Grave o valor de token codificado exibido no seu browser. Usa este valor simbólico para o cabeçalho de autorização do Carteiro.

    ![Valor simbólico codificado exibido no jwt.ms](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>Obtenha a chave de subscrição API

Uma aplicação de cliente (neste caso, Carteiro) que ligue para uma API publicada deve incluir uma chave de subscrição válida da API Management nos seus pedidos HTTP à API. Para obter uma chave de subscrição para incluir no seu pedido DE CARTEIRO HTTP:

1. Navegue para a sua instância de serviço de Gestão API Azure no [portal Azure](https://portal.azure.com).
1. Selecione **Subscrições**.
1. Selecione a elipse para **produto: Ilimitada,** em seguida, selecione **As teclas Mostrar/ocultar**.
1. Grave a **chave primária do** produto. Utilize esta chave para o `Ocp-Apim-Subscription-Key` cabeçalho no seu pedido HTTP no Carteiro.

![Página chave de subscrição com teclas Show/hide selecionadas no portal Azure](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>Teste uma chamada de API segura

Com a chave de subscrição de acesso e apim gravada, está agora pronta para testar se configura corretamente o acesso seguro à API.

1. Crie um novo `GET` pedido no [Carteiro.](https://www.getpostman.com/) Para o URL de pedido, especifique o ponto final da lista de altifalantes da API que publicou como um dos pré-requisitos. Por exemplo:

    `https://contosoapim.azure-api.net/conference/speakers`

1. Em seguida, adicione os seguintes cabeçalhos:

    | Chave | Valor |
    | --- | ----- |
    | `Authorization` | Valor simbólico codificado que gravou anteriormente, prefixado `Bearer ` (incluir o espaço após "Portador") |
    | `Ocp-Apim-Subscription-Key` | Chave de subscrição APIM que gravou anteriormente |

    O URL e **cabeçalhos de** pedido **GET** devem parecer semelhantes a:

    ![UI do carteiro mostrando o URL de pedido GET e cabeçalhos](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Selecione o botão **Enviar** no Carteiro para executar o pedido. Se configurar tudo corretamente, deve ser-lhe apresentada uma resposta JSON com uma coleção de oradores de conferências (mostrados aqui truncados):

    ```json
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

### <a name="test-an-insecure-api-call"></a>Teste uma chamada de API insegura

Agora que fez um pedido bem sucedido, teste o caso de falha para garantir que as chamadas para a sua API com um token *inválido* sejam rejeitadas como esperado. Uma maneira de realizar o teste é adicionar ou alterar alguns caracteres no valor simbólico e, em seguida, executar o mesmo `GET` pedido de antes.

1. Adicione vários caracteres ao valor simbólico para simular um token inválido. Por exemplo, adicione "INVALID" ao valor simbólico:

    ![Secção de cabeçalhos da UI do carteiro mostrando INVÁLIDO adicionado ao token](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. Selecione o botão **Enviar** para executar o pedido. Com um token inválido, o resultado esperado é um `401` código de estado não autorizado:

    ```json
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

Se vir o `401` código de estado, verificou que apenas os chamadores com um token de acesso válido emitido pela Azure AD B2C podem fazer pedidos bem sucedidos à sua API de Gestão API Azure.

## <a name="support-multiple-applications-and-issuers"></a>Apoiar múltiplas aplicações e emitentes

Várias aplicações normalmente interagem com uma única API REST. Para permitir que a sua API aceite fichas destinadas a múltiplas aplicações, adicione os seus IDs de aplicação ao `<audiences>` elemento na política de entrada da APIM.

```xml
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

Da mesma forma, para apoiar vários emitentes simbólicos, adicione os seus URIs de ponto final ao `<issuers>` elemento na política de entrada da APIM.

```xml
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>Migrar para b2clogin.com

Se tiver uma API APIM que valide fichas emitidas pelo ponto final do `login.microsoftonline.com` legado, deve migrar a API e as aplicações que a chamam de tokens emitidos por [b2clogin.com](b2clogin.md).

Pode seguir este processo geral para realizar uma migração faseada:

1. Adicione suporte na sua política de entrada APIM para fichas emitidas tanto por b2clogin.com como login.microsoftonline.com.
1. Atualize as suas aplicações uma de cada vez para obter fichas do b2clogin.com ponto final.
1. Uma vez que todas as suas aplicações estejam corretamente obtendo fichas de b2clogin.com, remova o suporte para fichas emitidas por login.microsoftonline.com da API.

O exemplo a seguir da política de entrada da APIM ilustra como aceitar fichas emitidas tanto por b2clogin.com como por login.microsoftonline.com. Além disso, apoia os pedidos da API de duas candidaturas.

```xml
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
            </issuers>
        </validate-jwt>
        <base />
    </inbound>
    <backend> <base /> </backend>
    <outbound> <base /> </outbound>
    <on-error> <base /> </on-error>
</policies>
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais detalhes sobre as políticas de gestão da API Azure, consulte o [índice de referência da política da APIM](../api-management/api-management-policies.md).

Pode encontrar informações sobre as APIs web baseadas em OWIN e as suas aplicações para b2clogin.com em [Migrar uma API web baseada em OWIN para b2clogin.com](multiple-token-endpoints.md).