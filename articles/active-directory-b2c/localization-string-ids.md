---
title: Localização de cadeias de caracteres IDs - Azure Active Directory B2C | Documentos da Microsoft
description: Especifique os IDs para uma definição de conteúdo com o Id api.signuporsignin numa política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5f937a86e4e063ad1e8346dc629a917ae7ce8b65
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58077212"
---
# <a name="localization-string-ids"></a>Cadeia de caracteres de localização IDs

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O **localização** elemento permite-lhe dar suporte a várias localidades ou idiomas na política para as jornadas de utilizador. Este artigo fornece a lista de IDs que pode usar na sua política de localização. Para se familiarizar com a localização da interface do Usuário, consulte [localização](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Elementos da página de inscrição ou início de sessão

Os seguintes Ids são utilizados para uma definição de conteúdo com o ID `api.signuporsignin`.

| ID | Valor predefinido |
| -- | ------------- |
| **local_intro_email** | Inicie sessão com a sua conta existente |
| **logonIdentifier_email** | Endereço de E-mail |
| **requiredField_email** | Introduza o seu e-mail |
| **invalid_email** | Introduza um endereço de e-mail válido |
| **email_pattern** | ^[a-zA-Z0-9.!#$%&’'*+/=?^_\`{\|}~-]+@[a-zA-Z0-9-]+(?:\\.[a-zA-Z0-9-]+)*$ |
| **local_intro_username** | Iniciar sessão com o nome de utilizador |
| **logonIdentifier_username** | Nome de utilizador |
| **requiredField_username** | Introduza o seu nome de utilizador |
| **password** | Palavra-passe |
| **requiredField_password** | Introduza a sua palavra-passe |
| **invalid_password** | A palavra-passe que introduziu não está no formato esperado. |
| **forgotpassword_link** | Esqueceu-se da palavra-passe? |
| **createaccount_intro** | Não tem uma conta? |
| **createaccount_link** | Inscreva-se já |
| **divider_title** | OU |
| **cancel_message** | O utilizador esqueceu-se da sua palavra-passe |
| **button_signin** | Iniciar sessão |
| **social_intro** | Inicie sessão com a sua conta de redes sociais |
  **remember_me** |Manter sessão iniciada|
| **unknown_error** | Estamos com dificuldades a iniciar sessão. Tente novamente mais tarde. |

O exemplo seguinte mostra o uso de alguns do utilizador elementos de interface na página de inscrição ou início de sessão:

![Elementos de experiência do Usuário da página de inscrição ou início de sessão](./media/localization-string-ids/localization-susi.png)

O ID do fornecedor de identidade é configurado no percurso do utilizador **ClaimsExchange** elemento. Para localizar o título do fornecedor de identidade, o **ElementType** está definida como `ClaimsProvider`, enquanto o **StringId** está definido para o ID do `ClaimsExchange`.

```XML
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

O exemplo seguinte localizes o fornecedor de identidade do Facebook para Árabe:

```XML
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

## <a name="sign-up-or-sign-in-error-messages"></a>Mensagens de erro de inscrição ou início de sessão

| ID | Valor predefinido |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | A palavra-passe está incorreta. |
| **UserMessageIfClaimsPrincipalDoesNotExist** | Não é possível encontrar a sua conta. |
| **UserMessageIfOldPasswordUsed** | Parece que utilizou uma palavra-passe antiga. |  
| **DefaultMessage** | Nome de utilizador ou palavra-passe inválido. |  
| **UserMessageIfUserAccountDisabled** | Sua conta foi bloqueada. Entre em contacto com seu suporte para desbloqueá-lo, em seguida, tente novamente. |  
| **UserMessageIfUserAccountLocked** | Sua conta está temporariamente bloqueada para evitar utilizações não autorizadas. Tente novamente mais tarde. |  
| **AADRequestsThrottled** | Existem demasiados pedidos neste momento. Aguarde algum tempo e tente novamente. |  

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Inscreva-se e Self-declaradas páginas elementos da interface do usuário

Seguem-se os IDs para uma definição de conteúdo com o ID `api.localaccountsignup` ou qualquer definição de conteúdo que começa por `api.selfasserted`, tal como `api.selfasserted.profileupdate` e `api.localaccountpasswordreset`.

| ID | Valor predefinido |
| -- | ------------- |
| **ver_sent** | O código de verificação foi enviado para: |
| **ver_but_default** | Predefinição |
| **cancel_message** | O utilizador cancelou a introduzir as informações de declaração própria |
| **preloader_alt** | Aguarde |
| **ver_but_send** | Enviar código de verificação |
| **alert_yes** | Sim |
| **error_fieldIncorrect** | Um ou mais campos são preenchidos incorretamente. Verifique suas entradas e tente novamente. |
| **Ano** | Ano |
| **verifying_blurb** | Aguarde enquanto processamos as suas informações. |
| **button_cancel** | Cancelar |
| **ver_fail_no_retry** | Criou demasiadas tentativas de introdução incorretas. Tente novamente mais tarde. |
| **Mês** | Mês |
| **ver_success_msg** | Endereço de correio eletrónico verificado. Agora pode continuar. |
| **months** | Janeiro, Fevereiro, Março, Abril, Maio, Junho, Julho, Agosto, Setembro, Outubro, Novembro, Dezembro |
| **ver_fail_server** | Estamos com problemas ao verificar o seu endereço de e-mail. Introduza um endereço de e-mail válido e tente novamente. |
| **error_requiredFieldMissing** | Um campo obrigatório está em falta. . Preencha todos os campos obrigatórios e tente novamente. |
| **initial_intro** | Forneça os seguintes detalhes. |
| **ver_but_resend** | Enviar novo código |
| **button_continue** | Criar |
| **error_passwordEntryMismatch** | Os campos de entrada de palavra-passe não correspondem. Introduza a mesma palavra-passe em ambos os campos e tente novamente. |
| **ver_incorrect_format** | Formato incorreto. |
| **ver_but_edit** | Alterar e-mail |
| **ver_but_verify** | Verificar código |
| **alert_no** | Não |
| **ver_info_msg** | Código de verificação foi enviado para a sua caixa de entrada. Copie-a para a caixa de entrada abaixo. |
| **day** | Dia |
| **ver_fail_throttled** | Tem havido demasiados pedidos para verificar este endereço de e-mail. Aguarde algum tempo, em seguida, tente novamente. |
| **helplink_text** | O que é isto? |
| **ver_fail_retry** | Esse código está incorreto. Tente novamente. |
| **alert_title** | Cancelar a Introdução dos seus Detalhes |
| **required_field** | Estas informações são necessárias. |
| **alert_message** | Quer mesmo cancelar a introdução dos seus detalhes? |
| **ver_intro_msg** | Verificação é necessária. Clique no botão de envio. |
| **ver_input** | Código de verificação |

## <a name="sign-up-and-self-asserted-pages-error-messages"></a>Inscreva-se e Self-declaradas páginas mensagens de erro

| ID | Valor predefinido |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Já existe um utilizador com o ID especificado. Escolha um diferente. |
| **UserMessageIfClaimNotVerified** | Afirmação não verificada: {0} |
| **UserMessageIfIncorrectPattern** | Padrão de incorreta para: {0} |
| **UserMessageIfMissingRequiredElement** | Elemento necessário em falta: {0} |
| **UserMessageIfValidationError** | Erro na validação por: {0} |
| **UserMessageIfInvalidInput** | {0} Tem a entrada inválida. |
| **ServiceThrottled** | Existem demasiados pedidos neste momento. Aguarde algum tempo e tente novamente. |

O exemplo seguinte mostra o uso de alguns dos elementos de interface de utilizador na página de inscrição:

![Elementos de experiência do Usuário da página de inscrição](./media/localization-string-ids/localization-sign-up.png)

O exemplo seguinte mostra o uso de alguns dos elementos de interface de utilizador na página de inscrição, depois do usuário clica no botão de código de verificação de enviar:

![Elementos de experiência do Usuário de verificação de e-mail de página de inscrição](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>Elementos da interface de utilizador de página de autenticação de fator telefone

Seguem-se os IDs para uma definição de conteúdo com o ID `api.phonefactor`. 

| ID | Valor predefinido |
| -- | ------------- |
| **button_verify** | Telefonar-me |
| **country_code_label** | Código do País |
| **cancel_message** | O utilizador cancelou a autenticação multifator |
| **text_button_send_second_code** | enviar um novo código |
| **code_pattern** | \\d{6} |
| **intro_mixed** | Temos o número seguinte no Registro para. Podemos pode enviar um código através de SMS ou por telefone para o autenticar. |
| **intro_mixed_p** | Temos os números a seguir no Registro para. Escolha um número de nós pode de telefone ou enviar um código através de SMS para o autenticar. |
| **button_verify_code** | Verificar Código |
| **requiredField_code** | Introduza o código de verificação que recebeu |
| **invalid_code** | Introduza o código de 6 dígitos recebido |
| **button_cancel** | Cancelar |
| **local_number_input_placeholder_text** | Número de telefone |
| **button_retry** | Repetir |
| **alternative_text** | Não tenho o meu telefone |
| **intro_phone_p** | Temos os números a seguir no Registro para. Escolha um número de nós pode de telefone para o autenticar. |
| **intro_phone** | Temos o número seguinte no Registro para. Podemos será de telefone para o autenticar. |
| **enter_code_text_intro** | Introduza o código de verificação abaixo, ou  |
| **intro_entry_phone** | Introduza um número abaixo para o qual possamos telefonar para autenticá-lo. |
| **intro_entry_sms** | Introduza um número abaixo para o qual possamos enviar um código por SMS para autenticá-lo. |
| **button_send_code** | Enviar Código |
| **invalid_number** | Introduza um número de telefone válido. |
| **intro_sms** | Temos o número seguinte no Registro para. Nós enviaremos um código através de SMS para o autenticar. |
| **intro_entry_mixed** | Introduza um número abaixo para o qual possamos enviar um código por SMS ou telefonar para autenticá-lo. |
| **number_pattern** | ^\\+(?:[0-9][\\x20-]?){6,14}[0-9]$ |
| **intro_sms_p** |Temos os números a seguir no Registro para. Escolha um número que podemos enviar um código através de SMS para o autenticar. |
| **requiredField_countryCode** | Selecione o indicativo do seu país |
| **requiredField_number** | Introduza o seu número de telemóvel |
| **country_code_input_placeholder_text** |País ou Região |
| **number_label** | Número de Telefone |
| **error_tryagain** | O número de telefone que forneceu está ocupado ou indisponível. Verifique o número e tente novamente. |
| **error_incorrect_code** | O código de verificação que introduziu não correspondem aos nossos registros. Tente novamente ou pedir um novo código. |
| **countryList** | {\"Predefinido\":\"país/região\",\"AF\":\"Afeganistão\",\"AX\":\"Alanda Ilhas\",\"AL\":\"Albânia\",\"DZ\":\"Argélia\",\"AS\":\" Samoa americana\",\"AD\":\"Andorra\",\"AO\":\"Angola\",\"IA\": \"Anguila\",\"AQ\":\"Antárctica\",\"AG\":\"Antígua e Barbuda\",\"AR\":\"Argentina\",\"AM\":\"Arménia\",\"AW\":\"Aruba \",\"AU\":\"Austrália\",\"no\":\"Áustria\",\" AZ\":\"Azerbaijão\",\"BS\":\"Baamas\",\"BH\":\" Barém\",\"BD\":\"Bangladeche\",\"BB\":\"Barbados\",\" POR\":\"Bielorrússia\",\"BE\":\"Bélgica\",\"BZ\":\" Belize\",\"BJ\":\"Benim\",\"BM\":\"Bermudas\",\"BT\":\"Butão\",\"BO\":\"Bolívia\",\"BQ\":\" Bonaire\",\"BA\":\"Bósnia e Herzegovina\",\"BW\":\"Botsuana<span class="notransla class=""></span class="notransla> Ilhas distantes\",\"VI\":\"dos EUA Ilhas Virgens\",\"UG\":\"Uganda\",\"UA\":\"Ucrânia\",\"AE\":\" Emirados Árabes Unidos\",\"GB\":\"Reino Unido\",\"E.U.A.\":\"dos Estados Unidos\",\"UY \":\"Uruguai\",\"UZ\":\"Usbequistão\",\"VU\":\"Vanuatu\", \"VA\":\"cidade do Vaticano\",\"VE\":\"Venezuela\",\"VN\":\"Vietname \",\"WF\":\"Wallis e Futuna\",\"YE\":\"Iémen\",\"ZM\":\"Zâmbia\",\"ZW\":\"Zimbabué\"} |
| **error_448** | O número de telefone fornecido não responde. |
| **error_449** | O utilizador excedeu o número de tentativas de repetição. |
| **verification_code_input_placeholder_text** | Código de verificação |

O exemplo seguinte mostra o uso de alguns dos elementos de interface de utilizador na página de inscrição de MFA:

![Elementos de experiência do Usuário de verificação de e-mail de página de inscrição](./media/localization-string-ids/localization-mfa1.png)

O exemplo seguinte mostra o uso de alguns dos elementos de interface de utilizador na página de validação de MFA:

![Elementos de experiência do Usuário de verificação de e-mail de página de inscrição](./media/localization-string-ids/localization-mfa2.png)







