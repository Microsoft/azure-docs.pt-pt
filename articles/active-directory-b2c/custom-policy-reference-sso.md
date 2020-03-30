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
ms.date: 03/09/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 80cf0d101a29de7fca9d4dd36e188a500d35e290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246035"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Gestão de sessão de inscrição única no Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A gestão da sessão de sessão de início de sessão (SSO) no Azure Ative Directory B2C (Azure AD B2C) permite a um administrador controlar a interação com um utilizador depois de o utilizador já ter autenticado. Por exemplo, o administrador pode controlar se a seleção dos fornecedores de identidade é apresentada ou se os detalhes da conta local precisam de ser novamente introduzidos. Este artigo descreve como configurar as definições sSO para Azure AD B2C.

A gestão da sessão sSO tem duas partes. O primeiro lida com as interações do utilizador diretamente com o Azure AD B2C e os outros negócios com as interações do utilizador com partes externas, como o Facebook. O Azure AD B2C não sobrepor-se ou contornar as sessões sso que possam ser realizadas por partes externas. Pelo contrário, a rota através do Azure AD B2C para chegar à parte externa é "lembrada", evitando a necessidade de recolocar o utilizador na escolha do seu fornecedor de identidade social ou empresarial. A decisão final do SSO permanece com o partido externo.

A gestão da sessão SSO utiliza a mesma semântica que qualquer outro perfil técnico em políticas personalizadas. Quando um passo de orquestração é executado, o perfil técnico associado `UseTechnicalProfileForSessionManagement` ao passo é consultado para uma referência. Se existe uma, o fornecedor de sessão SSO referenciado é verificado para ver se o utilizador é um participante da sessão. Em caso afirmativo, o provedor de sessão SSO é utilizado para repovoar a sessão. Da mesma forma, quando a execução de um passo de orquestração estiver completa, o fornecedor é utilizado para armazenar informações na sessão se for especificado um provedor de sessão SSO.

O Azure AD B2C definiu uma série de fornecedores de sessão SSO que podem ser utilizados:

* NoopSSOSessionProvider
* Provedor de Sessões por defeito
* ExternalLoginSSOSessionProvider
* Provedor samlSSOSession

As classes de gestão SSO são especificadas utilizando o `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` elemento de um perfil técnico.

## <a name="input-claims"></a>Reclamações de entrada

O `InputClaims` elemento está vazio ou ausente.

## <a name="persisted-claims"></a>Alegações persistidas

As reclamações que precisam de ser devolvidas à aplicação ou utilizadas por condições prévias em etapas posteriores, devem ser armazenadas na sessão ou aumentadas através de uma leitura do perfil do utilizador no diretório. A utilização de alegações persistentes garante que as suas viagens de autenticação não falharão nas reclamações em falta. Para adicionar reclamações na `<PersistedClaims>` sessão, utilize o elemento do perfil técnico. Quando o fornecedor é utilizado para repovoar a sessão, as reclamações persistidas são adicionadas ao saco de sinistros.

## <a name="output-claims"></a>Reclamações de produção

O `<OutputClaims>` é usado para recuperar reclamações da sessão.

## <a name="session-providers"></a>Provedores de sessão

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Como o nome dita, este fornecedor não faz nada. Este fornecedor pode ser utilizado para suprimir o comportamento do SSO para um perfil técnico específico. O `SM-Noop` perfil técnico seguinte está incluído no pacote de arranque de [política personalizada](custom-policy-get-started.md#custom-policy-starter-pack).

```XML
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>Provedor de Sessões por defeito

Este fornecedor pode ser utilizado para armazenar reclamações numa sessão. Este fornecedor é tipicamente referenciado num perfil técnico utilizado para gerir contas locais. O `SM-AAD` perfil técnico seguinte está incluído no pacote de arranque de [política personalizada](custom-policy-get-started.md#custom-policy-starter-pack).

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

O `SM-MFA` perfil técnico seguinte está incluído no pacote [custom policy starter pack](custom-policy-get-started.md#custom-policy-starter-pack) `SocialAndLocalAccountsWithMfa`de arranque de política personalizada . Este perfil técnico gere a sessão de autenticação de vários fatores.

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

Este fornecedor é utilizado para suprimir o ecrã "escolha do fornecedor de identidade". É tipicamente referenciado num perfil técnico configurado para um fornecedor de identidade externo, como o Facebook. O `SM-SocialLogin` perfil técnico seguinte está incluído no pacote de arranque de [política personalizada](custom-policy-get-started.md#custom-policy-starter-pack).

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

### <a name="samlssosessionprovider"></a>Provedor samlSSOSession

Este fornecedor é utilizado para gerir as sessões Azure AD B2C SAML entre uma aplicação de parte dependente ou um fornecedor de identidade SAML federado. Ao utilizar o fornecedor SSO para armazenar uma `RegisterServiceProviders` sessão de `false`fornecedor de identidade SAML, o deve ser definido para . O `SM-Saml-idp` perfil técnico seguinte é utilizado pelo [perfil técnico SAML](saml-technical-profile.md).

```XML
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

Ao utilizar o fornecedor para armazenar a sessão `RegisterServiceProviders` B2C SAML, o deve ser definido para `true`. O logout de sessão `SessionIndex` `NameID` SAML requer o e completar.

O `SM-Saml-idp` perfil técnico seguinte é utilizado pelo perfil técnico do [emitente SAML](saml-issuer-technical-profile.md)

```XML
<TechnicalProfile Id="SM-Saml-sp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```
#### <a name="metadata"></a>Metadados

| Atributo | Necessário | Descrição|
| --- | --- | --- |
| Incluir o SessionIndex | Não | Não usado atualmente, pode ser ignorado.|
| Registradores de Serviços | Não | Indica que o fornecedor deve registar todos os prestadores de serviços SAML que tenham sido emitidos uma afirmação. Valores `true` possíveis: `false`(padrão), ou .|



