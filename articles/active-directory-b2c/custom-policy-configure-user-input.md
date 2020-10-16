---
title: Adicione reclamações e personalize a entrada do utilizador em políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como personalizar a entrada do utilizador e adicione reclamações à viagem de inscrição ou de inscrição no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff9093872b2a5e069aef43ae2230b08447eea602
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92069862"
---
#  <a name="add-claims-and-customize-user-input-using-custom-policies-in-azure-active-directory-b2c"></a>Adicione reclamações e personalize a entrada do utilizador usando políticas personalizadas no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Neste artigo, você recolhe um novo atributo durante a sua viagem de inscrição em Azure Ative Directory B2C (Azure AD B2C). Você vai obter a cidade dos utilizadores, configurá-la como uma entrega, e definir se é necessário fornecer.

> [!NOTE]
> Esta amostra usa a reivindicação incorporada "cidade". Em vez disso, pode escolher um dos [atributos incorporados Azure AD B2C suportados](user-profile-attributes.md) ou um atributo personalizado. Para utilizar um atributo personalizado, [ative atributos personalizados na sua política](custom-policy-custom-attributes.md). Para utilizar um atributo incorporado ou personalizado diferente, substitua a 'cidade' pelo atributo à sua escolha, por exemplo, o *atributo incorporado, o JobTit* ou um atributo personalizado como *extension_loyaltyId*.  

Pode recolher dados iniciais dos seus utilizadores utilizando a viagem de utilizador de inscrição ou de inscrição. As reclamações adicionais podem ser recolhidas mais tarde utilizando uma viagem de utilizador de edição de perfil. Sempre que a Azure AD B2C recolhe informações diretamente do utilizador interativamente, o Quadro de Experiência de Identidade utiliza o seu [perfil técnico autoafirmado.](self-asserted-technical-profile.md) Nesta amostra, você:

1. Defina uma reivindicação de "cidade". 
1. Peça ao utilizador pela sua cidade.
1. Persistir a cidade para o perfil de utilizador no diretório Azure AD B2C.
1. Leia a reclamação da cidade do diretório Azure AD B2C em cada inscrição.
1. Devolva a cidade à sua candidatura de festa de gestão após a inscrição ou inscrição.  

## <a name="prerequisites"></a>Pré-requisitos

Complete os passos em [Começar com políticas personalizadas.](custom-policy-get-started.md) Você deve ter uma política personalizada de trabalho para se inscrever e iniciar sação com contas sociais e locais.

## <a name="define-a-claim"></a>Definir uma reclamação

Uma reclamação fornece um armazenamento temporário de dados durante uma execução política Azure AD B2C. O [esquema de reclamações](claimsschema.md) é o lugar onde declara as suas reivindicações. São utilizados os seguintes elementos para definir a alegação:

- **DisplayName** - Uma corda que define a etiqueta virada para o utilizador.
- [DataType](claimsschema.md#datatype) - O tipo de reclamação.
- **UserHelpText** - Ajuda o utilizador a compreender o que é necessário.
- [UserInputType](claimsschema.md#userinputtype) - O tipo de controlo de entrada, como caixa de texto, seleção de rádio, lista de abandono ou múltiplas seleções.

Abra o ficheiro de extensões da sua apólice. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Procure o elemento [Blocos de Construção.](buildingblocks.md) Se o elemento não existir, adicione-o.
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

## <a name="add-a-claim-to-the-user-interface"></a>Adicionar uma reclamação à interface de utilizador

Os seguintes perfis técnicos são [autoafirmados,](self-asserted-technical-profile.md)invocados quando se espera que um utilizador forneça informações:

- **LocalAccountSignUpWithLogonEmail** - Fluxo de inscrição de conta local.
- **SelfAsserted-Social** - Conta federada insinusado pela primeira vez.
- **SelfAsserted-ProfileUpdate** - Editar o fluxo de perfil.

Para recolher a reclamação da cidade durante a inscrição, deve ser adicionado como uma reivindicação de saída ao `LocalAccountSignUpWithLogonEmail` perfil técnico. Substitua este perfil técnico no ficheiro de extensão. Especifique toda a lista de pedidos de saída para controlar a ordem que as reclamações são apresentadas no ecrã. Encontre o elemento **ClaimsProviders.** Adicione um novo ClaimsProviders da seguinte forma:

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
```

Para recolher a reclamação da cidade após a iniciação inicial com uma conta federada, deve ser adicionado como um pedido de saída ao `SelfAsserted-Social` perfil técnico. Para que os utilizadores de contas locais e federados possam editar os seus dados de perfil mais tarde, adicione a reivindicação de saída ao `SelfAsserted-ProfileUpdate` perfil técnico. Substitua estes perfis técnicos no ficheiro de extensão. Especifique toda a lista dos pedidos de saída para controlar a ordem que as reclamações são apresentadas no ecrã. Encontre o elemento **ClaimsProviders.** Adicione um novo ClaimsProviders da seguinte forma:

```xml
<ClaimsProvider>
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

## <a name="read-and-write-a-claim"></a>Leia e escreva uma reclamação

Os seguintes perfis técnicos são [perfis técnicos do Ative Directory,](active-directory-technical-profile.md)que lêem e escrevem dados para o Azure Ative Directory.  
Utilize `PersistedClaims` para escrever dados para o perfil do utilizador e `OutputClaims` para ler dados a partir do perfil do utilizador dentro dos respetivos perfis técnicos do Ative Directory.

Substitua estes perfis técnicos no ficheiro de extensão. Encontre o elemento **ClaimsProviders.**  Adicione um novo ClaimsProviders da seguinte forma:

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

## <a name="include-a-claim-in-the-token"></a>Incluir uma reclamação no token 

Para devolver o pedido da cidade ao pedido do partido, adicione um pedido de saída ao <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> ficheiro. A alegação de saída será adicionada ao token após uma viagem bem sucedida do utilizador, e será enviada para a aplicação. Modifique o elemento de perfil técnico dentro da secção do partido dependente para adicionar a cidade como uma reivindicação de saída.
 
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

## <a name="test-the-custom-policy"></a>Teste a política personalizada

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **as inscrições da App.**
4. Selecione **o Quadro de Experiência de Identidade.**
5. Selecione **''Personal's Personal Policy ' 'Upload'** e, em seguida, faça o upload dos dois ficheiros de política que alterou.
2. Selecione a política de inscrição ou de inscrição que fez o upload e clique no botão **Executar agora.**
3. Deverá inscrever-se através de um endereço de e-mail.

O ecrã de inscrição deve ser semelhante ao seguinte screenshot:

![Screenshot da opção de inscrição modificada](./media/custom-policy-configure-user-input/signup-with-city-claim-dropdown-example.png)

O token enviado de volta para o seu pedido inclui a `city` reclamação.

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

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o elemento [ClaimsSchema](claimsschema.md) na referência IEF.
- Saiba como [usar atributos personalizados numa política de edição de perfis personalizados.](custom-policy-custom-attributes.md)
