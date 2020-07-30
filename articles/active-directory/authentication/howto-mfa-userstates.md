---
title: Ativar a autenticação multi-factor por utilizador - Diretório Ativo Azure
description: Saiba como ativar a autenticação multi-factor Azure por utilizador alterando o estado do utilizador
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bd688a9e488c1206b0c8531698b061f650e2afe
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87417935"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>Ativar o Multi-Factor Authentication do Azure por utilizador para garantir eventos de início de sessão seguros

Existem duas formas de garantir eventos de entrada do utilizador, exigindo a autenticação de vários fatores no Azure AD. A primeira opção, e preferida, é criar uma política de Acesso Condicional que exija a autenticação de vários fatores sob determinadas condições. A segunda opção é ativar cada utilizador para autenticação multi-factor Azure. Quando os utilizadores são ativados individualmente, realizam a autenticação de vários fatores sempre que fazem súpido (com algumas exceções, como quando fazem sentóias de endereços IP fidedignos ou quando a funcionalidade de _dispositivos lembrados_ é ativada).

> [!NOTE]
> Ativar a autenticação multi-factor Azure utilizando políticas de acesso condicional é a abordagem recomendada. A alteração dos estados dos utilizadores já não é recomendada a menos que as suas licenças não incluam Acesso Condicional, uma vez que exige que os utilizadores realizem MFA sempre que se inscrevam. Para começar a utilizar o Acesso Condicional, consulte [Tutorial: Proteja os eventos de entrada do utilizador com autenticação multi-factor Azure](tutorial-enable-azure-mfa.md).
>
> Para inquilinos gratuitos da Azure AD sem acesso condicional, pode [utilizar falhas de segurança para proteger os utilizadores.](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="azure-multi-factor-authentication-user-states"></a>Estados de autenticação multi-factor Azure

As contas de utilizador na Autenticação Multi-Factor Azure têm os seguintes três estados distintos:

> [!IMPORTANT]
> Permitir a autenticação multi-factor Azure através de uma política de acesso condicional não altera o estado do utilizador. Não se assuste se os utilizadores aparecerem desativados. O Acesso Condicional não muda o estado.
>
> **Não deve ativar ou impor aos utilizadores se estiver a utilizar políticas de Acesso Condicional.**

| Estado | Descrição | Aplicações não-navegador afetadas | Aplicativos de navegador afetados | Autenticação moderna afetada |
|:---:| --- |:---:|:--:|:--:|
| Desativado | O estado predefinido para um novo utilizador não inscrito na Autenticação Multi-Factor Azure. | Não | Não | Não |
| Ativado | O utilizador foi inscrito na Autenticação Multi-Factor Azure, mas não registou métodos de autenticação. Recebem um pedido para se registarem da próxima vez que assinarem. | Não.  Continuam a trabalhar até que o processo de registo esteja concluído. | Yes. Após o termo da sessão, é necessário o registo de autenticação multi-factor Azure.| Yes. Após o termo do token de acesso, é necessário o registo de autenticação multi-factor Azure. |
| Imposto | O utilizador foi inscrito e concluiu o processo de registo da Autenticação Multi-Factor Azure. | Yes. As aplicações requerem senhas de aplicação. | Yes. A autenticação multi-factor Azure é necessária no início de sessão. | Yes. A autenticação multi-factor Azure é necessária no início de sessão. |

O estado de um utilizador reflete se um administrador os inscreveu na Autenticação Multi-Factor Azure e se completaram o processo de registo.

Todos os utilizadores começam *desativados.* Quando inscreve os utilizadores na Autenticação Multi-Factor Azure, o seu estado muda para *Enabled*. Quando os utilizadores ativados iniciaram o seu registo e completam o processo de registo, as alterações de estado para *Aplicadas*.

> [!NOTE]
> Se o MFA for re-activado num objeto de utilizador que já tenha dados de registo, como telefone ou e-mail, então os administradores precisam de ter esse utilizador a re-registar MFA através do portal Azure ou powerShell. Se o utilizador não se re-registar, o seu estado de MFA não transita de *Enabled* para *Enforcado* na UI de gestão de MFA.

## <a name="view-the-status-for-a-user"></a>Ver o estado de um utilizador

Utilize os seguintes passos para aceder à página do portal Azure onde pode visualizar e gerir os estados do utilizador:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
1. Procure e selecione *O Diretório Ativo Azure,* selecione **Utilizadores**  >  **Todos os utilizadores**.
1. Selecione **Multi-Factor Authentication**. Pode ser necessário deslocar-se para a direita para ver esta opção de menu. Selecione a imagem de exemplo abaixo para ver a janela completa do portal Azure e a localização do menu: [ ![ Selecione a autenticação multi-factor da janela do Utilizadores em Azure AD.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Abre-se uma nova página que exibe o estado do utilizador, como mostra o exemplo a seguir.
   ![Screenshot que mostra exemplo informações do estado do utilizador para autenticação multi-factor Azure](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Alterar o estado de um utilizador

Para alterar o estado de autenticação multi-factor Azure para um utilizador, complete os seguintes passos:

1. Utilize os passos anteriores para chegar à página de **utilizadores** de autenticação multi-factor Azure.
1. Encontre o utilizador que pretende ativar para autenticação multi-factor Azure. Pode ser necessário alterar a vista no topo para **os utilizadores**.
   ![Selecione o utilizador para alterar o estado para a partir do separador utilizadores](./media/howto-mfa-userstates/enable1.png)
1. Verifique a caixa ao lado do(s) nome(s) do(s) utilizador(s) para alterar o estado para.
1. No lado direito, sob **passos rápidos,** escolha **Ativar** ou **Desativar**. No exemplo seguinte, o utilizador *John Smith* tem uma verificação ao lado do seu nome e está a ser ativado para utilização: Ative o utilizador ![ selecionado clicando ativar o menu de passos rápidos](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > Os utilizadores *ativados* são automaticamente comutadores para *Aplicados* quando se registam para autenticação multi-factor Azure. Não altere manualmente o estado do utilizador para *"Forçado".*

1. Confirme a sua seleção na janela pop-up que se abre.

Depois de ativar os utilizadores, notifique-os por e-mail. Informe os utilizadores de que é apresentada uma solicitação para lhes pedir que se registem da próxima vez que iniciarem sposição. Além disso, se a sua organização utilizar aplicações não-navegador que não suportem a autenticação moderna, precisam de criar senhas de aplicação. Para obter mais informações, consulte o [guia final de autenticação multi-factor Azure para ajudá-los](../user-help/multi-factor-authentication-end-user.md) a começar.

## <a name="change-state-using-powershell"></a>Alterar estado usando PowerShell

Para alterar o estado do utilizador utilizando [o Azure AD PowerShell,](/powershell/azure/)altere o `$st.State` parâmetro para uma conta de utilizador. Existem três estados possíveis para uma conta de utilizador:

* *Ativado*
* *Imposto*
* *Desativado*  

Não mova os utilizadores diretamente para o estado *forçado.* Se o fizer, as aplicações não baseadas no navegador deixam de funcionar porque o utilizador não passou pelo registo de autenticação multi-factor Azure e obteve uma [senha de aplicação.](howto-mfa-app-passwords.md)

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

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Converter utilizadores de MFA por utilizador para MFA baseado em acesso condicional

O Seguinte PowerShell pode ajudá-lo a fazer a conversão para a autenticação multi-factor de acesso condicional baseada em Azure.

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
> Se o MFA for re-activado num objeto de utilizador que já tenha dados de registo, como telefone ou e-mail, então os administradores precisam de ter esse utilizador a re-registar MFA através do portal Azure ou powerShell. Se o utilizador não se re-registar, o seu estado de MFA não transita de *Enabled* para *Enforcado* na UI de gestão de MFA.

## <a name="next-steps"></a>Próximos passos

Para configurar as definições de autenticação multi-factor Azure, consulte [as definições de autenticação multi-factor Configure Azure](howto-mfa-mfasettings.md).

Para gerir as definições do utilizador para autenticação multi-factor Azure, consulte [gerir as definições do utilizador com autenticação multi-factor Azure](howto-mfa-userdevicesettings.md).

Para entender por que um utilizador foi solicitado ou não solicitado para realizar MFA, consulte [os relatórios de autenticação multi-factor Azure](howto-mfa-reporting.md).
