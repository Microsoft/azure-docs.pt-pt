---
title: Gestão de sessão de sessão única usando políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como gerir as sessões de SSO utilizando políticas personalizadas em Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1f3fb07eaf7f63d15232f4c94eeee45f43c81616
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075141"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Gestão única da sessão de sessão de inscrição no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A gestão [de sessão de sessão de sessão de assinatura única (SSO)](session-behavior.md) utiliza a mesma semântica que qualquer outro perfil técnico em políticas personalizadas. Quando um passo de orquestração é executado, o perfil técnico associado ao passo é pedido para uma `UseTechnicalProfileForSessionManagement` referência. Se existir, o fornecedor de sessão SSO referenciado é verificado para ver se o utilizador é um participante da sessão. Em caso afirmativo, o provedor de sessão SSO é utilizado para repovoar a sessão. Da mesma forma, quando a execução de um passo de orquestração estiver concluída, o fornecedor é utilizado para armazenar informações na sessão se tiver sido especificado um provedor de sessão SSO.

A Azure AD B2C definiu uma série de fornecedores de sessão SSO que podem ser utilizados:

|Provedor de sessão  |Âmbito  |
|---------|---------|
|[NoopSOSessionProvider](#noopssosessionprovider)     |  Nenhum       |       
|[Predefinição SSSessionProvider](#defaultssosessionprovider)    | Gestor de sessão interna Azure AD B2C.      |       
|[ExternalLoginSSOSessionProvider](#externalloginssosessionprovider)     | Entre Azure AD B2C e OAuth1, OAuth2 ou OpenId Connect fornecedor de identidade.        | 
|[OAuthSSOSessionProvider](#oauthssosessionprovider)     | Entre um OAuth2 ou OpenId conecte a aplicação do partido de gestão e o Azure AD B2C.        |        
|[SamlSSOSessionProvider](#samlssosessionprovider)     | Entre a Azure AD B2C e o fornecedor de identidade SAML. E entre um prestador de serviços SAML (aplicação de partidos) e Azure AD B2C.  |        




As classes de gestão SSO são especificadas utilizando o `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` elemento de um perfil técnico.

## <a name="input-claims"></a>Reclamações de entrada

O `InputClaims` elemento está vazio ou ausente.

## <a name="persisted-claims"></a>Reivindicações persistentes

As reclamações que necessitem de ser devolvidas à aplicação ou utilizadas por pré-condições nas etapas subsequentes, devem ser armazenadas na sessão ou aumentadas por uma leitura do perfil do utilizador no diretório. A utilização de reivindicações persistiu garante que as suas viagens de autenticação não falharão em reclamações em falta. Para adicionar reclamações na sessão, utilize o `<PersistedClaims>` elemento do perfil técnico. Quando o fornecedor é utilizado para repovoar a sessão, as reclamações persistiu são adicionadas ao saco de reclamações.

## <a name="output-claims"></a>Reclamações de saída

O `<OutputClaims>` é usado para recuperar reclamações da sessão.

## <a name="session-providers"></a>Fornecedores de sessão

### <a name="noopssosessionprovider"></a>NoopSOSessionProvider

Como o nome dita, este provedor não faz nada. Este fornecedor pode ser utilizado para suprimir o comportamento SSO para um perfil técnico específico. O seguinte `SM-Noop` perfil técnico está incluído no pacote de arranque de política [personalizado](custom-policy-get-started.md#custom-policy-starter-pack).

```xml
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>Predefinição SSSessionProvider

Este fornecedor pode ser utilizado para armazenar reclamações numa sessão. Este fornecedor é tipicamente referenciado num perfil técnico utilizado para a gestão de contas locais e federadas. O seguinte `SM-AAD` perfil técnico está incluído no pacote de arranque de política [personalizado](custom-policy-get-started.md#custom-policy-starter-pack).

```xml
<TechnicalProfile Id="SM-AAD">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="signInName" />
    <PersistedClaim ClaimTypeReferenceId="authenticationSource" />
    <PersistedClaim ClaimTypeReferenceId="identityProvider" />
    <PersistedClaim ClaimTypeReferenceId="newUser" />
    <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```


O seguinte `SM-MFA` perfil técnico está incluído no pacote de arranque de política [personalizado](custom-policy-get-started.md#custom-policy-starter-pack) `SocialAndLocalAccountsWithMfa` . Este perfil técnico gere a sessão de autenticação de vários fatores.

```xml
<TechnicalProfile Id="SM-MFA">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isActiveMFASession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Este fornecedor é utilizado para suprimir o ecrã "escolher o fornecedor de identidade" e a assinatura de um fornecedor de identidade federado. É tipicamente referenciado num perfil técnico configurado para um fornecedor de identidade federado, como o Facebook, ou Azure Ative Directory. O seguinte `SM-SocialLogin` perfil técnico está incluído no pacote de arranque de política [personalizado](custom-policy-get-started.md#custom-policy-starter-pack).

```xml
<TechnicalProfile Id="SM-SocialLogin">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="AlternativeSecurityId" />
  </PersistedClaims>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição|
| --- | --- | --- |
| AlwaysFetchClaimsFromProvider | No | Não atualmente usado, pode ser ignorado. |

### <a name="oauthssosessionprovider"></a>OAuthSSOSessionProvider

Este fornecedor é utilizado para gerir as sessões Azure AD B2C entre uma festa de confiança OAuth2 ou OpenId Connect e Azure AD B2C.

```xml
<TechnicalProfile Id="SM-jwt-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Este fornecedor é utilizado para gerir as sessões SAML Azure AD B2C entre uma aplicação de partido dependente ou um fornecedor de identidade FEDERADA SAML. Ao utilizar o fornecedor SSO para armazenar uma sessão de fornecedor de identidade SAML, `RegisterServiceProviders` deve ser definido para `false` . O seguinte `SM-Saml-idp` perfil técnico é utilizado pelo fornecedor de identidade [SAML.](identity-provider-generic-saml.md)

```xml
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

Ao utilizar o fornecedor para armazenar a sessão DE SAML B2C, `RegisterServiceProviders` o deve ser definido para `true` . O logout da sessão SAML requer o `SessionIndex` e `NameID` para completar.

O seguinte `SM-Saml-issuer` perfil técnico é utilizado pelo perfil técnico do [emitente SAML](saml-service-provider.md)

```xml
<TechnicalProfile Id="SM-Saml-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição|
| --- | --- | --- |
| Inclui SessionIndex | No | Não atualmente usado, pode ser ignorado.|
| RegistrServiceProviders | No | Indica que o prestador deve registar todos os prestadores de serviços SAML que tenham sido emitidos uma afirmação. Valores possíveis: `true` (padrão), ou `false` .|


## <a name="next-steps"></a>Passos seguintes

Saiba como [configurar o comportamento da sessão](session-behavior.md).
