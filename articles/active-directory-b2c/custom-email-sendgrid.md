---
title: Verificação personalizada de e-mail com SendGrid
titleSuffix: Azure AD B2C
description: Saiba como integrar com a SendGrid para personalizar o e-mail de verificação enviado aos seus clientes quando se inscrevem para utilizar as suas aplicações ativadas a Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/25/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d2716c49c72674b53e52b021972a90cf89bd843a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85392946"
---
# <a name="custom-email-verification-with-sendgrid"></a>Verificação personalizada de e-mail com SendGrid

Utilize e-mail personalizado no Azure Ative Directory B2C (Azure AD B2C) para enviar e-mails personalizados aos utilizadores que se inscrevam para usar as suas aplicações. Ao utilizar [o DisplayControls](display-controls.md) (atualmente em pré-visualização) e o fornecedor de e-mail de terceiros SendGrid, pode utilizar o seu próprio modelo de e-mail e *de:* endereço e assunto, bem como apoiar a localização e as definições de senha única (OTP) personalizadas.

A verificação personalizada de e-mails requer a utilização de um fornecedor de e-mail de terceiros como [SendGrid,](https://sendgrid.com) [Mailjet](https://Mailjet.com)ou [SparkPost,](https://sparkpost.com)um API REST personalizado ou qualquer fornecedor de e-mail baseado em HTTP (incluindo o seu próprio). Este artigo descreve a criação de uma solução que utiliza o SendGrid.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>Criar uma conta SendGrid

Se ainda não tiver uma, comece por criar uma conta SendGrid (os clientes Azure podem desbloquear 25.000 e-mails gratuitos todos os meses). Para obter instruções de configuração, consulte a secção [Criar uma Conta SendGrid](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) de [Como enviar e-mail usando SendGrid com Azure](../sendgrid-dotnet-how-to-send-email.md).

Certifique-se de completar a secção na qual [cria uma chave SendGrid API](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key). Grave a chave API para utilização num passo posterior.

## <a name="create-azure-ad-b2c-policy-key"></a>Criar chave de política Azure AD B2C

Em seguida, guarde a chave SendGrid API numa chave de política AZure AD B2C para as suas políticas de referência.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o seu diretório Azure AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Na página 'Visão Geral', selecione **Identity Experience Framework**.
1. Selecione **As teclas de política** e, em seguida, selecione **Adicionar**.
1. Para **opções**, escolha **Manual**.
1. Insira um **Nome** para a chave de política. Por exemplo, `SendGridSecret`. O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave.
1. Em **Segredo,** insira o segredo do seu cliente que gravou anteriormente.
1. Para **a utilização da chave**, selecione **Signature**.
1. Selecione **Criar**.

## <a name="create-sendgrid-template"></a>Criar modelo SendGrid

Com uma conta SendGrid criada e uma chave API SendGrid armazenada numa chave de política AZure AD B2C, crie um [modelo de transacional dinâmico](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)SendGrid .

1. No site SendGrid, abra a página [de modelos de transação](https://sendgrid.com/dynamic_templates) e selecione **Criar Modelo**.
1. Introduza um nome de modelo único como `Verification email` e, em seguida, **selecione Save**.
1. Para começar a editar o seu novo modelo, **selecione Add Version**.
1. Selecione **Code Editor** e, em seguida, **Continue**.
1. No editor HTML, cole o modelo HTML ou use o seu próprio. Os `{{otp}}` `{{email}}` parâmetros e parâmetros serão substituídos dinamicamente pelo valor de senha única e pelo endereço de e-mail do utilizador.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <!-- Template B O365 -->
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{email}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{otp}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">


                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. Expandir **Definições** à esquerda e para **o Assunto de E-mail,** insira `{{subject}}` .
1. Selecione **o modelo de poupança**.
1. Volte à página **'Modelos Transacionais',** selecionando a seta traseira.
1. Grave o **ID** do modelo que criou para ser utilizado num passo posterior. Por exemplo, `d-989077fbba9746e89f3f6411f596fb96`. Especifica este ID quando [adicionar a transformação de sinistros](#add-the-claims-transformation).

## <a name="add-azure-ad-b2c-claim-types"></a>Adicione tipos de reclamação Azure AD B2C

Na sua política, adicione os seguintes tipos de reclamação ao `<ClaimsSchema>` elemento no seu interior `<BuildingBlocks>` .

Estes tipos de reclamações são necessários para gerar e verificar o endereço de e-mail usando um código de senha única (OTP).

```xml
<ClaimType Id="Otp">
  <DisplayName>Secondary One-time password</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="emailRequestBody">
  <DisplayName>SendGrid request body</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="VerificationCode">
  <DisplayName>Secondary Verification Code</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter your email verification code</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

## <a name="add-the-claims-transformation"></a>Adicione a transformação de sinistros

Em seguida, você precisa de uma transformação de reclamações para desempaçar uma alegação de cadeia JSON que será o corpo do pedido enviado à SendGrid.

A estrutura do objeto JSON é definida pelos IDs na notação de pontos dos InputParameters e pelos TransformationClaimTypes dos InputClaims. Números na notação de pontos implicam matrizes. Os valores provêm dos valores do InputClaims e das propriedades "Valor" dos InputParameters. Para obter mais informações sobre as transformações de alegações da JSON, consulte [a JSON reivindica transformações.](json-transformations.md)

Adicione a seguinte transformação de reclamações ao `<ClaimsTransformations>` elemento interior `<BuildingBlocks>` . Esm faça as seguintes atualizações para a transformação de sinistros XML:

* Atualize o `template_id` valor InputParameter com o ID do modelo de transação SendGrid que criou anteriormente no [modelo Create SendGrid](#create-sendgrid-template).
* Atualize o valor do `from.email` endereço. Use um endereço de e-mail válido para ajudar a evitar que o e-mail de verificação seja marcado como spam.
* Atualize o valor do parâmetro de entrada da `personalizations.0.dynamic_template_data.subject` linha de assunto com uma linha de assunto apropriada para a sua organização.

```xml
<ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
  </InputClaims>
  <InputParameters>
    <!-- Update the template_id value with the ID of your SendGrid template. -->
    <InputParameter Id="template_id" DataType="string" Value="d-989077fbba9746e89f3f6411f596fb96"/>
    <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com"/>
    <!-- Update with a subject line appropriate for your organization. -->
    <InputParameter Id="personalizations.0.dynamic_template_data.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="add-datauri-content-definition"></a>Adicionar definição de conteúdo DataUri

Abaixo as transformações de sinistros no interior `<BuildingBlocks>` , adicione o seguinte [ContentDefinition](contentdefinitions.md) para referência à versão 2.0.0 dados URI:

```xml
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>Criar um DisplayControl

Um controlo de verificação do ecrã é utilizado para verificar o endereço de e-mail com um código de verificação enviado ao utilizador.

Este exemplo de controlo do visor está configurado para:

1. Recolher o `email` tipo de reclamação de endereço do utilizador.
1. Aguarde que o utilizador forneça o `verificationCode` tipo de reclamação com o código enviado ao utilizador.
1. Volte a `email` colocar a parte de trás no perfil técnico autoafirmado que tenha uma referência a este controlo de visualização.
1. Utilizando a `SendCode` ação, gere um código OTP e envie um e-mail com o código OTP ao utilizador.

![Enviar ação de código de verificação por e-mail](media/custom-email-sendgrid/display-control-verification-email-action-01.png)

Nas definições de conteúdo, ainda dentro `<BuildingBlocks>` , adicione o seguinte [DisplayControl](display-controls.md) do tipo [VerificationControl](display-control-verification.md) à sua política.

```xml
<DisplayControls>
  <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
    <DisplayClaims>
      <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
      <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
    </DisplayClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="email" />
    </OutputClaims>
    <Actions>
      <Action Id="SendCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendOtp" />
        </ValidationClaimsExchange>
      </Action>
      <Action Id="VerifyCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
        </ValidationClaimsExchange>
      </Action>
    </Actions>
  </DisplayControl>
</DisplayControls>
```

## <a name="add-otp-technical-profiles"></a>Adicionar perfis técnicos OTP

O `GenerateOtp` perfil técnico gera um código para o endereço de e-mail. O `VerifyOtp` perfil técnico verifica o código associado ao endereço de e-mail. Pode alterar a configuração do formato e a expiração da senha única. Para obter mais informações sobre perfis técnicos da OTP, consulte [definir um perfil técnico de senha única](one-time-password-technical-profile.md).

Adicione os seguintes perfis técnicos ao `<ClaimsProviders>` elemento.

```xml
<ClaimsProvider>
  <DisplayName>One time password technical profiles</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="GenerateOtp">
      <DisplayName>Generate one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">GenerateCode</Item>
        <Item Key="CodeExpirationInSeconds">1200</Item>
        <Item Key="CodeLength">6</Item>
        <Item Key="CharacterSet">0-9</Item>
        <Item Key="ReuseSameCode">true</Item>
        <Item Key="MaxNumAttempts">5</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
      </OutputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="VerifyOtp">
      <DisplayName>Verify one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">VerifyCode</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
      </InputClaims>
    </TechnicalProfile>
   </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="add-a-rest-api-technical-profile"></a>Adicione um perfil técnico de API REST

Este perfil técnico da API REST gera o conteúdo de e-mail (utilizando o formato SendGrid). Para obter mais informações sobre perfis técnicos RESTful, consulte [Definir um perfil técnico RESTful](restful-technical-profile.md).

Tal como acontece com os perfis técnicos da OTP, adicione os seguintes perfis técnicos ao `<ClaimsProviders>` elemento.

```xml
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendOtp">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">emailRequestBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateEmailRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="emailRequestBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>Faça uma referência ao DisplayControl

No passo final, adicione uma referência ao DisplayControl que criou. Substitua o seu `LocalAccountSignUpWithLogonEmail` perfil técnico autoafirmado se utilizar uma versão anterior da política Azure AD B2C. Este perfil técnico `DisplayClaims` utiliza-se com uma referência ao DisplayControl.

Para obter mais informações, consulte [o perfil técnico autoafirmado](restful-technical-profile.md) e [o DisplayControl](display-controls.md).

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <DisplayName>Email signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
        <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
        <Item Key="language.button_continue">Create</Item>
        
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" Required="true" />
        <OutputClaim ClaimTypeReferenceId="objectId" />
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" />
        <OutputClaim ClaimTypeReferenceId="newUser" />
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
      </ValidationTechnicalProfiles>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>[Opcional] Localize o seu e-mail

Para localizado o e-mail, tem de enviar cordas localizadas para a SendGrid ou para o seu fornecedor de e-mail. Por exemplo, pode localizar o assunto do e-mail, o corpo, a sua mensagem de código ou a assinatura do e-mail. Para tal, pode utilizar a transformação de reclamações [GetLocalizedStringstransformation](string-transformations.md) para copiar cordas localizadas em tipos de reclamação. A `GenerateEmailRequestBody` transformação de sinistros, que gera a carga útil JSON, utiliza alegações de entrada que contêm as cordas localizadas.

1. Na sua política, defina as seguintes alegações de cadeia: assunto, mensagem, codeIntro e assinatura.
1. Defina uma transformação de pedidos de substituição de valores de cadeia localizados para os valores de cadeia [do GetLocalizedStrings](string-transformations.md) para substituir os valores de cadeia localizados nas reivindicações do passo 1.
1. Altere a transformação de `GenerateEmailRequestBody` sinistros para utilizar as reclamações de entrada com o seguinte corte XML.
1. Atualize o seu modelo SendGrid para utilizar parâmetros dinâmicos no lugar de todas as cordas que serão localizadas pelo Azure AD B2C.

    ```xml
    <ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
        <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
        <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
        <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
        <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="personalizations.0.dynamic_template_data.subject" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
        <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="personalizations.0.dynamic_template_data.message" />
        <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="personalizations.0.dynamic_template_data.codeIntro" />
        <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="personalizations.0.dynamic_template_data.signature" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="template_id" DataType="string" Value="d-1234567890" />
        <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
    ```

1. Adicione o seguinte elemento [de localização.](localization.md)

    ```xml
    <Localization Enabled="true">
      <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
        <SupportedLanguage>en</SupportedLanguage>
        <SupportedLanguage>es</SupportedLanguage>
      </SupportedLanguages>
      <LocalizedResources Id="api.localaccountsignup.en">
        <LocalizedStrings>
          <!--Email template parameters-->
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for validating the account</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
        </LocalizedStrings>
      </LocalizedResources>
      <LocalizedResources Id="api.localaccountsignup.es">
        <LocalizedStrings>
          <!--Email template parameters-->
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sinceramente</LocalizedString>
        </LocalizedStrings>
      </LocalizedResources>
    </Localization>
    ```

1. Adicione referências aos elementos LocaisResources atualizando o elemento [Definitions de Conteúdo.](contentdefinitions.md)

    ```XML
    <ContentDefinition Id="api.localaccountsignup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
      <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
      </LocalizedResourcesReferences>
    </ContentDefinition>
    ```

1. Por fim, adicione após a transformação de pedidos de entrada para o perfil técnico LocalAccountSignUpWithLogonEmail.

    ```XML
    <InputClaimsTransformations>
      <InputClaimsTransformation ReferenceId="GetLocalizedStringsForEmail" />
    </InputClaimsTransformations>
    ```

## <a name="next-steps"></a>Próximos passos

Pode encontrar um exemplo de uma política de verificação de email personalizada no GitHub:

- [Verificação personalizada de e-mail - DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)
- Para obter informações sobre a utilização de um API de REST personalizado ou qualquer fornecedor de e-mail SMTP baseado em HTTP, consulte [definir um perfil técnico RESTful numa política personalizada Azure AD B2C](restful-technical-profile.md).