---
title: Defina um perfil técnico para um emitente SAML numa política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico para um emitível de linguagem de marcação de afirmação de segurança (SAML) numa política personalizada no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f038868e80b600c805a1f33a54f9d0032e81dab8
ms.sourcegitcommit: 3616b42a0d6bbc31b965995d861930e53d2cf0d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78933196"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico para um emitente de token SAML numa política personalizada do Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) emite vários tipos de fichas de segurança à medida que processa cada fluxo de autenticação. Um perfil técnico para um emitente de token SAML emite um token SAML que é devolvido à aplicação do partido que depende (prestador de serviços). Normalmente, este perfil técnico é o último passo de orquestração na jornada do utilizador.

## <a name="protocol"></a>Protocolo

O **nome** atributo do elemento **protocolo** tem de ser definido para `None`. Detete o elemento **OutputTokenFormat** para `SAML2`.

O exemplo que se segue mostra um perfil técnico para `Saml2AssertionIssuer`:

```XML
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="None"/>
  <OutputTokenFormat>SAML2</OutputTokenFormat>
  <Metadata>
    <Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
    <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
  </CryptographicKeys>
  <InputClaims/>
  <OutputClaims/>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-sp"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Entrada, saída e persistir reclamações

Os elementos **InputClaims**, **OutputClaims**e **PersistClaims** estão vazios ou ausentes. Os **elementos InutputClaimsTransformations** and **OutputClaimsTransformations** também estão ausentes.

## <a name="metadata"></a>Metadados

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| Emituri | Não | O nome emitente que aparece na resposta SAML. O valor deve ser o mesmo nome configurado na aplicação do partido que depende. |

## <a name="cryptographic-keys"></a>Chaves criptográficas

O elemento CryptographicKeys contém os seguintes atributos:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| MetadadosSigning | Sim | O certificado X509 (conjunto de chaves RSA) para usar para assinar metadados SAML. O Azure AD B2C utiliza esta chave para assinar os metadados. |
| SamlMessageSigning| Sim| Especifique o certificado X509 (conjunto de teclas RSA) para utilizar para assinar mensagens SAML. O Azure AD B2C usa esta chave para assinar a resposta `<samlp:Response>` enviar para o partido que confia.|

## <a name="session-management"></a>Gestão de sessões

Para configurar as sessões SAML Azure AD B2C entre uma aplicação de parte dependente, o atributo do elemento `UseTechnicalProfileForSessionManagement`, referência à sessão [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) SSO.












