---
title: Componentes do pacote de início de política personalizada
titleSuffix: Azure AD B2C
description: Uma visão geral das políticas no Azure Active Directory B2C pacote de início de política personalizada.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7d49bd9af80b1bb9bd86466269b14ba0a47181e0
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948186"
---
# <a name="understanding-the-custom-policies-of-the-azure-ad-b2c-custom-policy-starter-pack"></a>Noções básicas sobre as políticas personalizadas do Azure AD B2C pacote de início de política personalizada

Esta seção lista todos os elementos principais da política de B2C_1A_base que acompanha o **pacote inicial** e que é utilizado para criar suas próprias políticas por meio da herança da *política de B2C_1A_base_extensions*.

Assim, ele se concentra mais principalmente nos tipos de declaração já definidos, nas transformações de declarações, nas definições de conteúdo, nos provedores de declarações com seus perfis técnicos e nos principais percursos do usuário.

> [!IMPORTANT]
> A Microsoft não oferece nenhuma garantia, expressa ou implícita, em relação às informações fornecidas daqui em diante. As alterações podem ser introduzidas a qualquer momento, antes do horário de GA, no horário de GA ou depois.

Suas próprias políticas e a política de B2C_1A_base_extensions podem substituir essas definições e estender essa política pai fornecendo as outras, conforme necessário.

Os elementos principais da *política de B2C_1A_base* são tipos de declaração, transformações de declarações e definições de conteúdo. Esses elementos podem ser suscetíveis a serem referenciados em suas próprias políticas, bem como na *política de B2C_1A_base_extensions*.

## <a name="claims-schemas"></a>Esquemas de declarações

Esses esquemas de declarações são divididos em três seções:

1.  Uma primeira seção que lista as declarações mínimas necessárias para que os percursos do usuário funcionem corretamente.
2.  Uma segunda seção que lista as declarações necessárias para parâmetros de cadeia de caracteres de consulta e outros parâmetros especiais a serem passados para outros provedores de declarações, especialmente login.microsoftonline.com para autenticação. **Não modifique essas declarações**.
3.  E, eventualmente, uma terceira seção que lista quaisquer declarações adicionais opcionais que podem ser coletadas do usuário, armazenadas no diretório e enviadas em tokens durante a entrada. Novos tipos de declarações a serem coletadas do usuário e/ou enviadas no token podem ser adicionados nesta seção.

> [!IMPORTANT]
> O esquema de declarações contém restrições em determinadas declarações, como senhas e nomes de acessações. A política de TF (estrutura confiável) trata o Azure AD como qualquer outro provedor de declarações e todas as suas restrições são modeladas na política personalizada. Uma política pode ser modificada para adicionar mais restrições ou usar outro provedor de declarações para o armazenamento de credenciais que terá suas próprias restrições.

Os tipos de declaração disponíveis estão listados abaixo.

### <a name="claims-that-are-required-for-the-user-journeys"></a>Declarações que são necessárias para os percursos do usuário

As declarações a seguir são necessárias para que os trajetos do usuário funcionem corretamente:

| Tipo de declarações | Descrição |
|-------------|-------------|
| *UserId* | Nome de utilizador |
| *signInName* | Nome de entrada |
| *tenantId* | Identificador do locatário (ID) do objeto de usuário no Azure AD B2C |
| *objectId* | Identificador de objeto (ID) do objeto de usuário no Azure AD B2C |
| *password* | Palavra-passe |
| *newPassword* | |
| *reenterPassword* | |
| *passwordPolicies* | Políticas de senha usadas por Azure AD B2C para determinar a força da senha, expiração, etc. |
| *sub* | |
| *alternativeSecurityId* | |
| *identityProvider* | |
| *displayName* | |
| *strongAuthenticationPhoneNumber* | Número de telefone |
| *Verified.strongAuthenticationPhoneNumber* | |
| *email* | Endereço de email que pode ser usado para contatar o usuário |
| *signInNamesInfo.emailAddress* | Endereço de email que o usuário pode usar para entrar |
| *otherMails* | Endereços de email que podem ser usados para contatar o usuário |
| *userPrincipalName* | Nome de usuário como armazenado no Azure AD B2C |
| *upnUserName* | Nome de usuário para criar o nome UPN |
| *mailNickName* | Nome do Nick de email do usuário, conforme armazenado no Azure AD B2C |
| *newUser* | |
| *executed-SelfAsserted-Input* | Declaração que especifica se os atributos foram coletados do usuário |
| *executed-PhoneFactor-Input* | Declaração que especifica se um novo número de telefone foi coletado do usuário |
| *authenticationSource* | Especifica se o usuário foi autenticado no provedor de identidade social, login.microsoftonline.com ou conta local |

### <a name="claims-required-for-query-string-parameters-and-other-special-parameters"></a>Declarações necessárias para parâmetros de cadeia de caracteres de consulta e outros parâmetros especiais

As declarações a seguir são necessárias para passar parâmetros especiais (incluindo alguns parâmetros de cadeia de caracteres de consulta) para outros provedores de declarações:

| Tipo de declarações | Descrição |
|-------------|-------------|
| *nux* | Parâmetro especial passado para autenticação de conta local para login.microsoftonline.com |
| *nca* | Parâmetro especial passado para autenticação de conta local para login.microsoftonline.com |
| *prompt* | Parâmetro especial passado para autenticação de conta local para login.microsoftonline.com |
| *mkt* | Parâmetro especial passado para autenticação de conta local para login.microsoftonline.com |
| *lc* | Parâmetro especial passado para autenticação de conta local para login.microsoftonline.com |
| *grant_type* | Parâmetro especial passado para autenticação de conta local para login.microsoftonline.com |
| *scope* | Parâmetro especial passado para autenticação de conta local para login.microsoftonline.com |
| *client_id* | Parâmetro especial passado para autenticação de conta local para login.microsoftonline.com |
| *objectIdFromSession* | Parâmetro fornecido pelo provedor de gerenciamento de sessão padrão para indicar que a ID de objeto foi recuperada de uma sessão de SSO |
| *isActiveMFASession* | Parâmetro fornecido pelo gerenciamento de sessão de MFA para indicar que o usuário tem uma sessão de MFA ativa |

### <a name="additional-optional-claims-that-can-be-collected"></a>Declarações adicionais (opcionais) que podem ser coletadas

As declarações a seguir são declarações adicionais que podem ser coletadas dos usuários, armazenadas no diretório e enviadas no token. Conforme descrito antes, declarações adicionais podem ser adicionadas a essa lista.

| Tipo de declarações | Descrição |
|-------------|-------------|
| *givenName* | Nome do usuário (também conhecido como primeiro nome) |
| *surname* | Sobrenome do usuário (também conhecido como nome de família ou sobrenome) |
| *Extension_picture* | Imagem do usuário do social |

## <a name="claim-transformations"></a>Transformações de declaração

As transformações de declaração disponíveis estão listadas abaixo.

| Transformação de declaração | Descrição |
|----------------------|-------------|
| *CreateOtherMailsFromEmail* | |
| *CreateRandomUPNUserName* | |
| *CreateUserPrincipalName* | |
| *CreateSubjectClaimFromObjectID* | |
| *CreateSubjectClaimFromAlternativeSecurityId* | |
| *CreateAlternativeSecurityId* | |

## <a name="content-definitions"></a>Definições de conteúdo

Esta seção descreve as definições de conteúdo já declaradas na política de *B2C_1A_base* . Essas definições de conteúdo são suscetíveis a serem referenciadas, substituídas e/ou estendidas conforme necessário em suas próprias políticas, bem como na política de *B2C_1A_base_extensions* .

| Fornecedor de afirmações | Descrição |
|-----------------|-------------|
| *Facebook* | |
| *Entrada de conta local* | |
| *PhoneFactor* | |
| *Azure Active Directory* | |
| *Autodeclarado* | |
| *Conta local* | |
| *Gerenciamento de sessão* | |
| *Mecanismo de política Trustframework* | |
| *TechnicalProfiles* | |
| *Emissor do token* | |

## <a name="technical-profiles"></a>Perfis técnicos

Esta seção descreve os perfis técnicos já declarados por provedor de declarações na política de *B2C_1A_base* . Esses perfis técnicos são suscetíveis a serem referenciados, substituídos e/ou estendidos conforme necessário em suas próprias políticas, bem como na política de *B2C_1A_base_extensions* .

### <a name="technical-profiles-for-facebook"></a>Perfis técnicos do Facebook

| Perfil técnico | Descrição |
|-------------------|-------------|
| *Facebook-OAUTH* | |

### <a name="technical-profiles-for-local-account-signin"></a>Perfis técnicos para entrada de conta local

| Perfil técnico | Descrição |
|-------------------|-------------|
| *Login-NonInteractive* | |

### <a name="technical-profiles-for-phone-factor"></a>Perfis técnicos para o fator de telefone

| Perfil técnico | Descrição |
|-------------------|-------------|
| *PhoneFactor-Input* | |
| *PhoneFactor-InputOrVerify* | |
| *PhoneFactor-Verify* | |

### <a name="technical-profiles-for-azure-active-directory"></a>Perfis técnicos para Azure Active Directory

| Perfil técnico | Descrição |
|-------------------|-------------|
| *AAD-Common* | Perfil técnico incluído nos outros perfis técnicos do AAD-xxx |
| *AAD-UserWriteUsingAlternativeSecurityId* | Perfil técnico para logons sociais |
| *AAD-UserReadUsingAlternativeSecurityId* | Perfil técnico para logons sociais |
| *AAD-UserReadUsingAlternativeSecurityId-NoError* | Perfil técnico para logons sociais |
| *AAD-UserWritePasswordUsingLogonEmail* | Perfil técnico para contas locais |
| *AAD-UserReadUsingEmailAddress* | Perfil técnico para contas locais |
| *AAD-UserWriteProfileUsingObjectId* | Perfil técnico para atualizar o registro de usuário usando objectId |
| *AAD-UserWritePhoneNumberUsingObjectId* | Perfil técnico para atualizar o registro de usuário usando objectId |
| *AAD-UserWritePasswordUsingObjectId* | Perfil técnico para atualizar o registro de usuário usando objectId |
| *AAD-UserReadUsingObjectId* | O perfil técnico é usado para ler dados após a autenticação do usuário |

### <a name="technical-profiles-for-self-asserted"></a>Perfis técnicos para autoasserted

| Perfil técnico | Descrição |
|-------------------|-------------|
| *SelfAsserted-Social* | |
| *SelfAsserted-ProfileUpdate* | |

### <a name="technical-profiles-for-local-account"></a>Perfis técnicos para a conta local

| Perfil técnico | Descrição |
|-------------------|-------------|
| *LocalAccountSignUpWithLogonEmail* | |

### <a name="technical-profiles-for-session-management"></a>Perfis técnicos para o gerenciamento de sessão

| Perfil técnico | Descrição |
|-------------------|-------------|
| *SM-Noop* | |
| *SM-AAD* | |
| *SM-SocialSignup* | O nome do perfil está sendo usado para desambiguar a sessão do AAD entre inscrever-se e entrar |
| *SM-SocialLogin* | |
| *SM-MFA* | |

### <a name="technical-profiles-for-the-trust-framework-policy-engine"></a>Perfis técnicos para o mecanismo de diretiva de estrutura confiável

No momento, nenhum perfil técnico é definido para o provedor de declarações **TechnicalProfiles do mecanismo de política Trustframework** .

### <a name="technical-profiles-for-token-issuer"></a>Perfis técnicos do emissor do token

| Perfil técnico | Descrição |
|-------------------|-------------|
| *JwtIssuer* | |

## <a name="user-journeys"></a>Viagens do usuário

Esta seção descreve os percursos do usuário já declarados na política de *B2C_1A_base* . Esses percursos do usuário são suscetíveis a serem referenciados, substituídos e/ou estendidos conforme necessário em suas próprias políticas, bem como na política de *B2C_1A_base_extensions* .

| Jornada do usuário | Descrição |
|--------------|-------------|
| *SignUp* | |
| *SignIn* | |
| *SignUpOrSignIn* | |
| *EditProfile* | |
| *PasswordReset* | |
