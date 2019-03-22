---
title: Estados de utilizador de multi-factor Authentication do Azure - Azure Active Directory
description: Saiba mais sobre os Estados do utilizador no multi-factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5411770e6f9d660557ab9360f026efe4c28a9256
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314387"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Como requerer verificação de dois passos para um utilizador

Pode efetuar uma de duas abordagens para exigir a verificação de dois passos, que necessitam de utilizar uma conta de administrador global. A primeira opção é permitir que cada utilizador para o Azure multi-factor Authentication (MFA). Quando os utilizadores estiverem ativados individualmente, eles executar a verificação de cada vez que iniciar sessão (com algumas exceções, por exemplo, quando iniciarem sessão de IP fidedigno endereços ou quando o _memorizadas dispositivos_ funcionalidade estiver ativada). A segunda opção é definir uma política de acesso condicional que exige a verificação de dois passos sob determinadas condições.

> [!TIP]
> Escolha um dos seguintes métodos para exigir verificação de dois passos, não ambos. Ativar um utilizador para o Azure multi-factor Authentication substitui quaisquer políticas de acesso condicional.

## <a name="choose-how-to-enable"></a>Escolha como pretende ativar

**Ativado, alterando o estado do utilizador** -este é o método tradicional para exigir a verificação de dois passos e será discutido neste artigo. Ele funciona com ambos os Azure MFA na cloud e o servidor MFA do Azure. Ao utilizar este método requer que os usuários realizem a verificação de dois passos **sempre que** que iniciar sessão e substitui as políticas de acesso condicional. Este é o método utilizado para aqueles com licenças do Office 365 ou do Microsoft 365 empresas à medida que estas não incluírem a funcionalidades de acesso condicional.

Ativado pela política de acesso condicional - esta é a maneira mais flexível para ativar a verificação de dois passos para os seus utilizadores. Ativar através da política de acesso condicional só funciona para o Azure MFA na cloud e é uma funcionalidade premium do Azure AD. Obter mais informações sobre este método podem ser encontradas na [implementar com base na cloud do Azure multi-factor Authentication](howto-mfa-getstarted.md).

Ativada pelo Azure AD Identity Protection – este método utiliza a política de risco do Azure AD Identity Protection para exigir verificação de dois passos com base apenas em risco de início de sessão para todas as aplicações na cloud. Este método requer licenciamento do Azure Active Directory P2. Obter mais informações sobre este método podem ser encontradas no [do Azure Active Directory Identity Protection](../identity-protection/howto-sign-in-risk-policy.md)

> [!Note]
> Podem encontrar mais informações sobre preços e licenças no [do Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) e [multi-factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) páginas de preços.

## <a name="enable-azure-mfa-by-changing-user-state"></a>Ativar a MFA do Azure ao alterar o estado do utilizador

Contas de utilizador no multi-factor Authentication do Azure tem os seguintes três Estados distintos:

| Estado | Descrição | Aplicações não baseadas no browser afetadas | Aplicações de browser afetadas | Autenticação moderna afetada |
|:---:|:---:|:---:|:--:|:--:|
| Desativado |O estado predefinido para um novo utilizador não estiver inscrito no MFA do Azure. |Não |Não |Não |
| Ativado |O utilizador inscreveu na MFA do Azure, mas não se registou. Eles recebem um pedido de registo da próxima vez que iniciarem sessão. |Não.  Eles continuar a funcionar até que o processo de registo é concluído. | Sim. Depois da sessão expira, o registo de MFA do Azure é necessário.| Sim. Depois do token de acesso expira, o registo de MFA do Azure é necessário. |
| Imposto |O utilizador foi inscrito e concluiu o processo de registo do MFA do Azure. |Sim. As aplicações necessitam de palavras-passe de aplicação. |Sim. A MFA do Azure é necessária no início de sessão. | Sim. A MFA do Azure é necessária no início de sessão. |

Um Estado do usuário reflete se um administrador inscreveu-los na MFA do Azure e, se eles concluir o processo de registo.

Todos os utilizadores começam *desativado*. Quando inscreve os utilizadores na MFA do Azure, o estado muda para *ativado*. Quando os utilizadores ativados iniciar sessão e concluir o processo de registo, o estado muda para *imposto*.  

### <a name="view-the-status-for-a-user"></a>Ver o estado de um utilizador

Utilize os seguintes passos para aceder à página onde pode ver e gerir perfis de usuário:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Aceda a **do Azure Active Directory** > **utilizadores e grupos** > **todos os utilizadores**.
3. Selecione **multi-factor Authentication**.
   ![Selecione o multi-factor Authentication](./media/howto-mfa-userstates/selectmfa.png)
4. É aberta uma nova página que mostra os Estados do utilizador.
   ![Estado de utilizador do multi-factor authentication - captura de ecrã](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Alterar o estado de um utilizador

1. Utilizar os passos anteriores para obter a multi-factor Authentication **utilizadores** página.
2. Localize o utilizador que pretende ativar a MFA do Azure. Poderá ter de alterar a vista na parte superior.
   ![Localizar utilizador - captura de ecrã](./media/howto-mfa-userstates/enable1.png)
3. Selecione a caixa junto do respetivo nome.
4. No lado direito, sob **passos rápidos**, escolha **ativar** ou **desativar**.
   ![Ativar o utilizador selecionado - captura de ecrã](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Ativada* os usuários estão automaticamente no modo *imposto* quando eles se registrar para o MFA do Azure. Fazer manualmente não alterar o estado do utilizador *imposto*.

5. Confirme a sua seleção na janela de pop-up que é aberta.

Depois de ativar os utilizadores, notificá-los por e-mail. Informe-os de que estes serão solicitados para se registar na próxima vez que iniciarem sessão. Além disso, se sua organização utilizar aplicações não baseadas no browser que não suportam a autenticação moderna, terá de criar palavras-passe de aplicação. Também pode incluir uma ligação para o [Guia do utilizador final de MFA do Azure](../user-help/multi-factor-authentication-end-user.md) para ajudá-los a começar a utilizar.

### <a name="use-powershell"></a>Utilizar o PowerShell

Alterar o estado do utilizador utilizando [do Azure AD PowerShell](/powershell/azure/overview), altere `$st.State`. Existem três Estados possíveis:

* Ativado
* Imposto
* Desativado  

Não é necessário mover os utilizadores diretamente para o *imposto* estado. Se o fizer, aplicações não baseadas no browser deixam de funcionar porque o utilizador não passou pelo registo do Azure MFA nem obteve uma [palavra-passe de aplicação](howto-mfa-mfasettings.md#app-passwords).

Instale o módulo em primeiro lugar, com:

       Install-Module MSOnline
       
> [!TIP]
> Não se esqueça de ligar pela primeira vez com **Connect-MsolService**


 Neste exemplo de script do PowerShell permite que o MFA para um usuário individual:

        Import-Module MSOnline
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = "Enabled"
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Com o PowerShell é uma boa opção quando precisa em massa permitir que os utilizadores. Por exemplo, o script a seguir faz um loop através de uma lista de utilizadores e permite que a MFA nas respetivas contas:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = "Enabled"
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }
    
Para desativar a MFA, utilize este script:

    Get-MsolUser -UserPrincipalName user@domain.com | Set-MsolUser -StrongAuthenticationRequirements @()
    
Também pode ser baixou que para:

    Set-MsolUser -UserPrincipalName user@domain.com -StrongAuthenticationRequirements @()

## <a name="next-steps"></a>Passos Seguintes

Por que foi que o usuário é solicitado ou não lhe for pedido para executar a MFA? Consulte a secção [relatório de inícios de sessão do Azure AD nos relatórios de documento de multi-factor Authentication](howto-mfa-reporting.md#azure-ad-sign-ins-report).

Para configurar definições adicionais, como os IPs fidedignos, mensagens de voz personalizada e alertas de fraude, consulte o artigo [definições de configurar o Azure multi-factor Authentication](howto-mfa-mfasettings.md)

Informações sobre como gerir as definições de utilizador para o Azure multi-factor Authentication podem ser encontrado no artigo [gerir definições de utilizador com o Azure multi-factor Authentication na cloud](howto-mfa-userdevicesettings.md)
