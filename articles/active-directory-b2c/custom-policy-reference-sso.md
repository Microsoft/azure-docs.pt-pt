---
title: Gestão de sessão de sessão de inscrição única usando políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como gerir as sessões SSO utilizando políticas personalizadas em Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a64af5d2b19b05ec9a5eda97c43e278cdfb8b4ff
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189111"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Gestão de sessão de inscrição única no Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A gestão da sessão de sessão de início de sessão (SSO) no Azure Ative Directory B2C (Azure AD B2C) permite a um administrador controlar a interação com um utilizador depois de o utilizador já ter autenticado. Por exemplo, o administrador pode controlar se a seleção dos fornecedores de identidade é apresentada ou se os detalhes da conta local precisam de ser novamente introduzidos. Este artigo descreve como configurar as definições sSO para Azure AD B2C.

A gestão da sessão sSO tem duas partes. O primeiro lida com as interações do utilizador diretamente com o Azure AD B2C e os outros negócios com as interações do utilizador com partes externas, como o Facebook. O Azure AD B2C não sobrepor-se ou contornar as sessões sso que possam ser realizadas por partes externas. Pelo contrário, a rota através do Azure AD B2C para chegar à parte externa é "lembrada", evitando a necessidade de recolocar o utilizador na escolha do seu fornecedor de identidade social ou empresarial. A decisão final do SSO permanece com o partido externo.

A gestão da sessão SSO utiliza a mesma semântica que qualquer outro perfil técnico em políticas personalizadas. Quando um passo de orquestração é executado, o perfil técnico associado ao passo é consultado para uma referência `UseTechnicalProfileForSessionManagement`. Se existe uma, o fornecedor de sessão SSO referenciado é verificado para ver se o utilizador é um participante da sessão. Em caso afirmativo, o provedor de sessão SSO é utilizado para repovoar a sessão. Da mesma forma, quando a execução de um passo de orquestração estiver completa, o fornecedor é utilizado para armazenar informações na sessão se for especificado um provedor de sessão SSO.

O Azure AD B2C definiu uma série de fornecedores de sessão SSO que podem ser utilizados:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

As classes de gestão SSO são especificadas utilizando o elemento `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` de um perfil técnico.

## <a name="input-claims"></a>Reclamações de entrada

O elemento `InputClaims` está vazio ou ausente.

## <a name="persisted-claims"></a>Alegações persistidas

As reclamações que precisam de ser devolvidas à aplicação ou utilizadas por condições prévias em etapas posteriores, devem ser armazenadas na sessão ou aumentadas através de uma leitura do perfil do utilizador no diretório. A utilização de alegações persistentes garante que as suas viagens de autenticação não falharão nas reclamações em falta. Para adicionar reclamações na sessão, utilize o elemento `<PersistedClaims>` do perfil técnico. Quando o fornecedor é utilizado para repovoar a sessão, as reclamações persistidas são adicionadas ao saco de sinistros.

## <a name="output-claims"></a>Reclamações de produção

O `<OutputClaims>` é usado para recuperar reclamações da sessão.

## <a name="session-providers"></a>Provedores de sessão

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Como o nome dita, este fornecedor não faz nada. Este fornecedor pode ser utilizado para suprimir o comportamento do SSO para um perfil técnico específico. O seguinte `SM-Noop` perfil técnico está incluído no pacote de arranque de [política personalizada](custom-policy-get-started.md#custom-policy-starter-pack).

```XML
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Este fornecedor pode ser utilizado para armazenar reclamações numa sessão. Este fornecedor é tipicamente referenciado num perfil técnico utilizado para gerir contas locais. O seguinte `SM-AAD` perfil técnico está incluído no pacote de arranque de [política personalizada](custom-policy-get-started.md#custom-policy-starter-pack).

```XML
<TechnicalProfile Id="SM-AAD">
  <DisplayName>Session Mananagement Provider</DisplayName>
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

O perfil técnico `SM-MFA` seguinte está incluído no pacote de arranque de [política personalizada](custom-policy-get-started.md#custom-policy-starter-pack) `SocialAndLocalAccountsWithMfa`. Este perfil técnico gere a sessão de autenticação de vários fatores.

```XML
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

Este fornecedor é utilizado para suprimir o ecrã "escolha do fornecedor de identidade". É tipicamente referenciado num perfil técnico configurado para um fornecedor de identidade externo, como o Facebook. O seguinte `SM-SocialLogin` perfil técnico está incluído no pacote de arranque de [política personalizada](custom-policy-get-started.md#custom-policy-starter-pack).

```XML
<TechnicalProfile Id="SM-SocialLogin">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="AlwaysFetchClaimsFromProvider">true</Item>
  </Metadata>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="AlternativeSecurityId" />
  </PersistedClaims>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metadados

| Atributo | Necessário | Descrição|
| --- | --- | --- |
| AlwaysfetchClaimsFromProvider | Não | Não usado atualmente, pode ser ignorado. |

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Este fornecedor é utilizado para gerir as sessões Azure AD B2C SAML entre uma aplicação de parte dependente ou um fornecedor de identidade SAML federado. Ao utilizar o fornecedor SSO para armazenar uma sessão de fornecedor de identidade SAML, o `IncludeSessionIndex` e o `RegisterServiceProviders` devem ser definidos para `false`. O perfil técnico `SM-Saml-idp` seguinte é utilizado pelo [perfil técnico SAML](saml-technical-profile.md).

```XML
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IncludeSessionIndex">false</Item>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

Ao utilizar o fornecedor para armazenar a sessão B2C SAML, o `IncludeSessionIndex` e o `RegisterServiceProviders` devem `true`. O logout de sessão SAML requer que a `SessionIndex` e `NameID` sejam concluídas.

O perfil técnico de `SM-Saml-idp` seguinte é utilizado pelo perfil técnico do [emitente SAML](connect-with-saml-service-providers.md)

```XML
<TechnicalProfile Id="SM-Saml-sp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```
#### <a name="metadata"></a>Metadados

| Atributo | Necessário | Descrição|
| --- | --- | --- |
| IncludeSessionIndex | Não | Indica ao fornecedor que o índice de sessão deve ser armazenado. Valores possíveis: `true` (predefinido) ou `false`.|
| RegisterServiceProviders | Não | Indica que o fornecedor deve registar todos os prestadores de serviços SAML que tenham sido emitidos uma afirmação. Valores possíveis: `true` (predefinido) ou `false`.|



