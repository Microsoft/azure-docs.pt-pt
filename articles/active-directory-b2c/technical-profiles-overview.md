---
title: Visão geral dos perfis técnicos em políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como os perfis técnicos são usados numa política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/11/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3b0e59912d740e30b0e29fb882542f1995ab6f54
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505666"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Sobre perfis técnicos em políticas personalizadas do Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um perfil técnico fornece um quadro com um mecanismo incorporado para comunicar com diferentes tipos de partes utilizando uma política personalizada no Azure Ative Directory B2C (Azure AD B2C). Os perfis técnicos são utilizados para comunicar com o seu inquilino Azure AD AD B2C, para criar um utilizador ou ler um perfil de utilizador. Um perfil técnico pode ser autoafirmado para permitir a interação com o utilizador. Por exemplo, recolher a credencial do utilizador para iniciar sessão e, em seguida, renderizar a página de inscrição ou página de reset da palavra-passe.

## <a name="type-of-technical-profiles"></a>Tipo de perfis técnicos

Um perfil técnico permite este tipo de cenários:

- [Azure Ative Directory](active-directory-technical-profile.md) - Fornece suporte para a gestão de utilizadores do Azure Ative Directory B2C.
- [Emissor de símboloj](jwt-issuer-technical-profile.md) - Emite um símbolo JWT que é devolvido à aplicação do partido que depende.
- **Fornecedor de fatores telefónicos** - Autenticação multifactor.
- [OAuth1](oauth1-technical-profile.md) - Federação com qualquer provedor de identidade protocolo OAuth 1.0.
- [OAuth2](oauth2-technical-profile.md) - Federação com qualquer fornecedor de identidade protocolo OAuth 2.0.
- [OpenID Connect](openid-connect-technical-profile.md) - Federação com qualquer fornecedor de identidade de protocolo OpenID Connect.
- [Transformação de sinistros](claims-transformation-technical-profile.md) - A produção de chamadas reclama transformações para manipular valores de sinistros, validar reclamações ou definir valores padrão para um conjunto de reclamações de saída.
- [Fornecedor RESTful](restful-technical-profile.md) - Ligue para os serviços rest API, tais como validar a entrada do utilizador, enriquecer os dados do utilizador ou integrar-se com aplicações de linha de negócio.
- [SAML2](saml-technical-profile.md) - Federação com qualquer fornecedor de identidade de protocolo SAML.
- [Self-Afirmado](self-asserted-technical-profile.md) - Interaja com o utilizador. Por exemplo, recolher a credencial do utilizador para iniciar sessão, renderizar a página de inscrição ou repor a palavra-passe.
- [Gestão de sessões](custom-policy-reference-sso.md) - Lidar com diferentes tipos de sessões.
- [Application Insights](../azure-monitor/app/usage-overview.md)
- [Palavra-passe única](one-time-password-technical-profile.md) - Fornece suporte para gerir a geração e verificação de uma senha única. 

## <a name="technical-profile-flow"></a>Fluxo de perfil técnico

Todos os tipos de perfis técnicos partilham o mesmo conceito. Envia reclamações de entrada, executa transformação de sinistros e comunica com a parte configurada, como um fornecedor de identidade, rest API ou serviços de diretório Azure AD. Após a conclusão do processo, o perfil técnico devolve as reclamações de saída e pode executar a transformação de sinistros de saída. O diagrama seguinte mostra como as transformações e mapeamentos referenciados no perfil técnico são processados. Independentemente da parte com que o perfil técnico interage, após a execução de qualquer transformação de sinistros, as alegações de saída do perfil técnico são imediatamente armazenadas no saco de sinistros.

![Diagrama ilustrando o fluxo de perfil técnico](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **Gestão de sessão de inscrição única (SSO)** - Restaura o estado de sessão do perfil técnico, utilizando a gestão da [sessão SSO](custom-policy-reference-sso.md). 
1. **Transformação** de créditos de entrada - As alegações de entrada de cada transformação de [créditos](claimstransformations.md) de entrada são recolhidas no saco de sinistros.  As alegações de saída de uma transformação de créditos de entrada podem ser alegações de entrada de uma transformação subsequente de créditos de entrada.
1. **Reclamações** de entrada - As reclamações são recolhidas no saco de sinistros e são utilizadas para o perfil técnico. Por exemplo, um [perfil técnico autoafirmado](self-asserted-technical-profile.md) utiliza as alegações de entrada para pré-povoar as alegações de saída que o utilizador fornece. Um perfil técnico REST API utiliza as alegações de entrada para enviar parâmetros de entrada para o ponto final da API REST. O Azure Ative Directory utiliza a alegação de entrada como um identificador único para ler, atualizar ou apagar uma conta.
1. **Execução** de perfil técnico - O perfil técnico troca as reclamações com a parte configurada. Por exemplo:
    - Redirecione o utilizador para o fornecedor de identidade para completar o inserição. Após o sucesso do início de sessão, o utilizador regressa e a execução do perfil técnico continua.
    - Ligue para um REST API enquanto envia parâmetros como InputClaims e obter informações de volta como OutputClaims.
    - Criar ou atualizar a conta de utilizador.
    - Envia e verifica a mensagem de texto do MFA.
1. **Perfis técnicos** de validação - Um [perfil técnico autoafirmado](self-asserted-technical-profile.md) pode chamar [perfis técnicos](validation-technical-profile.md)de validação . O perfil técnico de validação valida os dados perfilados pelo utilizador e devolve uma mensagem de erro ou Ok, com ou sem reclamações de saída. Por exemplo, antes de o Azure AD B2C criar uma nova conta, verifica se o utilizador já existe nos serviços de diretório. Pode chamar um perfil técnico REST API para adicionar a sua própria lógica de negócio.<p>O âmbito das alegações de saída de um perfil técnico de validação limita-se ao perfil técnico que invoca o perfil técnico de validação. e outros perfis técnicos de validação sob o mesmo perfil técnico. Se pretender utilizar as alegações de saída no próximo passo de orquestração, terá de adicionar as alegações de saída ao perfil técnico que invoca o perfil técnico de validação.
1. **Reclamações** de saída - As reclamações são devolvidas ao saco de sinistros. Pode usar essas reivindicações no próximo passo de orquestração, ou a produção reivindica transformações.
1. **Produção reclama transformações** - As alegações de entrada de cada transformação de [sinistros](claimstransformations.md) de saída são recolhidas no saco de sinistros. As alegações de saída do perfil técnico dos passos anteriores podem ser alegações de entrada de uma transformação de sinistros de saída. Após a execução, as alegações de saída são colocadas de volta no saco de reclamações. As alegações de produção de uma transformação de sinistros de produção também podem ser alegações de entrada de uma transformação subsequente de sinistros de produção.
1. **Gestão de sessão de inscrição única (SSO)** - Persiste os dados do perfil técnico para a sessão, utilizando a gestão da [sessão SSO](custom-policy-reference-sso.md).


## <a name="technical-profile-inclusion"></a>Inclusão de perfil técnico

Um perfil técnico pode incluir outro perfil técnico para alterar definições ou adicionar novas funcionalidades.  O elemento `IncludeTechnicalProfile` é uma referência ao perfil técnico base a partir do qual é derivado um perfil técnico. Não há limite para o número de níveis. 

Por exemplo, o perfil técnico **AAD-UserReadUsingAlternativeSecurityId-NoError** inclui o **AAD-UserReadUsingAlternativeSecurityId**. Este perfil técnico define o item de metadados `RaiseErrorIfClaimsPrincipalDoesNotExist` para `true`, e levanta um erro se uma conta social não existir no diretório. **AAD-UserReadUsingAlternativeSecurityId-NoError sobrepõe-se** a este comportamento e desativa essa mensagem de erro.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** inclui o perfil técnico `AAD-Common`.

```XML
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

Tanto **a AAD-UserReadUsingAlternativeSecurityId-NoError** e **a AAD-UserReadAlternativeSecurityId** não especificam o elemento **protocolo** exigido, uma vez que está especificado no perfil técnico **AAD-Common.**

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```
