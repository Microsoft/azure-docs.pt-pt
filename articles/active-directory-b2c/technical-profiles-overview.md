---
title: Sobre perfis técnicos em Azure Active Directory B2C políticas personalizadas | Microsoft Docs
description: Saiba mais sobre como os perfis técnicos são usados em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f90b69cde4a961457c987f004e2605e6884bf323
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063329"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Sobre perfis técnicos em Azure Active Directory B2C políticas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um perfil técnico fornece uma estrutura com um mecanismo interno para se comunicar com diferentes tipos de partes usando uma política personalizada no Azure Active Directory B2C (Azure AD B2C). Os perfis técnicos são usados para se comunicar com seu locatário Azure AD B2C, para criar um usuário ou ler um perfil de usuário. Um perfil técnico pode ser autodeclarado para permitir a interação com o usuário. Por exemplo, colete a credencial do usuário para entrar e, em seguida, renderizar a página de inscrição ou a página de redefinição de senha.

## <a name="type-of-technical-profiles"></a>Tipo de perfis técnicos

Um perfil técnico habilita esses tipos de cenários:

- [Azure Active Directory](active-directory-technical-profile.md) -fornece suporte para o gerenciamento de usuários Azure Active Directory B2C.
- [Emissor do token JWT](jwt-issuer-technical-profile.md) – emite um token JWT que é retornado para o aplicativo de terceira parte confiável.
- **Provedor de fator de telefone** – autenticação multifator.
- [OAuth1](oauth1-technical-profile.md) -Federation com qualquer provedor de identidade de protocolo OAuth 1,0.
- [OAuth2](oauth2-technical-profile.md) -Federation com qualquer provedor de identidade de protocolo OAuth 2,0.
- [OpenID Connect](openid-connect-technical-profile.md) – Federação com qualquer provedor de identidade de protocolo OpenID Connect.
- [Transformação de declarações](claims-transformation-technical-profile.md) – chame as transformações de declarações de saída para manipular valores de declarações, validar declarações ou definir valores padrão para um conjunto de declarações de saída.
- [Provedor RESTful](restful-technical-profile.md) -chamada para serviços de API REST, como validar entrada do usuário, enriquecer dados do usuário ou integrar com aplicativos de linha de negócios.
- [SAML2](saml-technical-profile.md) -Federation com qualquer provedor de identidade de protocolo SAML.
- [Autodeclarado](self-asserted-technical-profile.md) -interagir com o usuário. Por exemplo, colete a credencial do usuário para entrar, renderizar a página de inscrição ou redefinição de senha.
- [Gerenciamento de sessão](active-directory-b2c-reference-sso-custom.md) -manipule tipos diferentes de sessões.
- **Application insights**

## <a name="technical-profile-flow"></a>Fluxo de perfil técnico

Todos os tipos de perfis técnicos compartilham o mesmo conceito. Você envia declarações de entrada, executa a transformação de declarações e comunica-se com a parte configurada, como um provedor de identidade, API REST ou serviços de diretório do Azure AD. Após a conclusão do processo, o perfil técnico retorna as declarações de saída e pode executar a transformação de declarações de saída. O diagrama a seguir mostra como as transformações e os mapeamentos referenciados no perfil técnico são processados. Independentemente da parte com a qual o perfil técnico interage, após a execução de qualquer transformação de declarações, as declarações de saída do perfil técnico são armazenadas imediatamente no recipiente de declarações.

![Diagrama ilustrando o fluxo de perfil técnico](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **InputClaimsTransformation** -as declarações de entrada de cada [transformação de declarações](claimstransformations.md) de entrada são selecionadas a partir do recipiente de declarações e, após a execução, as declarações de saída são recolocadas na bolsa de declarações. As declarações de saída de uma transformação de declarações de entrada podem ser declarações de entrada de uma transformação de declarações de entrada subsequente.
2. **InputClaims** -as reivindicações são coletadas do recipiente de declarações e são usadas para o perfil técnico. Por exemplo, um [perfil técnico autodeclarado](self-asserted-technical-profile.md) usa as declarações de entrada para preencher previamente as declarações de saída que o usuário fornece. Um perfil técnico da API REST usa as declarações de entrada para enviar parâmetros de entrada para o ponto de extremidade da API REST. Azure Active Directory usa a declaração de entrada como um identificador exclusivo para ler, atualizar ou excluir uma conta.
3. **Execução do perfil técnico** -o perfil técnico troca as declarações pela parte configurada. Por exemplo:
    - Redirecione o usuário para o provedor de identidade para concluir a entrada. Após a entrada bem-sucedida, o usuário retorna de volta e a execução do perfil técnico continua.
    - Chamar uma API REST ao enviar parâmetros como InputClaims e obter informações de volta como OutputClaims.
    - Criar ou atualizar a conta de usuário.
    - Envia e verifica a mensagem de texto do MFA.
4. **ValidationTechnicalProfiles** -para um [perfil técnico autodeclarado](self-asserted-technical-profile.md), você pode chamar um [perfil técnico de validação](validation-technical-profile.md)de entrada. O perfil técnico de validação valida os dados analisados pelo usuário e retorna uma mensagem de erro ou Ok, com ou sem declarações de saída. Por exemplo, antes que Azure AD B2C crie uma nova conta, ela verifica se o usuário já existe nos serviços de diretório. Você pode chamar um perfil técnico da API REST para adicionar sua própria lógica de negócios.<p>O escopo das declarações de saída de um perfil técnico de validação é limitado ao perfil técnico que invoca o perfil técnico de validação e outros perfis técnicos de validação no mesmo perfil técnico. Se você quiser usar as declarações de saída na próxima etapa de orquestração, será necessário adicionar as declarações de saída ao perfil técnico que invoca o perfil técnico de validação.
5. **OutputClaims** -as declarações são retornadas de volta para o recipiente de declarações. Você pode usar essas declarações na próxima etapa de orquestrações ou as transformações de declarações de saída.
6. **OutputClaimsTransformations** -as declarações de entrada de cada [transformação de declarações](claimstransformations.md) de saída são coletadas no recipiente de declarações. As declarações de saída do perfil técnico das etapas anteriores podem ser declarações de entrada de uma transformação de declarações de saída. Após a execução, as declarações de saída são colocadas de volta no recipiente de declarações. As declarações de saída de uma transformação de declarações de saída também podem ser declarações de entrada de uma transformação de declarações de saída subsequente.
7.  - Gerenciamento de sessão SSO (logon único) o gerenciamento de sessão[SSO](active-directory-b2c-reference-sso-custom.md) controla a interação com um usuário após o usuário já ter sido autenticado. Por exemplo, o administrador pode controlar se a seleção de provedores de identidade é exibida ou se os detalhes da conta local precisam ser inseridos novamente.

Um perfil técnico pode herdar de outro perfil técnico para alterar as configurações ou adicionar uma nova funcionalidade.  O elemento **IncludeTechnicalProfile** é uma referência ao perfil técnico base do qual um perfil técnico é derivado.

Por exemplo, o perfil técnico **AAD-UserReadUsingAlternativeSecurityId-NOERROR** inclui o **AAD-UserReadUsingAlternativeSecurityId**. Esse perfil técnico define o item de metadados RaiseErrorIfClaimsPrincipalDoesNotExist `true`como e gerará um erro se uma conta social não existir no diretório. **AAD-UserReadUsingAlternativeSecurityId-NOERROR** substitui esse comportamento e desabilita a mensagem de erro se o usuário não existia.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

O **AAD-UserReadUsingAlternativeSecurityId** inclui `AAD-Common` o perfil técnico.

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

O **AAD-UserReadUsingAlternativeSecurityId-NOERROR** e o **AAD-UserReadUsingAlternativeSecurityId** não especificam o elemento de **protocolo** necessário porque ele está especificado no perfil técnico do **AAD-comum** .

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

Um perfil técnico pode incluir ou herdar outro perfil técnico, que pode incluir outro. Não há limite para o número de níveis. Dependendo dos requisitos de negócios, o percurso do usuário pode chamar **AAD-UserReadUsingAlternativeSecurityId** que gera um erro se uma conta social do usuário não existir ou **AAD-UserReadUsingAlternativeSecurityId-NOERROR** , que não gerar um erro.











