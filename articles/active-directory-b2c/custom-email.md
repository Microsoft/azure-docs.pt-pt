---
title: Verificações de email personalizadas
titleSuffix: Azure AD B2C
description: Saiba como personalizar o email de verificação enviado para seus clientes quando eles se inscrevem para usar seus aplicativos habilitados para Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3d9316f42c8d0ac5b44cda2e484ca4c92110813d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479153"
---
# <a name="custom-email-verification-in-azure-active-directory-b2c"></a>Verificação de email personalizada no Azure Active Directory B2C

Use o email personalizado no Azure Active Directory B2C (Azure AD B2C) para enviar email personalizado aos usuários que se inscrevem para usar seus aplicativos. Usando o [DisplayControls](display-controls.md) (atualmente em visualização) e um provedor de email de terceiros, você pode usar seu próprio modelo de email e *de:* endereço e assunto, bem como a localização de suporte e configurações de OTP (senha de uso único) personalizadas.

A verificação de email personalizada requer o uso de um provedor de email de terceiros, como [SendGrid](https://sendgrid.com) ou [SPARKPOST](https://sparkpost.com), uma API REST personalizada ou qualquer provedor de email baseado em http (incluindo o seu próprio). Este artigo descreve como configurar uma solução que usa SendGrid.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>Criar uma conta do SendGrid

Se você ainda não tiver uma, comece Configurando uma conta do SendGrid (os clientes do Azure podem desbloquear 25.000 emails gratuitos por mês). Para obter instruções de instalação, consulte a seção [criar uma conta do SendGrid](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) de [como enviar email usando o SendGrid com o Azure](../sendgrid-dotnet-how-to-send-email.md).

Certifique-se de concluir a seção na qual você [cria uma chave de API do SendGrid](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key). Registre a chave de API para uso em uma etapa posterior.

## <a name="create-azure-ad-b2c-policy-key"></a>Criar chave de política de Azure AD B2C

Em seguida, armazene a chave de API SendGrid em uma chave de política de Azure AD B2C para que suas políticas façam referência.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C. Selecione o **diretório +** filtro de assinatura no menu superior e escolha o diretório Azure ad B2C.
1. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
1. Na página Visão geral, selecione **Identity Experience Framework**.
1. Selecione **chaves de política** e, em seguida, selecione **Adicionar**.
1. Para **Opções**, escolha `Manual`.
1. Insira um **nome** para a chave de política. Por exemplo, `SendGridSecret`. O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave.
1. Em **segredo**, insira o segredo do cliente que você registrou anteriormente.
1. Para **uso de chave**, selecione `Signature`.
1. Selecione **Criar**.

## <a name="create-sendgrid-template"></a>Criar modelo de SendGrid

Com uma conta do SendGrid criada e a chave de API SendGrid armazenada em uma chave de política Azure AD B2C, crie um [modelo transacional dinâmico](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)SendGrid.

1. No site do SendGrid, abra a página [modelos transacionais](https://sendgrid.com/dynamic_templates) e selecione **criar modelo**.
1. Insira um nome de modelo exclusivo como `Verification email` e, em seguida, selecione **salvar**.
1. Para começar a editar seu novo modelo, selecione **Adicionar versão**.
1. Selecione **Editor de código** e **continue**.
1. No editor de HTML, Cole o modelo HTML a seguir ou use o seu próprio. Os parâmetros `{{otp}}` e `{{email}}` serão substituídos dinamicamente pelo valor de senha de uso único e pelo endereço de email do usuário.

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

1. Expanda **configurações** à esquerda e, para **assunto de Email**, digite `{{subject}}`.
1. Selecione **salvar modelo**.
1. Retorne à página de **modelos transacionais** selecionando a seta voltar.
1. Registre a **ID** do modelo criado para uso em uma etapa posterior. Por exemplo, `d-989077fbba9746e89f3f6411f596fb96`. Você especifica essa ID ao [Adicionar a transformação declarações](#add-the-claims-transformation).

## <a name="add-azure-ad-b2c-claim-types"></a>Adicionar tipos de declaração de Azure AD B2C

Em sua política, adicione os seguintes tipos de declaração ao elemento `<ClaimsSchema>` dentro de `<BuildingBlocks>`.

Esses tipos de declarações são necessários para gerar e verificar o endereço de email usando um código de OTP (senha de uso único).

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

## <a name="add-the-claims-transformation"></a>Adicionar a transformação de declarações

Em seguida, você precisa de uma transformação declarações para gerar uma declaração de cadeia de caracteres JSON que será o corpo da solicitação enviada para SendGrid.

A estrutura do objeto JSON é definida pelas IDs na notação de ponto de InputParameters e TransformationClaimTypes do InputClaims. Os números na notação de ponto implicam matrizes. Os valores são provenientes dos valores de InputClaims e das propriedades de "valor" de InputParameters. Para obter mais informações sobre transformações de declarações JSON, consulte [transformações de declarações JSON](json-transformations.md).

Adicione a transformação de declarações a seguir ao elemento `<ClaimsTransformations>` dentro de `<BuildingBlocks>`. Faça as seguintes atualizações para o XML de transformação de declarações:

* Atualize o valor de `template_id` InputParameter com a ID do modelo transacional SendGrid criado anteriormente no [modelo criar SendGrid](#create-sendgrid-template).
* Atualize o valor do endereço `from.email`. Use um endereço de email válido para ajudar a impedir que o email de verificação seja marcado como spam.
* Atualize o valor do `personalizations.0.dynamic_template_data.subject` parâmetro de entrada da linha de assunto com uma linha de assunto apropriada para sua organização.

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

Abaixo das transformações de declarações no `<BuildingBlocks>`, adicione o seguinte [ContentDefinition](contentdefinitions.md) para fazer referência ao URI de dados da versão 2.0.0:

```XML
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>Criar um DisplayControl

Um controle de exibição de verificação é usado para verificar o endereço de email com um código de verificação que é enviado ao usuário.

Este exemplo de controle de exibição está configurado para:

1. Coletar o tipo de declaração de endereço `email` do usuário.
1. Aguarde até que o usuário forneça o tipo de declaração de `verificationCode` com o código enviado ao usuário.
1. Retorne o `email` de volta para o perfil técnico autodeclarado que tem uma referência a esse controle de exibição.
1. Usando a ação de `SendCode`, gere um código de OTP e envie um email com o código de OTP para o usuário.

![Enviar ação de email do código de verificação](media/custom-email/display-control-verification-email-action-01.png)

Em definições de conteúdo, ainda dentro de `<BuildingBlocks>`, adicione o seguinte [DisplayControl](display-controls.md) do tipo [VerificationControl](display-control-verification.md) à sua política.

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

## <a name="add-otp-technical-profiles"></a>Adicionar perfis técnicos de OTP

O perfil técnico de `GenerateOtp` gera um código para o endereço de email. O perfil técnico `VerifyOtp` verifica o código associado ao endereço de email. Você pode alterar a configuração do formato e a expiração da senha de uso único. Para obter mais informações sobre perfis técnicos de OTP, consulte [definir um perfil técnico de senha de uso único](one-time-password-technical-profile.md).

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
        <Item Key="UserMessage.VerificationHasExpired">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessage.MaxRetryAttemped">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessage.InvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessage.ServerError">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
      </InputClaims>
    </TechnicalProfile>
   </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="add-a-rest-api-technical-profile"></a>Adicionar um perfil técnico da API REST

Esse perfil técnico da API REST gera o conteúdo do email (usando o formato SendGrid). Para obter mais informações sobre perfis técnicos RESTful, consulte [definir um perfil técnico RESTful](restful-technical-profile.md).

Assim como os perfis técnicos de OTP, adicione os seguintes perfis técnicos ao elemento `<ClaimsProviders>`.

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

## <a name="make-a-reference-to-the-displaycontrol"></a>Fazer uma referência para o DisplayControl

Na etapa final, adicione uma referência para o DisplayControl que você criou. Substitua seu perfil técnico autodeclarado `LocalAccountSignUpWithLogonEmail` existente pelo seguinte se você tiver usado uma versão anterior da política de Azure AD B2C. Este perfil técnico usa `DisplayClaims` com uma referência para o DisplayControl.

Para obter mais informações, consulte [perfil técnico autodeclarado](restful-technical-profile.md) e [DisplayControl](display-controls.md).

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

## <a name="next-steps"></a>Passos seguintes

Você pode encontrar um exemplo de uma política de verificação de email personalizada no GitHub:

[Verificação de email personalizada-DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)

Para obter informações sobre como usar uma API REST personalizada ou qualquer provedor de email SMTP baseado em HTTP, consulte [definir um perfil técnico RESTful em uma Azure ad B2C política personalizada](restful-technical-profile.md).
