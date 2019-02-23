---
title: B2B colaboração API e personalização - Azure Active Directory | Documentos da Microsoft
description: A colaboração do B2B Azure Active Directory suporta as relações entre empresas, permitindo a parceiros de negócios acederem, seletivamente, às suas aplicações empresariais
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 04/11/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbeffd012d2d2445d4a9a927981728e1bbe43e97
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56670575"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Colaboração do Azure Active Directory B2B API e personalização

Tivemos muitos clientes, diga-nos de que pretende personalizar o processo de convite de uma forma que funciona melhor para suas organizações. Com a nossa API, pode fazer exatamente isso. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Capacidades do convite, API
A API oferece as seguintes capacidades:

1. Convidar um utilizador externo com *qualquer* endereço de e-mail.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Personalize onde pretende que os seus utilizadores para firmar assim que aceitarem o convite.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Escolher enviar o e-mail de convite padrão por meio de nós

    ```
    "sendInvitationMessage": true
    ```

  com uma mensagem ao destinatário que pode personalizar

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. E optar por cc: as pessoas que pretende manter a par de tudo sobre sua convidar esta funcionário.

5. Ou personalizar totalmente o seu convite e o fluxo de trabalho de integração ao escolher não enviar notificações através do Azure AD.

    ```
    "sendInvitationMessage": false
    ```

  Neste caso, obtém um URL de resgate da API que pode incorporar um modelo de e-mail, mensagem Instantânea ou outro método de distribuição da sua preferência.

6. Por fim, se for um administrador, é possível convidar o utilizador como membro.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Modelo de autorização
A API pode ser executada nos seguintes modos de autorização:

### <a name="app--user-mode"></a>Aplicação + modo de utilizador
Neste modo, quem está a utilizar as necessidades de API para que as permissões para ser criar convites de B2B.

### <a name="app-only-mode"></a>Modo só de aplicação
No contexto de apenas de aplicação, a aplicação tem do âmbito de User.Invite.All para o convite com êxito.

Para obter mais informações, consulte: https://developer.microsoft.com/graph/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell
Pode utilizar o PowerShell para adicionar e convidar utilizadores externos para uma organização facilmente. Crie um convite através do cmdlet:

```
New-AzureADMSInvitation
```

Pode utilizar as seguintes opções:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

### <a name="invitation-status"></a>Estado de convite

Depois de enviar um convite um utilizador externo, pode utilizar o **Get-AzureADUser** cmdlet para ver se eles foram aceites. As seguintes propriedades de Get-AzureADUser são preenchidas quando um utilizador externo é enviado um convite:

* **O userstate não** indica se o convite está **PendingAcceptance** ou **aceites**.
* **UserStateChangedOn** mostra o carimbo de hora para que a alteração mais recente para o **o userstate não** propriedade.

Pode utilizar o **filtro** opção para filtrar os resultados por **o userstate não**. O exemplo abaixo mostra como filtrar os resultados para mostrar apenas os utilizadores que têm um convite pendente. O exemplo também mostra a **Format-List** opção, o que lhe permite especificar as propriedades para apresentar. 
 
```
Get-AzureADUser -Filter "UserState eq 'PendingAcceptance'" | Format-List -Property DisplayName,UserPrincipalName,UserState,UserStateChangedOn
```

> [!NOTE]
> Certifique-se de que tem a versão mais recente do módulo do AzureAD PowerShell ou do módulo do AzureADPreview PowerShell. 

## <a name="see-also"></a>Consulte também

Veja a referência de API de convite na [ https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation ](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

## <a name="next-steps"></a>Passos Seguintes

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Os elementos da mensagem de e-mail de convite de colaboração B2B](invitation-email-elements.md)
- [Resgate de convite de colaboração B2B](redemption-experience.md)
- [Adicionar utilizadores de colaboração do B2B sem convite](add-user-without-invite.md)

