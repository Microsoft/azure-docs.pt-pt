---
title: Compreender as políticas personalizadas do starter pack no Azure Active Directory B2C | Documentos da Microsoft
description: Um tópico em políticas personalizadas do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: dbd57e53fbaec4368e3c8ce9600bce1d37bcab8f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60317331"
---
# <a name="understanding-the-custom-policies-of-the-azure-ad-b2c-custom-policy-starter-pack"></a>Compreender as políticas personalizadas do pacote de iniciante de política personalizada do Azure AD B2C

Esta secção lista as todos os elementos principais da política B2C_1A_base que vem com o **pacote de iniciante** e que é utilizado para a criação de suas próprias políticas por meio de herança do *B2C_1A_base_extensions política* .

Como tal, mais especialmente concentra-se no tipos de afirmação já definido, transformações de afirmações, definições de conteúdo, os fornecedores de afirmações com seus perfis técnicos e jornadas de utilizador principal.

> [!IMPORTANT]
> A Microsoft faz não oferece quaisquer garantias, expressas ou implícitas, quanto às informações fornecidas daqui em diante. As alterações poderão ser introduzidas em qualquer altura, antes da hora de disponibilidade geral, no momento da disponibilidade geral, ou depois.

As suas políticas e a política de B2C_1A_base_extensions podem substituir estas definições e alargar esta política de principal ao fornecer outras opções, conforme necessário.

Principais elementos dos *B2C_1A_base política* são tipos de afirmação de transformação de declarações e definições de conteúdo. Estes elementos podem suscetível a ser referenciado nas suas próprias políticas, bem como na *B2C_1A_base_extensions política*.

## <a name="claims-schemas"></a>Esquemas de afirmações

Este afirmações esquemas é dividido em três seções:

1.  Uma primeira seção que lista as declarações mínimas necessários para as Jornadas utilizador funcione corretamente.
2.  Uma segunda seção que lista as declarações necessárias para parâmetros de cadeia de caracteres de consulta e outros parâmetros especiais para ser passado para outros fornecedores de afirmações, especialmente login.microsoftonline.com para autenticação. **Não modifique essas declarações**.
3.  E, eventualmente, uma terceira seção que lista quaisquer afirmações adicionais, opcionais que podem ser coletadas do usuário, armazenados no diretório e enviadas em tokens durante o início de sessão. Novo tipo de afirmações para ser coletados do usuário e/ou enviados no token pode ser adicionado nesta secção.

> [!IMPORTANT]
> O esquema de afirmações contém restrições para determinadas afirmações, tais como palavras-passe e nomes de utilizador. A política de confiança Framework (TF) trata do Azure AD como qualquer outro fornecedor de afirmações e todas as respetivas restrições são modelled na política personalizada. Uma política pode ser modificada para adicionar mais restrições ou utilize outro fornecedor de afirmações para armazenamento de credenciais que terá seus próprio restrições.

Os tipos de afirmação disponíveis estão listados abaixo.

### <a name="claims-that-are-required-for-the-user-journeys"></a>Afirmações que são necessárias para as jornadas de utilizador

As declarações seguintes são necessárias para Jornadas utilizador funcione corretamente:

| Tipo de afirmações | Descrição |
|-------------|-------------|
| *UserId* | Nome de utilizador |
| *signInName* | Inicie sessão no nome |
| *tenantId* | Identificador do inquilino (ID) do objeto de utilizador no Azure AD B2C |
| *objectId* | Identificador de objeto (ID) do objeto de utilizador no Azure AD B2C |
| *password* | Palavra-passe |
| *newPassword* | |
| *reenterPassword* | |
| *passwordPolicies* | Políticas de palavra-passe utilizadas pelo Azure AD B2C para determinar a força da senha, expiração, etc. |
| *sub* | |
| *alternativeSecurityId* | |
| *identityProvider* | |
| *displayName* | |
| *strongAuthenticationPhoneNumber* | Número de telefone |
| *Verified.strongAuthenticationPhoneNumber* | |
| *email* | Endereço de e-mail que pode ser utilizado para contactar o utilizador |
| *signInNamesInfo.emailAddress* | Endereço de e-mail que o utilizador pode utilizar para iniciar sessão |
| *otherMails* | Endereços de e-mail que podem ser utilizados para contactar o utilizador |
| *userPrincipalName* | Nome de utilizador conforme armazenado no Azure AD B2C |
| *upnUserName* | Nome de utilizador para a criação de nome principal de utilizador |
| *mailNickName* | Nome de nick de correio do utilizador conforme armazenado no Azure AD B2C |
| *newUser* | |
| *executed-SelfAsserted-Input* | Afirmação que especifica se os atributos foram recolhidos do utilizador |
| *executed-PhoneFactor-Input* | Afirmação que especifica se o número de telefone foi recolhido do usuário |
| *authenticationSource* | Especifica se o utilizador foi autenticado no fornecedor de identidade Social, login.microsoftonline.com ou conta local |

### <a name="claims-required-for-query-string-parameters-and-other-special-parameters"></a>Declarações necessárias para os parâmetros de cadeia de caracteres de consulta e outros parâmetros especiais

As declarações seguintes são necessários para transmitir parâmetros especiais (incluindo alguns parâmetros de cadeia de caracteres de consulta) para outros fornecedores de afirmações:

| Tipo de afirmações | Descrição |
|-------------|-------------|
| *nux* | Especial parâmetro passado para a autenticação de conta local para login.microsoftonline.com |
| *nca* | Especial parâmetro passado para a autenticação de conta local para login.microsoftonline.com |
| *prompt* | Especial parâmetro passado para a autenticação de conta local para login.microsoftonline.com |
| *mkt* | Especial parâmetro passado para a autenticação de conta local para login.microsoftonline.com |
| *lc* | Especial parâmetro passado para a autenticação de conta local para login.microsoftonline.com |
| *grant_type* | Especial parâmetro passado para a autenticação de conta local para login.microsoftonline.com |
| *scope* | Especial parâmetro passado para a autenticação de conta local para login.microsoftonline.com |
| *client_id* | Especial parâmetro passado para a autenticação de conta local para login.microsoftonline.com |
| *objectIdFromSession* | Parâmetro fornecido pelo fornecedor de gestão de sessão predefinida para indicar que o ID de objeto foi obtido a partir de uma sessão SSO |
| *isActiveMFASession* | Parâmetro fornecido pelo gerenciamento de sessões MFA para indicar que o utilizador tem uma sessão ativa do MFA |

### <a name="additional-optional-claims-that-can-be-collected"></a>Afirmações adicionais (opcionais) que podem ser coletadas

As seguintes declarações são declarações adicionais que podem ser recolhidas dos utilizadores, armazenadas no diretório e enviadas no token. Conforme descrito antes, as afirmações adicionais podem ser adicionadas a esta lista.

| Tipo de afirmações | Descrição |
|-------------|-------------|
| *givenName* | Nome do utilizador (também conhecido como nome próprio) |
| *surname* | Apelido do utilizador (também conhecido como nome de família ou apelido) |
| *Extension_picture* | Imagem do utilizador no social |

## <a name="claim-transformations"></a>Transformações de afirmação

As transformações de afirmação disponíveis estão listadas abaixo.

| Transformação de afirmações | Descrição |
|----------------------|-------------|
| *CreateOtherMailsFromEmail* | |
| *CreateRandomUPNUserName* | |
| *CreateUserPrincipalName* | |
| *CreateSubjectClaimFromObjectID* | |
| *CreateSubjectClaimFromAlternativeSecurityId* | |
| *CreateAlternativeSecurityId* | |

## <a name="content-definitions"></a>Definições de conteúdo

Esta secção descreve as definições de conteúdo já declaradas no *B2C_1A_base* política. Estas definições de conteúdo são suscetíveis a ser referenciado, substituído, e/ou estendido conforme necessário em suas próprias políticas, bem como na *B2C_1A_base_extensions* política.

| Fornecedor de afirmações | Descrição |
|-----------------|-------------|
| *Facebook* | |
| *Início de sessão da conta local* | |
| *PhoneFactor* | |
| *Azure Active Directory* | |
| *Auto-verificada* | |
| *Conta local* | |
| *Gerenciamento de sessões* | |
| *Motor de política Trustframework* | |
| *TechnicalProfiles* | |
| *Emissor de tokens* | |

## <a name="technical-profiles"></a>Perfis técnicos

Esta secção descreve os perfis técnicos já declarados por fornecedor de afirmação a *B2C_1A_base* política. Estes perfis técnicos são suscetíveis a ser ainda mais referenciada, substituído, e/ou estendido conforme necessário em suas próprias políticas, bem como na *B2C_1A_base_extensions* política.

### <a name="technical-profiles-for-facebook"></a>Perfis técnicos para o Facebook

| Perfil técnico | Descrição |
|-------------------|-------------|
| *Facebook-OAUTH* | |

### <a name="technical-profiles-for-local-account-signin"></a>Perfis técnicos para início de sessão de conta Local

| Perfil técnico | Descrição |
|-------------------|-------------|
| *Login-NonInteractive* | |

### <a name="technical-profiles-for-phone-factor"></a>Perfis técnicos para o fator de telefone

| Perfil técnico | Descrição |
|-------------------|-------------|
| *PhoneFactor-Input* | |
| *PhoneFactor-InputOrVerify* | |
| *PhoneFactor-Verify* | |

### <a name="technical-profiles-for-azure-active-directory"></a>Perfis técnicos do Azure Active Directory

| Perfil técnico | Descrição |
|-------------------|-------------|
| *AAD-Common* | Perfil técnico incluído por outros perfis técnicos xxx do AAD |
| *AAD-UserWriteUsingAlternativeSecurityId* | Perfil técnico para inícios de sessão sociais |
| *AAD-UserReadUsingAlternativeSecurityId* | Perfil técnico para inícios de sessão sociais |
| *AAD-UserReadUsingAlternativeSecurityId-NoError* | Perfil técnico para inícios de sessão sociais |
| *AAD-UserWritePasswordUsingLogonEmail* | Perfil técnico para contas locais |
| *AAD-UserReadUsingEmailAddress* | Perfil técnico para contas locais |
| *AAD-UserWriteProfileUsingObjectId* | Perfil técnico para atualizar o registo de utilizador com objectId |
| *AAD-UserWritePhoneNumberUsingObjectId* | Perfil técnico para atualizar o registo de utilizador com objectId |
| *AAD-UserWritePasswordUsingObjectId* | Perfil técnico para atualizar o registo de utilizador com objectId |
| *AAD-UserReadUsingObjectId* | Perfil técnico é utilizado para ler os dados depois do utilizador é autenticado |

### <a name="technical-profiles-for-self-asserted"></a>Perfis técnicos para Self verificada

| Perfil técnico | Descrição |
|-------------------|-------------|
| *SelfAsserted-Social* | |
| *SelfAsserted-ProfileUpdate* | |

### <a name="technical-profiles-for-local-account"></a>Perfis técnicos para a conta Local

| Perfil técnico | Descrição |
|-------------------|-------------|
| *LocalAccountSignUpWithLogonEmail* | |

### <a name="technical-profiles-for-session-management"></a>Perfis técnicos para gerenciamento de sessões

| Perfil técnico | Descrição |
|-------------------|-------------|
| *SM-Noop* | |
| *SM-AAD* | |
| *SM-SocialSignup* | Nome do perfil está sendo usado para desambiguar a sessão AAD entre o início de sessão se e iniciar sessão |
| *SM-SocialLogin* | |
| *SM-MFA* | |

### <a name="technical-profiles-for-the-trust-framework-policy-engine"></a>Perfis técnicos para o motor de política de estrutura de confiança

Atualmente, não existem perfis técnicos são definidas para o **os TechnicalProfiles do motor de política Trustframework** fornecedor de afirmações.

### <a name="technical-profiles-for-token-issuer"></a>Perfis técnicos para o emissor de tokens

| Perfil técnico | Descrição |
|-------------------|-------------|
| *JwtIssuer* | |

## <a name="user-journeys"></a>Jornadas de utilizador

Esta secção descreve as jornadas de utilizador já declaradas no *B2C_1A_base* política. Estes jornadas de utilizador são suscetíveis a ser ainda mais referenciada, substituído, e/ou estendido conforme necessário em suas próprias políticas, bem como na *B2C_1A_base_extensions* política.

| Percurso do utilizador | Descrição |
|--------------|-------------|
| *SignUp* | |
| *SignIn* | |
| *SignUpOrSignIn* | |
| *EditProfile* | |
| *PasswordReset* | |
