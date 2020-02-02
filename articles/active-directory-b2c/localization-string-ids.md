---
title: IDs de cadeia de localização - Diretório Ativo Azure B2C / Microsoft Docs
description: Especifique os IDs para uma definição de conteúdo com um Id de api.signuporsignin numa política personalizada no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/31/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 349932073394a967da58bf521449d22a48c1f91d
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76931724"
---
# <a name="localization-string-ids"></a>IDs de cadeia de localização

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O elemento **Localização** permite-lhe suportar vários locais ou idiomas na política para as viagens do utilizador. Este artigo fornece a lista de IDs de localização que pode usar na sua política. Para se familiarizar com a localização da UI, consulte [a Localização.](localization.md)

## <a name="sign-up-or-sign-in-page-elements"></a>Elementos de página de inscrição ou de inscrição

Os seguintes Ids são utilizados para uma definição de conteúdo com uma identificação de `api.signuporsignin`.

| ID | Valor predefinido |
| -- | ------------- |
| **local_intro_email** | Inscreva-se na sua conta existente |
| **logonIdentifier_email** | Endereço de E-mail |
| **requiredField_email** | Por favor, insira o seu e-mail |
| **invalid_email** | Por favor, insira um endereço de e-mail válido |
| **email_pattern** | ^[a-zA-Z0-9.!#$%&’' *+/=?^_\`{\|}~-]+@[a-zA-Z0-9-]+(?:\\.[a-zA-Z0-9-]+)* $ |
| **local_intro_username** | Inscreva-se com o seu nome de utilizador |
| **logonIdentifier_username** | Nome de utilizador |
| **requiredField_username** | Por favor, insira o seu nome de utilizador |
| **password** | Palavra-passe |
| **requiredField_password** | Por favor, insira a sua senha |
| **invalid_password** | A palavra-passe que inseriu não está no formato esperado. |
| **forgotpassword_link** | Esqueceu a sua senha? |
| **createaccount_intro** | Não tem uma conta? |
| **createaccount_link** | Registe-se já |
| **divider_title** | OU |
| **cancel_message** | O utilizador esqueceu-se da sua senha |
| **button_signin** | Iniciar sessão |
| **social_intro** | Inscreva-se na sua conta social |
  **remember_me** |Mantenha-me inscrito.|
| **unknown_error** | Estamos a ter dificuldades em contratá-lo. Tente novamente mais tarde. |

O exemplo seguinte mostra a utilização de alguns dos elementos da interface do utilizador na página de inscrição ou de inscrição:

![Elementos UX de inscrição ou inscrição na página](./media/localization-string-ids/localization-susi.png)

O ID dos fornecedores de identidade está configurado no elemento de viagem do utilizador **ClaimsExchange.** Para localizar o título do fornecedor de identidade, o **ElementType** está definido para `ClaimsProvider`, enquanto o **StringId** está definido para o ID do `ClaimsExchange`.

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

O exemplo seguinte localiza o fornecedor de identidade do Facebook para árabe:

```XML
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

### <a name="sign-up-or-sign-in-error-messages"></a>Mensagens de erro de inscrição ou de inscrição

| ID | Valor predefinido |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | A sua palavra-passe está incorreta. |
| **UserMessageIfClaimsPrincipalDoesNotExist** | Não conseguimos encontrar a sua conta. |
| **UserMessageIfOldPasswordUsed** | Parece que usaste uma senha antiga. |
| **DefaultMessage** | Nome de usuário ou senha inválido. |
| **UserMessageIfUserAccountDisabled** | Sua conta foi bloqueada. Contate a equipe de suporte para desbloqueá-lo e tente novamente. |
| **UserMessageIfUserAccountLocked** | Sua conta está temporariamente bloqueada para impedir o uso não autorizado. Tente novamente mais tarde. |
| **AADRequestsThrottled** | Há muitas solicitações neste momento. Aguarde algum tempo e tente novamente. |

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Elementos de interface de utilizador de páginas de inscrição e autoafirmadas

Seguem-se as IDs para uma definição de conteúdo com identificação de `api.localaccountsignup` ou qualquer definição de conteúdo que comece com `api.selfasserted`, como `api.selfasserted.profileupdate` e `api.localaccountpasswordreset`.

| ID | Valor predefinido |
| -- | ------------- |
| **ver_sent** | O código de verificação foi enviado para: |
| **ver_but_default** | Predefinição |
| **cancel_message** | O utilizador cancelou a introdução de informações autoafirmadas |
| **preloader_alt** | Aguarde |
| **ver_but_send** | Enviar código de verificação |
| **alert_yes** | Sim |
| **error_fieldIncorrect** | Um ou mais campos são preenchidos incorretamente. Por favor, verifique as suas entradas e tente novamente. |
| **ano** | Ano |
| **verifying_blurb** | Por favor, espere enquanto processamos a sua informação. |
| **button_cancel** | Cancelar |
| **ver_fail_no_retry** | Já fez muitas tentativas incorretas. Tente novamente mais tarde. |
| **mês** | Mês |
| **ver_success_msg** | Endereço de e-mail verificado. Agora pode continuar. |
| **months** | Janeiro, fevereiro, março, abril, maio, junho, julho, agosto, setembro, outubro, novembro, dezembro |
| **ver_fail_server** | Estamos com dificuldades em verificar o seu endereço de e-mail. Por favor, insira um endereço de e-mail válido e tente novamente. |
| **error_requiredFieldMissing** | Falta um campo necessário. Por favor, preencha todos os campos necessários e tente novamente. |
| **initial_intro** | Por favor, forneça os seguintes detalhes. |
| **ver_but_resend** | Enviar novo código |
| **button_continue** | Create |
| **error_passwordEntryMismatch** | Os campos de entrada de senhas não coincidem. Introduza a mesma senha em ambos os campos e tente novamente. |
| **ver_incorrect_format** | Formato incorreto. |
| **ver_but_edit** | Alterar e-mail |
| **ver_but_verify** | Verificar código |
| **alert_no** | Não |
| **ver_info_msg** | O código de verificação foi enviado para a sua caixa de entrada. Por favor, copie-o para a caixa de entrada abaixo. |
| **dia** | Dia |
| **ver_fail_throttled** | Tem havido muitos pedidos para verificar este endereço de e-mail. Por favor, espere um pouco e tente de novo. |
| **helplink_text** | O que é isto? |
| **ver_fail_retry** | O código está incorreto. Tente novamente. |
| **alert_title** | Cancele a entrada dos seus dados |
| **required_field** | Esta informação é necessária. |
| **alert_message** | Tem certeza de que quer cancelar a entrada dos seus dados? |
| **ver_intro_msg** | A verificação é necessária. Por favor, clique no botão Enviar. |
| **ver_input** | Código de verificação |

### <a name="sign-up-and-self-asserted-pages-error-messages"></a>Mensagens de erro de páginas de inscrição e autoafirmadas

| ID | Valor predefinido |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Já existe um utilizador com o ID especificado. Por favor, escolha outro. |
| **UserMessageIfClaimNotVerified** | Reclamação não verificada: {0} |
| **UserMessageIfIncorrectPattern** | Padrão incorreto para: {0} |
| **UserMessageIfMissingRequiredElement** | Elemento necessário em falta: {0} |
| **UserMessageIfValidationError** | Erro na validação por: {0} |
| **UserMessageIfInvalidInput** | {0} tem entrada inválida. |
| **ServiceThrottled** | Há muitas solicitações neste momento. Aguarde algum tempo e tente novamente. |

O exemplo seguinte mostra a utilização de alguns dos elementos da interface do utilizador na página de inscrição:

![Página de inscrição com os nomes dos seus elementos ui rotulados](./media/localization-string-ids/localization-sign-up.png)

O exemplo seguinte mostra a utilização de alguns dos elementos da interface do utilizador na página de inscrição, depois de o utilizador clicar no botão código de verificação de envio:

![Elementos UX de verificação de e-mail de página de inscrição](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>Elementos de interface de página de autenticação de fator de telefone

Seguem-se as identidades para uma definição de conteúdo com identificação de `api.phonefactor`.

| ID | Valor predefinido |
| -- | ------------- |
| **button_verify** | Liga-me |
| **country_code_label** | Indicativo do País |
| **cancel_message** | O utilizador cancelou a autenticação de vários fatores |
| **text_button_send_second_code** | enviar um novo código |
| **code_pattern** | \\d{6} |
| **intro_mixed** | Temos o seguinte número registado para si. Podemos enviar um código via SMS ou telefone para o autenticar. |
| **intro_mixed_p** | Temos os seguintes números registados para si. Escolha um número que podemos telefonar ou enviar um código via SMS para o autenticar. |
| **button_verify_code** | Verificar código |
| **requiredField_code** | Por favor, insira o código de verificação que recebeu |
| **invalid_code** | Por favor, insira o código de 6 dígitos que recebeu |
| **button_cancel** | Cancelar |
| **local_number_input_placeholder_text** | Número de telefone |
| **button_retry** | Repetir |
| **alternative_text** | Não tenho o meu telemóvel. |
| **intro_phone_p** | Temos os seguintes números registados para si. Escolha um número que podemos telefonar para o autenticar. |
| **intro_phone** | Temos o seguinte número registado para si. Vamos telefonar para te autenticar. |
| **enter_code_text_intro** | Insira o seu código de verificação abaixo, ou  |
| **intro_entry_phone** | Insira um número abaixo que podemos telefonar para o autenticar. |
| **intro_entry_sms** | Insira um número abaixo que podemos enviar um código via SMS para o autenticar. |
| **button_send_code** | Enviar Código |
| **invalid_number** | Por favor, insira um número de telefone válido |
| **intro_sms** | Temos o seguinte número registado para si. Enviaremos um código via SMS para o autenticar. |
| **intro_entry_mixed** | Insira um número abaixo que podemos enviar um código via SMS ou telefone para o autenticar. |
| **number_pattern** | ^\\+(?:[0-9][\\x20-]?){6,14}[0-9]$ |
| **intro_sms_p** |Temos os seguintes números registados para si. Escolha um número que podemos enviar um código via SMS para o autenticar. |
| **requiredField_countryCode** | Por favor, selecione o seu código de país |
| **requiredField_number** | Por favor, insira o seu número de telefone |
| **country_code_input_placeholder_text** |País ou região |
| **number_label** | Número de Telefone |
| **error_tryagain** | O número de telefone que forneceu está ocupado ou indisponível. Por favor, verifique o número e tente de novo. |
| **error_incorrect_code** | O código de verificação que inseriu não corresponde aos nossos registos. Por favor, tente de novo, ou peça um novo código. |
| **countryList** | {\"DEFAULT\":\"Country/Region\",\"AF\":\"Afghanistan\",\"AX\":\"Åland Islands\",\"AL\":\"Albania\",\"DZ\":\"Algeria\",\"AS\":\"American Samoa\",\"AD\":\"Andorra\",\"AO\":\"Angola\",\"AI\":\"Anguilla\",\"AQ\":\"Antarctica\",\"AG\":\"Antigua and Barbuda\",\"AR\":\"Argentina\",\"AM\":\"Armenia\",\"AW\":\"Aruba\",\"AU\":\"Australia\",\"AT\":\"Austria\",\"AZ\":\"Azerbaijan\",\"BS\":\"Bahamas\",\"BH\":\"Bahrain\",\"BD\":\"Bangladesh\",\"BB\":\"Barbados\",\"BY\":\"Belarus\",\"BE\":\"Belgium\",\"BZ\":\"Belize\",\"BJ\":\"Benin\",\"BM\":\"Bermuda\",\"BT\":\"Bhutan\",\"BO\":\"Bolivia\",\"BQ\":\"Bonaire\",\"BA\":\"Bosnia and Herzegovina\",\"BW\":\"Botswana\",\"BV\":\"Bouvet Island\",\"BR\":\"Brazil\",\"IO\":\"British Indian Ocean Territory\",\"VG\":\"British Virgin Islands\",\"BN\":\"Brunei\",\"BG\":\"Bulgaria\",\"BF\":\"Burkina Faso\",\"BI\":\"Burundi\",\"CV\":\"Cabo Verde\",\"KH\":\"Cambodia\",\"CM\":\"Cameroon\",\"CA\":\"Canada\",\"KY\":\"Cayman Islands\",\"CF\":\"Central African Republic\",\"TD\":\"Chad\",\"CL\":\"Chile\",\"CN\":\"China\",\"CX\":\"Christmas Island\",\"CC\":\"Cocos (Keeling) Islands\",\"CO\":\"Colombia\",\"KM\":\"Comoros\",\"CG\":\"Congo\",\"CD\":\"Congo (DRC)\",\"CK\":\"Cook Islands\",\"CR\":\"Costa Rica\",\"CI\":\"Côte d’Ivoire\",\"HR\":\"Croatia\",\"CU\":\"Cuba\",\"CW\":\"Curaçao\",\"CY\":\"Cyprus\",\"CZ\":\"Czech Republic\",\"DK\":\"Denmark\",\"DJ\":\"Djibouti\",\"DM\":\"Dominica\",\"DO\":\"Dominican Republic\",\"EC\":\"Ecuador\",\"EG\":\"Egypt\",\"SV\":\"El Salvador\",\"GQ\":\"Equatorial Guinea\",\"ER\":\"Eritrea\",\"EE\":\"Estonia\",\"ET\":\"Ethiopia\",\"FK\":\"Falkland Islands\",\"FO\":\"Faroe Islands\",\"FJ\":\"Fiji\",\"FI\":\"Finland\",\"FR\":\"France\",\"GF\":\"French Guiana\",\"PF\":\"French Polynesia\",\"TF\":\"French Southern Territories\",\"GA\":\"Gabon\",\"GM\":\"Gambia\",\"GE\":\"Georgia\",\"DE\":\"Germany\",\"GH\":\"Ghana\",\"GI\":\"Gibraltar\",\"GR\":\"Greece\",\"GL\":\"Greenland\",\"GD\":\"Grenada\",\"GP\":\"Guadeloupe\",\"GU\":\"Guam\",\"GT\":\"Guatemala\",\"GG\":\"Guernsey\",\"GN\":\"Guinea\",\"GW\":\"Guinea-Bissau\",\"GY\":\"Guyana\",\"HT\":\"Haiti\",\"HM\":\"Heard Island and McDonald Islands\",\"HN\":\"Honduras\",\"HK\":\"Hong Kong SAR\",\"HU\":\"Hungary\",\"IS\":\"Iceland\",\"IN\":\"India\",\"ID\":\"Indonesia\",\"IR\":\"Iran\",\"IQ\":\"Iraq\",\"IE\":\"Ireland\",\"IM\":\"Isle of Man\",\"IL\":\"Israel\",\"IT\":\"Italy\",\"JM\":\"Jamaica\",\"JP\":\"Japan\",\"JE\":\"Jersey\",\"JO\":\"Jordan\",\"KZ\":\"Kazakhstan\",\"KE\":\"Kenya\",\"KI\":\"Kiribati\",\"KR\":\"Korea\",\"KW\":\"Kuwait\",\"KG\":\"Kyrgyzstan\",\"LA\":\"Laos\",\"LV\":\"Latvia\",\"LB\":\"Lebanon\",\"LS\":\"Lesotho\",\"LR\":\"Liberia\",\"LY\":\"Libya\",\"LI\":\"Liechtenstein\",\"LT\":\"Lithuania\",\"LU\":\"Luxembourg\",\"MO\":\"Macao SAR\",\"MK\":\"North Macedonia\",\"MG\":\"Madagascar\",\"MW\":\"Malawi\",\"MY\":\"Malaysia\",\"MV\":\"Maldives\",\"ML\":\"Mali\",\"MT\":\"Malta\",\"MH\":\"Marshall Islands\",\"MQ\":\"Martinique\",\"MR\":\"Mauritania\",\"MU\":\"Mauritius\",\"YT\":\"Mayotte\",\"MX\":\"Mexico\",\"FM\":\"Micronesia\",\"MD\":\"Moldova\",\"MC\":\"Monaco\",\"MN\":\"Mongolia\",\"ME\":\"Montenegro\",\"MS\":\"Montserrat\",\"MA\":\"Morocco\",\"MZ\":\"Mozambique\",\"MM\":\"Myanmar\",\"NA\":\"Namibia\",\"NR\":\"Nauru\",\"NP\":\"Nepal\",\"NL\":\"Netherlands\",\"NC\":\"New Caledonia\",\"NZ\":\"New Zealand\",\"NI\":\"Nicaragua\",\"NE\":\"Niger\",\"NG\":\"Nigeria\",\"NU\":\"Niue\",\"NF\":\"Norfolk Island\",\"KP\":\"North Korea\",\"MP\":\"Northern Mariana Islands\",\"NO\":\"Norway\",\"OM\":\"Oman\",\"PK\":\"Pakistan\",\"PW\":\"Palau\",\"PS\":\"Palestinian Authority\",\"PA\":\"Panama\",\"PG\":\"Papua New Guinea\",\"PY\":\"Paraguay\",\"PE\":\"Peru\",\"PH\":\"Philippines\",\"PN\":\"Pitcairn Islands\",\"PL\":\"Poland\",\"PT\":\"Portugal\",\"PR\":\"Puerto Rico\",\"QA\":\"Qatar\",\"RE\":\"Réunion\",\"RO\":\"Romania\",\"RU\":\"Russia\",\"RW\":\"Rwanda\",\"BL\":\"Saint Barthélemy\",\"KN\":\"Saint Kitts and Nevis\",\"LC\":\"Saint Lucia\",\"MF\":\"Saint Martin\",\"PM\":\"Saint Pierre and Miquelon\",\"VC\":\"Saint Vincent and the Grenadines\",\"WS\":\"Samoa\",\"SM\":\"San Marino\",\"ST\":\"São Tomé and Príncipe\",\"SA\":\"Saudi Arabia\",\"SN\":\"Senegal\",\"RS\":\"Serbia\",\"SC\":\"Seychelles\",\"SL\":\"Sierra Leone\",\"SG\":\"Singapore\",\"SX\":\"Sint Maarten\",\"SK\":\"Slovakia\",\"SI\":\"Slovenia\",\"SB\":\"Solomon Islands\",\"SO\":\"Somalia\",\"ZA\":\"South Africa\",\"GS\":\"South Georgia and South Sandwich Islands\",\"SS\":\"South Sudan\",\"ES\":\"Spain\",\"LK\":\"Sri Lanka\",\"SH\":\"St Helena, Ascension, Tristan da Cunha\",\"SD\":\"Sudan\",\"SR\":\"Suriname\",\"SJ\":\"Svalbard\",\"SZ\":\"Swaziland\",\"SE\":\"Sweden\",\"CH\":\"Switzerland\",\"SY\":\"Syria\",\"TW\":\"Taiwan\",\"TJ\":\"Tajikistan\",\"TZ\":\"Tanzania\",\"TH\":\"Thailand\",\"TL\":\"Timor-Leste\",\"TG\":\"Togo\",\"TK\":\"Tokelau\",\"TO\":\"Tonga\",\"TT\":\"Trinidad and Tobago\",\"TN\":\"Tunisia\",\"TR\":\"Turkey\",\"TM\":\"Turkmenistan\",\"TC\":\"Turks and Caicos Islands\",\"TV\":\"Tuvalu\",\"UM\":\"U.S. Outlying Islands\",\"VI\":\"U.S. Virgin Islands\",\"UG\":\"Uganda\",\"UA\":\"Ukraine\",\"AE\":\"United Arab Emirates\",\"GB\":\"United Kingdom\",\"US\":\"United States\",\"UY\":\"Uruguay\",\"UZ\":\"Uzbekistan\",\"VU\":\"Vanuatu\",\"VA\":\"Vatican City\",\"VE\":\"Venezuela\",\"VN\":\"Vietnam\",\"WF\":\"Wallis and Futuna\",\"YE\":\"Yemen\",\"ZM\":\"Zambia\",\"ZW\":\"Zimbabwe\"} |
| **error_448** | O número de telefone que forneceu é incontactável. |
| **error_449** | O utilizador excedeu o número de tentativas de reprovação. |
| **verification_code_input_placeholder_text** | Código de verificação |

O exemplo que se segue mostra a utilização de alguns dos elementos da interface do utilizador na página de inscrição do MFA:

![Elementos UX de verificação de e-mail de página de inscrição](./media/localization-string-ids/localization-mfa1.png)

O exemplo que se segue mostra a utilização de alguns dos elementos da interface do utilizador na página de validação do MFA:

![Elementos UX de verificação de e-mail de página de inscrição](./media/localization-string-ids/localization-mfa2.png)

## <a name="verification-display-control-user-interface-elements"></a>Elementos de interface de controlo de ecrã de verificação

Seguem-se as IDs para um controlo de visualização de [verificação](display-control-verification.md)

| ID | Valor predefinido |
| -- | ------------- |
|verification_control_but_change_claims |Alterar |
|verification_control_fail_send_code |Não enviou o código, por favor tente mais tarde. |
|verification_control_fail_verify_code |Não verificou o código, por favor tente mais tarde. |
|verification_control_but_send_code |Enviar Código |
|verification_control_but_send_new_code |Enviar Novo Código |
|verification_control_but_verify_code |Verificar código |

### <a name="verification-display-control-error-messages"></a>Mensagens de erro de controlo de ecrã de verificação

| ID | Valor predefinido |
| -- | ------------- |
|UserMessageIfMaxRetryAttempted |Uma palavra-passe de uma vez fornecida verificação ultrapassou o número máximo de tentativas |
|UserMessageIfSessionDoesNotExist |Uma sessão de verificação de senha já expirou |
|UserMessageifSessionConflict |Uma sessão de verificação de senha sessão tem conflito |
|UserMessageIfInvalidCode |Uma palavra-passe de uma vez fornecida para verificação é incorreta |








