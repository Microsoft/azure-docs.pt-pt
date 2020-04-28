---
title: Garanta um API de gestão de API Azure utilizando o Diretório Ativo Azure B2C
description: Saiba como utilizar fichas de acesso emitidas pelo Azure Ative Directory B2C para garantir um ponto final da API management Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 00938d831e70289b24acb599b81016aa6e564d78
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78186935"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Assegurar uma API de Gestão De API Azure com Azure AD B2C

Saiba restringir o acesso à sua API Management Azure (APIM) API a clientes que tenham autenticado com o Azure Ative Directory B2C (Azure AD B2C). Siga os passos deste artigo para criar e testar uma política de entrada na APIM que restringe o acesso apenas aos pedidos que incluem um sinal de acesso emitido pelo Azure AD B2C.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa dos seguintes recursos no lugar antes de continuar com os passos neste artigo:

* [Inquilino Azure AD B2C](tutorial-create-tenant.md)
* [Pedido registado](tutorial-register-applications.md) no seu inquilino
* [Fluxos de utilizador criados](tutorial-create-user-flows.md) no seu inquilino
* [API publicado](../api-management/import-and-publish.md) na Azure API Management
* [Carteiro](https://www.getpostman.com/) para testar acesso seguro (opcional)

## <a name="get-azure-ad-b2c-application-id"></a>Obtenha id de aplicação Azure AD B2C

Quando você segura um API em Azure API Management com Azure AD B2C, você precisa de vários valores para a política de [entrada](../api-management/api-management-howto-policies.md) que você cria em APIM. Primeiro, grave a identificação de uma aplicação que criou anteriormente no seu inquilino Azure AD B2C. Se estiver a utilizar a aplicação que criou nos pré-requisitos, utilize o ID da aplicação para *webbapp1*.

Pode utilizar a experiência atual de **Aplicações** ou a nossa nova experiência unificada de registos de **Aplicações (Pré-visualização)** para obter o ID da aplicação. [Saiba mais sobre a nova experiência.](https://aka.ms/b2cappregintro)

#### <a name="applications"></a>[Aplicações](#tab/applications/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro de **subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. No menu esquerdo, **selecione Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Em **Gerir,** selecione **Aplicações**.
1. Registe o valor na coluna de ID da **APLICAÇÃO** para *webapp1* ou outra aplicação que tenha criado anteriormente.

#### <a name="app-registrations-preview"></a>[Registos de aplicativos (Pré-visualização)](#tab/app-reg-preview/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro de **subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. No menu esquerdo, **selecione Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **registos de aplicações (Pré-visualização)** e, em seguida, selecione o separador **de aplicações Owned.**
1. Registe o valor na coluna de ID da **Aplicação (cliente)** para *webapp1* ou outra aplicação que tenha criado anteriormente.

* * *

## <a name="get-token-issuer-endpoint"></a>Obter ponto final do emitente token

Em seguida, obtenha o conhecido URL de config para um dos fluxos de utilizador Do Seu AD B2C Azure. Você também precisa do emitente simbólico endpoint URI que você quer apoiar na Azure API Management.

1. Navegue até ao seu inquilino Azure AD B2C no [portal Azure.](https://portal.azure.com)
1. Em **Termos de Políticas,** selecione **fluxos de utilizador (políticas)**.
1. Selecione uma política existente, por *exemplo, B2C_1_signupsignin1,* em seguida, selecione **executar o fluxo do utilizador**.
1. Grave o URL em hiperligação apresentado sob o fluxo do **utilizador Run,** que se dirige para perto do topo da página. Este URL é o ponto final de descoberta conhecido do OpenID Connect para o fluxo do utilizador, e você usa-o na secção seguinte quando configurar a política de entrada na Gestão API Azure.

    ![Hiperligação URI bem conhecida na página Run agora do portal Azure](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. Selecione a hiperligação para navegar na página de configuração do OpenID Connect bem conhecida.
1. Na página que abre no seu `issuer` navegador, registe o valor, por exemplo:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    Utiliza este valor na secção seguinte quando configurar a Sua API na Gestão de API Azure.

Deverá agora ter dois URLs gravados para utilização na secção seguinte: o URL final de configuração openID Connect bem conhecido e o emitente URI. Por exemplo:

```
https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1
https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Configure a política de entrada na Gestão da API azure

Está agora pronto para adicionar a política de entrada na Azure API Management que valida chamadas API. Ao adicionar uma política de [validação JWT](../api-management/api-management-access-restriction-policies.md#ValidateJWT) que verifique o público e o emitente num token de acesso, pode garantir que apenas as chamadas API com um token válido são aceites.

1. Navegue na sua instância de Gestão API Azure no [portal Azure.](https://portal.azure.com)
1. Selecione **APIs**.
1. Selecione a API que pretende fixar com O Azure AD B2C.
1. Selecione o separador **Design**.
1. No processamento de ** \< / ** **Entrada,** selecione para abrir o editor de código de política.
1. Coloque a `<validate-jwt>` seguinte `<inbound>` etiqueta dentro da apólice.

    1. Atualize `url` o `<openid-config>` valor no elemento com o conhecido URL de configuração da sua política.
    1. Atualize `<audience>` o elemento com ID de aplicação da aplicação que criou anteriormente no seu inquilino B2C (por exemplo, *webapp1*).
    1. Atualize `<issuer>` o elemento com o ponto final do emitente simbólico que gravou anteriormente.

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

## <a name="validate-secure-api-access"></a>Validar acesso a API seguro

Para garantir que apenas os chamadores autenticados podem aceder à sua API, pode validar a sua configuração de Gestão API Azure, ligando para a API com [O Carteiro](https://www.getpostman.com/).

Para ligar para a API, precisa de um token de acesso emitido pelo Azure AD B2C e de uma chave de subscrição APIM.

### <a name="get-an-access-token"></a>Obter um token de acesso

Primeiro precisa de um símbolo emitido pelo Azure AD `Authorization` B2C para usar no cabeçalho do Carteiro. Pode obter um utilizando a funcionalidade **Executar agora** do seu fluxo de utilizador de inscrição/inscrição que deve ter criado como um dos pré-requisitos.

1. Navegue até ao seu inquilino Azure AD B2C no [portal Azure.](https://portal.azure.com)
1. Em **Termos de Políticas,** selecione **fluxos de utilizador (políticas)**.
1. Selecione um fluxo de utilizador de entrada/entrada existente, por *exemplo, B2C_1_signupsignin1*.
1. Para **aplicação**, selecione *webapp1*.
1. Para url de `https://jwt.ms` **resposta,** escolha .
1. Selecione **Executar o fluxo do utilizador**.

    ![Executar página de fluxo do utilizador para inscrever o sinal no fluxo do utilizador no portal Azure](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. Conclua o processo de início de sessão. Devia ser redirecionado `https://jwt.ms`para.
1. Grave o valor do token codificado exibido no seu navegador. Usa este valor simbólico para o cabeçalho de autorização no Carteiro.

    ![Valor simbólico codificado exibido em jwt.ms](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>Obtenha a chave de subscrição DaPI

Uma aplicação de cliente (neste caso, Carteiro) que ligue para uma API publicada deve incluir uma chave de subscrição válida da API Management nos seus pedidos http para a API. Para obter uma chave de subscrição para incluir no seu pedido de Carteiro HTTP:

1. Navegue na sua instância de serviço azure API Management no [portal Azure.](https://portal.azure.com)
1. Selecione **Subscrições**.
1. Selecione a elipse para **produto: Ilimitada,** em seguida, selecione **teclas Mostrar/ocultar**.
1. Grave a **CHAVE PRIMÁRIA** do produto. Utilize esta chave `Ocp-Apim-Subscription-Key` para o cabeçalho no seu pedido http no Carteiro.

![Página chave de subscrição com teclas Show/hide selecionadas no portal Azure](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>Teste uma chamada API segura

Com o token de acesso e a chave de subscrição APIM gravados, está agora pronto para testar se configurabem o acesso seguro à API.

1. Crie `GET` um novo pedido no [Carteiro.](https://www.getpostman.com/) Para o URL de pedido, especifique o ponto final da lista de oradores da API que publicou como um dos pré-requisitos. Por exemplo:

    `https://contosoapim.azure-api.net/conference/speakers`

1. Em seguida, adicione os seguintes cabeçalhos:

    | Chave | Valor |
    | --- | ----- |
    | `Authorization` | Valor simbólico codificado que gravou anteriormente, `Bearer ` pré-fixado (inclua o espaço após "Portador") |
    | `Ocp-Apim-Subscription-Key` | Chave de subscrição APIM que gravou anteriormente |

    O **GET** URL de pedido get e **os Cabeçalhos** devem parecer semelhantes a:

    ![Postman UI mostrando o URL de pedido GET e cabeçalhos](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Selecione o botão **Enviar** no Carteiro para executar o pedido. Se configurar tudo corretamente, deve ser apresentado com uma resposta JSON com uma coleção de oradores de conferências (mostrado aqui truncado):

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

### <a name="test-an-insecure-api-call"></a>Teste uma chamada de API insegura

Agora que fez um pedido bem sucedido, teste o caso de falha para garantir que as chamadas para a sua API com um token *inválido* são rejeitadas como esperado. Uma forma de realizar o teste é adicionar ou alterar alguns caracteres `GET` no valor simbólico e, em seguida, executar o mesmo pedido que antes.

1. Adicione vários caracteres ao valor simbólico para simular um símbolo inválido. Por exemplo, adicione "INVÁLIDO" ao valor simbólico:

    ![Secção de cabeçalhos do Carteiro UI mostrando INVALID adicionado ao símbolo](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. Selecione o botão **Enviar** para executar o pedido. Com um token inválido, o `401` resultado esperado é um código de estado não autorizado:

    ```JSON
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

Se vir `401` o código de estado, verificou que apenas os chamadores com um token de acesso válido emitido pelo Azure AD B2C podem fazer pedidos bem sucedidos à sua API de Gestão de API Azure.

## <a name="support-multiple-applications-and-issuers"></a>Apoiar múltiplas aplicações e emitentes

Várias aplicações normalmente interagem com uma única API REST. Para permitir que a sua API aceite fichas destinadas a múltiplas aplicações, adicione os seus IDs de aplicação ao `<audiences>` elemento na política de entrada da APIM.

```XML
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

Da mesma forma, para apoiar vários emitentes simbólicos, adicione os seus URIs de ponto final ao `<issuers>` elemento na política de entrada da APIM.

```XML
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>Migrar para b2clogin.com

Se tiver uma API API que valide fichas `login.microsoftonline.com` emitidas pelo ponto final do legado, deve migrar a API e as aplicações que a chamam para usar fichas emitidas por [b2clogin.com](b2clogin.md).

Pode seguir este processo geral para realizar uma migração encenada:

1. Adicione apoio na sua política de entrada aPIM para fichas emitidas tanto pela b2clogin.com como pela login.microsoftonline.com.
1. Atualize as suas aplicações uma de cada vez para obter fichas a partir do ponto final b2clogin.com.
1. Uma vez que todas as suas aplicações obtenham corretamente fichas de b2clogin.com, remova o suporte para fichas emitidas login.microsoftonline.com da API.

O exemplo seguinte a política de entrada da APIM ilustra como aceitar fichas emitidas tanto pelo b2clogin.com como pela login.microsoftonline.com. Além disso, apoia pedidos de API de duas aplicações.

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

## <a name="next-steps"></a>Passos seguintes

Para obter mais detalhes sobre as políticas de Gestão da API Azure, consulte o índice de referência da [política APIM](../api-management/api-management-policies.md).

Pode encontrar informações sobre APIs web baseados em OWIN migrando e suas aplicações para b2clogin.com em [Migrar uma API web baseada em OWIN para b2clogin.com](multiple-token-endpoints.md).
