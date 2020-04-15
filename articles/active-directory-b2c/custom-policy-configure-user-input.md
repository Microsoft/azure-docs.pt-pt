---
title: Adicione reclamações e personalize a entrada do utilizador em políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como personalizar a entrada do utilizador e adicionar reclamações à viagem de inscrição ou inscrição no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 56a3478f1c0dbc05eba07a5109f5bb6ba89b79d0
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79079883"
---
#  <a name="add-claims-and-customize-user-input-using-custom-policies-in-azure-active-directory-b2c"></a>Adicione reclamações e personalize a entrada do utilizador usando políticas personalizadas no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Neste artigo, você recolhe um novo atributo durante a sua jornada de inscrição no Azure Ative Directory B2C (Azure AD B2C). Você vai obter a cidade dos utilizadores, configurá-la como uma gota e definir se é necessário fornecer.

Pode recolher dados iniciais dos seus utilizadores utilizando a viagem de inscrição ou de inscrição no utilizador. As reclamações adicionais podem ser recolhidas mais tarde, utilizando uma viagem de utilizador de edição de perfil. Sempre que o Azure AD B2C recolhe informações diretamente do utilizador interactivamente, o Quadro de Experiência de Identidade utiliza o seu [perfil técnico autoafirmado](self-asserted-technical-profile.md). Nesta amostra, você:

1. Defina uma reivindicação de "cidade".
1. Pergunte ao utilizador pela sua cidade.
1. Persistir a cidade até ao perfil de utilizador no diretório Azure AD B2C.
1. Leia a reivindicação da cidade do diretório Azure AD B2C em cada entrada.
1. Devolva a cidade à sua candidatura de festa de base após inscrição ou inscrição.  

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em introdução [às políticas personalizadas](custom-policy-get-started.md). Você deve ter uma política personalizada de trabalho para inscrição e inscrição com contas sociais e locais.

## <a name="define-a-claim"></a>Definir uma reclamação

Uma reclamação fornece um armazenamento temporário de dados durante uma execução política Azure AD B2C. O [esquema das alegações](claimsschema.md) é o lugar onde declaras as tuas alegações. São utilizados os seguintes elementos para definir a alegação:

- **Nome do ecrã** - Uma cadeia que define a etiqueta virada para o utilizador.
- [DataType](claimsschema.md#datatype) - O tipo de reclamação.
- **UserHelpText** - Ajuda o utilizador a compreender o que é necessário.
- [UserInputType](claimsschema.md#userinputtype) - O tipo de controlo de entrada, como caixa de texto, seleção de rádio, lista de drop-down ou várias seleções.

Abra o ficheiro de extensões da sua apólice. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.

1. Procure o elemento [BuildingBlocks](buildingblocks.md) . Se o elemento não existir, adicione-o.
1. Localize o elemento [ClaimsSchema.](claimsschema.md) Se o elemento não existir, adicione-o.
1. Adicione a reivindicação da cidade ao elemento **ClaimsSchema.**  

```xml
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

## <a name="add-a-claim-to-the-user-interface"></a>Adicione uma reclamação à interface do utilizador

Os seguintes perfis técnicos são [autoafirmados,](self-asserted-technical-profile.md)invocados quando se espera que um utilizador forneça informações:

- **LocalAccountSignUpWithLogonEmail** - Fluxo de inscrição de conta local.
- **SelfAsserted-Social** - Conta federada pela primeira vez.
- **AutoAfirmou-ProfileUpdate** - Editar fluxo de perfil.

Para recolher a reivindicação da cidade durante a inscrição, deve ser adicionada como uma reivindicação de saída para o perfil técnico `LocalAccountSignUpWithLogonEmail`. Anular este perfil técnico no ficheiro de extensão. Especifique toda a lista de pedidos de saída para controlar a ordem que as reclamações são apresentadas no ecrã. Encontre o elemento **ClaimsProviders.** Adicione um novo ClaimsProviders da seguinte forma:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <!--Local account sign-up page-->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
       <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="displayName" />
       <OutputClaim ClaimTypeReferenceId="givenName" />
       <OutputClaim ClaimTypeReferenceId="surName" />
       <OutputClaim ClaimTypeReferenceId="city"/>
     </OutputClaims>
   </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
<ClaimsProvider>
```

Para recolher a reclamação da cidade após o início de sessão com uma conta federada, deve ser adicionado como uma reivindicação de saída ao perfil técnico `SelfAsserted-Social`. Para que os utilizadores de conta locais e federados possam editar os seus dados de perfil mais tarde, adicione a alegação de saída ao perfil técnico `SelfAsserted-ProfileUpdate`. Anular estes perfis técnicos no ficheiro de extensão. Especifique toda a lista das alegações de saída para controlar a ordem que as reclamações são apresentadas no ecrã. Encontre o elemento **ClaimsProviders.** Adicione um novo ClaimsProviders da seguinte forma:

```xml
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="read-and-write-a-claim"></a>Ler e escrever uma reclamação

Os seguintes perfis técnicos são [perfis técnicos ative diretórios,](active-directory-technical-profile.md)que lêem e escrevem dados para o Azure Ative Directory.  
Utilize `PersistedClaims` para escrever dados para o perfil do utilizador e `OutputClaims` para ler dados do perfil do utilizador dentro dos respetivos perfis técnicos do Diretório Ativo.

Anular estes perfis técnicos no ficheiro de extensão. Encontre o elemento **ClaimsProviders.**  Adicione um novo ClaimsProviders da seguinte forma:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <!-- Write data during a local account sign-up flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during a federated account first-time sign-in flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during edit profile flow. -->
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>Incluir uma reclamação no símbolo 

Para devolver a reivindicação da cidade à aplicação do partido que depende, adicione uma reclamação de saída ao <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> ficheiro. A alegação de saída será adicionada ao símbolo após uma viagem bem sucedida do utilizador, e será enviada para a aplicação. Modifique o elemento de perfil técnico dentro da secção do partido de fiação para adicionar a cidade como uma reivindicação de saída.
 
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
      <OutputClaim ClaimTypeReferenceId="city" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Testar a política personalizada

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione registos de **Aplicações**.
4. Selecione Quadro de **Experiência de Identidade**.
5. Selecione **'Upload Custom Policy'** e, em seguida, faça upload dos dois ficheiros de política que alterou.
2. Selecione a política de inscrição ou de inscrição que fez o upload e clique no botão **Executar agora.**
3. Deverá poder inscrever-se através de um endereço de e-mail.

O ecrã de inscrição deve ser semelhante ao seguinte screenshot:

![Screenshot da opção de inscrição modificada](./media/custom-policy-configure-user-input/signup-with-city-claim-dropdown-example.png)

O símbolo enviado de volta para o seu pedido inclui a reclamação `city`.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1583500140,
  "nbf": 1583496540,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1583496540,
  "auth_time": 1583496540,
  "name": "Emily Smith",
  "email": "joe@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "city": "Bellevue"
  ...
}
```

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o elemento [ClaimsSchema](claimsschema.md) na referência IEF.
- Aprenda a [usar atributos personalizados numa política](custom-policy-custom-attributes.md)de edição de perfil personalizado.
