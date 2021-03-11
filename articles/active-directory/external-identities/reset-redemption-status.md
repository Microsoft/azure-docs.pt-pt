---
title: Redefinir o estado de resgate de um utilizador convidado - Azure AD
description: Saiba como redefinir o estado de resgate de convites para um Azure Ative Directory B2B utilizadores convidados em Identidades Externas Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: dea13444a6bd18bd67f05d93a38af70b3b7a2368
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556320"
---
# <a name="reset-redemption-status-for-a-guest-user"></a>Redefinir o estado de resgate de um utilizador convidado

Depois de um utilizador convidado ter resgatado o seu convite para colaboração B2B, pode haver momentos em que terá de atualizar as suas informações de inscrição, por exemplo, quando:

- O utilizador quer fazer o seu sismo usando um fornecedor de e-mail e identidade diferente
- A conta para o utilizador no seu inquilino foi eliminada e recriada
- O utilizador mudou-se para outra empresa, mas ainda precisa do mesmo acesso aos seus recursos.
- As responsabilidades do utilizador foram transmitidas a outro utilizador

Para gerir estes cenários anteriormente, teve de eliminar manualmente a conta do utilizador convidado do seu diretório e voltar a convidar o utilizador. Agora pode utilizar o PowerShell ou o convite do Microsoft Graph API para redefinir o estado de resgate do utilizador e voltar a convidar o utilizador, mantendo o ID do objeto do utilizador, as filiações em grupo e as atribuições de aplicações. Quando o utilizador resgata o novo convite, a UPN do utilizador não muda, mas o nome de inscrição do utilizador muda para o novo e-mail. O utilizador pode posteriormente iniciar sposição através do novo e-mail ou de um e-mail que adicionou à `otherMails` propriedade do objeto do utilizador.

## <a name="use-powershell-to-reset-redemption-status"></a>Utilize o PowerShell para redefinir o estado de resgate

Instale o mais recente módulo AzureADPreview PowerShell e crie um novo convite com `InvitedUserEMailAddress` definido para o novo endereço de e-mail, e definido para `ResetRedemption` `true` .

```powershell  
Uninstall-Module AzureADPreview 
Install-Module AzureADPreview 
Connect-AzureAD 
$ADGraphUser = Get-AzureADUser -objectID "UPN of User to Reset"  
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId 
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser -ResetRedemption $True 
```

## <a name="use-microsoft-graph-api-to-reset-redemption-status"></a>Utilize a API do Microsoft Graph para redefinir o estado de resgate

Utilizando o convite do [Microsoft Graph API,](/graph/api/resources/invitation)deite a `resetRedemption` propriedade e `true` especifique o novo endereço de e-mail na `invitedUserEmailAddress` propriedade.

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
