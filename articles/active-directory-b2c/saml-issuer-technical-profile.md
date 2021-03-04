---
title: Defina um perfil técnico para um emitente SAML numa política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico para um emitente de linguagem de marcação de afirmação de segurança (SAML) numa política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 54869c14cf7c5a7e43f34102f5c95e37689dfee8
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095345"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico para um emitente de token SAML numa política personalizada do Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) emite vários tipos de fichas de segurança à medida que processa cada fluxo de autenticação. Um perfil técnico para um emissor de token SAML emite um token SAML que é devolvido à aplicação do partido dependente (prestador de serviços). Normalmente, este perfil técnico é o último passo de orquestração na jornada do utilizador.

## <a name="protocol"></a>Protocolo

O **atributo nome** do elemento **Protocolo** tem de ser definido para `SAML2` . Desacorda o elemento **OutputTokenFormat** para `SAML2` .

O exemplo a seguir mostra um perfil técnico `Saml2AssertionIssuer` para:

```xml
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="SAML2"/>
  <OutputTokenFormat>SAML2</OutputTokenFormat>
  <Metadata>
    <Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>
    <Item Key="TokenNotBeforeSkewInSeconds">600</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
    <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
  </CryptographicKeys>
  <InputClaims/>
  <OutputClaims/>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Entradas, saídas e reivindicações persistentes

Os **elementos InputClaims,** **OutputClaims** e **PersistClaims** estão vazios ou ausentes. Os **elementos InutputClaimsTransformations** e **OutputClaimsTransformations** também estão ausentes.

## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| EmitenteUri | Não | O nome do emitente que aparece na resposta SAML. O valor deve ser o mesmo que configurado na aplicação do partido em gestão. |
| XmlSignatureAlgorithm | Não | O método que a Azure AD B2C utiliza para assinar a Afirmação SAML. Valores possíveis: `Sha256` `Sha384` , , ou `Sha512` `Sha1` . Certifique-se de configurar o algoritmo de assinatura em ambos os lados com o mesmo valor. Use apenas o algoritmo que o seu certificado suporta. Para configurar a resposta SAML, consulte [Opções de registo de uma aplicação SAML](saml-service-provider.md)|
|TokenNotBeforeSkewInSeconds| Não| Especifica o desvio, como um inteiro, para o carimbo de tempo que marca o início do período de validade. Quanto maior for este número, mais recua no tempo o período de validade começa em relação ao tempo em que os pedidos são emitidos para a parte que conta. Por exemplo, quando o TokenNotBeforeSkewInSegundos está definido para 60 segundos, se o token for emitido às 13:05:10 UTC, o token é válido a partir das 13:04:10 UTC. O valor predefinido é 0. O valor máximo é de 3600 (uma hora). |
|TokenLifeTimeInSeconds| Não| Especifica a vida da Afirmação SAML. Este valor encontra-se em segundos a partir do valor Não Antes referido acima. O valor predefinido é de 300 segundos (5 Min). |


## <a name="cryptographic-keys"></a>Chaves criptográficas

O elemento CryptographicKeys contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| MetadadosSigning | Sim | O certificado X509 (conjunto de teclas RSA) para usar para assinar metadados SAML. Azure AD B2C utiliza esta chave para assinar os metadados. |
| SamlMessageSigning| Sim| Especifique o certificado X509 (conjunto de teclas RSA) para utilizar para assinar mensagens SAML. A Azure AD B2C utiliza esta chave para assinar a resposta `<samlp:Response>` enviada à parte que conta.|

## <a name="session-management"></a>Gestão de sessões

Para configurar as sessões Azure AD B2C SAML entre uma aplicação de partido em suporte, o atributo do `UseTechnicalProfileForSessionManagement` elemento, referência à sessão [SSO SSO da SamlSSOSessionProvider.](custom-policy-reference-sso.md#samlssosessionprovider)

## <a name="next-steps"></a>Passos seguintes

Consulte o seguinte artigo, por exemplo, utilizando um perfil técnico de emitente SAML:

- [Registar um pedido DE SAML em Azure AD B2C](saml-service-provider.md)

