---
title: Ativar a autenticação multi-factor por utilizador - Diretório Ativo Azure
description: Saiba como ativar a autenticação multi-factor Azure AD por utilizador alterando o estado do utilizador
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/17/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 240fb04068f255128f33e79748762305e4d6b704
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94838779"
---
# <a name="enable-per-user-azure-ad-multi-factor-authentication-to-secure-sign-in-events"></a>Ativar por utilizador Azure AD Multi-Factor Autenticação para garantir eventos de entrada

Para garantir eventos de entrada do utilizador em Azure AD, pode necessitar de autenticação multi-factor (MFA). Permitir a autenticação multi-factor Azure AD utilizando políticas de acesso condicional é a abordagem recomendada para proteger os utilizadores. O Acesso Condicional é uma funcionalidade Azure AD Premium P1 ou P2 que permite aplicar regras para exigir MFA conforme necessário em determinados cenários. Para começar a utilizar o Acesso Condicional, consulte [Tutorial: Proteja os eventos de entrada do utilizador com autenticação multi-factor Ad Azure](tutorial-enable-azure-mfa.md).

Para inquilinos gratuitos da Azure AD sem acesso condicional, pode [utilizar falhas de segurança para proteger os utilizadores.](../fundamentals/concept-fundamentals-security-defaults.md) Os utilizadores são solicitados para mFA conforme necessário, mas você não pode definir suas próprias regras para controlar o comportamento.

Se necessário, pode ativar cada conta para a autenticação multi-factor Azure AD por utilizador. Quando os utilizadores são ativados individualmente, realizam a autenticação de vários fatores sempre que fazem súpido (com algumas exceções, como quando fazem sentóias de endereços IP fidedignos ou quando o _MFA de ressalsse na funcionalidade dispositivos fidedignos_ é ativado).

Alterar estados de utilizador não é recomendado a menos que as suas licenças Azure AD não incluam Acesso Condicional e não queira usar padrão de segurança. Para obter mais informações sobre as diferentes formas de ativar o MFA, consulte [Funcionalidades e licenças para Autenticação Multi-Factor AD Azure.](concept-mfa-licensing.md)

> [!IMPORTANT]
>
> Este artigo detalha como visualizar e alterar o estado de autenticação multi-factor Azure AD por utilizador. Se utilizar acesso condicional ou incumprimentos de segurança, não revê ou ativa contas de utilizador utilizando estes passos.
>
> Permitir a autenticação multi-factor Azure através de uma política de acesso condicional não altera o estado do utilizador. Não se assuste se os utilizadores aparecerem desativados. O Acesso Condicional não muda o estado.
>
> **Não ative nem aplique a autenticação multi-factor Azure AD por utilizador se utilizar políticas de Acesso Condicional.**

## <a name="azure-ad-multi-factor-authentication-user-states"></a>Estados de autenticação multi-factor Azure AD

O estado de um utilizador reflete se um administrador os inscreveu na autenticação multi-factor Azure AD por utilizador. As contas de utilizador em Azure AD Multi-Factor Authentication têm os seguintes três estados distintos:

| Estado | Descrição | Autenticação do legado afetada | Aplicativos de navegador afetados | Autenticação moderna afetada |
|:---:| --- |:---:|:--:|:--:|
| Desativado | O estado predefinido para um utilizador não inscrito na Autenticação Multi-Factor Azure AD por utilizador. | No | No | No |
| Ativado | O utilizador está inscrito na autenticação multi-factor Azure AD por utilizador, mas ainda pode utilizar a sua palavra-passe para autenticação antiga. Se o utilizador ainda não tiver registado métodos de autenticação MFA, receberá um pedido para registar a próxima vez que iniciar a sua inscrição na autenticação moderna (por exemplo, através de um navegador web). | Não. A autenticação do legado continua a funcionar até que o processo de registo esteja concluído. | Sim. Após o termo da sessão, é necessário o registo de autenticação multi-factor Azure AD.| Sim. Após o termo do token de acesso, é necessário o registo de autenticação multi-factor Azure AD. |
| Imposto | O utilizador está inscrito por utilizador na Autenticação Multi-Factor Azure AD. Se o utilizador ainda não tiver registado métodos de autenticação, receberá um pedido para registar a próxima vez que iniciar a sua inscrição na autenticação moderna (por exemplo, através de um navegador web). Os utilizadores que completem o registo durante o estado *Ativado* são automaticamente transferidos para o estado *forçado.* | Sim. As aplicações requerem senhas de aplicação. | Sim. A autenticação multi-factor Azure AD é necessária no início de sação. | Sim. A autenticação multi-factor Azure AD é necessária no início de sação. |

Todos os utilizadores começam *desativados.* Quando inscreve os utilizadores na autenticação multi-factor Azure AD por utilizador, o seu estado muda para *Enabled*. Quando os utilizadores ativados iniciaram o seu registo e completam o processo de registo, as alterações de estado para *Aplicadas*. Os administradores podem deslocar os utilizadores entre estados, incluindo de *Forçados* a *Ativados* ou *Deficientes*.

> [!NOTE]
> Se o MFA por utilizador for re-activado num utilizador e o utilizador não se re-registar, o seu estado de MFA não transita de *Enabled* para *Enforcado* na UI de gestão de MFA. O administrador deve deslocar o utilizador diretamente para *o Imposto*.

## <a name="view-the-status-for-a-user"></a>Ver o estado de um utilizador

Para visualizar e gerir os estados do utilizador, complete os seguintes passos para aceder à página do portal Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
1. Procure e selecione *O Diretório Ativo Azure,* selecione **Utilizadores**  >  **Todos os utilizadores**.
1. Selecione **Multi-Factor Authentication**. Pode ser necessário deslocar-se para a direita para ver esta opção de menu. Selecione a imagem de exemplo abaixo para ver a janela completa do portal Azure e a localização do menu: [ ![ Selecione a autenticação multi-factor da janela do Utilizadores em Azure AD.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Abre-se uma nova página que exibe o estado do utilizador, como mostra o exemplo a seguir.
   ![Screenshot que mostra exemplo informações do estado do utilizador para autenticação multi-factor Azure AD](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Alterar o estado de um utilizador

Para alterar o estado de autenticação multi-factor Azure AD por utilizador para um utilizador, complete os seguintes passos:

1. Utilize os passos anteriores para [visualizar o estado de um utilizador](#view-the-status-for-a-user) para chegar à página de **utilizadores** de autenticação multi-factor Azure AD.
1. Encontre o utilizador que pretende ativar para a autenticação multi-factor Azure AD por utilizador. Pode ser necessário alterar a vista no topo para **os utilizadores**.
   ![Selecione o utilizador para alterar o estado para a partir do separador utilizadores](./media/howto-mfa-userstates/enable1.png)
1. Verifique a caixa ao lado do(s) nome(s) do(s) utilizador(s) para alterar o estado para.
1. No lado direito, sob **passos rápidos,** escolha **Ativar** ou **Desativar**. No exemplo seguinte, o utilizador *John Smith* tem uma verificação ao lado do seu nome e está a ser ativado para utilização: Ative o utilizador ![ selecionado clicando ativar o menu de passos rápidos](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > Os utilizadores *ativados* são automaticamente comutadores para *Aplicados* quando se registam para autenticação multi-factor Azure AD. Não altere manualmente o estado do utilizador para *"Enforcado",* a menos que o utilizador já esteja registado ou se for aceitável que o utilizador experimente a interrupção nas ligações aos protocolos de autenticação legado.

1. Confirme a sua seleção na janela pop-up que se abre.

Depois de ativar os utilizadores, notifique-os por e-mail. Informe os utilizadores de que é apresentada uma solicitação para lhes pedir que se registem da próxima vez que iniciarem sposição. Além disso, se a sua organização utilizar aplicações não-navegador que não suportem a autenticação moderna, precisam de criar senhas de aplicação. Para obter mais informações, consulte o [guia final de autenticação multi-factor Azure AD para ajudá-los](../user-help/multi-factor-authentication-end-user-first-time.md) a começar.

## <a name="change-state-using-powershell"></a>Alterar estado usando PowerShell

Para alterar o estado do utilizador utilizando [o Azure AD PowerShell,](/powershell/azure/)altere o `$st.State` parâmetro para uma conta de utilizador. Existem três estados possíveis para uma conta de utilizador:

* *Ativado*
* *Imposto*
* *Desativado*  

Em geral, não mova os utilizadores diretamente para o estado *forçado,* a menos que já estejam registados para MFA. Se o fizer, as aplicações de autenticação antigas deixam de funcionar porque o utilizador não passou pelo registo de autenticação multi-factor Azure AD e obteve uma [senha de aplicação.](howto-mfa-app-passwords.md) Em alguns casos, este comportamento pode ser desejado, mas tem impacto na experiência do utilizador até que o utilizador se registe.

Para começar, instale o módulo *MSOnline* utilizando [o Módulo de Instalação](/powershell/module/powershellget/install-module) da seguinte forma:

```PowerShell
Install-Module MSOnline
```

Em seguida, ligue-se usando [o Connect-MsolService:](/powershell/module/msonline/connect-msolservice)

```PowerShell
Connect-MsolService
```

O seguinte exemplo O script PowerShell permite MFA para um utilizador individual chamado *bsimon@contoso.com* :

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

A utilização do PowerShell é uma boa opção quando é necessário ativar a granel os utilizadores. O seguinte script loops através de uma lista de utilizadores e permite MFA nas suas contas. Defina as contas de utilizador defini-lo na primeira linha para o `$users` seguinte:

   ```PowerShell
   # Define your list of users to update state in bulk
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

Para desativar o MFA, o exemplo a seguir obtém um utilizador com [Get-MsolUser,](/powershell/module/msonline/get-msoluser)em seguida, remove quaisquer *requisitos strongAuthenticationRequirements definidos* para o utilizador definido usando [Set-MsolUser](/powershell/module/msonline/set-msoluser):

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

Também pode desativar diretamente o MFA para um utilizador que utilize [o Set-MsolUser](/powershell/module/msonline/set-msoluser) da seguinte forma:

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access"></a>Converter utilizadores de MFA por utilizador para Acesso Condicional

O Seguinte PowerShell pode ajudá-lo a fazer a conversão para a autenticação multi-factor Azure AD baseada em acesso condicional.

```PowerShell
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

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Se o MFA for re-activado num utilizador e o utilizador não se re-registar, o seu estado de MFA não transita de *Enabled* para *Enforcado* na UI de gestão de MFA. Neste caso, o administrador deve deslocar o utilizador diretamente para *o Imposto*.

## <a name="next-steps"></a>Passos seguintes

Para configurar as definições de autenticação multi-factor Azure AD, consulte as definições de  [autenticação multi-factor Azure Ad](howto-mfa-mfasettings.md).

Para gerir as definições do utilizador para autenticação multi-factor Azure AD, consulte [Gerir as definições do utilizador com autenticação multi-factor Ad Azure](howto-mfa-userdevicesettings.md).

Para entender por que um utilizador foi solicitado ou não solicitado para realizar MFA, consulte [os relatórios de autenticação multi-factor Azure AD](howto-mfa-reporting.md).
