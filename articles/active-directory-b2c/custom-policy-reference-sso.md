---
title: Gestão de sessão de sessão de inscrição única usando políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como gerir as sessões SSO utilizando políticas personalizadas em Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ee32b13820cb50fc1649672b78b34e7e293d65b5
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849087"
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

As classes de gestão SSO são especificadas utilizando o elemento `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` de um perfil técnico.

## <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Como o nome dita, este fornecedor não faz nada. Este fornecedor pode ser utilizado para suprimir o comportamento do SSO para um perfil técnico específico.

## <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Este fornecedor pode ser utilizado para armazenar reclamações numa sessão. Este fornecedor é tipicamente referenciado num perfil técnico utilizado para gerir contas locais. Ao utilizar o DefaultSSOSessionProvider para armazenar reclamações numa sessão, é necessário garantir que quaisquer reclamações que necessitem de ser devolvidas à aplicação ou utilizadas por pré-condições em etapas posteriores, sejam armazenadas na sessão ou aumentadas por uma leitura do perfil dos utilizadores em diretório. Isto garantirá que a sua jornada de autenticação não falhará em reclamações em falta.

```XML
<TechnicalProfile Id="SM-AAD">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="newUser" />
        <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
    </PersistedClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true" />
    </OutputClaims>
</TechnicalProfile>
```

Para adicionar reclamações na sessão, utilize o elemento `<PersistedClaims>` do perfil técnico. Quando o fornecedor é utilizado para repovoar a sessão, as reclamações persistidas são adicionadas ao saco de sinistros. `<OutputClaims>` é usado para recuperar reclamações da sessão.

## <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Este fornecedor é utilizado para suprimir o ecrã "escolha do fornecedor de identidade". É tipicamente referenciado num perfil técnico configurado para um fornecedor de identidade externo, como o Facebook.

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Este fornecedor é utilizado para gerir as sessões Azure AD B2C SAML entre apps, bem como fornecedores de identidade SAML externos.

```XML
<TechnicalProfile Id="SM-Reflector-SAML">
    <DisplayName>Session Management Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
    </Metadata>
</TechnicalProfile>
```

Existem dois itens de metadados no perfil técnico:

| Item | Default Value | Valores Possíveis | Descrição
| --- | --- | --- | --- |
| IncludeSessionIndex | true | verdadeiro/falso | Indica ao fornecedor que o índice de sessão deve ser armazenado. |
| RegisterServiceProviders | true | verdadeiro/falso | Indica que o fornecedor deve registar todos os prestadores de serviços SAML que tenham sido emitidos uma afirmação. |

Ao utilizar o fornecedor para armazenar uma sessão de fornecedor de identidade SAML, os itens acima devem ser falsos. Ao utilizar o fornecedor para armazenar a sessão B2C SAML, os itens acima devem ser verdadeiros ou omitidos, uma vez que os incumprimentos são verdadeiros. O logout de sessão SAML requer que a `SessionIndex` e `NameID` sejam concluídas.

