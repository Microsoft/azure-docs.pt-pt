---
title: Ativar a autenticação multi-factor por utilizador - Diretório Ativo Azure
description: Saiba como ativar a autenticação multi-factor do Azure Azure alterando o estado do utilizador
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e8ceaf13324864c7ec3df731c3e710815b0eba9
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309785"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>Ativar a autenticação multi-factor Azure por utilizador para garantir eventos de entrada

Existem duas formas de garantir eventos de entrada no utilizador, exigindo a autenticação de vários fatores em Azure AD. A primeira, e preferencial, opção é criar uma política de Acesso Condicional que exija a autenticação de vários fatores em determinadas condições. A segunda opção é ativar cada utilizador para autenticação de multifactor Azure. Quando os utilizadores são ativados individualmente, realizam a autenticação de vários fatores sempre que fazem o insessão (com algumas exceções, como quando fazem o insessão a partir de endereços IP fidedignos ou quando a função de _dispositivos recordados_ é ativada).

> [!NOTE]
> Permitir a autenticação multi-factor azure utilizando políticas de acesso condicional é a abordagem recomendada. A alteração dos estados de utilizador já não é recomendada a menos que as suas licenças não incluam acesso condicional, uma vez que exige que os utilizadores realizem MFA sempre que iniciarem o seu acesso.
>
> Para começar a utilizar o Acesso Condicional, consulte [Tutorial: Eventos de inscrição de utilizador seguros com autenticação de multi-factor sinuoso.](tutorial-enable-azure-mfa.md)

## <a name="azure-multi-factor-authentication-user-states"></a>Estados de utilizador de autenticação multi-factor Azure

As contas de utilizador na Autenticação Multifactor Azure têm os seguintes três estados distintos:

> [!IMPORTANT]
> Permitir a autenticação multi-factor azure através de uma política de acesso condicional não altera o estado do utilizador. Não se assuste se os utilizadores aparecerem desativados. O Acesso Condicional não muda o estado.
>
> **Não deve ativar ou impor utilizadores se estiver a utilizar políticas de Acesso Condicional.**

| Estado | Descrição | Aplicações não-navegador afetadas | Aplicativos de navegador afetados | Autenticação moderna afetada |
|:---:| --- |:---:|:--:|:--:|
| Desativado | O estado padrão para um novo utilizador não inscrito na Autenticação Multi-Factor Azure. | Não | Não | Não |
| Ativado | O utilizador foi inscrito na Autenticação Multi-Factor Azure, mas não está registado. Recebem uma solicitação para se registarem da próxima vez que assinarem. | Não.  Continuam a trabalhar até que o processo de registo esteja concluído. | Sim. Após o termo da sessão, é necessário o registo de autenticação de multifactor estoque Azure.| Sim. Após o termo do sinal de acesso, é necessário o registo de autenticação de multifactor estoirar. |
| Imposto | O utilizador foi inscrito e completou o processo de registo da Autenticação Multifactor Azure. | Sim. As aplicações requerem senhas de aplicação. | Sim. A autenticação de multifactor azure é necessária no login. | Sim. A autenticação de multifactor azure é necessária no login. |

O estado de um utilizador reflete se um administrador os inscreveu na Autenticação Multi-Factor Azure e se completaram o processo de registo.

Todos os utilizadores iniciam *o Desativado*. Quando inscreve os utilizadores na Autenticação Multifactor Azure, o seu estado muda para *Enabled*. Quando os utilizadores habilitados assinam e completam o processo de registo, o seu estado muda para *O Imposto*.

> [!NOTE]
> Se o MFA for reativado num objeto de utilizador que já tenha dados de registo, como telefone ou e-mail, os administradores precisam de ter esse utilizador a reregistar o MFA através do portal Azure ou da PowerShell. Se o utilizador não se reregistar, o seu estado de MFA não transita de *Enabled* para *Enforced* na UI de gestão de MFA.

## <a name="view-the-status-for-a-user"></a>Ver o estado de um utilizador

Utilize os seguintes passos para aceder à página do portal Azure onde pode ver e gerir os estados do utilizador:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
1. Procure e selecione *O Diretório Ativo Azure,* em seguida, selecione **Utilizadores** > **Todos os utilizadores**.
1. **Selecione a autenticação de vários fatores.** Pode ser necessário deslocar-se para a direita para ver esta opção de menu. Selecione a imagem de exemplo abaixo para ver a janela completa do portal Azure e a localização do menu:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Selecione autenticação multi-factor da janela utilizadores em Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Abre-se uma nova página que exibe o estado do utilizador, como mostra o seguinte exemplo.
   ![Screenshot que mostra informações do estado do utilizador exemplo para autenticação de multi-factor Supor](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Alterar o estado para um utilizador

Para alterar o estado de autenticação de multi-factores Azure para um utilizador, complete os seguintes passos:

1. Utilize os passos anteriores para chegar à página de **utilizadores** de autenticação multifactor Azure.
1. Encontre o utilizador que pretende ativar para autenticação de vários fatores Azure. Pode ser necessário alterar a vista no topo para **os utilizadores**.
   ![Selecione o utilizador para alterar o estado do separador utilizadores](./media/howto-mfa-userstates/enable1.png)
1. Verifique a caixa junto ao(s) nome(s) do(s) utilizador(s) para alterar o estado.
1. Do lado direito, sob **passos rápidos,** escolha **Ativar** ou **Desativar**. No exemplo seguinte, o utilizador *John Smith* tem uma verificação ao ![lado do seu nome e está a ser ativado para ser utilizado: Ativar o utilizador selecionado clicando em Ativar no menu de passos rápidos](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > Os utilizadores *ativados* são automaticamente trocados para *'Enforced'* quando se registam para autenticação multi-factor Azure. Não altere manualmente o estado de utilizador para *'Imposto'.*

1. Confirme a sua seleção na janela pop-up que se abre.

Depois de ativar os utilizadores, notifique-os por e-mail. Informe os utilizadores de que é apresentada uma solicitação para lhes pedir que se registem da próxima vez que iniciarem a sua inscrição. Além disso, se a sua organização usa aplicações não-navegador as que não suportam a autenticação moderna, precisam de criar senhas de aplicação. Para mais informações, consulte o guia de utilizador final de [autenticação multifactor Azure](../user-help/multi-factor-authentication-end-user.md) para os ajudar a começar.

## <a name="change-state-using-powershell"></a>Alterar estado usando powerShell

Para alterar o estado de utilizador utilizando o [Azure AD PowerShell,](/powershell/azure/overview)altere o `$st.State` parâmetro para uma conta de utilizador. Existem três estados possíveis para uma conta de utilizador:

* *Ativado*
* *Imposto*
* *Desativado*  

Não mova os utilizadores diretamente para o estado *forçado.* Se o fizer, as aplicações não baseadas no navegador deixam de funcionar porque o utilizador não passou pelo registo de autenticação de multifactor do Azure e obteve uma senha de [aplicação.](howto-mfa-mfasettings.md#app-passwords)

Para começar, instale o módulo *MSOnline* utilizando o [Módulo de Instalação](/powershell/module/powershellget/install-module) da seguinte forma:

```PowerShell
Install-Module MSOnline
```

Em seguida, [ligue-se utilizando o Connect-MsolService:](/powershell/module/msonline/connect-msolservice)

```PowerShell
Connect-MsolService
```

O seguinte exemplo O script PowerShell permite *bsimon@contoso.com*mFA para um utilizador individual chamado:

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

Utilizar o PowerShell é uma boa opção quando precisa de ativar os utilizadores em massa. O script seguinte passa por uma lista de utilizadores e permite o MFA nas suas contas. Defina as contas de utilizador definidas na primeira linha para `$users` o seguinte:

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

Para desativar o MFA, o seguinte exemplo recebe um utilizador com [Get-MsolUser](/powershell/module/msonline/get-msoluser)e, em seguida, remove quaisquer Requisitos de *Autenticação Forte definidos* para o utilizador definido utilizando [Set-MsolUser:](/powershell/module/msonline/set-msoluser)

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

Também pode desativar diretamente o MFA para um utilizador que utilize [o Set-MsolUser](/powershell/module/msonline/set-msoluser) da seguinte forma:

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Converter utilizadores de MFA por utilizador para MFA baseado em Acesso Condicional

O seguinte PowerShell pode ajudá-lo a efetuar a conversão para A autenticação multi-factor azure baseada em acesso condicional.

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
> Recentemente mudamos o comportamento e este guião da PowerShell. Anteriormente, o guião salvou os métodos de MFA, desativou o MFA e restaurou os métodos. Isto já não é necessário agora que o comportamento padrão para desativar não limpa os métodos.
>
> Se o MFA for reativado num objeto de utilizador que já tenha dados de registo, como telefone ou e-mail, os administradores precisam de ter esse utilizador a reregistar o MFA através do portal Azure ou da PowerShell. Se o utilizador não se reregistar, o seu estado de MFA não transita de *Enabled* para *Enforced* na UI de gestão de MFA.

## <a name="next-steps"></a>Passos seguintes

Para configurar definições de autenticação multi-factor Azure, como IPs fidedignos, mensagens de voz personalizadas e alertas de fraude, consulte as definições de [autenticação multi-factor Do Configure Azure](howto-mfa-mfasettings.md). Para gerir as definições do utilizador para autenticação de multi-factores, consulte Gerir as definições do [utilizador com a autenticação de vários fatores Azure](howto-mfa-userdevicesettings.md).

Para entender por que razão um utilizador foi solicitado ou não para executar MFA, consulte [os relatórios de autenticação de multi-factors do Azure](howto-mfa-reporting.md#azure-ad-sign-ins-report).
