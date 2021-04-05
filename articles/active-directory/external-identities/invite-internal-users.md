---
title: Convidar utilizadores internos para a colaboração B2B - Azure AD
description: Se tiver contas internas de utilizador para parceiros, distribuidores, fornecedores, fornecedores e outros hóspedes, pode alterar para a colaboração Azure AD B2B convidando-os a iniciar sessão com as suas próprias credenciais externas ou login. Utilize o PowerShell ou o convite do Microsoft Graph API.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 802307a21873d15242c2e387ec0defe35f50bb20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99576435"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>Convidar utilizadores internos para a colaboração B2B

Antes da disponibilidade da colaboração Azure AD B2B, as organizações poderiam colaborar com distribuidores, fornecedores, fornecedores e outros utilizadores convidados, estabelecendo credenciais internas para eles. Se tiver utilizadores convidados internos como estes, pode convidá-los a usar a colaboração B2B. Estes utilizadores convidados B2B poderão usar as suas próprias identidades e credenciais para iniciar scontabilidade, e não precisará de manter senhas ou gerir ciclos de vida de conta.

O envio de um convite para uma conta interna existente permite-lhe reter o ID, UPN, membros do grupo e atribuições de aplicações. Não precisa de apagar manualmente e voltar a convidar o utilizador ou reatribuir recursos. Para convidar o utilizador, utiliza o convite da API para passar tanto o objeto interno do utilizador como o endereço de e-mail do utilizador convidado juntamente com o convite. Quando o utilizador aceita o convite, o serviço B2B altera o objeto interno do utilizador existente para um utilizador B2B. Para a frente, o utilizador deve iniciar sôms para os serviços de recursos na nuvem utilizando as suas credenciais B2B.

## <a name="things-to-consider"></a>Coisas a considerar

- **Acesso aos recursos no local**: Após o convite do utilizador para a colaboração B2B, podem ainda utilizar as suas credenciais internas para aceder aos recursos no local. Pode evitar isso repondo ou alterando a palavra-passe na conta interna. A exceção é [a autenticação de senha de e-mail única;](one-time-passcode.md) se o método de autenticação do utilizador for alterado para uma senha única, não poderão mais utilizar as suas credenciais internas.

- **Faturação**: Esta função não altera o UserType para o utilizador, pelo que não muda automaticamente o modelo de faturação do utilizador para [o preço do utilizador ativo mensal (MAU).](external-identities-pricing.md) Para ativar os preços MAU para o utilizador, altere o UserType para o utilizador para `guest` . Note também que o seu inquilino Azure AD deve estar [ligado a uma subscrição da Azure](external-identities-pricing.md#link-your-azure-ad-tenant-to-a-subscription) para ativar a faturação MAU.

- **O convite é de ida**: Pode convidar utilizadores internos a utilizarem a colaboração B2B, mas não é possível remover as credenciais B2B uma vez adicionadas. Para voltar a alterar o utilizador para um utilizador interno, terá de eliminar o objeto do utilizador e criar um novo.

- **Equipas**: Quando o utilizador acede às Equipas utilizando as suas credenciais externas, o seu inquilino não estará disponível inicialmente no selecionador de inquilinos das Equipas. O utilizador pode aceder às Equipas utilizando um URL que contenha o contexto do inquilino, por exemplo: `https://team.microsoft.com/?tenantId=<TenantId>` . Depois disso, o inquilino ficará disponível no selecionador de inquilinos das Equipas.

- **Utilizadores sincronizados no local**: Para contas de utilizador sincronizadas entre as instalações e a nuvem, o diretório no local continua a ser a fonte de autoridade depois de serem convidados a utilizar a colaboração B2B. Quaisquer alterações que efetuar na conta no local sincronizarão a conta na nuvem, incluindo desativar ou apagar a conta. Por isso, não é possível impedir que o utilizador assine na sua conta no local, mantendo a sua conta na nuvem simplesmente eliminando a conta no local. Em vez disso, pode definir a palavra-passe da conta no local para um GUID aleatório ou outro valor desconhecido.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>Como convidar utilizadores internos para a colaboração B2B

Pode utilizar o PowerShell ou o convite da API para enviar um convite B2B ao utilizador interno. Certifique-se de que o endereço de e-mail que pretende utilizar para o convite está definido como o endereço de e-mail externo no objeto interno do utilizador.

- Tem de utilizar o endereço de e-mail na propriedade User.Mail para o convite.
- O domínio na propriedade do utilizador Mail deve coincidir com a conta que estão a usar para iniciar scontabilidade. Caso contrário, alguns serviços como o Teams não poderão autenticar o utilizador.

Por padrão, o convite enviará ao utilizador um e-mail informando-os de que foram convidados, mas você pode suprimir este e-mail e enviar o seu próprio.

> [!NOTE]
> Para enviar o seu próprio e-mail ou outra comunicação, pode usar `New-AzureADMSInvitation` `-SendInvitationMessage:$false` para convidar os utilizadores em silêncio e, em seguida, enviar a sua própria mensagem de e-mail para o utilizador convertido. Consulte [a Azure AD B2B colaboração API e personalização.](customize-invitation-api.md)

## <a name="use-powershell-to-send-a-b2b-invitation"></a>Use o PowerShell para enviar um convite B2B

Você precisará do módulo Azure AD PowerShell versão 2.0.2.130 ou mais tarde. Utilize o seguinte comando para atualizar para o mais recente módulo AzureAD PowerShell e convidar o utilizador interno para a colaboração B2B:

```powershell
Uninstall-Module AzureAD
Install-Module AzureAD
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
