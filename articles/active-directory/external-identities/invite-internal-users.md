---
title: Convidar utilizadores internos para a colaboração B2B - Azure AD
description: Se tiver contas internas de utilizador para parceiros, distribuidores, fornecedores, fornecedores e outros hóspedes, pode alterar para a colaboração Azure AD B2B convidando-os a iniciar sessão com as suas próprias credenciais externas ou login. Utilize o PowerShell ou o convite do Microsoft Graph API.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: c03c2c55988df04cc45ef4a1d66d959513c1626d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87909327"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>Convidar utilizadores internos para a colaboração B2B

> [!NOTE]
> Convidar utilizadores internos a utilizarem a colaboração B2B é uma funcionalidade de pré-visualização pública do Azure Ative Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Antes da disponibilidade da colaboração Azure AD B2B, as organizações poderiam colaborar com distribuidores, fornecedores, fornecedores e outros utilizadores convidados, estabelecendo credenciais internas para eles. Se tiver utilizadores convidados internos como este, pode convidá-los a usar a colaboração B2B para que possa usufruir dos benefícios do Azure AD B2B. Os seus utilizadores convidados B2B poderão usar as suas próprias identidades e credenciais para iniciar scontabilidade, e não precisará de manter senhas ou gerir ciclos de vida da conta.

O envio de um convite para uma conta interna existente permite-lhe reter o ID, UPN, membros do grupo e atribuições de aplicações. Não precisa de apagar manualmente e voltar a convidar o utilizador ou reatribuir recursos. Para convidar o utilizador, utilizará o convite da API para passar tanto o objeto interno do utilizador como o endereço de e-mail do utilizador convidado juntamente com o convite. Quando o utilizador aceita o convite, o serviço B2B altera o objeto interno do utilizador existente para um utilizador B2B. Para a frente, o utilizador deve iniciar sôms para os serviços de recursos na nuvem utilizando as suas credenciais B2B. Eles ainda podem usar as suas credenciais internas para aceder em recursos do local, mas você pode impedi-lo repondo ou alterando a palavra-passe na conta interna.

> [!NOTE]
> O convite é só de ida. Pode convidar utilizadores internos a utilizarem a colaboração B2B, mas não pode remover as credenciais B2B uma vez adicionadas. Para voltar a alterar o utilizador para um utilizador interno, terá de eliminar o objeto do utilizador e criar um novo.

Enquanto em pré-visualização pública, o método descrito neste artigo para convidar utilizadores internos para a colaboração B2B não pode ser usado nestes casos:

- O utilizador interno já foi designado uma licença de Troca.
- O utilizador é de um domínio que é criado para federação direta no seu diretório.
- O utilizador interno é uma conta apenas na nuvem, e a sua conta principal não está no Azure AD.

Nestes casos, se o utilizador interno tiver de ser alterado para um utilizador B2B, deve eliminar a conta interna e enviar ao utilizador um convite para colaboração B2B.

**Utilizadores sincronizados no local**: Para contas de utilizador sincronizadas entre as instalações e a nuvem, o diretório no local continua a ser a fonte de autoridade depois de serem convidados a utilizar a colaboração B2B. Quaisquer alterações que efetuar na conta no local sincronizarão a conta na nuvem, incluindo desativar ou apagar a conta. Por isso, não é possível impedir que o utilizador assine na sua conta no local, mantendo a sua conta na nuvem simplesmente eliminando a conta no local. Em vez disso, pode definir a palavra-passe da conta no local para um GUID aleatório ou outro valor desconhecido.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>Como convidar utilizadores internos para a colaboração B2B

Pode utilizar o PowerShell ou o convite da API para enviar um convite B2B ao utilizador interno. Certifique-se de que o endereço de e-mail que pretende utilizar para o convite está definido como o endereço de e-mail externo no objeto interno do utilizador.

- Para um utilizador apenas na nuvem, utilize o endereço de e-mail na propriedade User.OtherMails para o convite.
- Para um utilizador sincronizado no local, deve utilizar o valor na propriedade User.Mail para o convite.
- O domínio na propriedade do utilizador Mail deve coincidir com a conta que estão a usar para iniciar scontabilidade. Caso contrário, alguns serviços como o Teams não poderão autenticar o utilizador.

Por padrão, o convite enviará ao utilizador um e-mail informando-os de que foram convidados, mas você pode suprimir este e-mail e enviar o seu próprio.

> [!NOTE]
> Para enviar o seu próprio e-mail ou outra comunicação, pode utilizar New-AzureADMSInvitation com -SendInvitationMessage:$false para convidar os utilizadores em silêncio e, em seguida, enviar a sua própria mensagem de e-mail para o utilizador convertido. Consulte [a Azure AD B2B colaboração API e personalização.](customize-invitation-api.md)

## <a name="use-powershell-to-send-a-b2b-invitation"></a>Use o PowerShell para enviar um convite B2B

Utilize o seguinte comando para convidar o utilizador para a colaboração B2B:

```powershell
Uninstall-Module AzureADPreview
Install-Module AzureADPreview
$ADGraphUser = Get-AzureADUser -objectID "UPN of Internal User"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>Use o convite da API para enviar um convite B2B

A amostra abaixo ilustra como chamar a API convite para convidar um utilizador interno como utilizador B2B.

```json
POST https://graph.microsoft.com/v1.0/invitations
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
        "id": "<<ID for the user you want to convert>>"
    }
}
```

A resposta à API é a mesma que obtém quando convida um novo utilizador convidado para o diretório.

## <a name="next-steps"></a>Passos seguintes

- [Resgate de convite de colaboração B2B](redemption-experience.md)
