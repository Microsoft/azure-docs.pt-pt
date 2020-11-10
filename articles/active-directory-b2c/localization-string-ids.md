---
title: IDs de cadeia de localização - Azure Ative Directory B2C / Microsoft Docs
description: Especifique os IDs para uma definição de conteúdo com um ID de api.signuporsignin em uma política personalizada em Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 11/09/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 45357092784bd9c8821a81b07ce3c381c4ce7989
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410509"
---
# <a name="localization-string-ids"></a>IDs de cadeia de localização

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O elemento **Localização** permite-lhe suportar vários locais ou idiomas na política para as viagens do utilizador. Este artigo fornece a lista de IDs de localização que pode usar na sua política. Para se familiarizar com a localização da UI, consulte [a Localização.](localization.md)

## <a name="sign-up-or-sign-in-page-elements"></a>Elementos de página de inscrição ou inscrição

Os iDs seguintes são usados para uma definição de conteúdo com um ID `api.signuporsignin` de, e [perfil técnico autoafirmado](self-asserted-technical-profile.md).

| ID | Valor predefinido |
| -- | ------------- |
| **local_intro_email** | Inscreva-se com a sua conta existente |
| **logonIdentifier_email** | Endereço de E-mail |
| **requiredField_email** | Por favor, insira o seu e-mail |
| **invalid_email** | Por favor, insira um endereço de e-mail válido |
| **email_pattern** | ^[a-zA-Z0-9.! #$%&'' *+/=?^_ \` \| {____+@[a-zA-Z0-9-]++.:: \\ .[ a-zA-Z0-9-]+)*$ |
| **local_intro_username** | Inscreva-se com o seu nome de utilizador |
| **logonIdentifier_username** | Nome de utilizador |
| **requiredField_username** | Por favor, insira o seu nome de utilizador |
| **palavra-passe** | Palavra-passe |
| **requiredField_password** | Por favor, insira a sua senha |
| **invalid_password** | A palavra-passe que introduziu não está no formato esperado. |
| **forgotpassword_link** | Forgot your password? |
| **createaccount_intro** | Não tem conta? |
| **createaccount_link** | Inscreva-se já |
| **divider_title** | OR |
| **cancel_message** | O utilizador esqueceu-se da sua senha |
| **button_signin** | Iniciar sessão |
| **social_intro** | Inscreva-se na sua conta social |
  **remember_me** |Mantenha-me inscrito. |
| **unknown_error** | Estamos com dificuldades em contratar-te. Tente novamente mais tarde. |

O exemplo a seguir mostra a utilização de alguns dos elementos da interface do utilizador na página de inscrição ou de inscrição:

![Elementos UX de página de inscrição ou inscrição](./media/localization-string-ids/localization-susi.png)


### <a name="sign-up-or-sign-in-identity-providers"></a>Prestadores de serviços de inscrição ou de inscrição

O ID dos fornecedores de identidade está configurado no elemento de viagem do utilizador **ClaimsExchange.** Para localizar o título do fornecedor de identidade, o **ElementType** está definido para `ClaimsProvider` , enquanto o **StringId** é definido para o ID do `ClaimsExchange` .

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

O exemplo a seguir coloca o fornecedor de identidade do Facebook em árabe:

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

### <a name="sign-up-or-sign-in-error-messages"></a>Mensagens de erro de inscrição ou de inscrição

| ID | Valor predefinido |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | A sua palavra-passe está errada. |
| **UserMessageIfPasswordExpired**| A sua senha expirou.|
| **UserMessageIfClaimsPrincipalDoesNotExist** | Não conseguimos encontrar a sua conta. |
| **UserMessageIfOldPasswordUsed** | Parece que usaste uma senha antiga. |
| **Padrão Desmesagem** | Nome de utilizador ou senha inválido. |
| **UserMessageIfUserAccountDisabled** | A sua conta está bloqueada. Contacte a sua pessoa de apoio para desbloqueá-la e tente novamente. |
| **UserMessageIfUserAccountLocked** | A sua conta está temporariamente bloqueada para evitar uma utilização não autorizada. Tente novamente mais tarde. |
| **AADRequestsThrottled** | Há muitos pedidos neste momento. Por favor, espere um pouco e tente de novo. |

### <a name="sign-up-or-sign-in-example"></a>Exemplo de inscrição ou inscrição

```xml
<LocalizedResources Id="api.signuporsignin.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">Email Address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_email">Please enter your email</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">Username</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="password">Password</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_link">Sign up now</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_username">Please enter your user name</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_intro">Don't have an account?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">Forgot your password?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="divider_title">OR</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="cancel_message">The user has forgotten their password</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_signin">Sign in</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="social_intro">Sign in with your social account</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_password">Please enter your password</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_password">The password you entered is not in the expected format.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_intro_username">Sign in with your user name</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_intro_email">Sign in with your existing account</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_email">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="unknown_error">We are having trouble signing you in. Please try again later.</LocalizedString>
    <!-- Uncomment the remember_me only if the keep me signed in is activated. 
    <LocalizedString ElementType="UxElement" StringId="remember_me">Keep me signed in</LocalizedString> -->
    <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;’'+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)$</LocalizedString>
    <LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfPasswordExpired">Your password has expired.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">Looks like you used an old password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">Invalid username or password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Elementos de interface de utilizador de páginas de inscrição e autoafirmação

Seguem-se os IDs para uma definição de conteúdo com identificação ou qualquer definição de `api.localaccountsignup` conteúdo que comece `api.selfasserted` por, por `api.selfasserted.profileupdate` `api.localaccountpasswordreset` exemplo, e pelo perfil técnico [autoafirmado.](self-asserted-technical-profile.md)

| ID | Valor predefinido |
| -- | ------------- |
| **ver_sent** | O código de verificação foi enviado para: |
| **ver_but_default** | Predefinição |
| **cancel_message** | O utilizador cancelou a introdução de informações autoafirmadas |
| **preloader_alt** | Aguarde |
| **ver_but_send** | Enviar código de verificação |
| **alert_yes** | Yes |
| **error_fieldIncorrect** | Um ou mais campos são preenchidos incorretamente. Por favor, verifiquem as suas entradas e tentem novamente. |
| **ano** | Ano |
| **verifying_blurb** | Por favor, aguarde enquanto processamos a sua informação. |
| **button_cancel** | Cancelar |
| **ver_fail_no_retry** | Fizeste muitas tentativas incorretas. Tente novamente mais tarde. |
| **mês** | Mensal |
| **ver_success_msg** | Endereço de e-mail verificado. Agora pode continuar. |
| **months** | Janeiro, fevereiro, março, abril, maio, junho, julho, agosto, setembro, outubro, novembro, dezembro |
| **ver_fail_server** | Estamos com dificuldades em verificar o seu endereço de e-mail. Por favor, insira um endereço de e-mail válido e tente novamente. |
| **error_requiredFieldMissing** | Falta um campo necessário. Por favor preencha todos os campos necessários e tente novamente. |
| **initial_intro** | Por favor, forneça os seguintes detalhes. |
| **ver_but_resend** | Enviar novo código |
| **button_continue** | Criar |
| **error_passwordEntryMismatch** | Os campos de entrada de senha não correspondem. Introduza a mesma palavra-passe em ambos os campos e tente novamente. |
| **ver_incorrect_format** | Formato incorreto. |
| **ver_but_edit** | Alterar e-mail |
| **ver_but_verify** | Verificar código |
| **alert_no** | No |
| **ver_info_msg** | O código de verificação foi enviado para a sua caixa de entrada. Por favor, copie-o para a caixa de entrada abaixo. |
| **dia** | Dia |
| **ver_fail_throttled** | Houve muitos pedidos para verificar este endereço de e-mail. Por favor, espere um pouco, e tente de novo. |
| **helplink_text** | O que é isto? |
| **ver_fail_retry** | Este código está incorreto. Tente novamente. |
| **alert_title** | Cancelar Insira os seus dados |
| **required_field** | Esta informação é necessária. |
| **alert_message** | Tem a certeza de que pretende cancelar a introdução dos seus dados? |
| **ver_intro_msg** | A verificação é necessária. Clique no botão Enviar por favor. |
| **ver_input** | Código de verificação |

### <a name="sign-up-and-self-asserted-pages-error-messages"></a>Inscrições e mensagens de erro de páginas autoafirmadas

| ID | Valor predefinido |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Um utilizador com o ID especificado já existe. Por favor, escolha um diferente. |
| **UserMessageIfClaimNotVerified** | Reclamação não verificada: {0} |
| **UserMessageIfIncorrectPattern** | Padrão incorreto para: {0} |
| **UserMessageIfMissingRequiredElement** | Elemento necessário em falta: {0} |
| **UserMessageIfValidationError** | Erro de validação por: {0} |
| **UserMessageIfInvalidInput** | {0} tem entrada inválida. |
| **ServiceThrottled** | Há muitos pedidos neste momento. Por favor, espere um pouco e tente de novo. |

O exemplo a seguir mostra a utilização de alguns dos elementos da interface do utilizador na página de inscrição:

![Página de inscrição com os nomes dos elementos de UI rotulados](./media/localization-string-ids/localization-sign-up.png)

O exemplo a seguir mostra a utilização de alguns dos elementos de interface do utilizador na página de inscrição, depois de o utilizador clicar no botão de código de verificação de envio:

![Elementos UX de verificação de página de inscrição](./media/localization-string-ids/localization-email-verification.png)

## <a name="sign-up-and-self-asserted-pages-example"></a>Exemplo de páginas de inscrição e autoafirmação

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email Address</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Email address that can be used to contact you.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">Enter new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">Confirm New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">Confirm new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">Display Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">Your display name.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">Surname</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">Your surname (also known as family name or last name).</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">Given Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">Your given name (also known as first name).</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">A required field is missing. Please fill out all required fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="helplink_text">What is this?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please provide the following details.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="preloader_alt">Please wait</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="required_field">This information is required.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_edit">Change e-mail</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_resend">Send new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_send">Send verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_verify">Verify code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">That code is incorrect. Please try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_server">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_info_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_input">Verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_success_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">Claim not verified: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">A user with the specified ID already exists. Please choose a different one.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">Incorrect pattern for: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">{0} has invalid input.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">Missing required element: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">Error in validation by: {0}</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="phone-factor-authentication-page-user-interface-elements"></a>Elementos de interface de utilizador de página de autenticação de fator de telefone

Seguem-se os IDs para uma definição de conteúdo com identificação `api.phonefactor` de , e perfil técnico do fator [telefónico](phone-factor-technical-profile.md).

| ID | Valor predefinido |
| -- | ------------- |
| **button_verify** | Liga-me |
| **country_code_label** | Código do País |
| **cancel_message** | O utilizador cancelou a autenticação de vários fatores |
| **text_button_send_second_code** | enviar um novo código |
| **code_pattern** | \\d{6} |
| **intro_mixed** | Temos o seguinte número registado para si. Podemos enviar um código via SMS ou telefone para autenticá-lo. |
| **intro_mixed_p** | Temos os seguintes números registados para si. Escolha um número que possamos telefonar ou envie um código via SMS para autenticá-lo. |
| **button_verify_code** | Verificar Código |
| **requiredField_code** | Introduza o código de verificação que recebeu |
| **invalid_code** | Introduza o código de 6 dígitos que recebeu |
| **button_cancel** | Cancelar |
| **local_number_input_placeholder_text** | Número de telefone |
| **button_retry** | Tentar novamente |
| **alternative_text** | Não tenho o meu telemóvel. |
| **intro_phone_p** | Temos os seguintes números registados para si. Escolha um número que podemos telefonar para autenticá-lo. |
| **intro_phone** | Temos o seguinte número registado para si. Vamos telefonar para autenticá-lo. |
| **enter_code_text_intro** | Introduza o seu código de verificação abaixo, ou  |
| **intro_entry_phone** | Introduza um número abaixo que podemos telefonar para autenticá-lo. |
| **intro_entry_sms** | Introduza um número abaixo que podemos enviar um código via SMS para autenticá-lo. |
| **button_send_code** | Enviar Código |
| **invalid_number** | Introduza um número de telefone válido |
| **intro_sms** | Temos o seguinte número registado para si. Enviaremos um código via SMS para autenticá-lo. |
| **intro_entry_mixed** | Introduza um número abaixo que podemos enviar um código via SMS ou telefone para autenticá-lo. |
| **number_pattern** | ^\\+?:[0-9][x20-]?) \\ {6,14} [0-9]$ |
| **intro_sms_p** |Temos os seguintes números registados para si. Escolha um número que possamos enviar um código via SMS para autenticá-lo. |
| **requiredField_countryCode** | Por favor, selecione o seu código de país |
| **requiredField_number** | Por favor, insira o seu número de telefone |
| **country_code_input_placeholder_text** |País ou região |
| **number_label** | Número de Telefone |
| **error_tryagain** | O número de telefone que forneceu está ocupado ou indisponível. Por favor, verifique o número e tente de novo. |
| **error_incorrect_code** | O código de verificação que inseriu não corresponde aos nossos registos. Por favor, tente de novo, ou peça um novo código. |
| **paísLista** | Consulte [a lista dos países.](#phone-factor-authentication-page-example) |
| **error_448** | O número de telefone que forneceu é inacessível. |
| **error_449** | O utilizador excedeu o número de tentativas de repetição. |
| **verification_code_input_placeholder_text** | Código de verificação |

O exemplo a seguir mostra a utilização de alguns dos elementos da interface do utilizador na página de inscrição do MFA:

![Elementos UX de autenticação de fator telefónico](./media/localization-string-ids/localization-mfa1.png)

O exemplo a seguir mostra a utilização de alguns dos elementos de interface de utilizador na página de validação do MFA:

![Elementos UX de validação de fator de telefone](./media/localization-string-ids/localization-mfa2.png)

## <a name="phone-factor-authentication-page-example"></a>Exemplo de página de autenticação de fator de telefone

```xml
<LocalizedResources Id="api.phonefactor.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="button_verify">Call Me</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_label">Country Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="cancel_message">The user has canceled multi-factor authentication</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="text_button_send_second_code">Send a new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="code_pattern">\d{6}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed">We have the following number on record for you. We can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed_p">We have the following numbers on record for you. Choose a number that we can phone or send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_code">Please enter the verification code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_code">Please enter the 6-digit code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_number_input_placeholder_text">Phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_retry">Retry</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="alternative_text">I don't have my phone</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone_p">We have the following numbers on record for you. Choose a number that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone">We have the following number on record for you. We will phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="enter_code_text_intro">Enter your verification code below, or</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_phone">Enter a number below that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_sms">Enter a number below that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_number">Please enter a valid phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms">We have the following number on record for you. We will send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_mixed">Enter a number below that we can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_pattern">^\+(?:[0-9][\x20-]?){6,14}[0-9]$</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms_p">We have the following numbers on record for you. Choose a number that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_countryCode">Please select your country code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_number">Please enter your phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_input_placeholder_text">Country or region</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_label">Phone Number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_tryagain">The phone number you provided is busy or unavailable. Please check the number and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_incorrect_code">The verification code you have entered does not match our records. Please try again, or request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="countryList">{"DEFAULT":"Country/Region","AF":"Afghanistan","AX":"Åland Islands","AL":"Albania","DZ":"Algeria","AS":"American Samoa","AD":"Andorra","AO":"Angola","AI":"Anguilla","AQ":"Antarctica","AG":"Antigua and Barbuda","AR":"Argentina","AM":"Armenia","AW":"Aruba","AU":"Australia","AT":"Austria","AZ":"Azerbaijan","BS":"Bahamas","BH":"Bahrain","BD":"Bangladesh","BB":"Barbados","BY":"Belarus","BE":"Belgium","BZ":"Belize","BJ":"Benin","BM":"Bermuda","BT":"Bhutan","BO":"Bolivia","BQ":"Bonaire","BA":"Bosnia and Herzegovina","BW":"Botswana","BV":"Bouvet Island","BR":"Brazil","IO":"British Indian Ocean Territory","VG":"British Virgin Islands","BN":"Brunei","BG":"Bulgaria","BF":"Burkina Faso","BI":"Burundi","CV":"Cabo Verde","KH":"Cambodia","CM":"Cameroon","CA":"Canada","KY":"Cayman Islands","CF":"Central African Republic","TD":"Chad","CL":"Chile","CN":"China","CX":"Christmas Island","CC":"Cocos (Keeling) Islands","CO":"Colombia","KM":"Comoros","CG":"Congo","CD":"Congo (DRC)","CK":"Cook Islands","CR":"Costa Rica","CI":"Côte d'Ivoire","HR":"Croatia","CU":"Cuba","CW":"Curaçao","CY":"Cyprus","CZ":"Czech Republic","DK":"Denmark","DJ":"Djibouti","DM":"Dominica","DO":"Dominican Republic","EC":"Ecuador","EG":"Egypt","SV":"El Salvador","GQ":"Equatorial Guinea","ER":"Eritrea","EE":"Estonia","ET":"Ethiopia","FK":"Falkland Islands","FO":"Faroe Islands","FJ":"Fiji","FI":"Finland","FR":"France","GF":"French Guiana","PF":"French Polynesia","TF":"French Southern Territories","GA":"Gabon","GM":"Gambia","GE":"Georgia","DE":"Germany","GH":"Ghana","GI":"Gibraltar","GR":"Greece","GL":"Greenland","GD":"Grenada","GP":"Guadeloupe","GU":"Guam","GT":"Guatemala","GG":"Guernsey","GN":"Guinea","GW":"Guinea-Bissau","GY":"Guyana","HT":"Haiti","HM":"Heard Island and McDonald Islands","HN":"Honduras","HK":"Hong Kong SAR","HU":"Hungary","IS":"Iceland","IN":"India","ID":"Indonesia","IR":"Iran","IQ":"Iraq","IE":"Ireland","IM":"Isle of Man","IL":"Israel","IT":"Italy","JM":"Jamaica","JP":"Japan","JE":"Jersey","JO":"Jordan","KZ":"Kazakhstan","KE":"Kenya","KI":"Kiribati","KR":"Korea","KW":"Kuwait","KG":"Kyrgyzstan","LA":"Laos","LV":"Latvia","LB":"Lebanon","LS":"Lesotho","LR":"Liberia","LY":"Libya","LI":"Liechtenstein","LT":"Lithuania","LU":"Luxembourg","MO":"Macao SAR","MK":"North Macedonia","MG":"Madagascar","MW":"Malawi","MY":"Malaysia","MV":"Maldives","ML":"Mali","MT":"Malta","MH":"Marshall Islands","MQ":"Martinique","MR":"Mauritania","MU":"Mauritius","YT":"Mayotte","MX":"Mexico","FM":"Micronesia","MD":"Moldova","MC":"Monaco","MN":"Mongolia","ME":"Montenegro","MS":"Montserrat","MA":"Morocco","MZ":"Mozambique","MM":"Myanmar","NA":"Namibia","NR":"Nauru","NP":"Nepal","NL":"Netherlands","NC":"New Caledonia","NZ":"New Zealand","NI":"Nicaragua","NE":"Niger","NG":"Nigeria","NU":"Niue","NF":"Norfolk Island","KP":"North Korea","MP":"Northern Mariana Islands","NO":"Norway","OM":"Oman","PK":"Pakistan","PW":"Palau","PS":"Palestinian Authority","PA":"Panama","PG":"Papua New Guinea","PY":"Paraguay","PE":"Peru","PH":"Philippines","PN":"Pitcairn Islands","PL":"Poland","PT":"Portugal","PR":"Puerto Rico","QA":"Qatar","RE":"Réunion","RO":"Romania","RU":"Russia","RW":"Rwanda","BL":"Saint Barthélemy","KN":"Saint Kitts and Nevis","LC":"Saint Lucia","MF":"Saint Martin","PM":"Saint Pierre and Miquelon","VC":"Saint Vincent and the Grenadines","WS":"Samoa","SM":"San Marino","ST":"São Tomé and Príncipe","SA":"Saudi Arabia","SN":"Senegal","RS":"Serbia","SC":"Seychelles","SL":"Sierra Leone","SG":"Singapore","SX":"Sint Maarten","SK":"Slovakia","SI":"Slovenia","SB":"Solomon Islands","SO":"Somalia","ZA":"South Africa","GS":"South Georgia and South Sandwich Islands","SS":"South Sudan","ES":"Spain","LK":"Sri Lanka","SH":"St Helena, Ascension, Tristan da Cunha","SD":"Sudan","SR":"Suriname","SJ":"Svalbard","SZ":"Swaziland","SE":"Sweden","CH":"Switzerland","SY":"Syria","TW":"Taiwan","TJ":"Tajikistan","TZ":"Tanzania","TH":"Thailand","TL":"Timor-Leste","TG":"Togo","TK":"Tokelau","TO":"Tonga","TT":"Trinidad and Tobago","TN":"Tunisia","TR":"Turkey","TM":"Turkmenistan","TC":"Turks and Caicos Islands","TV":"Tuvalu","UM":"U.S. Outlying Islands","VI":"U.S. Virgin Islands","UG":"Uganda","UA":"Ukraine","AE":"United Arab Emirates","GB":"United Kingdom","US":"United States","UY":"Uruguay","UZ":"Uzbekistan","VU":"Vanuatu","VA":"Vatican City","VE":"Venezuela","VN":"Vietnam","WF":"Wallis and Futuna","YE":"Yemen","ZM":"Zambia","ZW":"Zimbabwe"}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_448">The phone number you provided is unreachable.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_449">User has exceeded the number of retry attempts.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_code_input_placeholder_text">Verification code</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="strongAuthenticationPhoneNumber" StringId="DisplayName">Phone Number</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>

```

## <a name="verification-display-control-user-interface-elements"></a>Elementos de interface do utilizador do controlo do ecrã de verificação

Seguem-se os IDs para um [controlo de visualização de verificação](display-control-verification.md) com [a versão de layout](page-layout.md) da página 2.1.0 ou superior.

| ID | Valor predefinido |
| -- | ------------- |
|intro_msg| A verificação é necessária. Clique no botão Enviar por favor.|
|success_send_code_msg | O código de verificação foi enviado para a sua caixa de entrada. Por favor, copie-o para a caixa de entrada abaixo.|
|failure_send_code_msg | Estamos com dificuldades em verificar o seu endereço de e-mail. Por favor, insira um endereço de e-mail válido e tente novamente.|
|success_verify_code_msg | Endereço de e-mail verificado. Agora pode continuar.|
|failure_verify_code_msg | Estamos com dificuldades em verificar o seu endereço de e-mail. Tente novamente.|
|but_send_code | Enviar código de verificação|
|but_verify_code | Verificar código|
|but_send_new_code | Enviar novo código|
|but_change_claims | Alterar e-mail|

### <a name="verification-display-control-example"></a>Exemplo de controlo de visualização de verificação

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
   <!-- Display control UI elements-->
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_send_code_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_send_code_msg">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_verify_code_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_verify_code_msg">We are having trouble verifying your email address. Please try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_new_code">Send new code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_change_claims">Change e-mail</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="verification-display-control-user-interface-elements-deprecated"></a>Elementos de interface do utilizador do controlo do ecrã de verificação (precotados)

Seguem-se os IDs para um [controlo de visualização de verificação](display-control-verification.md) com [a versão layout](page-layout.md) da página 2.0.0.

| ID | Valor predefinido |
| -- | ------------- |
|verification_control_but_change_claims |Alterar |
|verification_control_fail_send_code |Não conseguiu enviar o código, por favor tente novamente mais tarde. |
|verification_control_fail_verify_code |Não conseguiu verificar o código, por favor tente novamente mais tarde. |
|verification_control_but_send_code |Enviar Código |
|verification_control_but_send_new_code |Enviar Novo Código |
|verification_control_but_verify_code |Verificar Código |
|verification_control_code_sent| O código de verificação foi enviado. Por favor, copie-o para a caixa de entrada abaixo. |

### <a name="verification-display-control-example-deprecated"></a>Exemplo de controlo do ecrã de verificação (precotado)

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_change_claims">Change</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_send_code">Failed to send the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_verify_code">Failed to verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_new_code">Send New Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_code_sent">Verification code has been sent. Please copy it to the input box below.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="restful-service-error-messages"></a>Mensagens de erro de serviço repousante

Seguem-se os IDs para mensagens de erro [de perfil técnico do serviço Restful:](restful-technical-profile.md)

| ID | Valor predefinido |
| -- | ------------- |
|DefaultUserMessageIfRequestFailed | Não conseguiu estabelecer a ligação ao ponto final de serviço repousante. URL de serviço repousante: {0} |
|UserMessageIfCircuitOpen | {0} URL de serviço repousante: {1} |
|UserMessageIfDnsResolutionFailed | Não conseguiu resolver o nome de anfitrião do ponto final de serviço repousante. URL de serviço repousante: {0} |
|UserMessageIfRequestTimeout | Não conseguiu estabelecer a ligação ao ponto final de serviço repousante dentro dos segundos limite {0} de tempo. URL de serviço repousante: {1} |


### <a name="restful-service-example"></a>Exemplo de serviço repousante

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultUserMessageIfRequestFailed">Failed to establish connection to restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCircuitOpen">Unable to connect to the restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfDnsResolutionFailed">Failed to resolve the hostname of the restful service endpoint.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfRequestTimeout">Failed to establish connection to restful service end point within timeout limit.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-mfa-error-messages"></a>Mensagens de erro do Azure MFA

Seguem-se os IDs para uma [azure MFA mensagens de](multi-factor-auth-technical-profile.md) erro de perfil técnico:

| ID | Valor predefinido |
| -- | ------------- |
|UserMessageIfCodntSendSms | Não é possível enviar SMS para o telefone, por favor tente outro número de telefone. |
|UserMessageIfInvalidFormat | O seu número de telefone não está num formato válido, por favor corrija-o e tente novamente.|
|UserMessageIfMaxAllowedCodeRetryReached | Código errado introduzido demasiadas vezes, por favor tente novamente mais tarde.|
|UserMessageIfServerError | Não pode utilizar o serviço MFA, tente novamente mais tarde.|
|UserMessageIfThrottled | O seu pedido foi acelerado, por favor tente de novo mais tarde.|
|UserMessageIfWrongCodeEntered|Código errado introduzido, por favor tente de novo.|

### <a name="azure-mfa-example"></a>Exemplo de Azure MFA

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCouldntSendSms">Cannot Send SMS to the phone, please try another phone number.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidFormat">Your phone number is not in a valid format, please correct it and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxAllowedCodeRetryReached">Wrong code entered too many times, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfServerError">Cannot use MFA service, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">Your request has been throttled, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfWrongCodeEntered">Wrong code entered, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-sspr"></a>SSPR do Azure AD

Seguem-se os IDs para as mensagens de erro [de perfil técnico da Azure AD SSPR:](aad-sspr-technical-profile.md)

| ID | Valor predefinido |
| -- | ------------- |
|UserMessageIfChallengeExpired | O código expirou.|
|UserMessageIfInternalError | O serviço de e-mail encontrou um erro interno, por favor tente novamente mais tarde.|
|UserMessageIfThrottled | Enviou muitos pedidos, por favor tente de novo mais tarde.|
|UserMessageIfVerificationFailedNoRetry | Excedeu o número máximo de tentativas de verificação.|
|UserMessageIfVerificationFailedRetryAllowed | A verificação falhou, por favor tente de novo.|


### <a name="azure-ad-sspr-example"></a>Exemplo Azure AD SSPR

```XML
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInternalError">We are having trouble verifying your email address. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfChallengeExpired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedNoRetry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="one-time-password-error-messages"></a>Mensagens de erro de senha de uma vez

Seguem-se os IDs para [uma única palavra-passe de](one-time-password-technical-profile.md) mensagens de erro de perfil técnica

| ID | Valor predefinido |
| -- | ------------- |
|UserMessageIfMaxRetryAttempted |Uma vez a palavra-passe fornecida excedeu o número máximo de tentativas |
|UserMessageIfSessionDoesNotExist |Uma vez a sessão de verificação da palavra-passe expirou |
|UserMessageIfSessionConflict |Uma vez a sessão de verificação de senhas tem conflito |
|UserMessageIfInvalidCode |Uma palavra-passe de uma vez fornecida para verificação é incorreta |
|UserMessageIfVerificationFailedRetryAllowed |Este código está incorreto. Tente novamente. | 

### <a name="one-time-password-example"></a>Exemplo de senha de uma vez

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="claims-transformations-error-messages"></a>Reclama mensagens de erro de transformações

Seguem-se os IDs para as mensagens de erro de transformação de reclamações:

| ID | Transformação de afirmações | Valor predefinido |
| -- | ------------- |------------- |
|UserMessageIfClaimsTransformationBooleanValueIsNotEqual |[AssertBooleanClaimIsEqualToValue](boolean-transformations.md#assertbooleanclaimisequaltovalue) | A comparação do valor da alegação booleana falhou para o tipo de reclamação "inputClaim".| 
|DateTimeGreaterThan |[AssertDateTimeIsGreaterThan](date-transformations.md#assertdatetimeisgreaterthan) | A comparação de valor de reivindicação falhou: A ópera à esquerda fornecida é maior do que a ópera certa.|
|UserMessageIfClaimsTransformationStringsAreNotEqual |[AssertStringClaimsAreEqual](string-transformations.md#assertstringclaimsareequal) | A comparação de valor de reclamação falhou usando StringComparison "OrdinalIgnoreCase".|

### <a name="claims-transformations-example"></a>Exemplo de transformações de reclamações

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Your email address hasn't been verified.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DateTimeGreaterThan">Expiration date must be greater that the current date.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationStringsAreNotEqual">The email entry fields do not match. Please enter the same email address in both fields and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para exemplos de localização:

- [Personalização linguística com política personalizada no Azure Ative Directory B2C](custom-policy-localization.md)
- [Personalização linguística com fluxos de utilizadores no Azure Ative Directory B2C](user-flow-language-customization.md)
