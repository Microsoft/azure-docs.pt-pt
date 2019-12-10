---
title: Gerenciamento de sessão de logon único usando políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como gerenciar sessões de SSO usando políticas personalizadas no Azure AD B2C.
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
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950498"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Gerenciamento de sessão de logon único no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O gerenciamento de sessão de SSO (logon único) no Azure Active Directory B2C (Azure AD B2C) permite que um administrador controle a interação com um usuário depois que o usuário já tiver se autenticado. Por exemplo, o administrador pode controlar se a seleção de provedores de identidade é exibida ou se os detalhes da conta local precisam ser inseridos novamente. Este artigo descreve como definir as configurações de SSO para Azure AD B2C.

O gerenciamento de sessão de SSO tem duas partes. A primeira lida com as interações do usuário diretamente com Azure AD B2C e a outra lida com as interações do usuário com partes externas, como o Facebook. Azure AD B2C não substitui nem ignora as sessões de SSO que podem ser mantidas por partes externas. Em vez disso, a rota por meio de Azure AD B2C para chegar à parte externa é "lembrada", evitando a necessidade de solicitar novamente que o usuário selecione seu provedor de identidade social ou empresarial. A decisão de SSO final permanece com a parte externa.

O gerenciamento de sessão de SSO usa a mesma semântica que qualquer outro perfil técnico em políticas personalizadas. Quando uma etapa de orquestração é executada, o perfil técnico associado à etapa é consultado para uma referência de `UseTechnicalProfileForSessionManagement`. Se houver um, o provedor de sessão de SSO referenciado será verificado para ver se o usuário é um participante da sessão. Nesse caso, o provedor de sessão de SSO é usado para repopular a sessão. Da mesma forma, quando a execução de uma etapa de orquestração for concluída, o provedor será usado para armazenar informações na sessão se um provedor de sessão de SSO tiver sido especificado.

Azure AD B2C definiu um número de provedores de sessão SSO que podem ser usados:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

As classes de gerenciamento de SSO são especificadas usando o elemento `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` de um perfil técnico.

## <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Como o nome dita, esse provedor não faz nada. Esse provedor pode ser usado para suprimir o comportamento de SSO para um perfil técnico específico.

## <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Esse provedor pode ser usado para armazenar declarações em uma sessão. Normalmente, esse provedor é referenciado em um perfil técnico usado para gerenciar contas locais. Ao usar o DefaultSSOSessionProvider para armazenar declarações em uma sessão, você precisa garantir que todas as declarações que precisam ser retornadas para o aplicativo ou usadas pelas pré-condições nas etapas subsequentes sejam armazenadas na sessão ou aumentadas por uma leitura do perfil de usuários no active. Isso garantirá que a jornada de autenticação não falhe em declarações ausentes.

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

Para adicionar declarações na sessão, use o elemento `<PersistedClaims>` do perfil técnico. Quando o provedor é usado para repopular a sessão, as declarações persistentes são adicionadas ao recipiente de declarações. `<OutputClaims>` é usado para recuperar declarações da sessão.

## <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Esse provedor é usado para suprimir a tela "escolher provedor de identidade". Normalmente, ele é referenciado em um perfil técnico configurado para um provedor de identidade externo, como o Facebook.

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Esse provedor é usado para gerenciar o Azure AD B2C sessões SAML entre aplicativos, bem como provedores de identidade SAML externos.

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

Há dois itens de metadados no perfil técnico:

| Item | Default Value | Valores Possíveis | Descrição
| --- | --- | --- | --- |
| IncludeSessionIndex | true | verdadeiro/falso | Indica ao provedor que o índice de sessão deve ser armazenado. |
| RegisterServiceProviders | true | verdadeiro/falso | Indica que o provedor deve registrar todos os provedores de serviço SAML que foram emitidos uma asserção. |

Ao usar o provedor para armazenar uma sessão de provedor de identidade SAML, os itens acima devem ser falsos. Ao usar o provedor para armazenar a sessão de SAML do B2C, os itens acima devem ser verdadeiros ou omitidos, pois os padrões são verdadeiros. O logoff da sessão SAML requer que a `SessionIndex` e a `NameID` sejam concluídas.

