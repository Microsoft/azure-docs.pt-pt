---
title: API colaboração B2B e personalização - Diretório Ativo Azure
description: A colaboração do B2B Azure Active Directory suporta as relações entre empresas, permitindo a parceiros de negócios acederem, seletivamente, às suas aplicações empresariais
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 04/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a090ee3f9588ff6bff01e12db469bf04407a7fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263468"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Azure Ative Directory B2B colaboração API e personalização

Tivemos muitos clientes a dizer-nos que querem personalizar o processo de convite de uma forma que funcione melhor para as suas organizações. Com a nossa API, podes fazer isso. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Capacidades do convite API

A API oferece as seguintes capacidades:

1. Convide um utilizador externo com *qualquer* endereço de e-mail.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Personalize onde quer que os seus utilizadores aterrem depois de aceitarem o convite.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Opte por enviar o correio de convite padrão através de nós

    ```
    "sendInvitationMessage": true
    ```

   com uma mensagem para o destinatário que você pode personalizar

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. E opte por cc: pessoas que pretende manter informadas sobre o seu convite para este colaborador.

5. Ou personalize completamente o seu convite e o fluxo de trabalho de embarque, optando por não enviar notificações através do Azure AD.

    ```
    "sendInvitationMessage": false
    ```

   Neste caso, obtém-se um URL de redenção da API que pode incorporar num modelo de e-mail, IM ou outro método de distribuição à sua escolha.

6. Finalmente, se for administrador, pode optar por convidar o utilizador como membro.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Modelo de autorização

A API pode ser executada nos seguintes modos de autorização:

### <a name="app--user-mode"></a>App + Modo de utilizador

Neste modo, quem estiver a utilizar a API precisa de ter as permissões para ser criado convites B2B.

### <a name="app-only-mode"></a>Modo apenas app

No contexto apenas da aplicação, a aplicação precisa do Utilizador.Invite.All scope for the invitation to succeed.

Para mais informações, consulte:https://developer.microsoft.com/graph/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell

Pode utilizar o PowerShell para adicionar e convidar utilizadores externos para uma organização facilmente. Crie um convite utilizando o cmdlet:

```powershell
New-AzureADMSInvitation
```

Pode utilizar as seguintes opções:

* -Nome de visualização userdisplay
* -Endereço de email do UserUser
* -Enviar Mensagem de Convite
* -InvitedUserMessageInfo

### <a name="invitation-status"></a>Estatuto do convite

Depois de enviar um convite a um utilizador externo, pode utilizar o cmdlet **Get-AzureADUser** para ver se o aceitaram. As seguintes propriedades do Get-AzureADUser são povoadas quando um utilizador externo é enviado um convite:

* **O UserState** indica se o convite é **Aceitação Pendente** ou **Aceito**.
* **UserStateChangedOn** mostra a marca de tempo para a mais recente alteração na propriedade **userState.**

Pode utilizar a opção **Filter** para filtrar os resultados pelo **UserState**. O exemplo abaixo mostra como filtrar resultados para mostrar apenas utilizadores que tenham um convite pendente. O exemplo também mostra a opção **Format-List,** que permite especificar as propriedades a exibir. 
 

```powershell
Get-AzureADUser -Filter "UserState eq 'PendingAcceptance'" | Format-List -Property DisplayName,UserPrincipalName,UserState,UserStateChangedOn
```

> [!NOTE]
> Certifique-se de que tem a versão mais recente do módulo PowerShell AzureAD ou do módulo PowerShell AzureADPreview. 

## <a name="see-also"></a>Consulte também

Confira a referência da [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)API do convite em .

## <a name="next-steps"></a>Passos seguintes

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Os elementos do convite de colaboração B2B enviar e-mail](invitation-email-elements.md)
- [Resgate de convite de colaboração B2B](redemption-experience.md)
- [Adicione utilizadores de colaboração B2B sem convite](add-user-without-invite.md)