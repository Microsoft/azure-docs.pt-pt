---
title: Fichas de configuração - Azure Ative Directory B2C | Microsoft Docs
description: Saiba como configurar as definições de vida útil e compatibilidade simbólicas no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f22a55a65b7dc4fd4f714d880804e6fd65dbbe46
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101654379"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Configurar tokens no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Neste artigo, aprende-se a configurar a [vida e a compatibilidade de um símbolo](tokens-overview.md) em Azure Ative Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="token-lifetime-behavior"></a>Comportamento vitalício simbólico

Pode configurar a vida útil simbólica, incluindo:

- **Acesso e ID token lifes (minutos)** - A vida útil dos tokens e id portadores de OAuth 2.0. O predefinido é de 60 minutos (1 hora). O mínimo (inclusivo) é de 5 minutos. O máximo (inclusive) é de 1.440 minutos (24 horas).
- **Atualizar a vida útil (dias)** - O período máximo de tempo antes do qual um token de atualização pode ser usado para adquirir um novo token de acesso, se a sua aplicação tivesse sido concedida o `offline_access` âmbito. O padrão é de 14 dias. O mínimo (inclusivo) é um dia. O máximo (inclusivo) 90 dias.
- **Refrescar a vida útil da janela deslizante** - O tipo de janela de deslizamento de ficha de refrescamento. `Bounded` indica que o token de atualização pode ser estendido conforme especificado no comprimento da **vida útil (dias)**. `No expiry` indica que a janela de deslizamento de token de atualização nunca expira.
- **Duração vitalícia (dias)** - Após este período de tempo, o utilizador é obrigado a reautenticar, independentemente do período de validade do mais recente token de atualização adquirido pela aplicação. O valor deve ser maior ou igual ao valor **vitalício de RefreshKen.**

O diagrama seguinte mostra o comportamento de tempo de vida da janela deslizante do token.

![Refrescar a vida útil do token](./media/configure-tokens/refresh-token-lifetime.png)

> [!NOTE]
> >As aplicações de uma página única que utilizam o fluxo de código de autorização com o PKCE têm sempre um prazo de vida útil de 24 horas, enquanto aplicações móveis, aplicações de desktop e aplicações web não experimentam esta limitação. [Saiba mais sobre as implicações de segurança dos tokens de atualização no navegador.](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser)


## <a name="configure-token-lifetime"></a>Configurar a vida útil simbólica

::: zone pivot="b2c-user-flow"

Para configurar o seu número de vida simbólica de fluxo de utilizador:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino Azure AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Selecione **fluxos de utilizador (políticas)**.
1. Abra o fluxo de utilizador que criou anteriormente.
1. Selecione **Propriedades**.
1. Durante **a vida útil da Token,** ajuste as propriedades de acordo com as necessidades da sua aplicação.
1. Clique em **Guardar**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Para alterar as definições da sua compatibilidade simbólica, define os metadados de perfil técnico do [Emitente Token](jwt-issuer-technical-profile.md) na extensão ou o ficheiro de partidos da política que pretende impactar. O perfil técnico do emitente simbólico parece seguir o exemplo:

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          <Item Key="token_lifetime_secs">3600</Item>
          <Item Key="id_token_lifetime_secs">3600</Item>
          <Item Key="refresh_token_lifetime_secs">1209600</Item>
          <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
          <!--<Item Key="allow_infinite_rolling_refresh_token">true</Item>-->
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

Os seguintes valores são definidos no exemplo anterior:

- **token_lifetime_secs** - Acesso a vidas simbólicas (segundos). O padrão é de 3.600 (1 hora). O mínimo é de 300 (5 minutos). O máximo é de 86.400 (24 horas). 
- **id_token_lifetime_secs** - ID token lifetimes (segundos). O padrão é de 3.600 (1 hora). O mínimo é de 300 (5 minutos). O máximo é de 86.400 (24 horas). 
- **refresh_token_lifetime_secs** Refrescar as vidas simbólicas (segundos). O padrão é de 120.9600 (14 dias). O mínimo é de 86.400 (24 horas). O máximo é de 7.776.000 (90 dias). 
- **rolling_refresh_token_lifetime_secs** - Refrescar a vida útil da janela deslizante (segundos). O padrão é de 7.776.000 (90 dias). O mínimo é de 86.400 (24 horas). O máximo é de 31.536.000 (365 dias). Se não quiser impor uma janela deslizante, desapedace o valor de `allow_infinite_rolling_refresh_token` `true` . 
- **allow_infinite_rolling_refresh_token** - Refrescar a janela deslizante de token nunca expira. 

::: zone-end


## <a name="token-compatibility-settings"></a>Definições de compatibilidade token

Pode configurar a compatibilidade simbólica, incluindo:

- **Reclamação emitente (iss)** - O formato de emitente de acesso e id token.
- **Reclamação de objeto (sub)** - O principal sobre o qual o símbolo afirma informações, como o utilizador de uma aplicação. Este valor é imutável e não pode ser reatribuído ou reutilizado. Pode ser usado para efetuar verificações de autorização com segurança, como quando o token é usado para aceder a um recurso. Por predefinição, a reclamação do sujeito é povoada com o ID do objeto do utilizador no diretório.
- **Reclamação que representa o fluxo do utilizador** - Esta alegação identifica o fluxo de utilizador que foi executado. Valores possíveis: `tfp` (padrão), ou `acr` .

::: zone pivot="b2c-user-flow"

Para configurar as definições de compatibilidade do fluxo do utilizador:

1. Selecione **fluxos de utilizador (políticas)**.
1. Abra o fluxo de utilizador que criou anteriormente.
1. Selecione **Propriedades**.
1. Nas **definições de compatibilidade token,** ajuste as propriedades de acordo com as necessidades da sua aplicação.
1. Clique em **Guardar**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Para alterar as definições da sua compatibilidade simbólica, define os metadados de perfil técnico do [Emitente Token](jwt-issuer-technical-profile.md) na extensão ou o ficheiro de partidos da política que pretende impactar. O perfil técnico do emitente simbólico parece seguir o exemplo:

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          ...
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

- **Reclamação emitente (iss)** - A alegação do Emitente (iss) é definida com o item metadados **IssuanceClaimPattern.** Os valores aplicáveis são `AuthorityAndTenantGuid` `AuthorityWithTfp` e.
- **Definição de reclamação que representa o ID da política** - As opções para definir este valor são `TFP` (política de quadro de confiança) e `ACR` (referência de contexto de autenticação). `TFP` é o valor recomendado. Definir **AutenticaçãoContextReferenceClaimPattern** com o valor de `None` .

    No elemento **ClaimsSchema,** adicione este elemento:

    ```xml
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    No seu elemento **OutputClaims,** adicione este elemento:

    ```xml
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Para ACR, **remova o produto AuthenticationContextReferenceClaimPattern.**

- **Reclamação (sub) assunto** - Esta opção é predefinida para ObjectID, se pretender mudar esta definição para `Not Supported` , substituir esta linha:

    ```xml
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    com esta linha:

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

::: zone-end

## <a name="provide-optional-claims-to-your-app"></a>Fornecer reclamações opcionais à sua app

As reclamações da aplicação são valores que são devolvidos ao pedido. Atualize o fluxo do utilizador para conter as reclamações desejadas.

::: zone pivot="b2c-user-flow"

1. Selecione **fluxos de utilizador (políticas)**.
1. Abra o fluxo de utilizador que criou anteriormente.
1. Selecione **Afirmações de aplicação**.
1. Escolha as reclamações e atributos que deseja enviar de volta para a sua aplicação.
1. Clique em **Guardar**.

::: zone-end

::: zone pivot="b2c-custom-policy"

As alegações [de produção de perfis técnicos](relyingparty.md#technicalprofile) da política do partido de Relying são valores que são devolvidos a uma aplicação. A adição de reclamações de saída irá emitir as reclamações no token após uma viagem bem sucedida do utilizador, e será enviada para a aplicação. Modifique o elemento de perfil técnico dentro da secção do partido dependente para adicionar as reclamações desejadas como uma reclamação de saída.

1. Abra o seu ficheiro de política personalizado. Por exemplo, SignUpOrSignin.xml.
1. Encontre o elemento OutputClaims. Adicione o OutputClaim que pretende ser incluído no token. 
1. Desa estação os atributos de reclamação de saída. 

O exemplo a seguir acrescenta a `accountBalance` alegação. A reclamação de contaBalance é enviada para o pedido como saldo. 

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <!--Add the optional claims here-->
      <OutputClaim ClaimTypeReferenceId="accountBalance" DefaultValue="" PartnerClaimType="balance" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

O elemento OutputClaim contém os seguintes atributos:

- **ClaimTypeReferenceId** - O identificador de um tipo de reclamação já definido na secção [ClaimsSchema](claimsschema.md) no ficheiro de política ou ficheiro de política dos pais.
- **PartnerClaimType** - Permite alterar o nome da reclamação no token. 
- **DefaultValue** - Um valor predefinido. Também pode definir o valor padrão para um [resolver de reclamação,](claim-resolver-overview.md)como iD de inquilino.
- **AlwaysUseDefaultValue** - Force a utilização do valor predefinido.

::: zone-end

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [solicitar fichas de acesso.](access-tokens.md)