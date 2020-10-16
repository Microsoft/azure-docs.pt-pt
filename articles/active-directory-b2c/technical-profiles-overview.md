---
title: Visão geral dos perfis técnicos em políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como os perfis técnicos são usados numa política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7417e2d39371066a5c5e8576040cbe22e7632043
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90562881"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Sobre perfis técnicos em políticas personalizadas Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um perfil técnico fornece um quadro com um mecanismo incorporado para comunicar com diferentes tipos de partes usando uma política personalizada em Azure Ative Directory B2C (Azure AD B2C). Os perfis técnicos são utilizados para comunicar com o seu inquilino Azure AD B2C, para criar um utilizador ou ler um perfil de utilizador. Um perfil técnico pode ser autoafirmado para permitir a interação com o utilizador. Por exemplo, colete a credencial do utilizador para iniciar sessão e, em seguida, torne a página de inscrição ou a página de reset da palavra-passe.

## <a name="type-of-technical-profiles"></a>Tipo de perfis técnicos

Um perfil técnico permite este tipo de cenários:

- [Application Insights](application-insights-technical-profile.md) - Envio de dados de eventos para [Insights de Aplicação](../azure-monitor/app/app-insights-overview.md).
- [Azure Ative Directory](active-directory-technical-profile.md) - Fornece suporte para a gestão de utilizadores Azure Ative Directory B2C.
- [Azure Multi-Factor Authentication](multi-factor-auth-technical-profile.md) - fornece suporte para verificar um número de telefone utilizando a autenticação multi-factor Azure (MFA). 
- [Transformação de reclamações](claims-transformation-technical-profile.md) - A produção de chamadas reclama transformações para manipular valores de sinistros, validar reclamações ou definir valores padrão para um conjunto de reclamações de saída.
- [Dica de id token](id-token-hint.md) - Valida `id_token_hint` a assinatura simbólica JWT, o nome do emitente e o público simbólico e extrai a reivindicação do token de entrada.
- [Emitente JWT token](jwt-issuer-technical-profile.md) - Emite um símbolo JWT que é devolvido à aplicação do partido em gestão.
- [OAuth1](oauth1-technical-profile.md) - Federação com qualquer provedor de identidade do protocolo OAuth 1.0.
- [OAuth2](oauth2-technical-profile.md) - Federação com qualquer provedor de identidade do protocolo OAuth 2.0.
- [Palavra-passe única](one-time-password-technical-profile.md) - Fornece suporte para gerir a geração e verificação de uma senha única.
- [OpenID Connect](openid-connect-technical-profile.md) - Federação com qualquer fornecedor de identidade de protocolo OpenID Connect.
- [Fator telefone](phone-factor-technical-profile.md) - Suporte para inscrição e verificação de números de telefone.
- [RESTful provider](restful-technical-profile.md) - Ligue para os serviços de API REST, tais como validar a entrada do utilizador, enriquecer os dados dos utilizadores ou integrar-se com aplicações de linha de negócio.
- [Fornecedor de identidade SAML](saml-identity-provider-technical-profile.md) - Federação com qualquer fornecedor de identidade de protocolo SAML.
- [Emissor de token SAML](saml-issuer-technical-profile.md) - Emite um token SAML que é devolvido à aplicação do partido em gestão.
- [Auto-Assertado](self-asserted-technical-profile.md) - Interaja com o utilizador. Por exemplo, recolher a credencial do utilizador para iniciar sessão, fazer a página de inscrição ou reiniciar a palavra-passe.
- [Gestão de sessão](custom-policy-reference-sso.md) - Lidar com diferentes tipos de sessões.

## <a name="technical-profile-flow"></a>Fluxo de perfil técnico

Todos os tipos de perfis técnicos partilham o mesmo conceito. Envia reclamações de entradas, execute a transformação de reclamações e comunica com a parte configurada, como um fornecedor de identidade, REST API ou serviços de diretório AD Azure. Após o processo estar concluído, o perfil técnico devolve os pedidos de saída e pode executar a transformação de sinistros de saída. O diagrama seguinte mostra como as transformações e mapeamentos referenciados no perfil técnico são processados. Independentemente da parte, o perfil técnico interage com, após a execução de qualquer transformação de sinistros, os pedidos de saída do perfil técnico são imediatamente armazenados no saco de reclamações.

![Diagrama que ilustra o fluxo de perfil técnico](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)

1. **Gestão única de sessão de sessão de sessão (SSO)** - Restaura o estado de sessão do perfil técnico, utilizando a gestão da [sessão SSO](custom-policy-reference-sso.md).
1. **A transformação** dos pedidos de entrada - As alegações de entrada de cada [entrada de sinistros](claimstransformations.md) são recolhidas a partir do saco de reclamações.  As alegações de saída de uma transformação de pedidos de entrada podem ser alegações de entrada de uma transformação de pedidos de entrada subsequentes.
1. **Reclamações de entrada** - As reclamações são recolhidas no saco de reclamações e são utilizadas para o perfil técnico. Por exemplo, um [perfil técnico autoafirmado](self-asserted-technical-profile.md) utiliza as alegações de entrada para pré-povoar as alegações de saída que o utilizador fornece. Um perfil técnico da API REST utiliza as alegações de entrada para enviar parâmetros de entrada para o ponto final da API REST. O Azure Ative Directory utiliza a reclamação de entrada como um identificador único para ler, atualizar ou apagar uma conta.
1. **Execução de perfil técnico** - O perfil técnico troca as reclamações com a parte configurada. Por exemplo:
    - Redirecione o utilizador para o fornecedor de identidade para completar a inscrição. Após o sucesso da sessão, o utilizador regressa e a execução do perfil técnico continua.
    - Ligue para uma API REST enquanto envia parâmetros como InputClaims e obter informações de volta como OutputClaims.
    - Criar ou atualizar a conta do utilizador.
    - Envia e verifica a mensagem de texto do MFA.
1. **Perfis técnicos** de validação - Um [perfil técnico autoafirmado](self-asserted-technical-profile.md) pode chamar [perfis técnicos de validação](validation-technical-profile.md). O perfil técnico de validação valida os dados perfilados pelo utilizador e devolve uma mensagem de erro ou Ok, com ou sem reclamações de saída. Por exemplo, antes de o Azure AD B2C criar uma nova conta, verifica se o utilizador já existe nos serviços de diretório. Pode chamar um perfil técnico da API REST para adicionar a sua própria lógica de negócio.<p>O âmbito das reclamações de saída de um perfil técnico de validação limita-se ao perfil técnico que invoca o perfil técnico de validação. e outros perfis técnicos de validação com o mesmo perfil técnico. Se quiser utilizar as reclamações de saída no próximo passo de orquestração, tem de adicionar as reclamações de saída ao perfil técnico que invoca o perfil técnico de validação.
1. **Reclamações de saída** - As reclamações são devolvidas ao saco de reclamações. Pode usar essas reivindicações na próxima etapa de orquestrações, ou a produção reclama transformações.
1. **A produção reclama transformações** - As alegações de entrada de cada [produção alega que](claimstransformations.md) a transformação são retiradas do saco de sinistros. As alegações de saída do perfil técnico dos passos anteriores podem ser alegações de entrada de uma transformação de sinistros de saída. Após a execução, os pedidos de saída são colocados de volta no saco de reclamações. Os pedidos de produção de uma transformação de pedidos de produção também podem ser alegações de entrada de uma transformação de pedidos de produção subsequentes.
1. **Gestão única da sessão de sessão de sessão de sessão de sessão ( SSO)** - Persiste os dados do perfil técnico para a sessão, utilizando a gestão da [sessão SSO](custom-policy-reference-sso.md).


## <a name="technical-profile-inclusion"></a>Inclusão de perfil técnico

Um perfil técnico pode incluir outro perfil técnico para alterar definições ou adicionar uma nova funcionalidade.  O `IncludeTechnicalProfile` elemento é uma referência ao perfil técnico base a partir do qual é derivado um perfil técnico. Não há limite para o número de níveis.

Por exemplo, o perfil técnico **AAD-UserReadUsingAlternativeSecurityId-NoError** inclui o perfil técnico **AAD-UserReadUsingAlternativeSecurityId**. Este perfil técnico define o `RaiseErrorIfClaimsPrincipalDoesNotExist` item dos metadados para `true` , e levanta um erro se uma conta social não existir no diretório. **AAD-UserReadUsingAlternativeSecurityId-NoError** substitui este comportamento e desativa essa mensagem de erro.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** inclui o `AAD-Common` perfil técnico.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Tanto **o AAD-UserReadUsingAlternativeSecurityId-NoError** e **o AAD-UserReadUsingAlternativeSecurityId** não especificam o elemento **de protocolo** exigido, uma vez que está especificado no perfil técnico **AAD-Common.**

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```
