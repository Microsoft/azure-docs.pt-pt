---
title: IDs de cadeia de caracteres de localização-Azure Active Directory B2C | Microsoft Docs
description: Especifique as IDs para uma definição de conteúdo com uma ID de API. signuporsignin em uma política personalizada em Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: da1390de4e2eb0624032dc490416e7b6e5d61baa
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "67846841"
---
# <a name="localization-string-ids"></a>IDs de cadeia de localização

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O elemento de **localização** permite que você dê suporte a várias localidades ou idiomas na política para os percursos do usuário. Este artigo fornece a lista de IDs de localização que você pode usar em sua política. Para se familiarizar com a localização da interface do usuário, consulte [localização](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Elementos da página de inscrição ou entrada

As IDs a seguir são usadas para uma definição de conteúdo com uma `api.signuporsignin`ID de.

| id | Valor predefinido |
| -- | ------------- |
| **local_intro_email** | Inicie sessão com a sua conta existente |
| **logonIdentifier_email** | Endereço de E-mail |
| **requiredField_email** | Introduza o seu e-mail |
| **invalid_email** | Introduza um endereço de e-mail válido |
| **email_pattern** | ^[a-zA-Z0-9.!#$%&’' *+/=?^_\`{\|}~-]+@[a-zA-Z0-9-]+(?:\\.[a-zA-Z0-9-]+)* $ |
| **local_intro_username** | Iniciar sessão com o nome de utilizador |
| **logonIdentifier_username** | Nome de utilizador |
| **requiredField_username** | Introduza o seu nome de utilizador |
| **password** | Palavra-passe |
| **requiredField_password** | Introduza a sua palavra-passe |
| **invalid_password** | A palavra-passe que introduziu não está no formato esperado. |
| **forgotpassword_link** | Esqueceu-se da palavra-passe? |
| **createaccount_intro** | Não tem uma conta? |
| **createaccount_link** | Inscrever-se agora |
| **divider_title** | OU |
| **cancel_message** | O utilizador esqueceu-se da sua palavra-passe |
| **button_signin** | Iniciar sessão |
| **social_intro** | Inicie sessão com a sua conta de redes sociais |
  **remember_me** |Manter sessão iniciada|
| **unknown_error** | Estamos com problemas para conectá-lo. Tente novamente mais tarde. |

O exemplo a seguir mostra o uso de alguns dos elementos da interface do usuário na página de inscrição ou entrada:

![Elementos de UX da página de inscrição ou entrada](./media/localization-string-ids/localization-susi.png)

A ID dos provedores de identidade é configurada no elemento **ClaimsExchange** de jornada do usuário. Para localizar o título do provedor de identidade, **ElementType** é definido como `ClaimsProvider`, enquanto stringid é `ClaimsExchange`definido como a ID do.

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

O exemplo a seguir localiza o provedor de identidade do Facebook como árabe:

```XML
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

## <a name="sign-up-or-sign-in-error-messages"></a>Mensagens de erro de inscrição ou entrada

| id | Valor predefinido |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | A palavra-passe está incorreta. |
| **UserMessageIfClaimsPrincipalDoesNotExist** | Não é possível encontrar a sua conta. |
| **UserMessageIfOldPasswordUsed** | Parece que utilizou uma palavra-passe antiga. |
| **DefaultMessage** | Nome de utilizador ou palavra-passe inválido. |
| **UserMessageIfUserAccountDisabled** | Sua conta foi bloqueada. Contate a equipe de suporte para desbloqueá-lo e tente novamente. |
| **UserMessageIfUserAccountLocked** | Sua conta está temporariamente bloqueada para impedir o uso não autorizado. Tente novamente mais tarde. |
| **AADRequestsThrottled** | Há muitas solicitações neste momento. Aguarde algum tempo e tente novamente. |

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Elementos da interface do usuário de páginas autodeclaradas e de inscrição

Veja a seguir as IDs para uma definição de conteúdo com uma ID `api.localaccountsignup` ou qualquer definição de conteúdo que comece `api.selfasserted`com, `api.selfasserted.profileupdate` como e `api.localaccountpasswordreset`.

| id | Valor predefinido |
| -- | ------------- |
| **ver_sent** | O código de verificação foi enviado para: |
| **ver_but_default** | Predefinição |
| **cancel_message** | O usuário cancelou a inserção de informações autodeclaradas |
| **preloader_alt** | Aguarde |
| **ver_but_send** | Enviar código de verificação |
| **alert_yes** | Sim |
| **error_fieldIncorrect** | Um ou mais campos estão preenchidos incorretamente. Verifique suas entradas e tente novamente. |
| **year** | Ano |
| **verifying_blurb** | Aguarde enquanto processamos as suas informações. |
| **button_cancel** | Cancelar |
| **ver_fail_no_retry** | Você fez muitas tentativas incorretas. Tente novamente mais tarde. |
| **mês** | Mês |
| **ver_success_msg** | Endereço de email verificado. Agora você pode continuar. |
| **months** | Janeiro, Fevereiro, Março, Abril, Maio, Junho, Julho, Agosto, Setembro, Outubro, Novembro, Dezembro |
| **ver_fail_server** | Estamos com problemas para verificar seu endereço de email. Insira um endereço de email válido e tente novamente. |
| **error_requiredFieldMissing** | Um campo obrigatório está ausente. Preencha todos os campos obrigatórios e tente novamente. |
| **initial_intro** | Forneça os seguintes detalhes. |
| **ver_but_resend** | Enviar novo código |
| **button_continue** | Criar |
| **error_passwordEntryMismatch** | Os campos de entrada de senha não coincidem. Insira a mesma senha em ambos os campos e tente novamente. |
| **ver_incorrect_format** | Formato incorreto. |
| **ver_but_edit** | Alterar e-mail |
| **ver_but_verify** | Verificar código |
| **alert_no** | Não |
| **ver_info_msg** | O código de verificação foi enviado para sua caixa de entrada. Copie-o na caixa de entrada abaixo. |
| **diário** | Dia |
| **ver_fail_throttled** | Houve muitas solicitações para verificar esse endereço de email. Aguarde um momento e tente novamente. |
| **helplink_text** | O que é isto? |
| **ver_fail_retry** | Esse código está incorreto. Tente novamente. |
| **alert_title** | Cancelar a Introdução dos seus Detalhes |
| **required_field** | Estas informações são necessárias. |
| **alert_message** | Quer mesmo cancelar a introdução dos seus detalhes? |
| **ver_intro_msg** | A verificação é necessária. Clique no botão enviar. |
| **ver_input** | Código de verificação |

## <a name="sign-up-and-self-asserted-pages-error-messages"></a>Mensagens de erro de inscrição e de páginas autodeclaradas

| id | Valor predefinido |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Já existe um usuário com a ID especificada. Escolha um nome diferente. |
| **UserMessageIfClaimNotVerified** | Declaração não verificada:{0} |
| **UserMessageIfIncorrectPattern** | Padrão incorreto para:{0} |
| **UserMessageIfMissingRequiredElement** | Elemento necessário ausente:{0} |
| **UserMessageIfValidationError** | Erro na validação por:{0} |
| **UserMessageIfInvalidInput** | {0}tem entrada inválida. |
| **ServiceThrottled** | Há muitas solicitações neste momento. Aguarde algum tempo e tente novamente. |

O exemplo a seguir mostra o uso de alguns dos elementos da interface do usuário na página de inscrição:

![Página de inscrição com seus nomes de elementos de interface do usuário rotulados](./media/localization-string-ids/localization-sign-up.png)

O exemplo a seguir mostra o uso de alguns dos elementos da interface do usuário na página de inscrição, depois que o usuário clica no botão enviar código de verificação:

![Página inscrever-se na verificação de email elementos UX](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>Elementos da interface de usuário da página de autenticação do fator de telefone

Veja a seguir as IDs para uma definição de conteúdo com uma ID `api.phonefactor`de.

| id | Valor predefinido |
| -- | ------------- |
| **button_verify** | Telefonar-me |
| **country_code_label** | Indicativo de país |
| **cancel_message** | O usuário cancelou a autenticação multifator |
| **text_button_send_second_code** | enviar um novo código |
| **code_pattern** | \\d{6} |
| **intro_mixed** | Temos o seguinte número no registro para você. Podemos enviar um código via SMS ou telefone para autenticá-lo. |
| **intro_mixed_p** | Temos os números a seguir no registro para você. Escolha um número para o qual possamos telefonar ou envie um código via SMS para autenticá-lo. |
| **button_verify_code** | Verificar Código |
| **requiredField_code** | Introduza o código de verificação que recebeu |
| **invalid_code** | Introduza o código de 6 dígitos recebido |
| **button_cancel** | Cancelar |
| **local_number_input_placeholder_text** | Número de telefone |
| **button_retry** | Repetir |
| **alternative_text** | Não tenho o meu telefone |
| **intro_phone_p** | Temos os números a seguir no registro para você. Escolha um número que possamos telefonar para autenticar você. |
| **intro_phone** | Temos o seguinte número no registro para você. O telefone será autenticado. |
| **enter_code_text_intro** | Introduza o código de verificação abaixo, ou  |
| **intro_entry_phone** | Introduza um número abaixo para o qual possamos telefonar para autenticá-lo. |
| **intro_entry_sms** | Introduza um número abaixo para o qual possamos enviar um código por SMS para autenticá-lo. |
| **button_send_code** | Enviar Código |
| **invalid_number** | Introduza um número de telefone válido. |
| **intro_sms** | Temos o seguinte número no registro para você. Enviaremos um código via SMS para autenticá-lo. |
| **intro_entry_mixed** | Introduza um número abaixo para o qual possamos enviar um código por SMS ou telefonar para autenticá-lo. |
| **number_pattern** | ^\\+(?:[0-9][\\x20-]?){6,14}[0-9]$ |
| **intro_sms_p** |Temos os números a seguir no registro para você. Escolha um número para o qual podemos enviar um código via SMS para autenticá-lo. |
| **requiredField_countryCode** | Selecione o indicativo do seu país |
| **requiredField_number** | Introduza o seu número de telemóvel |
| **country_code_input_placeholder_text** |País ou Região |
| **number_label** | Número de telefone |
| **error_tryagain** | O número de telefone fornecido está ocupado ou indisponível. Verifique o número e tente novamente. |
| **error_incorrect_code** | O código de verificação que você inseriu não corresponde aos nossos registros. Tente novamente ou solicite um novo código. |
| **countryList** | {\"Padrão\":país/\"região,AF\":\"Afeganistão,\"AX:Åland\"\"\"\"\" Ilhas\",\"Al:\"Albânia\",DZ\":Argélia,como\":\"\"\"\"\"\" \"Samoa Americana,\"ad\": Andorra, ao:Angola\",ia\":\"\"\"\"\"\" \"Anguila,\"AQ\":Antártica\",AG: Antígua eBarbuda\",\"\"\"\"\"\"Ar\":Argentina\",\"am:\"Armênia, AW:\"Aruba\"\"\"\"\" \",Au\":\"Austrália,\"at:Áustria,\"\"\"\"\"\" AZ\":\"Azerbaijão,\"BS\":Bahamas\" BH:\"\"\"\"\" Bahrein\",\"BD:\"Bangladesh\",BB\":Barbados,\"\"\"\"\" Por\":\"Belarus,\"ser\":Bélgica\",por:\"\"\"\"\" Belize\",\"BJ:\"Benin\",BM\":Bermuda,\"\"\"\"\"BT\":Butão\",\"Bo:\"Bolívia,BQ:\"\"\"\"\"\" Bonaire\",\"BA:Bósnia\"eHerzegovina\",\"BW:Botsuana\"\"\"<span class="notransla class=""></span class="notransla> Territórios\"insulares,\"vi\":\"EUA Ilhas\"virgens\",\"UG:\"Uganda,\"UA:\"Ucrânia,AE\":\"\"\"\"\" Emirados\"Árabes Unidos,\"GB\":\"Reino Unido\",EUA\":\"EstadosUnidos,uy\"\"\" \":Uruguai\",\"uz:\"Uzbequistão, VU:Vanuatu\",\"\"\"\"\"\" \"VA:\"cidadedoVaticano\", ve:\"Venezuela, vn:Vietnã\"\"\"\"\"\"\" \",WF\":Wallise\"Futuna,Ye:\"Iêmen,\"ZM\"\"\"\"\"\":Zâmbia,\"zw:Zimbábue\"}\"\"\"\" |
| **error_448** | O número de telefone fornecido não responde. |
| **error_449** | O utilizador excedeu o número de tentativas de repetição. |
| **verification_code_input_placeholder_text** | Código de verificação |

O exemplo a seguir mostra o uso de alguns dos elementos da interface do usuário na página de registro do MFA:

![Página inscrever-se na verificação de email elementos UX](./media/localization-string-ids/localization-mfa1.png)

O exemplo a seguir mostra o uso de alguns dos elementos da interface do usuário na página validação do MFA:

![Página inscrever-se na verificação de email elementos UX](./media/localization-string-ids/localization-mfa2.png)







