---
title: Sobre os perfis técnicos em políticas personalizadas do Azure Active Directory B2C | Documentos da Microsoft
description: Saiba mais sobre os perfis de técnicos como são utilizados numa política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 8865da2f39f574656fe7f018eb1f1900b913391c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64710911"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Sobre os perfis técnicos em políticas personalizadas do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um perfil técnico fornece uma estrutura com um mecanismo interno para comunicar com outro tipo de partes utilizando uma política personalizada no Azure Active Directory (Azure AD) B2C. Perfis técnicos são utilizados para comunicar com o seu inquilino do Azure AD B2C, criar um utilizador ou ler um perfil de utilizador. Um perfil técnico pode ser declaração própria para permitir a interação com o utilizador. Por exemplo, recolher as credenciais do utilizador para iniciar sessão e, em seguida, processar a página de inscrição ou a página de reposição de palavra-passe. 

## <a name="type-of-technical-profiles"></a>Tipo de perfis técnicos

Um perfil técnico permite que esses tipos de cenários:

- [O Azure Active Directory](active-directory-technical-profile.md) -fornece suporte para a gestão de utilizadores do Azure Active Directory B2C.
- [Emissor de tokens JWT](jwt-issuer-technical-profile.md) -emite um token JWT, que é devolvido para a aplicação da entidade confiadora de terceiros. 
- **Fornecedor do multi factor Phone** -multi-factor authentication.
- [OAuth1](oauth1-technical-profile.md) -federação com qualquer fornecedor de identidade do protocolo de OAuth 1.0.
- [OAuth2](oauth2-technical-profile.md) -federação com qualquer fornecedor de identidade do protocolo de OAuth 2.0.
- [OpenIdConnect](openid-connect-technical-profile.md) -federação com qualquer fornecedor de identidade do protocolo OpenId Connect.
- [Transformação de afirmações](claims-transformation-technical-profile.md) - valores de afirmações de transformação de declarações de saída de chamada para manipular, validar afirmações ou definir os valores predefinidos para um conjunto de afirmações de saída.
- [Fornecedor de restful](restful-technical-profile.md) -chamada para serviços de REST API, como validar a entrada do usuário, enriquecer os dados de utilizador, ou integrar com aplicações de linha de negócio.
- [SAML2](saml-technical-profile.md) -federação com qualquer fornecedor de identidade do protocolo SAML.
- [Autodeclarativas](self-asserted-technical-profile.md) -interagir com o utilizador. Por exemplo, recolher as credenciais do utilizador para iniciar sessão, renderizar a página de inscrição ou de reposição de palavra-passe.
- **WsFed** -federação com qualquer fornecedor de identidade de protocolo do WsFed. 
- [Gerenciamento de sessões](active-directory-b2c-reference-sso-custom.md) -lidar com diferentes tipos de sessões. 
- **O Application insights**

## <a name="technical-profile-flow"></a>Fluxo do perfil técnico

Todos os tipos de perfis técnicos partilham o mesmo conceito. Enviar afirmações de entrada, executar a transformação de declarações e comunicar com a terceiros configurada, como um fornecedor de identidade, REST API ou serviços de diretório do Azure AD. Depois do processo estar concluído, o perfil técnico devolve as afirmações de saída e pode executar a transformação de declarações de saída. O diagrama seguinte mostra como as transformações e mapeamentos referenciados no perfil técnico são processados. Independentemente da parte que do perfil técnico interage com, após a quaisquer afirmações de transformação é executada, as afirmações de saída do perfil técnico da imediatamente são armazenadas no conjunto de afirmações. 

![Fluxo do perfil técnico](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **InputClaimsTransformation** -afirmações de cada entrada de entrada [transformação de afirmações](claimstransformations.md) são captados a partir do conjunto de afirmações e após a execução, a afirmações são colocadas para trás no conjunto de afirmações de saída. As afirmações de saída de uma transformação de afirmações de entrada podem ser afirmações de entrada de uma transformação de afirmações de entrada subsequentes.
2. **InputClaims** - as afirmações são captados a partir do conjunto de afirmações e são utilizados para o perfil técnico. Por exemplo, um [autodeclarativas perfil técnico](self-asserted-technical-profile.md) utiliza as afirmações de entrada para pré-povoar as afirmações de saída que o utilizador fornece. Um perfil técnico da REST API utiliza afirmações de entrada para enviar parâmetros de entrada para o ponto final de REST API. O Azure Active Directory utiliza afirmações de entrada como um identificador exclusivo para ler, atualizar ou eliminar uma conta.
3. **A execução do perfil técnico** -o perfil técnico troca as afirmações com a parte configurada. Por exemplo:
    - Redirecione o utilizador para o fornecedor de identidade para concluir o início de sessão. Após o início de sessão com êxito, o usuário retorna e continua a execução do perfil técnico.
    - Chame uma API de REST ao envio de parâmetros como InputClaims e obtenção de informações de volta como OutputClaims.
    - Criar ou atualizar a conta de utilizador.
    - Envia e verifica se a mensagem de texto MFA.
4. **ValidationTechnicalProfiles** – para uma [auto-avaliar relativamente perfil técnico](self-asserted-technical-profile.md), pode chamar uma entrada [perfil técnico de validação](validation-technical-profile.md). O perfil técnico de validação valida os dados com perfis criados pelo usuário e retorna uma mensagem de erro ou Ok, com ou sem afirmações de saída. Por exemplo, antes que o Azure AD B2C, crie uma nova conta, ele verifica se o utilizador já existe nos serviços de diretório. Pode chamar um perfil técnico da REST API para adicionar sua própria lógica de negócios.<p>O escopo das afirmações de saída de um perfil de técnicas de validação é limitado para o perfil técnico que invoca o perfil técnico de validação e de outros perfis de técnicas de validação sob o mesmo perfil técnico. Se pretender utilizar as afirmações de saída no próximo passo de orquestração, terá de adicionar as afirmações de saída para o perfil técnico que invoca o perfil técnico de validação.
5. **OutputClaims** -afirmações são devolvidas para o conjunto de afirmações. Pode usar essas declarações na próxima etapa de orquestrações ou transformações de afirmações de saída.
6. **OutputClaimsTransformations** -afirmações de cada saída de entrada [transformação de afirmações](claimstransformations.md) são captados a partir do conjunto de afirmações. As afirmações de saída do perfil técnico dos passos anteriores podem ser afirmações de entrada de uma transformação de afirmações de saída. Após a execução, as afirmações de saída são colocadas para trás no conjunto de afirmações. As afirmações de saída de uma transformação de afirmações de saída também podem ser afirmações de entrada de uma transformação de afirmações de saída subsequentes.
7. **Único início de sessão em gerenciamento de sessão (SSO)** - [gestão de sessões SSO](active-directory-b2c-reference-sso-custom.md) controla a interação com um utilizador depois do utilizador já foi autenticado. Por exemplo, o administrador pode controlar se a seleção de fornecedores de identidade é apresentada, ou se os detalhes da conta local precisam de ser introduzido novamente.

Um perfil técnico pode herdar de outro perfil técnico para alterar definições ou adicionar novas funcionalidades.  O **IncludeTechnicalProfile** elemento é uma referência para o perfil técnico base do que um perfil técnico é derivado.  

Por exemplo, o **UserReadUsingAlternativeSecurityId-AAD-NoError** perfil técnico inclui o **AAD UserReadUsingAlternativeSecurityId**. Este perfil técnico define a **RaiseErrorIfClaimsPrincipalDoesNotExist** item de metadados para `true`e gera um erro se não existir uma conta de redes sociais no diretório. **AAD-UserReadUsingAlternativeSecurityId-NoError** substitui este comportamento e desativa a mensagem de erro, caso o usuário não tenha existido anteriormente.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
``` 

**AAD-UserReadUsingAlternativeSecurityId** inclui o `AAD-Common` perfil técnico.

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

Ambos **UserReadUsingAlternativeSecurityId-AAD-NoError** e **AAD UserReadUsingAlternativeSecurityId** não especificar necessários **protocolo** elemento porque é especificado com o **comuns de AAD** perfil técnico.

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

Um perfil técnico pode incluir ou herdar de outro perfil técnico, que pode incluir a outra. Não existe nenhum limite no número de níveis. Consoante os requisitos de negócios, pode chamar o seu percurso do utilizador **AAD UserReadUsingAlternativeSecurityId** que irá gerar um erro se não existir uma conta de rede social do utilizador, ou  **AAD-UserReadUsingAlternativeSecurityId-NoError** que não a emitir um erro.











