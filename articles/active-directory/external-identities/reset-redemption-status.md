---
title: Redefinir o estado de resgate de um utilizador convidado - Azure AD
description: Saiba como redefinir o estado de resgate de convites para um Azure Ative Directory B2B utilizadores convidados em Identidades Externas Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/06/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5bfce7ef2621cbe3bbbfdd95bf9a75e427c8cbd
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531874"
---
# <a name="reset-redemption-status-for-a-guest-user-preview"></a>Redefinir o estado de resgate de um utilizador convidado (Pré-visualização)

Depois de um utilizador convidado ter resgatado o seu convite para colaboração B2B, pode haver momentos em que terá de atualizar as suas informações de inscrição, por exemplo, quando:

- O utilizador quer fazer o seu sismo usando um fornecedor de e-mail e identidade diferente
- A conta para o utilizador no seu inquilino foi eliminada e recriada
- O utilizador mudou-se para outra empresa, mas ainda precisa do mesmo acesso aos seus recursos.
- As responsabilidades do utilizador foram transmitidas a outro utilizador

Para gerir estes cenários anteriormente, teve de eliminar manualmente a conta do utilizador convidado do seu diretório e voltar a convidar o utilizador. Agora pode utilizar o PowerShell ou o convite do Microsoft Graph API para redefinir o estado de resgate do utilizador e voltar a convidar o utilizador, mantendo o ID do objeto do utilizador, as filiações em grupo e as atribuições de aplicações. Quando o utilizador resgata o novo convite, a UPN do utilizador não muda, mas o nome de inscrição do utilizador muda para o novo e-mail. O utilizador pode posteriormente iniciar sposição através do novo e-mail ou de um e-mail que adicionou à `otherMails` propriedade do objeto do utilizador.

## <a name="reset-the-email-address-used-for-sign-in"></a>Redefinir o endereço de e-mail utilizado para o s-in

Se um utilizador quiser iniciar susutá-lo usando um e-mail diferente:

1. Certifique-se de que o novo endereço de e-mail é adicionado à `mail` propriedade ou propriedade do objeto do `otherMails` utilizador. 
2.  Substitua o endereço de e-mail na `InvitedUserEmailAddress` propriedade pelo novo endereço de e-mail.
3. Utilize um dos métodos abaixo para redefinir o estado de resgate do utilizador.

> [!NOTE]
>Durante a pré-visualização pública, temos duas recomendações:
>- Quando estiver a redefinir o endereço de e-mail do utilizador para um novo endereço, recomendamos a definição da `mail` propriedade. Desta forma, o utilizador pode resgatar o convite ao inscrever-se no seu diretório, além de utilizar o link de resgate no convite.
>- Quando estiver a redefinir o estado de um utilizador convidado B2B, certifique-se de que o faz no contexto do utilizador. Atualmente, as chamadas apenas para aplicações não são suportadas.
>
## <a name="use-powershell-to-reset-redemption-status"></a>Utilize o PowerShell para redefinir o estado de resgate

Instale o mais recente módulo AzureADPreview PowerShell e crie um novo convite com `InvitedUserEmailAddress` definido para o novo endereço de e-mail, e definido para `ResetRedemption` `true` .

```powershell  
Uninstall-Module AzureADPreview 
Install-Module AzureADPreview 
Connect-AzureAD 
$ADGraphUser = Get-AzureADUser -objectID "UPN of User to Reset"  
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId 
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser -ResetRedemption $True 
```

## <a name="use-microsoft-graph-api-to-reset-redemption-status"></a>Utilize a API do Microsoft Graph para redefinir o estado de resgate

Utilizando o convite do [Microsoft Graph API,](/graph/api/resources/invitation?view=graph-rest-beta&preserve-view=true)deite a `resetRedemption` propriedade e `true` especifique o novo endereço de e-mail na `invitedUserEmailAddress` propriedade.

```json
POST https://graph.microsoft.com/beta/invitations  
Authorization: Bearer eyJ0eX...  
ContentType: application/json  
{  
   "invitedUserEmailAddress": "<<external email>>",  
   "sendInvitationMessage": true,  
   "invitedUserMessageInfo": {  
      "messageLanguage": "en-US",  
      "ccRecipients": [  
         {  
            "emailAddress": {  
               "name": null,  
               "address": "<<optional additional notification email>>"  
            }  
         } 
      ],  
      "customizedMessageBody": "<<custom message>>"  
},  
"inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",  
"invitedUser": {  
   "id": "<<ID for the user you want to reset>>"  
}, 
"resetRedemption": true 
}
```

## <a name="next-steps"></a>Passos seguintes

- [Adicione utilizadores de colaboração B2B do Azure Ative Directory através da utilização do PowerShell](customize-invitation-api.md#powershell)
- [Propriedades de um utilizador convidado Azure AD B2B](user-properties.md)
