---
title: Estados do usuário da autenticação multifator do Azure-Azure Active Directory
description: Saiba mais sobre os Estados do usuário na autenticação multifator do Azure.
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
ms.openlocfilehash: 54146927bf344eed63e24a3df073aa13f7fa0676
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68319919"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Como exigir a verificação em duas etapas para um usuário

Você pode adotar uma das duas abordagens para exigir a verificação em duas etapas, ambas exigindo o uso de uma conta de administrador global. A primeira opção é habilitar cada usuário para a autenticação multifator do Azure (MFA). Quando os usuários são habilitados individualmente, eles executam a verificação em duas etapas sempre que entrarem (com algumas exceções, como quando eles entram em endereços IP confiáveis ou quando o recurso de _dispositivos lembrados_ está ativado). A segunda opção é configurar uma política de acesso condicional que exija a verificação em duas etapas em determinadas condições.

> [!TIP]
> Habilitar a autenticação multifator do Azure usando políticas de acesso condicional é a abordagem recomendada. Não é mais recomendável alterar os Estados do usuário, a menos que suas licenças não incluam o acesso condicional, pois eles exigirão que os usuários executem a MFA sempre que entrarem.

## <a name="choose-how-to-enable"></a>Escolha como habilitar

**Habilitado alterando o estado do usuário** – esse é o método tradicional para exigir a verificação em duas etapas e é discutido neste artigo. Ele funciona com a MFA do Azure na nuvem e no servidor Azure MFA. O uso desse método exige que os usuários executem a verificação em duas etapas **sempre** que entrarem e substituam as políticas de acesso condicional.

Habilitado pela política de acesso condicional – esse é o meio mais flexível para habilitar a verificação em duas etapas para seus usuários. Habilitar o uso da política de acesso condicional funciona apenas para o Azure MFA na nuvem e é um recurso Premium do Azure AD. Mais informações sobre esse método podem ser encontradas em [implantar a autenticação multifator do Azure baseada em nuvem](howto-mfa-getstarted.md).

Habilitado por Azure AD Identity Protection-esse método usa a política de risco de Azure AD Identity Protection para exigir a verificação em duas etapas com base apenas no risco de entrada para todos os aplicativos de nuvem. Esse método requer o licenciamento do Azure Active Directory P2. Mais informações sobre esse método podem ser encontradas em [Azure Active Directory Identity Protection](../identity-protection/howto-sign-in-risk-policy.md)

> [!Note]
> Mais informações sobre licenças e preços podem ser encontradas nas páginas [de preços](https://azure.microsoft.com/pricing/details/active-directory/
) do Azure AD e da [autenticação](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) multifator.

## <a name="enable-azure-mfa-by-changing-user-state"></a>Habilitar o Azure MFA alterando o estado do usuário

As contas de usuário na autenticação multifator do Azure têm os três Estados distintos a seguir:

| Estado | Descrição | Aplicativos sem navegador afetados | Aplicativos de navegador afetados | Autenticação moderna afetada |
|:---:|:---:|:---:|:--:|:--:|
| Desativado |O estado padrão para um novo usuário não registrado no Azure MFA. |Não |Não |Não |
| Enabled |O usuário foi inscrito no Azure MFA, mas não foi registrado. Eles receberão uma solicitação para registrar na próxima vez que entrarem. |Não.  Eles continuam funcionando até que o processo de registro seja concluído. | Sim. Depois que a sessão expira, o registro do Azure MFA é necessário.| Sim. Depois que o token de acesso expira, o registro do Azure MFA é necessário. |
| Imposto |O usuário foi registrado e concluiu o processo de registro para o Azure MFA. |Sim. Os aplicativos exigem senhas de aplicativo. |Sim. O Azure MFA é necessário no logon. | Sim. O Azure MFA é necessário no logon. |

O estado de um usuário reflete se um administrador o registrou no Azure MFA e se ele concluiu o processo de registro.

Todos os usuários iniciam *desabilitados*. Quando você registra os usuários no Azure MFA, seu estado muda para *habilitado*. Quando os usuários habilitados entram e concluem o processo de registro, seu estado muda para *imposto*.  

### <a name="view-the-status-for-a-user"></a>Exibir o status de um usuário

Use as etapas a seguir para acessar a página em que você pode exibir e gerenciar os Estados do usuário:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Vá para **Azure Active Directory** > **usuários e grupos** > **todos os usuários**.
3. Selecione **autenticação**multifator.
   ![Selecionar autenticação multifator](./media/howto-mfa-userstates/selectmfa.png)
4. Uma nova página que exibe os Estados do usuário é aberta.
   ![status do usuário da autenticação multifator-captura de tela](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Alterar o status de um usuário

1. Use as etapas anteriores para acessar a página **usuários** da autenticação multifator do Azure.
2. Localize o usuário que você deseja habilitar para o Azure MFA. Talvez seja necessário alterar a exibição na parte superior.
   ![Selecione o usuário para o qual alterar o status na guia usuários](./media/howto-mfa-userstates/enable1.png)
3. Marque a caixa ao lado do nome.
4. À direita, em **etapas rápidas**, escolha **habilitar** ou **desabilitar**.
   ![Habilitar o usuário selecionado clicando em habilitar no menu etapas rápidas](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > Os usuários habilitados são automaticamente alternados para *imposto* quando se registram no Azure MFA. Não altere manualmente o estado do usuário para *imposto*.

5. Confirme sua seleção na janela pop-up que é aberta.

Depois de habilitar os usuários, notifique-os por email. Informe que eles serão solicitados a se registrarem na próxima vez que entrarem. Além disso, se sua organização usa aplicativos sem navegador que não dão suporte à autenticação moderna, eles precisam criar senhas de aplicativo. Você também pode incluir um link para o [Guia do usuário final do Azure MFA](../user-help/multi-factor-authentication-end-user.md) para ajudá-los a começar.

### <a name="use-powershell"></a>Utilizar o PowerShell

Para alterar o estado do usuário usando o [PowerShell do Azure ad](/powershell/azure/overview), altere `$st.State`. Há três estados possíveis:

* Enabled
* Imposto
* Desativado  

Não mova os usuários diretamente para o estado *imposto* . Se você fizer isso, aplicativos não baseados em navegador deixarão de funcionar porque o usuário não passou pelo registro do Azure MFA e obteve uma [senha de aplicativo](howto-mfa-mfasettings.md#app-passwords).

Instale o módulo primeiro, usando:

   ```PowerShell
   Install-Module MSOnline
   ```

> [!TIP]
> Não se esqueça de conectar-se primeiro usando **Connect-MsolService**

Este exemplo de script do PowerShell habilita a MFA para um usuário individual:

   ```PowerShell
   Import-Module MSOnline
   $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
   $st.RelyingParty = "*"
   $st.State = "Enabled"
   $sta = @($st)
   Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
   ```

Usar o PowerShell é uma boa opção quando você precisa habilitar usuários em massa. Por exemplo, o script a seguir executa um loop em uma lista de usuários e habilita a MFA em suas contas:

   ```PowerShell
   $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
   foreach ($user in $users)
   {
       $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
       $st.RelyingParty = "*"
       $st.State = "Enabled"
       $sta = @($st)
       Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
   }
   ```

Para desabilitar o MFA, use este script:

   ```PowerShell
   Get-MsolUser -UserPrincipalName user@domain.com | Set-MsolUser -StrongAuthenticationMethods @()
   ```

que também pode ser reduzido para:

   ```PowerShell
   Set-MsolUser -UserPrincipalName user@domain.com -StrongAuthenticationRequirements @()
   ```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Converter usuários de MFA por usuário para MFA baseada em acesso condicional

O PowerShell a seguir pode ajudá-lo a fazer a conversão para o acesso condicional com base na autenticação multifator do Azure.

```PowerShell
# Disable MFA for all users, keeping their MFA methods intact
Get-MsolUser -All | Disable-MFA -KeepMethods

# Enforce MFA for all users
Get-MsolUser -All | Set-MfaState -State Enforced

# Wrapper to disable MFA with the option to keep the MFA
# methods (to avoid having to proof-up again later)
function Disable-Mfa {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipeline=$True)]
        $User,
        [switch] $KeepMethods
    )

    Process {

        Write-Verbose ("Disabling MFA for user '{0}'" -f $User.UserPrincipalName)
        $User | Set-MfaState -State Disabled

        if ($KeepMethods) {
            # Restore the MFA methods which got cleared when disabling MFA
            Set-MsolUser -ObjectId $User.ObjectId `
                         -StrongAuthenticationMethods $User.StrongAuthenticationMethods
        }
    }
}

# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

```

## <a name="next-steps"></a>Passos Seguintes

* Por que um usuário foi solicitado ou não solicitado a executar MFA? Consulte a seção [relatório de entradas do Azure AD no documento relatórios no Azure autenticação](howto-mfa-reporting.md#azure-ad-sign-ins-report)multifator.
* Para definir configurações adicionais, como IPs confiáveis, mensagens de voz personalizadas e alertas de fraude, consulte o artigo [definir as configurações da autenticação multifator do Azure](howto-mfa-mfasettings.md)
* Informações sobre como gerenciar configurações de usuário para a autenticação multifator do Azure podem ser encontradas no artigo [gerenciar configurações de usuário com a autenticação multifator do Azure na nuvem](howto-mfa-userdevicesettings.md)
