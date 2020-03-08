---
title: Verificações de e-mail personalizadas
titleSuffix: Azure AD B2C
description: Saiba como personalizar o e-mail de verificação enviado aos seus clientes quando se inscrevem para utilizar as suas aplicações ativadas pelo Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6cc0508a63f26b955ac5e0ebf3ef58a184a35997
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671639"
---
# <a name="custom-email-verification-in-azure-active-directory-b2c"></a>Verificação personalizada de e-mail no Diretório Ativo Azure B2C

Utilize e-mails personalizados no Azure Ative Directory B2C (Azure AD B2C) para enviar e-mails personalizados aos utilizadores que se inscrevam para utilizar as suas aplicações. Ao utilizar o [DisplayControls](display-controls.md) (atualmente em pré-visualização) e um fornecedor de e-mail de terceiros, pode utilizar o seu próprio modelo de e-mail e *A partir:* endereço e assunto, bem como apoiar as definições de localização e senha personalizada (OTP).

A verificação personalizada de e-mail requer a utilização de um fornecedor de e-mail de terceiros como [sendGrid](https://sendgrid.com) ou [SparkPost,](https://sparkpost.com)um REST API personalizado ou qualquer fornecedor de e-mail baseado em HTTP (incluindo o seu próprio). Este artigo descreve a criação de uma solução que utiliza a SendGrid.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>Criar uma conta SendGrid

Se ainda não tiver uma, comece por criar uma conta SendGrid (os clientes Azure podem desbloquear 25.000 e-mails gratuitos todos os meses). Para obter instruções de configuração, consulte a secção [Criar uma Conta SendGrid](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) de [Como enviar e-mail utilizando sendGrid com Azure](../sendgrid-dotnet-how-to-send-email.md).

Certifique-se de que completa a secção em que [cria uma tecla API SendGrid](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key). Grave a tecla API para utilização num passo posterior.

## <a name="create-azure-ad-b2c-policy-key"></a>Criar a chave política Azure AD B2C

Em seguida, guarde a tecla SendGrid API numa chave de política Azure AD B2C para as suas políticas de referência.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro de **subscrição Diretório +** no menu superior e escolha o seu diretório Azure AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Na página 'Visão Geral', selecione Quadro de **Experiência de Identidade**.
1. Selecione **Teclas de política** e, em seguida, selecione **Adicionar**.
1. Para **opções,** escolha `Manual`.
1. Introduza um **nome** para a chave política. Por exemplo, `SendGridSecret`. O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave.
1. Em **Segredo,** insira o seu segredo de cliente que gravou anteriormente.
1. Para **a utilização da chave,** selecione `Signature`.
1. Selecione **Criar**.

## <a name="create-sendgrid-template"></a>Criar modelo SendGrid

Com uma conta SendGrid criada e chave SendGrid API armazenada numa chave política Azure AD B2C, crie um [modelo de transação dinâmico](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)SendGrid .

1. No site SendGrid, abra a página de [modelos transacionais](https://sendgrid.com/dynamic_templates) e selecione **Criar Modelo**.
1. Introduza um nome de modelo único como `Verification email` e, em seguida, selecione **Guardar**.
1. Para começar a editar o seu novo modelo, selecione **Adicionar Versão**.
1. Selecione Editor de **Código** **e,** em seguida, Continue .
1. No editor HTML, cola o modelo HTML ou usa o teu próprio. Os parâmetros `{{otp}}` e `{{email}}` serão substituídos dinamicamente pelo valor de senha única e pelo endereço de e-mail do utilizador.

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

1. Expandir **Definições** à esquerda e, para O Assunto do **E-mail,** insira `{{subject}}`.
1. Selecione **modelo de guardar**.
1. Volte à página de **Modelos Transacionais** selecionando a seta traseira.
1. Grave a **identificação** do modelo que criou para ser usada num passo posterior. Por exemplo, `d-989077fbba9746e89f3f6411f596fb96`. Especifica este ID quando [adiciona a transformação de sinistros.](#add-the-claims-transformation)

## <a name="add-azure-ad-b2c-claim-types"></a>Adicione tipos de reclamação Azure AD B2C

Na sua política, adicione os seguintes tipos de reclamação ao elemento `<ClaimsSchema>` dentro `<BuildingBlocks>`.

Estes tipos de reclamações são necessários para gerar e verificar o endereço de e-mail usando um código de senha única (OTP).

```XML
<ClaimType Id="Otp">
  <DisplayName>Secondary One-time password</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="sendGridReqBody">
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

Em seguida, você precisa de uma transformação de reclamações para obter uma reivindicação de cadeia JSON que será o corpo do pedido enviado para SendGrid.

A estrutura do objeto JSON é definida pelos IDs na notação de pontos dos InputParameters e pelos Tipos de Reclamações de Transformação das InputClaims. Os números na notação do ponto implicam matrizes. Os valores provêm dos valores dos InputClaims e das propriedades "Valor" dos InputParameters. Para obter mais informações sobre as transformações da JSON, consulte a [JSON alega transformações](json-transformations.md).

Adicione a seguinte transformação de reclamações ao elemento `<ClaimsTransformations>` dentro `<BuildingBlocks>`. Faça as seguintes atualizações para a transformação de sinistros XML:

* Atualize o valor `template_id` InputParameter com o ID do modelo transacional SendGrid que criou anteriormente no [modelo Create SendGrid](#create-sendgrid-template).
* Atualize o valor de endereço `from.email`. Utilize um endereço de e-mail válido para evitar que o e-mail de verificação seja marcado como spam.
* Atualize o valor do parâmetro de entrada da linha de assunto `personalizations.0.dynamic_template_data.subject` com uma linha de assunto adequada para a sua organização.

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
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
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="add-datauri-content-definition"></a>Adicionar definição de conteúdo DataUri

Abaixo das transformações de sinistros dentro de `<BuildingBlocks>`, adicione o seguinte [ContentDefinition](contentdefinitions.md) para fazer referência aos dados URI da versão 2.0.0:

```XML
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>Criar um DisplayControl

Um controlo de visualização é utilizado para verificar o endereço de e-mail com um código de verificação que é enviado ao utilizador.

Este controlo de exibição de exemplo está configurado para:

1. Recolher o tipo de reclamação de endereço `email` do utilizador.
1. Aguarde que o utilizador forneça o `verificationCode` tipo de reclamação com o código enviado ao utilizador.
1. Volte a colocar o `email` ao perfil técnico autoafirmado que tem uma referência a este controlo de exibição.
1. Utilizando a `SendCode` ação, gere um código OTP e envie um e-mail com o código OTP para o utilizador.

![Enviar ação de e-mail código de verificação](media/custom-email/display-control-verification-email-action-01.png)

De acordo com as definições de conteúdo, ainda dentro `<BuildingBlocks>`, adicione o seguinte [DisplayControl](display-controls.md) do tipo [VerificationControl](display-control-verification.md) à sua política.

```XML
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
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
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

O perfil técnico `GenerateOtp` gera um código para o endereço de e-mail. O perfil técnico `VerifyOtp` verifica o código associado ao endereço de e-mail. Pode alterar a configuração do formato e a expiração da senha única. Para obter mais informações sobre os perfis técnicos da OTP, consulte [Definir um perfil técnico de senha única](one-time-password-technical-profile.md).

Adicione os seguintes perfis técnicos ao elemento `<ClaimsProviders>`.

```XML
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

## <a name="add-a-rest-api-technical-profile"></a>Adicione um perfil técnico REST API

Este perfil técnico REST API gera o conteúdo de e-mail (utilizando o formato SendGrid). Para obter mais informações sobre perfis técnicos RESTful, consulte [Definir um perfil técnico RESTful](restful-technical-profile.md).

Tal como acontece com os perfis técnicos da OTP, adicione os seguintes perfis técnicos ao elemento `<ClaimsProviders>`.

```XML
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendGrid">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>Faça uma referência ao DisplayControl

No passo final, adicione uma referência ao DisplayControl que criou. Substitua o seu perfil técnico `LocalAccountSignUpWithLogonEmail` autoafirmado com o seguinte se tiver utilizado uma versão anterior da política Azure AD B2C. Este perfil técnico utiliza `DisplayClaims` com referência ao DisplayControl.

Para mais informações, consulte o [perfil técnico autoafirmado](restful-technical-profile.md) e o [DisplayControl](display-controls.md).

```XML
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

Para localizar o e-mail, tem de enviar cordas localizadas para a SendGrid ou para o seu fornecedor de e-mail. Por exemplo, localizar o sujeito de e-mail, corpo, sua mensagem de código ou assinatura do e-mail. Para isso, pode utilizar a Transformação de [StringsTransformation GetLocalized](string-transformations.md) para copiar cordas localizadas em tipos de reclamação. No `GenerateSendGridRequestBody` a transformação de reivindicações, que gera a carga útil JSON, utiliza alegações de entrada que contêm as cordas localizadas.

1. Na sua política, defina as seguintes reclamações de cordas: sujeito, mensagem, codeIntro e assinatura.
1. Defina uma [GetLocalizedStringsTransformation](string-transformations.md) reclama transformação para substituir valores de cordas localizados nas reclamações a partir do passo 1.
1. Altere a transformação de `GenerateSendGridRequestBody` alega que utilize reclamações de entrada com o seguinte corte XML.
1. Atualize o seu modelo SendGrind para utilizar parâmetros dinâmicos no lugar de todas as cordas que serão localizadas pelo Azure AD B2C.

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
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
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="next-steps"></a>Passos seguintes

Pode encontrar um exemplo de uma política personalizada de verificação de e-mails no GitHub:

[Verificação personalizada de e-mail - DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)

Para obter informações sobre a utilização de uma API REST personalizada ou de qualquer fornecedor de e-mail SMTP baseado em HTTP, consulte [Definir um perfil técnico RESTful numa política personalizada Azure AD B2C](restful-technical-profile.md).
