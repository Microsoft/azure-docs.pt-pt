---
title: API de colaboração B2B e personalização-Azure Active Directory
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272867"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Azure Active Directory a API e a personalização da colaboração B2B

Tivemos muitos clientes nos dizer que desejam personalizar o processo de convite de uma maneira que funcione melhor para suas organizações. Com nossa API, você pode fazer exatamente isso. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Recursos da API de convite

A API oferece os seguintes recursos:

1. Convide um usuário externo com *qualquer* endereço de email.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Personalize o local em que você deseja que os usuários se pousem depois de aceitarem o convite.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Escolha enviar o email de convite padrão até nós

    ```
    "sendInvitationMessage": true
    ```

   com uma mensagem para o destinatário que você pode personalizar

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. E escolha CC: as pessoas que você deseja manter no loop sobre o convite deste colaborador.

5. Ou personalize completamente o convite e o fluxo de trabalho de integração escolhendo não enviar notificações por meio do Azure AD.

    ```
    "sendInvitationMessage": false
    ```

   Nesse caso, você obtém novamente uma URL de resgate da API que pode ser inserida em um modelo de email, mensagem instantânea ou outro método de distribuição de sua escolha.

6. Por fim, se você for um administrador, poderá optar por convidar o usuário como membro.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Modelo de autorização

A API pode ser executada nos seguintes modos de autorização:

### <a name="app--user-mode"></a>Aplicativo + modo de usuário

Nesse modo, quem está usando a API precisa ter as permissões para criar convites B2B.

### <a name="app-only-mode"></a>Modo somente aplicativo

No contexto somente do aplicativo, o aplicativo precisa do escopo user. Invite. All para que o convite tenha sucesso.

Para obter mais informações, consulte: https://developer.microsoft.com/graph/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell

Você pode usar o PowerShell para adicionar e convidar usuários externos a uma organização facilmente. Crie um convite usando o cmdlet:

```powershell
New-AzureADMSInvitation
```

Você pode usar as seguintes opções:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

### <a name="invitation-status"></a>Status do convite

Depois de enviar um convite a um usuário externo, você pode usar o cmdlet **Get-AzureADUser** para ver se ele o aceitou. As seguintes propriedades de Get-AzureADUser são populadas quando um usuário externo recebe um convite:

* **UserState** indica se o convite é **PendingAcceptance** ou **aceito**.
* **UserStateChangedOn** mostra o carimbo de data/hora para a alteração mais recente na propriedade **userState** .

Você pode usar a opção de **filtro** para filtrar os resultados por **userState**. O exemplo a seguir mostra como filtrar os resultados para mostrar apenas os usuários que têm um convite pendente. O exemplo também mostra a opção **Format-List** , que permite especificar as propriedades a serem exibidas. 
 

```powershell
Get-AzureADUser -Filter "UserState eq 'PendingAcceptance'" | Format-List -Property DisplayName,UserPrincipalName,UserState,UserStateChangedOn
```

> [!NOTE]
> Verifique se você tem a versão mais recente do módulo AzureAD do PowerShell ou do módulo AzureADPreview do PowerShell. 

## <a name="see-also"></a>Consulte também

Confira a referência da API de convite em [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

## <a name="next-steps"></a>Passos seguintes

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Os elementos do email de convite para colaboração B2B](invitation-email-elements.md)
- [Resgate de convite para colaboração B2B](redemption-experience.md)
- [Adicionar usuários de colaboração B2B sem um convite](add-user-without-invite.md)